> unit test framwork for python  
%Python%/Lib/unittest

### 四个概念
*test fixture*
> 执行测试用例前的准备和相关的清理工作，通过覆盖TestCase类的setUp()和tearDown()方法实现。如创建临时或代理的数据库，目录，又或者起服务器进程。  

*test case*
> 独立的测试单元。它会针对特定的一套输入去检查具体的响应。  

*test suite*
> 测试用例，用例组合或者两者的集合。它可以对测试用例进行归类并同时去运行它们。  

*test runner*
> 编排用例的执行并输出结果给用户。  

### 测试类的结构
```
import unittest  
import #待测的模块，待测类等

class TestClassName(unittest.TestCase):

      #重载unittest.TestCase类的setup方法
      def setUp(self):
             ...

      #重载unittest.TestCase类的teardown方法
      def tearDown(self):
             ...

      #测试用例名以"test" 开始
      def test*(self):
             ...
       ......
if __name_=='__main__':
     unittest.main()
```

### 执行测试用例
#### command line
* 测试模块  
` python -m unittest test_module1 test_module2`
* 测试类  
` python -m unittest test_module.TestClass`
* 测试独立的方法  
` python -m unittest test_module.TestClass.test_method`
* 测试指定路径的模块  
` python -m unittest tests/test_something.py`
* 详细输出测试信息 -v  
` python -m unittest -v test_module`
* 通过Test Discovery执行测试用例  
` python -m unittest`
* 帮助信息  
` python -m unittest -h`

#### **run**
* IDLE
  > [PyCharm](http://www.jetbrains.com/pycharm/)

### 断言方法
| Method | Checks that | New in|
|:-:|:-:|:-:|
| assertEqual(a, b) | a == b | |
| assertNotEqual(a, b)| a != b | |
| assertTrue(x) | bool(x) is True | |	
| assertFalse(x) | bool(x) is False | |	
| assertIs(a, b) | a is b | 3.1 |
| assertIsNot(a, b) | a is not b | 3.1 |
| assertIsNone(x) | x is None | 3.1 |
| assertIsNotNone(x) | x is not None | 3.1 |
| assertIn(a, b) | a in b | 3.1 |
| assertNotIn(a, b) | a not in b | 3.1 |
| assertIsInstance(a, b) | isinstance(a, b) | 3.2 |
| assertNotIsInstance(a, b) |not isinstance(a, b) | 3.2 |
>Notes: assert*(..., msg='提示信息')

### 组织测试用例
#### main()方法
> unittest.TestLoader类搜索模块中所有以"test"开始的测试方法并执行
```
if __name=='__main':
     unittest.main()
```
#### unittest.TestSuite类
1. 构造测试集
2. 实例化TestSuite对象  
`suite=unittest.TestSuite()`
3. TestSuite的addTest()方法将不同测试类的测试方法组装到测试套件中  
`suite.addTest(TestClassName('test*'))`
4. 运行测试集
5. 实例化TextTestRunner对象  
`runner=unittest.TextTestRunner()`
6. TextTestRunner的run方法运行测试集  
`runner.run(suite)`
#### unittest.TestLoader类
#####  代码
正常情况，不需要实例化TestLoader对象，unittest提供了共享的defaultTestLoader类，可使用其子类和方法创建实例，discover方法就是其中之一:  
`discover(start_dir, pattern='test*.py', top_level_dir=None)`
* *start_dir*
  > 要测试的模块名或测试用例目录

* *pattern=\'test*.py\'*
  > 测试用例文件名的匹配原则

* *top_level_dir=None*
  > 测试模块的顶层目录，默认为None

discover()方法找到指定目录下的所有测试模块，并可递归查找到子目录下的测试模块，只有匹配到的文件内的模块才能被加载。如果启动的不是顶层目录，那么顶层目录必须单独指定
> Notes: 各级子目录下放一个__init__.py空文件，如此才可被discover()递归查找到

###### 创建discover实例  
`discover=unittest.defaultTestLoader.discover(start_dir, pattern='test*.py')`
###### 运行测试集  
discover方法将查找到的测试用例组装到测试套件中，通过run()方法运行discover测试集
`runner.run(discover)`
##### 命令行
```
python -m unittest discover  
-v, --verbose  
Verbose output  
-s, --start-directory directory  
Directory to start discovery (. default)  
-p, --pattern pattern  
Pattern to match test files (test*.py default)  
-t, --top-level-directory directory  
Top level directory of project (defaults to start directory)
```
### 测试用例执行顺序
unittest框架默认根据ASCII码的顺序加载测试用例: 0\~9, A\~Z, a\~z;
测试目录, 测试文件, main(), discover()加载测试用例按照默认规则
TestSuite类按照addTest()方法添加用例的顺序执行

#### 跳过测试和预期失败
`@unittest.skip('reason')`
> 无条件跳过装饰的测试，说明跳过测试的原因
  
`@unittest.skipIf(condition, 'reason')`
> 如果条件为真，跳过装饰的测试
  
`@unittest.skipUnless(condition, 'reason')`
> 除非条件为真，跳过装饰的测试

`@unittest.expectedFailure()`
> 不管执行结果是否失败，测试始终标记为失败

### fixtures
* *setUpModule()/tearDownModule()*
  > 在整个模块的开始与结束时被执行

* *setUpClass(cls)/tearDownClass(cls)*
  > 在测试类的开始与结束时被执行  
需要通过@classmethord进行装饰  
方法参数为cls(等同于self, 约定俗成的命名)

* *setUp(self)/tearDown(self)*
  > 在测试用例的开始与结束时被执行