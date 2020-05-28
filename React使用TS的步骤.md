<!--
    title="React使用TS的步骤"
    thumbnail=["https://tva1.sinaimg.cn/large/007S8ZIlly1gdvbiue918j31280iwwno.jpg"]
    summary="在React中集成Typescript和Material UI的步骤。 适合新手，不适合老鸟"
-->
# React使用TS的步骤
> 个人亲测可用 脚手架地址: https://github.com/andy-zhangtao/react-ts.git

##  前置工具

* node
* npm
* yarn

## 安装`create-react-app`

```
npm install -g create-react-app
```

##  创建工程

```
create-react-app <name> --template typescript
```

## 安装Material UI

```
yarn add @material-ui/core
```

## 配置
### store

#### 创建rootReducer。

```typescript
import { combineReducers } from 'redux';

import xxxaction from './actions'

const reducers = {
    xxxactions,
}
export default combineReducers(reducers)
```

#### 创建`store.ts`文件，例如创建在`redux/store.ts`。
> redux-devtools-extension 可以在浏览器查看每次发起的action请求
>
``` typescript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

import rootReducer from './rootReducer';
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(rootReducer,
    composeWithDevTools(
        applyMiddleware(thunk)
    )
);

export default store;
```

### 主题
> 如果想使用`material-ui`提供的主题功能，参考`devex-ng`的theme目录

### 路由

将所有路由都定义到`routers.ts`中。
```typescript
// routes.ts
// 首页重定向到dashboard页面
import React from 'react';
import { Redirect } from 'react-router-dom';
import DashBoardClass from './componts/dashboard'
import LoginClass from './componts/login'
import LogoutClass from './componts/logout'
import Index from './componts/index'

const routes = [
  {
    path: '/',
    exact: true,
    component: () => <Redirect to="/dashboard" />
  },
  {
    path: '/login',
    exact: true,
    component: LoginClass
  },
  {
    path: '/logout',
    exact: true,
    component: LogoutClass
  },
  {
    route: '*',
    component: Index, // 其它页面都在Index中进行渲染
    routes: [
      {
        path: '/dashboard',
        exact: true,
        component: DashBoardClass
      },
    ]
  },

];

export default routes;

```

### 首页

Index负责集中渲染其它页面

``` typescript
import React, { Component } from 'react';
import { bindActionCreators, Dispatch } from 'redux'
import { connect } from 'react-redux'
import { withRouter, } from 'react-router-dom';
import { History, LocationState } from 'history'
import { RouteConfig } from 'react-router-config'

import './index.css'
import TopBar from './topbar';

interface IndexPros {
    history: History<LocationState>
    route?: RouteConfig
}

class Index extends Component<IndexPros, {}>{

    componentWillMount() {
        // 可以判定当前用户是否需要登录
    }
    render() {
        const { route } = this.props

        return (
            <ConfigProvider>
                <Layout >
                    <TopBar route={route} />
                </Layout>
            </ConfigProvider>
        )
    }

}

const mapStateToProps = (state: any) => ({

})
const mapDispatchToProps = (dispatch: Dispatch) => bindActionCreators(
    {},
    dispatch
)

export default withRouter(connect(
    mapStateToProps,
    mapDispatchToProps,
)(Index))
```

`tarbar`是一个菜单组件

