# 事件管理

基本的事件注册与触发管理

- implement the [Psr 14](https://github.com/php-fig/fig-standards/blob/master/proposed/event-manager.md) - Event Manager
- 支持设置事件优先级
- 支持快速的事件组注册
- 支持通配符事件的监听

## 事件分组

除了一些特殊的事件外，在一个应用中，大多数事件是有关联的，此时我们就可以对事件进行分组，方便识别和管理使用。

- **事件分组**  推荐将相关的事件，在名称设计上进行分组

例如：

```text
// 模型相关：
model.insert
model.update
model.delete

// DB相关：
db.connect
db.disconnect
db.query

// 应用相关：
app.start
app.run
app.stop
```

## 事件通配符 `*`

支持使用事件通配符 `*` 对一组相关的事件进行监听, 分两种。

1. `*` 全局的事件通配符。直接对 `*` 添加监听器(`$em->attach('*', 'global_listener')`), 此时所有触发的事件都会被此监听器接收到。
2. `{prefix}.*` 指定分组事件的监听。eg `$em->attach('db.*', 'db_listener')`, 此时所有触发的以 `db.` 为前缀的事件(eg `db.query` `db.connect`)都会被此监听器接收到。

> 当然，你在事件到达监听器前停止了本次事件的传播`$event->stopPropagation(true);`，就不会被后面的监听器接收到了。

## swoft 中使用

### 注册事件管理服务

> 作为核心服务组件，会自动启用

>\swoft\app\Middlewares 在这个下面创建文件

>\swoft\config\beans\base.php 配置在这里去添加


```php
'eventManager'    => [
    'class'     => \Swoft\Event\EventManager::class,
],		     
```

### 注册事件监听

- 用注解tag `@Listener("event name")` 来注册

```php
/**
 * 应用加载事件
 *
 * @Listener(AppEvent::APPLICATION_LOADER)
 */
class ApplicationLoaderListener implements EventHandlerInterface
{
    /**
     * @param EventInterface $event      事件对象
     */
    public function handle(EventInterface $event)
    {
        // do something ....
    }
}
```

> 事件名称管理推荐放置在一个单独类的常量里面，方便管理和维护

- 触发事件

```php
\Swoft::trigger('event name', null, $arg0, $arg1);
// OR use \Swoft\App::trigger();
```
