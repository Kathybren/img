<p>有时候人们很喜欢造一些名字很吓人的名词，让人一听这个名词就觉得自己不可能学会，从而
让人望而却步。但是其实这些名词背后所代表的东西其实很简单。</p>

<p>我不能说高阶组件就是这么一个东西。但是它是一个概念上很简单，但却非常常用、实用的东
西，被大量 React.js 相关的第三方库频繁地使用。在前端的业务开发当中，你不掌握高阶组件其实也可以完成项目的开发，但是如果你能够灵活地使用高阶组件，可以让你代码更加优雅，复用性、灵活性更强。它是一个加分项，而且加的分还不少。</p>

<p>本章节可能有部分内容理解起来会有难度，如果你觉得无法完全理解本节内容。可以先简单理
解高阶组件的概念和作用即可，其他内容选择性地跳过。</p>

<p>了解高阶组件对我们理解各种 React.js 第三方库的原理很有帮助。</p>

<h2 id="什么是高阶组件">什么是高阶组件</h2>
<p><em>高阶组件就是一个函数，传给它一个组件，它返回一个新的组件。</em></p>

<pre><code class="language-javascript">const NewComponent = higherOrderComponent(OldComponent)
</code></pre>

<p>重要的事情再重复一次，高阶组件是一个函数（而不是组件），它接受一个组件作为参数，返
回一个新的组件。这个新的组件会使用你传给它的组件作为子组件，我们看看一个很简单的高阶组件：</p>

<pre><code class="language-javascript">import React, { Component } from 'react'

export default (WrappedComponent) =&gt; {
  class NewComponent extends Component {
    // 可以做很多自定义逻辑
    render () {
      return &lt;WrappedComponent /&gt;
    }
  }
  return NewComponent
}
</code></pre>

<p>现在看来好像什么用都没有，它就是简单的构建了一个新的组件类 <code>NewComponent</code>，然后把传进入去的 <code>WrappedComponent</code> 渲染出来。但是我们可以给 <code>NewCompoent</code> 做一些数据启动工作：</p>

<pre><code class="language-javascript">import React, { Component } from 'react'

export default (WrappedComponent, name) =&gt; {
  class NewComponent extends Component {
    constructor () {
      super()
      this.state = { data: null }
    }

    componentWillMount () {
      let data = localStorage.getItem(name)
      this.setState({ data })
    }

    render () {
      return &lt;WrappedComponent data={this.state.data} /&gt;
    }
  }
  return NewComponent
}
</code></pre>

<p>现在 <code>NewComponent</code> 会根据第二个参数 <code>name</code> 在挂载阶段从 LocalStorage 加载数据，并且 <code>setState</code> 到自己的 <code>state.data</code> 中，而
渲染的时候将 <code>state.data</code> 通过 <code>props.data</code> 传给 <code>WrappedComponent</code>。</p>

<p>这个高阶组件有什么用呢？假设上面的代码是在 <code>src/wrapWithLoadData.js</code> 文
件中的，我们可以在别的地方���么用它：</p>

<pre><code class="language-javascript">import wrapWithLoadData from './wrapWithLoadData'

class InputWithUserName extends Component {
  render () {
    return &lt;input value={this.props.data} /&gt;
  }
}

InputWithUserName = wrapWithLoadData(InputWithUserName, 'username')
export default InputWithUserName
</code></pre>

<p>假如 <code>InputWithUserName</code> 的功能需求是挂载的时候从 LocalStorage 里面加载
<code>username</code> 字段作为 <code>&lt;input /&gt;</code> 的 <code>value</code> 值，现在有了 <code>wrapWithLoadData</code>，我们可以很容易地做到这件事情。</p>

<p>只需要定义一个非常简单的 <code>InputWithUserName</code>，它会把 <code>props.data</code> 作为 <code>&lt;input /&gt;</code> 的 <code>value</code> 值。然把这个组件和 <code>'username'</code> 传给 <code>wrapWithLoadData</code>，<code>wrapWithLoadData</code> 会返回一个新的组件，我们用这个新的组件覆盖原来的 <code>InputWithUserName</code>，然后再
导出去模块。</p>

<p>别人用这个组件的时候实际是用了<em>被加工过</em>的组件：</p>

<pre><code class="language-javascript">import InputWithUserName from './InputWithUserName'

class Index extends Component {
  render () {
    return (
      &lt;div&gt;
        用户名：&lt;InputWithUserName /&gt;
      &lt;/div&gt;
    )
  }
}
</code></pre>

<p>根据 <code>wrapWithLoadData</code> 的代码我们可以知道，这个新的组件挂载的时候会先去 LocalStorage 加载数据，渲染的时候再通过 <code>props.data</code> 传给真正的 <code>InputWithUserName</code>。</p>

<p>如果现在我们需要另外一个文本输入框组件，它也需要 LocalStorage 加载 <code>'content'</code> 字段的数据。我们只需要定义一个新的 <code>TextareaWithContent</code>：</p>

<pre><code class="language-javascript">import wrapWithLoadData from './wrapWithLoadData'

