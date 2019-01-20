Validator类内置校验函数
---

#### API

#### 列表

~~~php
<?php

/******判断基本数据类型******/
public function isInt($v): bool //整形
public function isFloat($v): bool //浮点型
public function isNumeric($v): bool //数字
public function isBool($v): bool //布尔
public function isString($v): bool //字符
public function isNull($v): bool //null
public function isScalar($v): bool //标量
public function isArray($v): bool //数组
public function isObject($v): bool //对象
public function isResource($v): bool //资源

/****判断常见数字类型****/
public function isZNum($v): bool //整数
public function isNatNum($v): bool //自然数
public function isPNum($v): bool //正整数
public function isNNum($v): bool //负整数
public function isOdd($v): bool //奇数
public function isEven($v): bool //偶数
public function isDecimal($v): bool //小数
public function isPDecimal($v): bool //正小数
public function isNDecimal($v): bool //负小数

/****常见字符串类型****/
public function isEmail($v): bool //email
public function isIP($v): bool //ip地址
public function isURL($v): bool //url地址
public function isAlpha($v): bool //纯英文字符
public function isZH($v): bool //纯中文字符
public function isMobile($v): bool //手机
public function isZIP($v): bool //邮编
public function isTel($v): bool //座机
public function isDate($v): bool //日期
public function isActive($v): bool //常见是否型勾选用字符，见Formatter的Function说明
public function isID($v): bool //18位身份证
public function isBC($v): bool //16-19位银行卡号
public function isAccount($v): bool //账号常见字符，中英文、数字、横线、下划线、点、@字符

/***二值比较,支持多种数据类型***/
public function checkPwd($pwd, $hash): bool //使用password_hash的密码校验
public function gt($a, $b): bool //大于
public function lt($a, $b): bool //小于
public function ge($a, $b): bool //大等于
public function le($a, $b): bool //小等于
public function eq($a, $b): bool //等于
public function ne($a, $b): bool //不等于

~~~