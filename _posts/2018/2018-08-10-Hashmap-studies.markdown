---
layout: "post"
title: "Hashmap-studies #2"
date: "2018-08-10 23:30"
tag:
- HashMap
comments : true
---

# I Wrote a Fast HashTable
 > Malte Skarupke

* As others have pointed out,<a href="http://sebastiansylvan.com/2013/05/08/robin-hood-hashing-should-be-your-default-hash-table-implementation/">
 Robin Hood Hashing should be your default hash table implementation</a>.

 * Unfortunately I couldn't find a hashtable on the internet that uses robin hood hashing while offering a STL-style interface. I know that some people don't like the STL but I've found that those people tend to write poorer interfaces. So I learn from that by not trying to invent my own interface. You can use this hash table as a replacement for std::unordered_map and you will get a speedup in most cases.

* In order to reduce name conflicts I call it <a href="https://github.com/skarupke/sherwood_map/tree/master/finished">sherwood_map</a>,
because Robin Hood Hashing.


* It's good to have bad jokes in your code. Other than that name though there are good reasons to prefer this hash table over std::unordered_map.

* The main benefit of sherwood_map over unordered_map is that it doesn't put every element into it's own heap allocation. Less allocations give you a speedup because a) less allocations and b) more cache hits.

* To be up front here is the list of limitations:

 - No support for custom allocator propagation. Sorry. I didn't know about this feature and I learned about it too late. It can be quite a pain to implement so I haven't done it yet.
 - Less exception safety. I assume noexcept moves. If you have that you can throw pretty much anywhere else though.
 - Only load factors up to 1
 - No iterations within a bucket. I don't know what that interface is for and it is difficult to implement in Robin Hood Hashing.
 - Any modification to the container will invalidate all iterators and references

* I think those limitations are OK. But let's get to performance tests. Since sherwood_map may have to move elements around when the map gets modified, that would suggest that sherwood_map may be slower than unordered_map for large objects. Because of that I ran all performance tests with a small, (sherwood_map<int, int>) a medium-sized, (sherwood_map<pair<int, string>, pair<int, string>>) and a large (sherwood_map<int, char[512]>) object.

<table>
<tbody>
<tr>
<td></td>
<td></td>
<td style="text-align:center;" colspan="3">Clang 3.4</td>
<td style="text-align:center;" colspan="3">GCC 4.8.2</td>
</tr>
<tr>
<td></td>
<td></td>
<td>sherwood_map</td>
<td>unordered_map</td>
<td>speedup</td>
<td>sherwood_map</td>
<td>unordered_map</td>
<td>speedup</td>
</tr>
<tr>
<td rowspan="3">insert</td>
<td>small</td>
<td>113</td>
<td>134</td>
<td>16%</td>
<td>106</td>
<td>136</td>
<td>23%</td>
</tr>
<tr>
<td>medium</td>
<td>124</td>
<td>150</td>
<td>17%</td>
<td>128</td>
<td>152</td>
<td>16%</td>
</tr>
<tr>
<td>large</td>
<td>326</td>
<td>393</td>
<td>17%</td>
<td>286</td>
<td>387</td>
<td><strong>26%</strong></td>
</tr>
<tr>
<td rowspan="3">reserve insert</td>
<td>small</td>
<td>72</td>
<td>119</td>
<td><strong>39%</strong></td>
<td>72</td>
<td>119</td>
<td><strong>46%</strong></td>
</tr>
<tr>
<td>medium</td>
<td>80</td>
<td>131</td>
<td><strong>39%</strong></td>
<td>82</td>
<td>136</td>
<td><strong>39%</strong></td>
</tr>
<tr>
<td>large</td>
<td>147</td>
<td>342</td>
<td><strong>57%</strong></td>
<td>114</td>
<td>335</td>
<td><strong>66%</strong></td>
</tr>
<tr>
<td rowspan="3">erase</td>
<td>small</td>
<td>137</td>
<td>196</td>
<td><strong>30%</strong></td>
<td>149</td>
<td>199</td>
<td><strong>25%</strong></td>
</tr>
<tr>
<td>medium</td>
<td>205</td>
<td>225</td>
<td>9%</td>
<td>205</td>
<td>231</td>
<td>11%</td>
</tr>
<tr>
<td>large</td>
<td>347</td>
<td>361</td>
<td>4%</td>
<td>353</td>
<td>345</td>
<td>-2%</td>
</tr>
<tr>
<td rowspan="3">lookup</td>
<td>small</td>
<td>228</td>
<td>361</td>
<td><strong>37%</strong></td>
<td>230</td>
<td>473</td>
<td><strong>52%</strong></td>
</tr>
<tr>
<td>medium</td>
<td>243</td>
<td>436</td>
<td><strong>44%</strong></td>
<td>242</td>
<td>634</td>
<td><strong>62%</strong></td>
</tr>
<tr>
<td>large</td>
<td>277</td>
<td>571</td>
<td><strong>51%</strong></td>
<td>263</td>
<td>709</td>
<td><strong>63%</strong></td>
</tr>
<tr>
<td rowspan="3">modify</td>
<td>small</td>
<td>384</td>
<td>459</td>
<td>16%</td>
<td>388</td>
<td>508</td>
<td>24%</td>
</tr>
<tr>
<td>medium</td>
<td>549</td>
<td>519</td>
<td>-6%</td>
<td>530</td>
<td>577</td>
<td>8%</td>
</tr>
<tr>
<td>large</td>
<td>1120</td>
<td>724</td>
<td><strong>-55%</strong></td>
<td>1279</td>
<td>750</td>
<td><strong>-71%</strong></td>
</tr>
</tbody>
</table>