```typescript
import React, { Suspense } from 'react';
import { renderRoutes } from 'react-router-config';
import PropTypes from 'prop-types';
import clsx from 'clsx';
import { Link as RouterLink, } from 'react-router-dom';
import { makeStyles, useTheme, Theme, createStyles } from '@material-ui/core/styles';
import Drawer from '@material-ui/core/Drawer';
import CssBaseline from '@material-ui/core/CssBaseline';
import AppBar from '@material-ui/core/AppBar';
import Toolbar from '@material-ui/core/Toolbar';
import List from '@material-ui/core/List';
import Divider from '@material-ui/core/Divider';
import IconButton from '@material-ui/core/IconButton';
import MenuIcon from '@material-ui/icons/Menu';
import ChevronLeftIcon from '@material-ui/icons/ChevronLeft';
import ChevronRightIcon from '@material-ui/icons/ChevronRight';
import ListItem from '@material-ui/core/ListItem';
import ListItemIcon from '@material-ui/core/ListItemIcon';
import ListItemText from '@material-ui/core/ListItemText';
import CallSplitIcon from '@material-ui/icons/CallSplit';
import DashboardIcon from '@material-ui/icons/Dashboard';
import PersonIcon from '@material-ui/icons/Person';
import SwapHorizIcon from '@material-ui/icons/SwapHoriz';
import CategoryIcon from '@material-ui/icons/Category';
import { Hidden, Link, LinearProgress, Avatar, Typography } from '@material-ui/core';
import { getUser } from '../../utils/token';

const drawerWidth = 240;

const useStyles = makeStyles((theme: Theme) =>
    createStyles({
        root: {
            display: 'flex',
        },
        flexGrow: {
            flexGrow: 1
        },
        appBar: {
            transition: theme.transitions.create(['margin', 'width'], {
                easing: theme.transitions.easing.sharp,
                duration: theme.transitions.duration.leavingScreen,
            }),
        },
        appBarShift: {
            width: `calc(100% - ${drawerWidth}px)`,
            marginLeft: drawerWidth,
            transition: theme.transitions.create(['margin', 'width'], {
                easing: theme.transitions.easing.easeOut,
                duration: theme.transitions.duration.enteringScreen,
            }),
        },
        menuButton: {
            marginRight: theme.spacing(2),
        },
        hide: {
            display: 'none',
        },
        drawer: {
            width: drawerWidth,
            flexShrink: 0,
        },
        drawerPaper: {
            width: drawerWidth,
        },
        drawerHeader: {
            display: 'flex',
            alignItems: 'center',
            padding: theme.spacing(0, 1),
            ...theme.mixins.toolbar,
            justifyContent: 'flex-end',
        },
        content: {
            flexGrow: 1,
            padding: theme.spacing(3),
            transition: theme.transitions.create('margin', {
                easing: theme.transitions.easing.sharp,
                duration: theme.transitions.duration.leavingScreen,
            }),
            marginLeft: -(drawerWidth / 4),
        },
        contentShift: {
            transition: theme.transitions.create('margin', {
                easing: theme.transitions.easing.easeOut,
                duration: theme.transitions.duration.enteringScreen,
            }),
            marginLeft: 0,
        },
    }),
);

const PersistentDrawerLeft = (props: any) => {
    const classes = useStyles();
    const theme = useTheme();
    const [open, setOpen] = React.useState(true);
    const { route } = props;

    const handleDrawerOpen = () => {
        setOpen(true);
    };

    const handleDrawerClose = () => {
        setOpen(false);
    };

    let u = Math.ceil(Math.random() * 100000)

    let avatat = "https://avatars1.githubusercontent.com/u/".concat(String(u))

    return (
        <div className={classes.root}>
            <div className="content">
            </div>
            <CssBaseline />
            <AppBar
                position="fixed"
                className={clsx(classes.appBar, {
                    [classes.appBarShift]: open,
                })}
            >
                <Toolbar>
                    <IconButton
                        color="inherit"
                        aria-label="open drawer"
                        onClick={handleDrawerOpen}
                        edge="start"
                        className={clsx(classes.menuButton, open && classes.hide)}
                    >
                        <MenuIcon />
                    </IconButton>
                    <RouterLink to="/dashboard">
                        <img
                            alt="Logo"
                            src="/images/logos/devex-admin.svg"
                        />
                    </RouterLink>
                    <div className={classes.flexGrow} />
                    <Hidden mdDown>
                        <Link href="/logout" color="inherit">退出</Link>
                    </Hidden>
                </Toolbar>
            </AppBar>
            <Drawer
                className={classes.drawer}
                variant="persistent"
                anchor="left"
                open={open}
                classes={{
                    paper: classes.drawerPaper,
                }}
            >
                <div className={classes.drawerHeader}>
                    <IconButton onClick={handleDrawerClose}>
                        {theme.direction === 'ltr' ? <ChevronLeftIcon /> : <ChevronRightIcon />}
                    </IconButton>
                </div>
                <List>
                    <ListItem >

                        <Avatar
                            alt="Author"
                            style={{
                                marginRight: 2,
                                backgroundColor: 'orange',
                                width: theme.spacing(7),
                                height: theme.spacing(7),
                            }}
                            src={avatat}
                        >

                        </Avatar>
                        <div style={{
                            marginLeft: 40
                        }}>
                            <Typography variant="body1">
                                {getUser().toUpperCase()}
                            </Typography>
                        </div>

                    </ListItem>
                </List>
                <Divider />
                <List>
                    <ListItem button key='dashboard' component="a" href="/dashboard">
                        <ListItemIcon>
                            <DashboardIcon />
                        </ListItemIcon>
                        <ListItemText primary='平台总览' />
                    </ListItem>
                </List>
                <Divider />
                <List>
                    <ListItem button key='user' component="a" href="/user">
                        <ListItemIcon>
                            <PersonIcon />
                        </ListItemIcon>
                        <ListItemText primary='用户' />
                    </ListItem>
                </List>
            </Drawer>
            <main
                className={clsx(classes.content, {
                    [classes.contentShift]: open,
                })}
            >
                <main className={classes.content}>
                    <Suspense fallback={<LinearProgress />}>
                        {renderRoutes(route.routes)}
                    </Suspense>
                </main>

            </main>
        </div>
    );
}

PersistentDrawerLeft.propTypes = {
    route: PropTypes.object
};
export default PersistentDrawerLeft
```