class TextareaWithContent extends Component {
  render () {
    return &lt;textarea value={this.props.data} /&gt;
  }
}

TextareaWithContent = wrapWithLoadData(TextareaWithContent, 'content')
export default TextareaWithContent
</code></pre>

<p>写起来非常轻松，我们根本不需要重复写从 LocalStorage 加载数据字段的逻辑，直接用 <code>wrapWithLoadData</code> 包装一下就可以了。</p>

<p>我们来回顾一下到底发生了什么事情，对于 <code>InputWithUserName</code> 和 <code>TextareaWithContent</code> 这两个组件来说，它们的需求有着这么一个相同的逻辑：“挂载阶段从
LocalStorage 中加载特定字段数据”。</p>

<p>如果按照之前的做法，我们需要给它们两个都加上 <code>componentWillMount</code> 生命周期，然后在里面调用 LocalStorage。要是有第三个组件也有这样的加载逻辑，我又得写一遍这样
的逻辑。但有了 <code>wrapWithLoadData</code> 高阶组件，我们把这样的逻辑用一个组件包裹
了起来，并且通过给高阶组件传入 <code>name</code> 来达到不同字段的数据加载。充分复用了
逻辑代码。</p>

<p>到这里，高阶组件的作用其实不言而喻，<em>其实就是为了组件之间的代码复用</em>。组件可能有着某些相同的逻辑，把这些逻辑抽离出来，放到高阶组件中进行复用。<em>高阶组件内部的包装组件和被包装组件之间通过 <code>props</code> 传递数据</em>。</p>

<h2 id="高阶组件的灵活性">高阶组件的灵活性</h2>
<p>代码复用的方法、形式有很多种，你可以用类继承来做到代码复用，也可以分离模块的方式。
但是高阶组件这种方式很有意思，也很灵活。学过设计模式的同学其实应该能反应过来，它其实就是设计模式里面的装饰者模式。它通过组合的方式达到很高的灵活程度。</p>

<p>假设现在我们需求变化了，现在要的是通过 Ajax 加载数据而不是从 LocalStorage 加载数据
。我们只需要新建一个 <code>wrapWithAjaxData</code> 高阶组件：</p>

<pre><code class="language-javascript">import React, { Component } from 'react'

export default (WrappedComponent, name) =&gt; {
  class NewComponent extends Component {
    constructor () {
      super()
      this.state = { data: null }
    }

    componentWillMount () {
      ajax.get('/data/' + name, (data) =&gt; {
        this.setState({ data })
      })
    }

    render () {
      return &lt;WrappedComponent data={this.state.data} /&gt;
    }
  }
  return NewComponent
}
</code></pre>

<p>其实就是改了一下 <code>wrapWithLoadData</code> 的 <code>componentWillMount</code>
中的逻辑，改成了从服务器加载数据。现在只需要把 <code>InputWithUserName</code> 稍微改一下：</p>

<pre><code class="language-javascript">import wrapWithAjaxData from './wrapWithAjaxData'

class InputWithUserName extends Component {
  render () {
    return &lt;input value={this.props.data} /&gt;
  }
}

InputWithUserName = wrapWithAjaxData(InputWithUserName, 'username')
export default InputWithUserName
</code></pre>

<p>只要改一下包装的高阶组件就可以达到需要的效果。而且我们并没有改动 <code>InputWithUserName</code> 组件内部的任何逻辑，也没有改动 <code>Index</code> 的任何逻辑，只是改动了
中间的高阶组件函数。</p>

<p>（以下内容为选读内容，有兴趣的同学可以继续往下读，否则也可以直接跳到文末的总结部分
。）</p>

<h2 id="多层高阶组件选读">多层高阶组件（选读）</h2>
<p>假如现在需求有变化了：我们需要先从 LocalStorage 中加载数据，再用这个数据去服务器取
数据。我们改一下（或者新建一个）<code>wrapWithAjaxData</code> 高阶组件，修改其中的 <code>componentWillMount</code>：</p>

<pre><code class="language-javascript">...
    componentWillMount () {
      ajax.get('/data/' + this.props.data, (data) =&gt; {
        this.setState({ data })
      })
    }
...
</code></pre>

<p>它会用传进来的 <code>props.data</code> 去服务器取数据。这时候修改 <code>InputWithUserName</code>：</p>

<pre><code class="language-javascript">import wrapWithLoadData from './wrapWithLoadData'
import wrapWithAjaxData from './wrapWithAjaxData'

class InputWithUserName extends Component {
  render () {
    return &lt;input value={this.props.data} /&gt;
  }
}

InputWithUserName = wrapWithAjaxData(InputWithUserName)
InputWithUserName = wrapWithLoadData(InputWithUserName, 'username')
export default InputWithUserName
</code></pre>

<p>大家可以看到，我们给 <code>InputWithUserName</code> 应用了两种高阶组件：先用 <code>wrapWithAjaxData</code> 包裹 <code>InputWithUserName</code>，再用 <code>wrapWithLoadData </code> 包含上次包裹的结果。它们的关系就如下图的三个圆圈：</p>

