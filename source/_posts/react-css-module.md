---
title: create-react-app 相关Webpack 配置
tags: React
date: 2021-03-15
---

# `create-react-app` 相关Webpack  配置

Created: May 22, 2021 6:28 PM
Tags: React, Webpack, create-react-app

最近在学习React，建立了项目，从Vue转型到React学习速度很快，发现React很多东西都很灵活，比如React的模块化CSS文件，对于防止命名冲突的问题官方并没有给出解答，社区却有很多答案。

初步搭建项目，考虑到CSS模块化问题，又去研究相关项目`CSS Loader`，由于Vue之前的全自动化的操作，基本没担心过CSS冲突的问题，所以对这一块基本空白。

项目创建是用`create-react-app`脚手架建立出来的空项目，为了方便后期修改Webpack的配置，运行了`yarn eject`将配置文件移动到了根目录。

## Webpack alias config

个人从`VS Code`迁移到`Web Storm`主要是因为VS Code对一些引用分析支持比较弱，既然迁移到WebStorm肯定希望在别名的地方可以直接点过去，相关的配置

```jsx
// 根目录jsconfig.json
// 因为react-scripts生成出来的脚本是自动读取这个文件的配置的，所有要配置一下baseUrl，否则别名无法生效，具体参考源码
{
  "compilerOptions": {
    "baseUrl": "."
  }
}
```

```jsx
//   /config/paths.js 导出增加你想添加的别名目录，方便管理
  appIndexJs: resolveModule(resolveApp, 'src/index'),
  appPackageJson: resolveApp('package.json'),
  appSrc: resolveApp('src'),
+ assetsSrc: resolveApp('src/assets'),
  appTsConfig: resolveApp('tsconfig.json'),
 
//   /config/modules.js 在function getWebpackAliases中的返回值添加你想要增加的alias

function getWebpackAliases(options = {}) {
  const baseUrl = options.baseUrl;

  if (!baseUrl) {
    return {};
  }

  const baseUrlResolved = path.resolve(paths.appPath, baseUrl);

  if (path.relative(paths.appPath, baseUrlResolved) === '') {
    return {
      src: paths.appSrc,
			// 资源文件目录别名，引用上一步paths.js的目录
      "@assets": paths.assetsSrc
    };
  }
}
```

此时Webpack 就配置好了，WebStorm提示还是没有的话要去手动在设置中指定一下Webpack的配置文件。

## CSS Loader

模块化管理CSS文件，改了.module的命名却发现还没效果，一顿研究发现Webpack的配置中只对CSS进行了模块化，Sass/Scss/Less并没有进行模块化，找到如下代码

```jsx
		// Adds support for CSS Modules, but using SASS
		// using the extension .module.scss or .module.sass
		{
		  test: sassModuleRegex,
		  use: getStyleLoaders(
		    {
		      importLoaders: 3,
		      sourceMap: isEnvProduction
		        ? shouldUseSourceMap
		        : isEnvDevelopment,
 +	      modules: {
 +	        getLocalIdent: getCSSModuleLocalIdent
 +	      },
		    },
		    'sass-loader'
		  ),
		},
```

参照CSS的模块化添加对应的配置就可以了。

## CSS Modules 驼峰命名问题

照以上修改别的项目应该是没有问题了，但是在React项目中，由于符号 - 在JSX是一个特殊字符，导致CSS必须强制用驼峰命名，不能使用kebab-case，社区的解决方案是使用`react-css-modules`或者`babel-plugin-react-css-modules`，但一番研究发现issue中有人在生产环境下出现无法更新问题，而且项目方没有给出Sass/Scss/Less的解决方案。

用过Vue的都知道，Vue有在命名问题上有自动转换功能，然后就去查了CSS Loader 官方文档。发现确实有其功能，JSX中写驼峰，Sass中使用kebab-case，可以很好的做map。

给出文档链接 [link](https://github.com/webpack-contrib/css-loader#modules)，只用在上述的配置中添加以下行就可以了

```jsx
	{
	  test: sassModuleRegex,
	  use: getStyleLoaders(
	    {
	      importLoaders: 3,
	      sourceMap: isEnvProduction
	        ? shouldUseSourceMap
	        : isEnvDevelopment,
	      modules: {
	        getLocalIdent: getCSSModuleLocalIdent,
+         exportLocalsConvention: "camelCase"
	      },
	    },
	    'sass-loader'
	  ),
	},
```
