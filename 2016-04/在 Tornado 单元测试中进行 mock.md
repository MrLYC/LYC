# 在 Tornado 单元测试中进行 mock 

## 难点

Tornado 是一个异步的框架，为了处理大量的异步回调问题，Tornado 引入了生成器方式的`gen.coroutine`来减少回调函数的使用。

通过在生成器中` yield`一个 **Future** 对象来让出运行权，当一个 **Future** 对象的结果被真正生成之后，框架将结果发送回生成器，继续生成器的执行：

```python
@gen.conroutine
def do_something_async():
    future = get_data_async()
    data = yield future
    raise gen.Return(do_something_with_data(data))
```

如果`get_data_async`是依赖外部系统的一个函数，我们测试`do_something_async`会遇到两个问题：

1. 如何进行`get_data_async`的 mock。
2. 如何驱动`do_something_async`。



## mock get_data_async

单纯 mock `get_data_async`函数可以借助 mock 模块，但需要注意的是替换掉的那个模块需要返回 **Future** 对象，并调用`future.set_result`装入真正的结果以便框架顺利运行。



## 驱动 `do_something_async`

测试类继承`tornado.testing.AsyncTestCase`，并在每个测试前加上`@tornado.testing.gen_test`来驱动。



## 示例

```python
class TestMock(tornado.testing.AsyncTestCase):
    @mock.patch("some.path.to.get_data_async")
    @tornado.testing.gen_test
    def test_mock(self, get_data_async):
        future = tornado.concurrent.Future()
        future.set_result(mock.MagicMock())
        get_data_async.return_value = future
        
        result = yield do_something_async()
        self.assertTrue(check_result(result))
```

