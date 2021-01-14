---
title: radium的一点结构
date: 2021-01-14 11:25:44
tags:
---


# radium

## StyleRoot

通过StyleKeeper将功能组织起来

```typescript
const StyleRoot = (props: StyleRootProps) => {
  /* eslint-disable no-unused-vars */
  // Pass down all props except config to the rendered div.
  /* eslint-enable no-unused-vars */
  const {radiumConfig} = props;

  const configContext = useContext(RadiumConfigContext);
  const styleKeeper = useRef<StyleKeeper>(
    getStyleKeeper(radiumConfig, configContext)
  );//获取一个保存css的对象.用于注入样式使用订阅-发布模式

  return (
    <StyleKeeperContext.Provider value={styleKeeper.current}>
      <StyleRootInner {...props} />
    </StyleKeeperContext.Provider>
  );
};
const StyleRootInner = Enhancer(({children, ...otherProps}: StyleRootProps) => (
  <div {...otherProps}>
    {children}
    <StyleSheet />//生成的样式表
  </div>
));
```

### StyleKeeperContext

最外层包裹的上下文,

radium的函数需要用styleRoot包裹,他会接受两个参数,子节点和配置项.

```typescript
type StyleRootProps = {
  radiumConfig: Config,
  children: Node
};
export type Config = {
  matchMedia?: MatchMediaType,
  plugins?: Array<Plugin>,//插件
  userAgent?: string//在getStyleKeeper是会进行绑定
};
```

## StyleSheet

StyleRootInner中的核心部分,返回一个对应的样式表,采用发布订阅模式,某些情况下回更新自己的内容