### 集成

最后修改index.tsx，将以上元素集成一起
```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import { renderRoutes } from 'react-router-config';
import { Router } from 'react-router-dom';
import { createBrowserHistory } from 'history';
import { Provider as StoreProvider } from 'react-redux';
import { ThemeProvider } from '@material-ui/styles';
import MomentUtils from '@date-io/moment';
import { MuiPickersUtilsProvider } from '@material-ui/pickers';
import store from './redux/store';
import routes from './routes';
import theme from './theme';

const history = createBrowserHistory();

ReactDOM.render(
    <StoreProvider store={store}>
        <ThemeProvider theme={theme}>
            <MuiPickersUtilsProvider utils={MomentUtils}>
                <Router history={history}>
                    {renderRoutes(routes)}
                </Router>
            </MuiPickersUtilsProvider>
        </ThemeProvider>
    </StoreProvider>,
    document.querySelectorAll('.app')[0]
);
```


最后工程目录结构如下:

```shell
.
├── README.md
├── package-lock.json
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── manifest.json
│   └── robots.txt
├── src
│   ├── App.css
│   ├── App.test.tsx
│   ├── App.tsx
│   ├── compoment
│   ├── index.css
│   ├── index.tsx
│   ├── logo.svg
│   ├── react-app-env.d.ts
│   ├── redux
│   ├── routes.js
│   ├── serviceWorker.ts
│   ├── setupTests.ts
│   └── theme
├── tsconfig.json
└── yarn.lock
```

## 注意事项

1. 如果Dom创建失败，注意public/index.html中的div类型。 应该是class，而不是id

2. 注意依赖的版本

## 疑难问题

### GRPC-WEB编译报错
> 发生在使用create-react-app的场景中

错误原因是CRA使用的ESLint规则规定TS检查失败，则构建失败。 因此需要忽略*.pb.js的eslint检查。

创建`.env`文件，内容为：`EXTEND_ESLINT=true`。 然后再package.json中增加需要忽略的文件:

```json
  "eslintConfig": {
    "extends": "react-app",
    "ignorePatterns": [
      "**/*_pb.js"
    ]
  },
```

### <Redirect to="/dashboard" /> 找不到to
> 低级错误

注意`routes`的后缀，不能是`ts/tsx`。 改成`js`跳过ts语法检查

### 添加新页面后, 在Index中看不到
> 有可能是被遮挡住了

在`index.tsx`页面中，设置全局`padding`。 如下：

```
        return (
            <div style={{ padding: 50 }}>
                <TopBar route={route} />
            </div>
        )
```