---
layout: post
title: nginx之iphash算法
date: 2018-07-28
tag: nginx
---
今日客户生产环境中发现了个奇怪的现象：nginx的upstream中部署了10多台后端服务器，但是在实际的监控中发现用户的请求只负载到了其中3-4台服务器。虽然后来已经证实是由于nginx前还有部署WAF，导致了获取到的ip非用户的真实ip，从而导致了负载的混乱。

问题发生期间，客户it方面经过讨论，原先提了一个这样的一个解决方案：他们认为现在不是配置了10来台，现在实际只负载到3-4台上吗？那么我们直接把剩下没有负载到的后端服务器从负载清单里去除掉，然后把剩下被负载到的几台服务器进行配置升级（用的阿里云，可以弹性扩展）。这样真的可以解决问题吗？我们一般会想到iphash如果后台服务器的配置总数发生了变化，那么其转发的规则也会自然发生变化，也就是说后端配置了10台服务器负载了到3台上，那么把配置修改为3台的话，很可能就转发到1-2台上了。我们来看下nginx iphash的算法，是否可以验证上面的说法。

直接看代码：

```c++
178	    for ( ;; ) {
179	
180	        for (i = 0; i < (ngx_uint_t) iphp->addrlen; i++) {
181	            hash = (hash * 113 + iphp->addr[i]) % 6271;
182	        }
183	
184	        w = hash % iphp->rrp.peers->total_weight;
185	        peer = iphp->rrp.peers->peer;
186	        p = 0;
187	
188	        while (w >= peer->weight) {
189	            w -= peer->weight;
190	            peer = peer->next;
191	            p++;
192	        }
193	
194	        n = p / (8 * sizeof(uintptr_t));
195	        m = (uintptr_t) 1 << p % (8 * sizeof(uintptr_t));
196	
197	        if (iphp->rrp.tried[n] & m) {
198	            goto next;
199	        }
200	
201	        ngx_log_debug2(NGX_LOG_DEBUG_HTTP, pc->log, 0,
202	                       "get ip hash peer, hash: %ui %04XL", p, (uint64_t) m);
203	
204	        ngx_http_upstream_rr_peer_lock(iphp->rrp.peers, peer);
205	
206	        if (peer->down) {
207	            ngx_http_upstream_rr_peer_unlock(iphp->rrp.peers, peer);
208	            goto next;
209	        }
210	
211	        if (peer->max_fails
212	            && peer->fails >= peer->max_fails
213	            && now - peer->checked <= peer->fail_timeout)
214	        {
215	            ngx_http_upstream_rr_peer_unlock(iphp->rrp.peers, peer);
216	            goto next;
217	        }
218	
219	        if (peer->max_conns && peer->conns >= peer->max_conns) {
220	            ngx_http_upstream_rr_peer_unlock(iphp->rrp.peers, peer);
221	            goto next;
222	        }
223	
224	        break;
225	
226	    next:
227	
228	        if (++iphp->tries > 20) {
229	            ngx_http_upstream_rr_peers_unlock(iphp->rrp.peers);
230	            return iphp->get_rr_peer(pc, &iphp->rrp);
231	        }
232	    }
```

主要代码在180-184的几行。

1、for循环 i取 012三个值，而ip的点分十进制表示方法将ip分成四段（如：192.168.1.1），但是这里循环时只是将ip的前三个端作为参数加入hash函数。这样做的目的是保证ip地址前三位相同的用户经过hash计算将分配到相同的后端server。

作者的这个考虑是极为可取的，因此ip地址前三位相同通常意味着来着同一个局域网或者相邻区域，使用相同的后端服务让nginx在一定程度上更具有一致性。

2、哈希函数：hash = (hash * 113 + iphp->addr[i]) % 6271

作者使用了这样一个极为简单的hash函数，当然目的是为了性能。虽然该hash函数的效果并不是很好，产生的的分布并不是太均衡。但这在nginx选择后端server这样的应用场景已经足够，关键是其简单性。

3、iphash后如何决定分配在哪台服务器上？
从184行的代码w = hash % iphp->rrp.peers->total_weight;可以看到：把ip地址hash以后，立刻就针对当前总共的服务器个数取模。那么也就初步证实了后端服务器的总数是会直接影响到负载的规则，证实了之前的设想。故解决之前的问题，最正确合理的做法，还是需要通过nginx的realip模块，把WAF的ip过滤掉才是正确且合理的做法。