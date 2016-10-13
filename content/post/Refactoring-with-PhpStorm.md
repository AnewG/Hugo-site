+++
date = "2016-04-16T13:11:23+08:00"
description = "phpstorm"
title = "Refactoring with PhpStorm"

+++

change Signature

    * To change the function name.

    * To add new parameters and remove the existing ones.

    * To assign default values to the parameters.

    * To reorder(重排) parameters.

    * To change parameter names.

    * To propagate(传播) new parameters through the function call hierarchy.

    * Tips:

        * For each new parameter added to a function, you can specify its default value (or an expression) in the `Default` field.

        * Extract

    * Extract Field

    ```php
    //Before
    public static function find($params){
    if (isset($params[\'param_query\'])) {
        $result = MyDatabase::execute($params[\'param_query\']);
        }
    }

    public static function findAll($params){
    if (isset($params[\'param_query\'])) {
        $result = MyDatabase::executeAll($params[\'param_query\']);
        }
    }

    //After
    public static
    $query = \'param_query\';

    public static function find($params){
    if (isset($params[self::$query])) {
        $result = MyDatabase::execute($params[self::$query]);
        }
    }

    public static function findAll($params){
    if(isset($params[self::$query])){
        $result = MyDatabase::executeAll($params[self::$query]);
        }
    }
    ```
    * Extract Constant & Interface & Method & Parameter & Variable

    * Inline refactoring is opposite to Extract

* Pull Members up(down) refactoring allows you to move class members to a superclass .

* Rename & Safe Delete

