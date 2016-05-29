# he_test

## packagist 流程安装  ##
    composer require "jigui/he_test:*" -vvv

## 手动流程安装  ##

若只是想把这个类库的资源.在别的composer项目B中用..且不想走packagist这套流程...则可以按:

 1. 项目B的vender文件夹下建立一个Ford文件夹..
 2. 将本项目的src文件夹.复制到别的项目B的vender/Ford文件夹下
 3.  在项目B的composer的autoload_psr4.php 中手动添加 下行,
        `'Ford\\' => array($vendorDir . '/Ford/src/Ford'),`

    使用时按namespace规则如下:



            use Ford;
            public function getTest() {
                Ford\Escape\Escape2013::info();
                Ford\Fiesta\Fiesta2013::info();
                Ford\Focus\Focus2013::info();
                var_dump('adfaf');
            }


    正常会输出:

        This is Ford Escape2013!
        This is Ford Fiesta2013!
        This is Ford Focus2013!
        string(5) "adfaf"

 4. 需要注意的是此时项目B的composer并不能高效加载且每次引入新包都要手动添加一次,

若在项目b中执行

    composer dumpautoload -o -vvv
**正常执行后.不知为何会将刚才手动添加的 map记录..给清除掉..郁闷呀..**
即使将当前项目的composer.json也复制过去...也是不行...composer.phar内部的逻辑..还真没去细看呀..

这是因为
1. composer 读取composer.lock, 取得安装了哪些包，遍历这些包，下一步
2. composer 默认根据每个包的autoload段的描述...生成对应的加载方式
所以你如果不是通过composer 安装的包,每次dumpautoload时..都会根据composer.lock里的列表重新生成一次结果到对应的加载文件的文件中...

所以.若此时项目B要引入其它包时.
每次 composer install/update/requirer成功后..都会自动的dump-autoload -o一下...
就会把手动添加的清除掉.导致的结果就是不能自动加载了..


若是手动的现有的包里加了一个文件...此时dumpautoload -o时则会把新增的文件 补充到classmap里...