## Explanations for the tests:

 - <em>insert</em> inserts a bunch of elements into a map
 - <em>reserve insert</em> is the same test but I call reserve() before inserting the elements
 - <em>erase</em> builds a map and then erases all elements again
 - <em>lookup</em> builds a map and then performs many lookups where most of the lookups will fail
 - <em>modify</em> builds a map and then erases one element and adds one new element over and over again

* In all of the non-insert tests I call reserve() when building the map. But I use a different number of elements for each test, so the overhead for building the map is not always the same. I also fill up the map until it has 85% load factor, which is the default max_load_factor() for sherwood_map. The default max_load_factor() for unordered_map is 100%, but for comparisons sake I use the same value for both.

* The results are kind of what I expected. The biggest speed improvements are for lookups, and for when inserting into a map that has reserved enough space to fit all elements. I didn't measure this exactly, but I expect that the main reason for the improved lookup time is that there are fewer pointer indirections, and the reason for the improved insert time is that sherwood_map doesn't need to do new heap allocations.

* I was surprised though to see that sherwood_map also performs better in almost all other areas. Only when there are many modifications to the map is unordered_map faster. Note though that this is not the case if you modify values that are already stored in the map. Only when you erase objects and add new objects is unordered_map faster. (updating existing objects is as fast as performing a lookup)

* I also did a brief test to see how much memory each map is using: When storing one million objects in a unordered_map<int, string> my application uses 39 MiB of memory. When storing one million objects in a sherwood_map<int, string> my applications uses 27.6 MiB of memory. I expect that the reason for this is that unordered_map has more overhead for every object stored in the map. I did not modify the max_load_factor of the map for this test, so unordered_map uses a max_load_factor of 1, where sherwood_map uses a max_load_factor of 0.85.

