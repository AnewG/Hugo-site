+++
date = "2016-01-03T17:43:14+08:00"
description = "debug"
title = "Debug and Test"

+++

chrome:

    查找函数定义：在Js调试界面里：`Ctrl + Shift + o`

    查找文件：在Js调试界面里：`Ctrl + o`

    全局查找：`Cmd + option + f`

PHPUnit

    多个同时测试

  ```php
  class Add
  {
      protected $_num1 = 0, $_num2 = 0;
      public function setNum1($num)
      {
          $this->_num1 = (int) $num;
      }
      public function setNum2($num)
      {
          $this->_num2 = (int) $num;
      }
      public function getResult()
      {
          return $this->_num1 + $this->_num2;
      }
  }
  ```

  测试源码：

  ```php
  require_once \'PHPUnit/Framework/TestCase.php\';
  require_once \'Add.php\';

  class AddTest extends PHPUnit_Framework_TestCase
  {
      /**
       * @dataProvider provider
       */
      public function testAdd($num1,$num2,$expection)
      {
          $add = new Add();
          $add->setNum1($num1);
          $add->setNum2($num2);
          $this->assertEquals($expection,$add->getResult());
      }

      public function provider()
      {
          return array(
              array(0,0,0),
              array(0,1,1),
              array(1,0,1),
              array(1,1,3)   //false
          );
      }
  }
  ```

  对应该是错误情况的测试

  ```php
  require_once \'PHPUnit/Framework/TestCase.php\';

  class ExceptionTest extends PHPUnit_Framework_TestCase
  {
      /**
       * @expectedException InvalidArgumentException
       */
      public function testException()  //这里的代码应该抛出异常才是通过测试
      {
          //throw new IncalidArgumentException(\'Test\'); 有这个通过，没这个false
      }
  }
  ```

  执行顺序：

  <br />

  `setUp() -> func1() -> tearDown() -> setUp() -> func2() ...`

  <br />

  为每个测试方法准备同一个资源，省去分别在每个函数中建立的成本

  ```php
  require_once \'PHPUnit/Framework.php\';

  class ArrayTest extends PHPUnit_Framework_TestCase
  {
      protected $fixture;

      protected function setUp()
      {
          $this->fixture = array();  //不清除的话可以和其他class共用
      }

      public function func1()
      {
      }

      public function func2()
      {
      }

      protected function tearDown()
      {
          unset($this->fixture);
      }
  }
  ```

  同性质的包组合起来测试

  ```php
  # file Package/Class1Test.php
  require_once \'PHPUnit/Framework.php\';

  class Package_Class1Test extends PHPUnit_Framework_TestCase
  {
      public function test1()
      {
         //...
      }

      public function test2()
      {
         //...
      }
  }

  # file Package/Class2Test.php
  require_once \'PHPUnit/Framework.php\';

  class Package_Class2Test extends PHPUnit_Framework_TestCase
  {
      public function test3()
      {
         //...
      }

      public function test4()
      {
         //...
      }
  }
  ```

  测试源码：

  ```php
  require_once \'PHPUnit/Framework/TestCase.php\';
  require_once \'Package/Class1Test.php\';
  require_once \'Package/Class2Test.php\';

  class AddTest extends PHPUnit_Framework_TestCase
  {
      public static function suite()
      {
          $suite = new PHPUnit_Framework_TestSuite(\'Package\');
          $suite->addTestSuite(\'Package_Class1Test\');
          $suite->addTestSuite(\'Package_Class2Test\');
          return $suite;
      }

      protected function setUp()
      {
          $this->fixture = array();
      }

      protected function tearDown()
      {
          unset($this->fixture);    //这里的setUp和tearDown是在最开始和最后执行的，各个文件中的setUp和tearDown在其中运行
      }
  }
  ```

  PHPUnit中，注解也是有用处的。

  <br />

  备用套件：SimpleTest
