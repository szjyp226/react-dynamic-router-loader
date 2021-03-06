重点说明（notice！！！）： 由于渣英语，就尽量不多描述，而是以可读代码作为表达。
<br>
### 1. setup
<pre>npm i --save-dev react-dynamic-router-loader</pre>
### 2. using
<pre>
    // webpack.config.js
    ...
    loader: "react-dynamic-router-loader",
    options: {
        <font color="#258125">// Loading Component</font>
        asyncDefaultComponent: './components/loading.jsx'
    }
    ...
</pre>
<pre>
    // ./components/loading.jsx
    import React from 'react';

    const _AsyncComponent = (loaderComponent, loadingComponent, cacheComponent) => (
        class _AsyncComponent extends React.Component {
            state = {
                Component: null
            }

            async componentWillMount() {
                let Component = cacheComponent || await loaderComponent();

                this.setState({
                    Component
                });
            }

            render() {
                const { Component } = this.state;

                return (Component) ? 
                            &lt;Component {...this.props} /&gt; : 
                            loadingComponent();
            }
        }
    );

    <font color="#258125">// Currently only to do so</font>
    module.exports = _AsyncComponent;

</pre>
<pre>
    // Reactjs Code
    ...
    <font color="#a61c00">&lt;MyComponent</font> <font color="red">component={require(
                                /* webpackChunkName: "index" */
                                /* loadingComponentPath: "./loading.jsx" */
                                'path/my-component.jsx')}</font> <font color="#a61c00">/&gt</font>;
    ...
    <font color="#258125">// webpackChunkName
    // the "import" feature of the CommonJS API for webpack2</font>
    
    <font color="#258125">/* 
       loadingComponentPath is the loader's property 
       that specifies the corresponding loadingComponent 
       for this component by configuring 
       this property or the default asyncDefaultComponent   
       ps: Path is relativePath, equal "require('[path]') "  
    */</font>

    // Reactjs Code 2
    <font color="#6fa8dc">class</font> <font color="#a61c00">MyComponent</font> <font color="#4a86e8">extends</font> <font color="#1c4587">React.Component</font>{
        preRender(){
            <font color="#258125">
            // Don't use props. 
            // because this.props not equal MyComponent's props</font>

            return (
                <font color="#a61c00">&lt;div&gt;</font>My Loading View<font color="#a61c00">&lt;/div&gt</font>;
            );
        }
    }
</pre>
### 3. Webpack.config.js info
<pre>
    // webpack config must has client and server config

    let clientConfig = {
        ...
        // client config
        ...
    };

    let serverConfig = {
        ...
        // server config
        ...
    };

    module.exports = [serverConfig, clientConfig]
</pre>
以下为中文简略说明：
只是想着能应用于 服务端渲染 与 客户端渲染 的同时，客户端渲染 的一方面上，希望可以通过Webpack2的import来实现 Code Splitting（代码分离）， 但目前会有一个问题。
<br/>
就是会遇到： <font color="red">Warning: XXX did not match. Server</font>的情况，视情况整个思路可能也都是存在一定问题的。
<br/>
有待改进（Hope to improve）

----------------------------------------------
2017-11-23：
因服务端渲染导致的问题 现已解决。通过判定cacheComponent != null 来得到初始化render来解决（resolve) 问题。

--------------------------------------------
2017-12-03：
新增字段：loadingComponentPath，使用方式与webpack的import注释法同样
对应的值路径为 当前代码文件的 位置