* One thing that I'm kind of embarrassed about (because I just did a big post about this) is that sherwood_map compiles more slowly than unordered_map. I know why it compiles more slowly and I know how to change it, but it requires some non-trivial changes to the structure of the map. Maybe I'll do that when I support allocator propagation. But C++ does make it difficult to predict how fast or slow your code will compile??? (in my case it's actually because std::swap compiles slowly, who would have thought???)

* So when should you use unordered_map? If you plan to do lots of modifications to the map (lots of erases and insertions of new objects) or if you need your iterators and references to not be invalidated. Otherwise using Robin Hood Linear Probing seems to be a better alternative.

* And here is a <a href="https://github.com/skarupke/sherwood_map/tree/master/finished">link to the github files</a>.

## edit:

* gnzlbg in the comments pointed me to a Joaqu??n M L??pez Mu??oz  <a href="http://bannalia.blogspot.de/2014/01/a-better-hash-table-clang.html">blog</a> where he had done performance comparisons for hashtable implementations. I ran his same benchmarks and the results are??? embarrasing. Let's start with the results:

* Inserting elements:

  <a href="https://probablydance.files.wordpress.com/2014/05/running_insertion.png"><img data-attachment-id="1625" data-permalink="https://probablydance.com/2014/05/03/i-wrote-a-fast-hash-table/running_insertion/" data-orig-file="https://probablydance.files.wordpress.com/2014/05/running_insertion.png?w=650" data-orig-size="605,340" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;}" data-image-title="running_insertion" data-image-description="" data-medium-file="https://probablydance.files.wordpress.com/2014/05/running_insertion.png?w=650?w=300" data-large-file="https://probablydance.files.wordpress.com/2014/05/running_insertion.png?w=650?w=605" class="alignnone size-full wp-image-1625" src="https://probablydance.files.wordpress.com/2014/05/running_insertion.png?w=650" alt="running_insertion" srcset="https://probablydance.files.wordpress.com/2014/05/running_insertion.png 605w, https://probablydance.files.wordpress.com/2014/05/running_insertion.png?w=150 150w, https://probablydance.files.wordpress.com/2014/05/running_insertion.png?w=300 300w" sizes="(max-width: 605px) 100vw, 605px"   /></a>

* Reserving and then inserting elements:

  <a href="https://probablydance.files.wordpress.com/2014/05/no_rehash_running_insertion.png"><img data-attachment-id="1626" data-permalink="https://probablydance.com/2014/05/03/i-wrote-a-fast-hash-table/no_rehash_running_insertion/" data-orig-file="https://probablydance.files.wordpress.com/2014/05/no_rehash_running_insertion.png?w=650" data-orig-size="605,340" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;}" data-image-title="no_rehash_running_insertion" data-image-description="" data-medium-file="https://probablydance.files.wordpress.com/2014/05/no_rehash_running_insertion.png?w=650?w=300" data-large-file="https://probablydance.files.wordpress.com/2014/05/no_rehash_running_insertion.png?w=650?w=605" class="alignnone size-full wp-image-1626" src="https://probablydance.files.wordpress.com/2014/05/no_rehash_running_insertion.png?w=650" alt="no_rehash_running_insertion" srcset="https://probablydance.files.wordpress.com/2014/05/no_rehash_running_insertion.png 605w, https://probablydance.files.wordpress.com/2014/05/no_rehash_running_insertion.png?w=150 150w, https://probablydance.files.wordpress.com/2014/05/no_rehash_running_insertion.png?w=300 300w" sizes="(max-width: 605px) 100vw, 605px"   /></a>

* Erasing elements:

 <a href="https://probablydance.files.wordpress.com/2014/05/scattered_erasure.png"><img data-attachment-id="1627" data-permalink="https://probablydance.com/2014/05/03/i-wrote-a-fast-hash-table/scattered_erasure/" data-orig-file="https://probablydance.files.wordpress.com/2014/05/scattered_erasure.png?w=650" data-orig-size="605,340" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;}" data-image-title="scattered_erasure" data-image-description="" data-medium-file="https://probablydance.files.wordpress.com/2014/05/scattered_erasure.png?w=650?w=300" data-large-file="https://probablydance.files.wordpress.com/2014/05/scattered_erasure.png?w=650?w=605" class="alignnone size-full wp-image-1627" src="https://probablydance.files.wordpress.com/2014/05/scattered_erasure.png?w=650" alt="scattered_erasure" srcset="https://probablydance.files.wordpress.com/2014/05/scattered_erasure.png 605w, https://probablydance.files.wordpress.com/2014/05/scattered_erasure.png?w=150 150w, https://probablydance.files.wordpress.com/2014/05/scattered_erasure.png?w=300 300w" sizes="(max-width: 605px) 100vw, 605px"   /></a>

