# vue2 to vue3 migration log

[the offical docs for migration](https://v3-migration.vuejs.org/)

## step

1. use [vue@compat](https://v3-migration.vuejs.org/migration-build.html#known-limitations) version to run and check
2. change the major api to vue3 and test
3. remove vue@compat and test
4. update the doc and release on npm for test
5. pull request and contribute

## dev log

- upgrade the tooling

vue-cli: upgrade to the latest @vue/cli-service with vue upgrade

not have global vue-cli, so I do this:

`npm install @vue/cli-service@latest`

- change the dependency

```json
"dependencies": {
-  "vue": "^2.6.12",
+  "vue": "^3.1.0",
+  "@vue/compat": "^3.1.0"
   ...
},
"devDependencies": {
-  "vue-template-compiler": "^2.6.12"
+  "@vue/compiler-sfc": "^3.1.0"
}
```

- alias vue@compat in vue.config.js

```javascript
// vue.config.js
module.exports = {
  chainWebpack: (config) => {
    config.resolve.alias.set('vue', '@vue/compat')

    config.module
      .rule('vue')
      .use('vue-loader')
      .tap((options) => {
        return {
          ...options,
          compilerOptions: {
            compatConfig: {
              MODE: 2
            }
          }
        }
      })
  }
}
```

- At this point, your application may encounter some compile-time errors / warnings (e.g. use of filters). Fix them first. If all compiler warnings are gone, you can also set the compiler to Vue 3 mode.

some [examples](https://github.com/vuejs/vue-hackernews-2.0/commit/b05d9555f6e115dea7016d7e5a1a80e8f825be52) in official repo

it seems that the filter is not used

- npm run dev find type error

```
TypeError: transpileDependencies.map is not a function
    at genTranspileDepRegex (/data1/su/app/smartdata/JinBohan20220312/vue-3d-model/node_modules/@vue/cli-plugin-babel/index.js:6:38)
    at module.exports (/data1/su/app/smartdata/JinBohan20220312/vue-3d-model/node_modules/@vue/cli-plugin-babel/index.js:22:29)
    at /data1/su/app/smartdata/JinBohan20220312/vue-3d-model/node_modules/@vue/cli-service/lib/Service.js:82:9
    at Array.forEach (<anonymous>)
    at loadedCallback (/data1/su/app/smartdata/JinBohan20220312/vue-3d-model/node_modules/@vue/cli-service/lib/Service.js:80:20)
    at Service.init (/data1/su/app/smartdata/JinBohan20220312/vue-3d-model/node_modules/@vue/cli-service/lib/Service.js:97:14)
    at Service.run (/data1/su/app/smartdata/JinBohan20220312/vue-3d-model/node_modules/@vue/cli-service/lib/Service.js:247:16)
    at Object.<anonymous> (/data1/su/app/smartdata/JinBohan20220312/vue-3d-model/node_modules/@vue/cli-service/bin/vue-cli-service.js:37:9)
    at Module._compile (internal/modules/cjs/loader.js:1085:14)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1114:10)
```

it seems that other @vue/cli packages need upgrading

```bash
npm install @vue/cli-plugin-babel@latest
npm install @vue/cli-plugin-eslint@latest
npm install @vue/cli-plugin-unit-jest@latest
```

run success with some typo warning

```
Module Warning (from ./node_modules/vue-loader/dist/templateLoader.js):
(Emitted value instead of an instance of Error) (deprecation COMPILER_NATIVE_TEMPLATE) <template> with no special directives will render as a native template element instead of its inner content in Vue 3.
```

browser console have RouterLink warning

```
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation CONFIG_OPTION_MERGE_STRATS) config.optionMergeStrategies no longer exposes internal strategies. Use custom merge functions instead.
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
softAssertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2527
get @ vue.runtime.esm-bundler.js?fe26:6024
install @ vue-router.esm.js?3423:1323
Vue.use @ vue.runtime.esm-bundler.js?fe26:6065
eval @ router.js?5092:7
./examples/router.js @ app.js:41
__webpack_require__ @ app.js:1522
fn @ app.js:1756
eval @ index.js:4
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation GLOBAL_MOUNT) The global app bootstrapping API has changed: vm.$mount() and the "el" option have been removed. Use createApp(RootComponent).mount() instead.
  Details: https://v3-migration.vuejs.org/breaking-changes/global-api.html#mounting-app-instance
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
assertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2518
createCompatApp @ vue.runtime.esm-bundler.js?fe26:6042
Vue @ vue.runtime.esm-bundler.js?fe26:6039
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation GLOBAL_PROTOTYPE) Vue.prototype is no longer available in Vue 3. Use app.config.globalProperties instead.
  Details: https://v3-migration.vuejs.org/breaking-changes/global-api.html#vue-prototype-replaced-by-config-globalproperties
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
applySingletonPrototype @ vue.runtime.esm-bundler.js?fe26:6274
applySingletonAppMutations @ vue.runtime.esm-bundler.js?fe26:6255
installAppCompatProperties @ vue.runtime.esm-bundler.js?fe26:6187
createApp @ vue.runtime.esm-bundler.js?fe26:6634
createApp @ vue.runtime.esm-bundler.js?fe26:12812
createCompatApp @ vue.runtime.esm-bundler.js?fe26:6049
Vue @ vue.runtime.esm-bundler.js?fe26:6039
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation RENDER_FUNCTION) Vue 3's render function API has changed. You can opt-in to the new API with:

  configureCompat({ RENDER_FUNCTION: false })

  (This can also be done per-component via the "compatConfig" option.)
  Details: https://v3-migration.vuejs.org/breaking-changes/render-function-api.html
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
checkCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2539
convertLegacyRenderFn @ vue.runtime.esm-bundler.js?fe26:8853
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10509
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
app._createRoot @ vue.runtime.esm-bundler.js?fe26:6297
createCompatApp @ vue.runtime.esm-bundler.js?fe26:6053
Vue @ vue.runtime.esm-bundler.js?fe26:6039
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation INSTANCE_EVENT_HOOKS) "hook:destroyed" lifecycle events are no longer supported. From templates, use the "vnode" prefix instead of "hook:". For example, @hook:destroyed should be changed to @vnode-destroyed. From JavaScript, use Composition API to dynamically register lifecycle hooks.
  Details: https://v3-migration.vuejs.org/breaking-changes/vnode-lifecycle-events.html
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
assertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2518
on @ vue.runtime.esm-bundler.js?fe26:2558
once @ vue.runtime.esm-bundler.js?fe26:2574
init @ vue-router.esm.js?3423:2959
beforeCreate @ vue-router.esm.js?3423:1298
callWithErrorHandling @ vue.runtime.esm-bundler.js?fe26:1645
callWithAsyncErrorHandling @ vue.runtime.esm-bundler.js?fe26:1654
callHook @ vue.runtime.esm-bundler.js?fe26:5072
applyOptions @ vue.runtime.esm-bundler.js?fe26:4821
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10558
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
app._createRoot @ vue.runtime.esm-bundler.js?fe26:6297
createCompatApp @ vue.runtime.esm-bundler.js?fe26:6053
Vue @ vue.runtime.esm-bundler.js?fe26:6039
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation GLOBAL_EXTEND) Vue.extend() has been removed in Vue 3. Use defineComponent() instead.
  Details: https://vuejs.org/api/general.html#definecomponent
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
assertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2518
extendCtor @ vue.runtime.esm-bundler.js?fe26:6100
extractGuard @ vue-router.esm.js?3423:2496
eval @ vue-router.esm.js?3423:2480
eval @ vue-router.esm.js?3423:2171
eval @ vue-router.esm.js?3423:2171
flatMapComponents @ vue-router.esm.js?3423:2170
extractGuards @ vue-router.esm.js?3423:2479
extractEnterGuards @ vue-router.esm.js?3423:2520
eval @ vue-router.esm.js?3423:2395
step @ vue-router.esm.js?3423:2001
eval @ vue-router.esm.js?3423:2005
eval @ vue-router.esm.js?3423:2384
eval @ vue-router.esm.js?3423:2162
iterator @ vue-router.esm.js?3423:2362
step @ vue-router.esm.js?3423:2004
step @ vue-router.esm.js?3423:2008
runQueue @ vue-router.esm.js?3423:2012
confirmTransition @ vue-router.esm.js?3423:2392
transitionTo @ vue-router.esm.js?3423:2260
init @ vue-router.esm.js?3423:2994
beforeCreate @ vue-router.esm.js?3423:1298
callWithErrorHandling @ vue.runtime.esm-bundler.js?fe26:1645
callWithAsyncErrorHandling @ vue.runtime.esm-bundler.js?fe26:1654
callHook @ vue.runtime.esm-bundler.js?fe26:5072
applyOptions @ vue.runtime.esm-bundler.js?fe26:4821
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10558
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
app._createRoot @ vue.runtime.esm-bundler.js?fe26:6297
createCompatApp @ vue.runtime.esm-bundler.js?fe26:6053
Vue @ vue.runtime.esm-bundler.js?fe26:6039
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
Show 11 more frames
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation GLOBAL_PRIVATE_UTIL) Vue.util has been removed. Please refactor to avoid its usage since it was an internal API even in Vue 2.
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
assertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2518
get @ vue.runtime.esm-bundler.js?fe26:6170
beforeCreate @ vue-router.esm.js?3423:1299
callWithErrorHandling @ vue.runtime.esm-bundler.js?fe26:1645
callWithAsyncErrorHandling @ vue.runtime.esm-bundler.js?fe26:1654
callHook @ vue.runtime.esm-bundler.js?fe26:5072
applyOptions @ vue.runtime.esm-bundler.js?fe26:4821
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10558
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
app._createRoot @ vue.runtime.esm-bundler.js?fe26:6297
createCompatApp @ vue.runtime.esm-bundler.js?fe26:6053
Vue @ vue.runtime.esm-bundler.js?fe26:6039
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation OPTIONS_DESTROYED) `destroyed` has been renamed to `unmounted`.
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
softAssertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2527
applyOptions @ vue.runtime.esm-bundler.js?fe26:4988
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10558
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
app._createRoot @ vue.runtime.esm-bundler.js?fe26:6297
createCompatApp @ vue.runtime.esm-bundler.js?fe26:6053
Vue @ vue.runtime.esm-bundler.js?fe26:6039
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation OPTIONS_DESTROYED) (2) 
  at <App> 
  at <App>
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2436
softAssertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2527
applyOptions @ vue.runtime.esm-bundler.js?fe26:4988
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10558
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
mountComponent @ vue.runtime.esm-bundler.js?fe26:7646
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
render @ vue.runtime.esm-bundler.js?fe26:8402
instance.ctx._compat_mount @ vue.runtime.esm-bundler.js?fe26:6357
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation COMPONENT_FUNCTIONAL) Functional component <RouterView> should be defined as a plain function in Vue 3. The "functional" option has been removed. NOTE: Before migrating to use plain functions for functional components, first make sure that all async components usage have been migrated and its compat behavior has been disabled.
  Details: https://v3-migration.vuejs.org/breaking-changes/functional-components.html 
  at <App> 
  at <App>
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
softAssertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2527
convertLegacyComponent @ vue.runtime.esm-bundler.js?fe26:9109
_createVNode @ vue.runtime.esm-bundler.js?fe26:9330
createVNodeWithArgsTransform @ vue.runtime.esm-bundler.js?fe26:9225
render @ App.vue?f2cf:52
renderComponentRoot @ vue.runtime.esm-bundler.js?fe26:2922
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7757
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
render @ vue.runtime.esm-bundler.js?fe26:8402
instance.ctx._compat_mount @ vue.runtime.esm-bundler.js?fe26:6357
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation RENDER_FUNCTION) (2) 
  at <RouterLink to="demo-basic" > 
  at <App> 
  at <App>
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2436
checkCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2539
convertLegacyRenderFn @ vue.runtime.esm-bundler.js?fe26:8853
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10509
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
mountComponent @ vue.runtime.esm-bundler.js?fe26:7646
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
render @ vue.runtime.esm-bundler.js?fe26:8402
instance.ctx._compat_mount @ vue.runtime.esm-bundler.js?fe26:6357
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
Show 17 more frames
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation OPTIONS_DESTROYED) (3) 
  at <RouterLink to="demo-basic" > 
  at <App> 
  at <App>
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2436
softAssertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2527
applyOptions @ vue.runtime.esm-bundler.js?fe26:4988
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10558
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
mountComponent @ vue.runtime.esm-bundler.js?fe26:7646
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
render @ vue.runtime.esm-bundler.js?fe26:8402
instance.ctx._compat_mount @ vue.runtime.esm-bundler.js?fe26:6357
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
Show 17 more frames
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation INSTANCE_SCOPED_SLOTS) vm.$scopedSlots has been removed. Use vm.$slots instead.
  Details: https://v3-migration.vuejs.org/breaking-changes/slots-unification.html 
  at <RouterLink to="demo-basic" > 
  at <App> 
  at <App>
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2441
assertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2518
$scopedSlots @ vue.runtime.esm-bundler.js?fe26:9912
get @ vue.runtime.esm-bundler.js?fe26:10076
render @ vue-router.esm.js?3423:1156
compatRender @ vue.runtime.esm-bundler.js?fe26:8856
renderComponentRoot @ vue.runtime.esm-bundler.js?fe26:2922
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7757
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
render @ vue.runtime.esm-bundler.js?fe26:8402
instance.ctx._compat_mount @ vue.runtime.esm-bundler.js?fe26:6357
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
Show 21 more frames
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation OPTIONS_DESTROYED) (4) 
  at <DemoGltf> 
  at <App> 
  at <App>
warn$1 @ vue.runtime.esm-bundler.js?fe26:1528
warnDeprecation @ vue.runtime.esm-bundler.js?fe26:2436
softAssertCompatEnabled @ vue.runtime.esm-bundler.js?fe26:2527
applyOptions @ vue.runtime.esm-bundler.js?fe26:4988
finishComponentSetup @ vue.runtime.esm-bundler.js?fe26:10558
setupStatefulComponent @ vue.runtime.esm-bundler.js?fe26:10455
setupComponent @ vue.runtime.esm-bundler.js?fe26:10377
mountComponent @ vue.runtime.esm-bundler.js?fe26:7646
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
mountChildren @ vue.runtime.esm-bundler.js?fe26:7405
mountElement @ vue.runtime.esm-bundler.js?fe26:7314
processElement @ vue.runtime.esm-bundler.js?fe26:7286
patch @ vue.runtime.esm-bundler.js?fe26:7206
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
componentUpdateFn @ vue.runtime.esm-bundler.js?fe26:7764
run @ vue.runtime.esm-bundler.js?fe26:527
setupRenderEffect @ vue.runtime.esm-bundler.js?fe26:7895
mountComponent @ vue.runtime.esm-bundler.js?fe26:7663
processComponent @ vue.runtime.esm-bundler.js?fe26:7617
patch @ vue.runtime.esm-bundler.js?fe26:7209
render @ vue.runtime.esm-bundler.js?fe26:8402
instance.ctx._compat_mount @ vue.runtime.esm-bundler.js?fe26:6357
eval @ index.js?2e47:7
./examples/index.js @ app.js:19
__webpack_require__ @ app.js:1522
(anonymous) @ app.js:2644
__webpack_require__.O @ app.js:1568
(anonymous) @ app.js:2645
(anonymous) @ app.js:2647
Show 13 more frames
vue.runtime.esm-bundler.js?fe26:1528 [Vue warn]: (deprecation OPTIONS_DESTROYED) (5) 
  at <DemoBasic routerView=true routerViewDepth=0 registerRouteInstance=fn > 
  at <RouterView> 
  at <App> 
  at <App>
```

- tips for fixing step by step.  [doc](https://v3-migration.vuejs.org/breaking-changes/)
    - You can filter for specific warnings in the browser console. It's a good idea to use the filter and focus on fixing one item at a time. You can also use negated filters like -GLOBAL_MOUNT.
    - You can suppress specific deprecations via compat configuration.
    - Some warnings may be caused by a dependency that you use (e.g. vue-router). You can check this from the warning's component trace or stack trace (expanded on click). Focus on fixing the warnings that originate from your own source code first.
    - If you are using vue-router, note <transition> and <keep-alive> will not work with <router-view> until you upgrade to vue-router v4.


- upgrade vue-router from v3 to v4(for vue3)


