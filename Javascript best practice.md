<h2 id="use-object-method-shorthand.">Use object method shorthand.</h2>
<h4 id="bad">Bad</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> atom <span class="token operator">=</span> <span class="token punctuation">{</span>
  value<span class="token punctuation">:</span> <span class="token number">1</span><span class="token punctuation">,</span>

  addValue<span class="token punctuation">:</span> <span class="token keyword">function</span> <span class="token punctuation">(</span>value<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> atom<span class="token punctuation">.</span>value <span class="token operator">+</span> value<span class="token punctuation">;</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="good">Good</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> atom <span class="token operator">=</span> <span class="token punctuation">{</span>
  value<span class="token punctuation">:</span> <span class="token number">1</span><span class="token punctuation">,</span>

  <span class="token function">addValue</span><span class="token punctuation">(</span>value<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token keyword">return</span> atom<span class="token punctuation">.</span>value <span class="token operator">+</span> value<span class="token punctuation">;</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<hr>
<h2 id="use-property-value-shorthand.">Use property value shorthand.</h2>
<h4 id="bad-1">Bad</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> obj <span class="token operator">=</span> <span class="token punctuation">{</span>
  lukeSkywalker<span class="token punctuation">:</span> lukeSkywalker<span class="token punctuation">,</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="good-1">Good</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> obj <span class="token operator">=</span> <span class="token punctuation">{</span>
  lukeSkywalker<span class="token punctuation">,</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<hr>
<h2 id="only-quote-properties-that-are-invalid-identifiers">Only quote properties that are invalid identifiers</h2>
<h4 id="bad-2">Bad</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> bad <span class="token operator">=</span> <span class="token punctuation">{</span>
  <span class="token string">'foo'</span><span class="token punctuation">:</span> <span class="token number">3</span><span class="token punctuation">,</span>
  <span class="token string">'bar'</span><span class="token punctuation">:</span> <span class="token number">4</span><span class="token punctuation">,</span>
  <span class="token string">'data-blah'</span><span class="token punctuation">:</span> <span class="token number">5</span><span class="token punctuation">,</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="good-2">Good</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> good <span class="token operator">=</span> <span class="token punctuation">{</span>
  foo<span class="token punctuation">:</span> <span class="token number">3</span><span class="token punctuation">,</span>
  bar<span class="token punctuation">:</span> <span class="token number">4</span><span class="token punctuation">,</span>
  <span class="token string">'data-blah'</span><span class="token punctuation">:</span> <span class="token number">5</span><span class="token punctuation">,</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<hr>
<h2 id="do-not-call-object.prototype-methods-directly">Do not call Object.prototype methods directly</h2>
<p>Such as hasOwnProperty, propertyIsEnumerable, and isPrototypeOf. Why? These methods may be shadowed by properties on the object in question - consider { hasOwnProperty: false } - or, the object may be a null object (Object.create(null)).</p>
<h4 id="bad-3">Bad</h4>
<pre class=" language-js"><code class="prism  language-js">console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>object<span class="token punctuation">.</span><span class="token function">hasOwnProperty</span><span class="token punctuation">(</span>key<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="good-3">Good</h4>
<pre class=" language-js"><code class="prism  language-js">console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>Object<span class="token punctuation">.</span>prototype<span class="token punctuation">.</span>hasOwnProperty<span class="token punctuation">.</span><span class="token function">call</span><span class="token punctuation">(</span>object<span class="token punctuation">,</span> key<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="best">Best</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> has <span class="token operator">=</span> Object<span class="token punctuation">.</span>prototype<span class="token punctuation">.</span>hasOwnProperty<span class="token punctuation">;</span> <span class="token comment">// cache the lookup once, in module scope.</span>
<span class="token comment">/* or */</span>
<span class="token keyword">import</span> has <span class="token keyword">from</span> <span class="token string">'has'</span><span class="token punctuation">;</span> <span class="token comment">// https://www.npmjs.com/package/has</span>
<span class="token comment">// ...</span>
console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>has<span class="token punctuation">.</span><span class="token function">call</span><span class="token punctuation">(</span>object<span class="token punctuation">,</span> key<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<hr>
<h2 id="use-array.from-instead-of-spread-...-for-mapping-over-iterables-because-it-avoids-creating-an-intermediate-array.">Use Array.from instead of spread … for mapping over iterables, because it avoids creating an intermediate array.</h2>
<h4 id="bad-4">Bad</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> baz <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token operator">...</span>foo<span class="token punctuation">]</span><span class="token punctuation">.</span><span class="token function">map</span><span class="token punctuation">(</span>bar<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="good-4">Good</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> baz <span class="token operator">=</span> Array<span class="token punctuation">.</span><span class="token keyword">from</span><span class="token punctuation">(</span>foo<span class="token punctuation">,</span> bar<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<hr>
<h2 id="use-named-function-expressions-instead-of-function-declarations.">Use named function expressions instead of function declarations.</h2>
<h4 id="bad-5">Bad</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> <span class="token function-variable function">foo</span> <span class="token operator">=</span> <span class="token keyword">function</span> <span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token operator">...</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="good-5">Good</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token comment">// lexical name distinguished from the variable-referenced invocation(s)</span>

<span class="token keyword">const</span> <span class="token function-variable function">short</span> <span class="token operator">=</span> <span class="token keyword">function</span> <span class="token function">longUniqueMoreDescriptiveLexicalFoo</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<hr>
<h2 id="always-put-default-parameters-last">Always put default parameters last</h2>
<h4 id="bad-6">Bad</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">function</span> <span class="token function">handleThings</span><span class="token punctuation">(</span>opts <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">,</span> name<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
</code></pre>
<h4 id="good-6">Good</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">function</span> <span class="token function">handleThings</span><span class="token punctuation">(</span>name<span class="token punctuation">,</span> opts <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token punctuation">}</span><span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token punctuation">}</span>
</code></pre>
<hr>
<h2 id="never-reassign-parameters.">Never reassign parameters.</h2>
<h4 id="bad-7">Bad</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">function</span> <span class="token function">f2</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>a<span class="token punctuation">)</span> <span class="token punctuation">{</span> 
	  a <span class="token operator">=</span> <span class="token number">1</span><span class="token punctuation">;</span> 
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h4 id="good-7">Good</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">function</span> <span class="token function">f3</span><span class="token punctuation">(</span>a<span class="token punctuation">)</span> <span class="token punctuation">{</span>
  <span class="token keyword">const</span> b <span class="token operator">=</span> a <span class="token operator">||</span> <span class="token number">1</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<hr>
<h2 id="remove-argumnets-dublication">Remove argumnets dublication</h2>
<h4 id="bad-8">Bad</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> result <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">,</span> <span class="token number">7</span><span class="token punctuation">,</span> <span class="token number">8</span><span class="token punctuation">,</span> <span class="token number">9</span><span class="token punctuation">,</span> <span class="token number">10</span><span class="token punctuation">]</span>
	<span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>int <span class="token operator">=&gt;</span> <span class="token function">isEven</span><span class="token punctuation">(</span>int<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="good-8">Good</h4>
<pre class=" language-js"><code class="prism  language-js"><span class="token keyword">const</span> result <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">,</span> <span class="token number">2</span><span class="token punctuation">,</span> <span class="token number">3</span><span class="token punctuation">,</span> <span class="token number">4</span><span class="token punctuation">,</span> <span class="token number">5</span><span class="token punctuation">,</span> <span class="token number">6</span><span class="token punctuation">,</span> <span class="token number">7</span><span class="token punctuation">,</span> <span class="token number">8</span><span class="token punctuation">,</span> <span class="token number">9</span><span class="token punctuation">,</span> <span class="token number">10</span><span class="token punctuation">]</span>
	<span class="token punctuation">.</span><span class="token function">filter</span><span class="token punctuation">(</span>isEven<span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></pre>

