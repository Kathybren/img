<div class="post__title">
        <h1>使用真正的 Redux 和 React-redux</h1>
    </div>
    <div class="post__meta">
        <p></p>
    </div>
    <div class="post__content"?>
      <ul style="font-size: 14px; margin-top: -10px;">
  <li>
    作者：<a href="https://www.zhihu.com/people/hu-zi-da-ha" target="_blank">胡子大哈</a>
  </li>
  <li>
    原文链接：<a href="http://huziketang.com/books/react/lesson42"> http://huziketang.com/books/react/lesson42 </a>
  </li>
  <li>转载请注明出处，保留原文链接和作者信息。</li>
</ul>

<p>（本文未审核）</p>

<p>现在 <code>make-react-redux</code> 工程代码中的 Redux 和 React-redux 都是我们自己写的，现在让我们来使用真���的官方版本的 Redux 和 React-redux。</p>

<p>在工程目录下使用 npm 安装 Redux 和 React-redux 模块：</p>

<pre><code class="language-bash">npm install redux react-redux --save
</code></pre>

<p>把 <code>src/</code> 目录下 <code>Header.js</code>、<code>ThemeSwitch.js</code>、<code>Content.js</code> 的模块导入中的：</p>

<pre><code class="language-javascript">import { connect } from './react-redux'
</code></pre>

<p>改成：</p>

<pre><code class="language-javascript">import { connect } from 'react-redux'
</code></pre>

<p>也就是本来从本地 <code>./react-redux</code> 导入的 <code>connect</code> 改成从第三
方 <code>react-redux</code> 模块中导入。</p>

<p>修改 <code>src/index.js</code>，把前面部分的代码调整为：</p>

<pre><code class="language-javascript">import React, { Component } from 'react'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import { Provider } from 'react-redux'
import Header from './Header'
import Content from './Content'
import './index.css'

const themeReducer = (state, action) =&gt; {
  if (!state) return {
    themeColor: 'red'
  }
  switch (action.type) {
    case 'CHANGE_COLOR':
      return { ...state, themeColor: action.themeColor }
    default:
      return state
  }
}

const store = createStore(themeReducer)

...
</code></pre>

<p>我们删除了自己写的 <code>createStore</code>，改成使用第三方模块 <code>redux</code>
的 <code>createStore</code>；<code>Provider</code> 本来从本地的 <code>./react-redux</code> 引入，改成从第三方 <code>react-redux</code> 模块中引入。其余代码保持不变。</p>

<p>接着删除 <code>src/react-redux.js</code>，它的已经用处不大了。最后启动工程 <code>npm start</code>：</p>

<p><a href="http://huzidaha.github.io/static/assets/img/posts/A6103C15-A0C3-4540-9147-67ABC24FCD48.png" target="_blank"><img src="http://huzidaha.github.io/static/assets/img/posts/A6103C15-A0C3-4540-9147-67ABC24FCD48.png" alt="实例图片" /></a></p>

<p>可以看到我们原来的业务代码其实都没有太多的改动，实际上我们实现的 <code>redux</code> 和 <code>react-redux</code> 和官方版本在该场景的用法上是兼容的。接下来的章节我们都会
使用官方版本的 <code>redux</code> 和 <code>react-redux</code>。</p>

<h2 id="课后练习">课后练习</h2>
<ul>
  <li><a target="_blank" href="http://scriptoj.com/problems/17">React-redux 实现用户列表的显示、增加、删除</a></li>
</ul>