<p><a href="http://huzidaha.github.io/static/assets/img/posts/A8F1DD5F-1995-419E-8551-4FC2D59F58B4.png" target="_blank"><img src="http://huzidaha.github.io/static/assets/img/posts/A8F1DD5F-1995-419E-8551-4FC2D59F58B4.png" alt="React.js 小书实战高阶组件图片"
/></a></p>

<p>实际上最终得到的组件会先去 LocalStorage 取数据，然后通过 <code>props.data</code> 传��下一层组件，下一层用这个 <code>props.data</code> 通过 Ajax 去服务端取数据，然后再
通过 <code>props.data</code> 把数据传给下一层，也就是 <code>InputWithUserName</code>。大家可以体会一下下图尖头代表的组件之间的数据流向：</p>

<p><a href="http://huzidaha.github.io/static/assets/img/posts/8F6C1E91-B365-4919-84C3-2252223621F8.png" target="_blank"><img src="http://huzidaha.github.io/static/assets/img/posts/8F6C1E91-B365-4919-84C3-2252223621F8.png" alt="React.js 小书实战高阶组件图片"
/></a></p>

<h2 id="用高阶组件改造评论功能选读">用高阶组件改造评论功能（选读）</h2>
<p>大家对这种在挂载阶段从 LocalStorage 加载数据的模式都很熟悉，在上一阶段的实战中，<code>CommentInput</code> 和 <code>CommentApp</code> 都用了这种方式加载、保存数据。实际上我们可以构建一个高阶组件把它们的相同的逻辑抽离出来，构建一个高阶组件 <code>wrapWithLoadData</code>：</p>

<pre><code class="language-javascript">export default (WrappedComponent, name) =&gt; {
  class LocalStorageActions extends Component {
    constructor () {
      super()
      this.state = { data: null }
    }

    componentWillMount () {
      let data = localStorage.getItem(name)
      try {
        // 尝试把它解析成 JSON 对象
        this.setState({ data: JSON.parse(data) })
      } catch (e) {
        // 如果出错了就当普通字符串读取
        this.setState({ data })
      }
    }

    saveData (data) {
      try {
        // 尝试把它解析成 JSON 字符串
        localStorage.setItem(name, JSON.stringify(data))
      } catch (e) {
        // 如果出错了就当普通字符串保存
        localStorage.setItem(name, `${data}`)
      }
    }

    render () {
      return (
        &lt;WrappedComponent
          data={this.state.data}
          saveData={this.saveData.bind(this)}
          // 这里的意思是把其他的参数原封不动地传递给被包装的组件
          {...this.props} /&gt;
      )
    }
  }
  return LocalStorageActions
}
</code></pre>

<p><code>CommentApp</code> 可以这样使用：</p>

<pre><code class="language-javascript">class CommentApp extends Component {
  static propTypes = {
    data: PropTypes.any,
    saveData: PropTypes.func.isRequired
  }

  constructor (props) {
    super(props)
    this.state = { comments: props.data }
  }

  handleSubmitComment (comment) {
    if (!comment) return
    if (!comment.username) return alert('请输入用户名')
    if (!comment.content) return alert('请输入评论内容')
    const comments = this.state.comments
    comments.push(comment)
    this.setState({ comments })
    this.props.saveData(comments)
  }

  handleDeleteComment (index) {
    const comments = this.state.comments
    comments.splice(index, 1)
    this.setState({ comments })
    this.props.saveData(comments)
  }

  render() {
    return (
      &lt;div className='wrapper'&gt;
        &lt;CommentInput onSubmit={this.handleSubmitComment.bind(this)} /&gt;
        &lt;CommentList
          comments={this.state.comments}
          onDeleteComment={this.handleDeleteComment.bind(this)} /&gt;
      &lt;/div&gt;
    )
  }
}

CommentApp = wrapWithLoadData(CommentApp, 'comments')
export default CommentApp
</code></pre>

<p>同样地可以在 <code>CommentInput</code> 中使用 <code>wrapWithLoadData</code>，这里就不贴代码了。有兴趣的同学可以查看<a href="https://github.com/huzidaha/react-naive-book-examples/commit/0d67eab713c042301fa4992c719069e92a7243f5">高阶组件重构的 CommentApp 版本</a>。</p>

<h2 id="总结">总结</h2>
<p><em>高阶组件就是一个函数，传给它一个组件，它返回一个新的组件</em>。新的组件使用传入的组件作为子组件。</p>

<p><em>高阶组件的作用是用于代码复用</em>，可以把组件之间可复用的代码、逻辑抽离到高阶组件当中。<em>新的组件和传入的组件通过 <code>props</code> 传递信息</em>。</p>

<p>高阶组件有助于提高我们代码的灵活性，逻辑的复用性。灵活和熟练地掌握高阶组件的用法需
要经验的积累还有长时间的思考和练习，如果你觉得本章节的内容无法完全消化和掌握也没有关系，可以先简单了解高阶组件的定义、形式和作用即可。</p>