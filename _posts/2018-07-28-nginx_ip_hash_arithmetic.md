---
layout: post
title: nginx之iphash算法
date: 2018-07-28
tag: nginx
---
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

主要代码在180-182的三行。

1、for循环 i取 012三个值，而ip的点分十进制表示方法将ip分成四段（如：192.168.1.1），但是这里循环时只是将ip的前三个端作为参数加入hash函数。这样做的目的是保证ip地址前三位相同的用户经过hash计算将分配到相同的后端server。

作者的这个考虑是极为可取的，因此ip地址前三位相同通常意味着来着同一个局域网或者相邻区域，使用相同的后端服务让nginx在一定程度上更具有一致性。

2、哈希函数：hash = (hash * 113 + iphp->addr[i]) % 6271

作者使用了这样一个极为简单的hash函数，当然目的是为了性能。虽然该hash函数的效果并不是很好，产生的的分布并不是太均衡。但这在nginx选择后端server这样的应用场景已经足够，关键是其简单性。