* Successful lookups:


  <a href="https://probablydance.files.wordpress.com/2014/05/scattered_successful_lookup.png"><img data-attachment-id="1628" data-permalink="https://probablydance.com/2014/05/03/i-wrote-a-fast-hash-table/scattered_successful_lookup/" data-orig-file="https://probablydance.files.wordpress.com/2014/05/scattered_successful_lookup.png?w=650" data-orig-size="605,340" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;}" data-image-title="scattered_successful_lookup" data-image-description="" data-medium-file="https://probablydance.files.wordpress.com/2014/05/scattered_successful_lookup.png?w=650?w=300" data-large-file="https://probablydance.files.wordpress.com/2014/05/scattered_successful_lookup.png?w=650?w=605" class="alignnone size-full wp-image-1628" src="https://probablydance.files.wordpress.com/2014/05/scattered_successful_lookup.png?w=650" alt="scattered_successful_lookup" srcset="https://probablydance.files.wordpress.com/2014/05/scattered_successful_lookup.png 605w, https://probablydance.files.wordpress.com/2014/05/scattered_successful_lookup.png?w=150 150w, https://probablydance.files.wordpress.com/2014/05/scattered_successful_lookup.png?w=300 300w" sizes="(max-width: 605px) 100vw, 605px"   /></a>

* Unsuccessful lookups:


  <a href="https://probablydance.files.wordpress.com/2014/05/scattered_unsuccessful_lookup.png"><img data-attachment-id="1629" data-permalink="https://probablydance.com/2014/05/03/i-wrote-a-fast-hash-table/scattered_unsuccessful_lookup/" data-orig-file="https://probablydance.files.wordpress.com/2014/05/scattered_unsuccessful_lookup.png?w=650" data-orig-size="605,340" data-comments-opened="1" data-image-meta="{&quot;aperture&quot;:&quot;0&quot;,&quot;credit&quot;:&quot;&quot;,&quot;camera&quot;:&quot;&quot;,&quot;caption&quot;:&quot;&quot;,&quot;created_timestamp&quot;:&quot;0&quot;,&quot;copyright&quot;:&quot;&quot;,&quot;focal_length&quot;:&quot;0&quot;,&quot;iso&quot;:&quot;0&quot;,&quot;shutter_speed&quot;:&quot;0&quot;,&quot;title&quot;:&quot;&quot;}" data-image-title="scattered_unsuccessful_lookup" data-image-description="" data-medium-file="https://probablydance.files.wordpress.com/2014/05/scattered_unsuccessful_lookup.png?w=650?w=300" data-large-file="https://probablydance.files.wordpress.com/2014/05/scattered_unsuccessful_lookup.png?w=650?w=605" class="alignnone size-full wp-image-1629" src="https://probablydance.files.wordpress.com/2014/05/scattered_unsuccessful_lookup.png?w=650" alt="scattered_unsuccessful_lookup" srcset="https://probablydance.files.wordpress.com/2014/05/scattered_unsuccessful_lookup.png 605w, https://probablydance.files.wordpress.com/2014/05/scattered_unsuccessful_lookup.png?w=150 150w, https://probablydance.files.wordpress.com/2014/05/scattered_unsuccessful_lookup.png?w=300 300w" sizes="(max-width: 605px) 100vw, 605px"   /></a>


* In all of these lower numbers are better. So sherwood_map doesn't do terribly, but unfortunately the right hand side of that graph doesn't really count. The left hand side is interesting because at least for me most maps have few elements. Unfortunately I ran my comparison above on the right hand side of the graph. I didn't do that intentionally, I just ran a test that took long enough to be easy to measure.

* So what do I take from this? Time to optimize sherwood_map :-). I spend all of my time on the calculation for the bucket of a given index. The way you find out when to stop iterating in Robin Hood Linear Probing is to look at how far you are from the initial bucket of a given element. When you find an element that's less steps away from its initial bucket than your element is from your initial bucket, then you can stop iterating because you know that your object is not in the map. Unfortunately that means that as you're iterating you keep on having to calculate the initial bucket of elements that you walk past. So I'll play around with caching that calculation. If that doesn't work I could also change my bucket count to be a power of two. That's frowned upon for hashtables because it increases clustering but the increased clustering may be worth it if iterating over those clusters to find your element is cheap.

* I'll probably write a new blog post once I've done that.
