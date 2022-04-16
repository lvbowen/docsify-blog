# 权限管理

对于没有权限查看当前页面的情况，一般来讲有两种处理方式，一是直接重定向到另一个页面（如首页），二是渲染一个无权限页面，提示用户因为没有当前页面的权限所以无法查看。二者是排他的，即每个页面只需要使用其中一种即可，于是我们在路由配置中可以根据需要去配置 redirect 或 unauthorized 属性，分别对应无权限重定向及无权限显示无权限页面两种处理方式。具体代码大家可以参考示例项目 [react-acl-router](https://github.com/AlanWei/react-acl-router/blob/master/src/AclRouter.jsx) 中的实现，这里摘录一小段核心部分。

```js
renderRedirectRoute = route => (
  <Route
    key={route.path}
    {...omitRouteRenderProperties(route)}
    render={() => <Redirect to={route.redirect} />}
  />
);

renderAuthorizedRoute = (route) => {
  const { authorizedLayout: AuthorizedLayout } = this.props;
  const { authorities } = this.state;
  const {
    permissions,
    path,
    component: RouteComponent,
    unauthorized: Unauthorized,
  } = route;
  const hasPermission = checkPermissions(authorities, permissions);

  if (!hasPermission && route.unauthorized) {
    return (
      <Route
        key={path}
        {...omitRouteRenderProperties(route)}
        render={props => (
          <AuthorizedLayout {...props}>
            <Unauthorized {...props} />
          </AuthorizedLayout>
        )}
      />
    );
  }

  if (!hasPermission && route.redirect) {
    return this.renderRedirectRoute(route);
  }

  return (
    <Route
      key={path}
      {...omitRouteRenderProperties(route)}
      render={props => (
        <AuthorizedLayout {...props}>
          <RouteComponent {...props} />
        </AuthorizedLayout>
      )}
    />
  );
}
```
于是，在最终的路由中，我们会优先匹配无需鉴权的页面路径，保证所有用户在访问无需鉴权的页面时，第一时间就可以看到页面。然后再去匹配需要鉴权的页面路径，最终如果所有的路径都匹配不到的话，再渲染 404 页面告知用户当前页面路径不存在。

```js
<Switch>
  {map(normalRoutes, route => (
    this.renderNormalRoute(route)
  ))}
  {map(authorizedRoutes, route => (
    this.renderAuthorizedRoute(route)
  ))}
  {this.renderNotFoundRoute()}
</Switch>
```