---
title: Vue 自定义验证规则
date: 2018-08-11 15:48:48
tags:
---

vue 使用element 样式模板 校验 el-input 输入规则
普通的校验规则 在:rule 里面完成
但是复杂的规则 ，则需要 写校验函数，全局校验函数

1.创建校验函数
```
const rules = {
	uer:{
		    //用户名验证
		username(len_min,len_max,text){
			return [
	    		{ required: true, message: text, trigger: 'blur' },
	        	{
	        		validator:(rule,value,callback) =>{
	        			if(value.match(/^[a-zA-Z0-9\u4e00-\u9fa5]{5,16}$/)){
	        				callback();
	        			}else{
	        				callback("只能是数字、字母和汉字5-16位")
	        			}
	        		},
	        		trigger:'blur'
	        	}
	    	]
		},

        //密码验证
		password(len_min,len_max,text) {
			 return [
		        { required: true, message: text, trigger: 'blur' },
		        { validator:(rule,value,callback) =>{
	        			if(value.match(/^[a-zA-Z0-9^%&'!@#*()_+[{}:'"<>/,;=?$\x22]{5,16}$/)){
	        				callback();
	        			}if(value.match(/^\s*|\s*$/)){
                  callback("除空格外数字,字母和任意字符6-16位")
                }
                
	        		},
	        		trigger:'blur'
		        }
			]
		},

        //手机号验证
		phone(){
			return [
	        		{ required: true, message: '请输入手机号', trigger: 'blur' },
	          		{
	                	validator: (rule, value, callback) => {
		                    if (value.match(/^(13[0-9]|14[579]|15[0-3,5-9]|16[6]|17[0135678]|18[0-9]|19[89])\d{8}$/)){
		                        callback();
		                    } else {
		                        callback("请输入正确的手机号");
		                    }
	                	},
	                	trigger: 'blur'
	            	}
	        	]
		},

        //姓名验证
		truename(){
			return [
	    		{ required: true, message: '请输入姓名', trigger: 'blur' },

	        	{
	                validator: (rule, value, callback) => {
		                    if (value.match(/^[a-zA-Z\u4e00-\u9fa5]{2,8}$/)){
		                        callback();
		                    } else {
		                        callback("请输入2-8位汉字或英文");
		                    }
	                	},
	                	trigger: 'blur'
	            	}
	    	]
		},
	}
}
```
这里我给出了 三组校验函数，密码，手机号，姓名

2.页面引入 函数所在的文件
import globalRules from '@/config/global_rules'

3.在规则里面调用函数的方法
```
rule:{
	passWord:globalRules.rules.user.password(6,20,'请输入当前密码：)
}
```
这样一个全局封装的 校验方法就完成了，简单的方式，漂亮的代码，完整的规则


后记：
校验函数，除了封装函数，最重要的是 如何通过正则去校验你要输入的内容

下面就写一些，平常会用的校验方法 和 正则使用
一、常用的校验
1. 只能输入大于0的整数
```
check(value) {
    let reg = /^[1-9]\d*$/;
    var _this = this;
    if (value) {
        if (new RegExp(reg).test(value) == false) {
            setTimeout(() => {
                _this.actionDataForm.studNum = '';
_this.errorTip = true;
　　　　　　　　}, 500); }else{ this.errorTip = false; } } }, 
```
2.邮箱验证
```
var checkEmail = (rule, value, callback) => {
    if (!value) {
        return callback();
    }
    if (value) {
        setTimeout(() => {
            var reg = /^([a-zA-Z0-9]+[_|\_|\.]?)*[a-zA-Z0-9]+@([a-zA-Z0-9]+[_|\_|\.]?)*[a-zA-Z0-9]+\.[a-zA-Z]{2,3}$/;
            if (!reg.test(value)) {
                callback(new Error('请输入有效的电子邮箱！'));
            } else {
                callback();
            }
          }, 500);
      }
}
```
3. 直接页面验证 有且只能有两位小数
```
<el-form-item label="成交总额：" prop="money">
                <el-col :span="15">
                  <el-input v-model="item.money" v-on:input="getMoney(item.money)"  value="0" :maxlength="inputMaxL"  @input="inputMaxL = /^\d+\.?\d{0,1}$/.test(item.money) ? null : item.money.length - 1" v-bind:disabled="isCan==1" ></el-input>
                </el-col>
              </el-form-item>
```
	这里要记得 函数里 定义一下 inputMaxL

二、正则验证规则
1.验证数字
```
1 数字：^[0-9]*$
 2 n位的数字：^\d{n}$
 3 至少n位的数字：^\d{n,}$
 4 m-n位的数字：^\d{m,n}$
 5 零和非零开头的数字：^(0|[1-9][0-9]*)$
 6 非零开头的最多带两位小数的数字：^([1-9][0-9]*)+(.[0-9]{1,2})?$
 7 带1-2位小数的正数或负数：^(\-)?\d+(\.\d{1,2})?$
 8 正数、负数、和小数：^(\-|\+)?\d+(\.\d+)?$
 9 有两位小数的正实数：^[0-9]+(.[0-9]{2})?$
10 有1~3位小数的正实数：^[0-9]+(.[0-9]{1,3})?$
11 非零的正整数：^[1-9]\d*$ 或 ^([1-9][0-9]*){1,3}$ 或 ^\+?[1-9][0-9]*$
12 非零的负整数：^\-[1-9][]0-9"*$ 或 ^-[1-9]\d*$
13 非负整数：^\d+$ 或 ^[1-9]\d*|0$
14 非正整数：^-[1-9]\d*|0$ 或 ^((-\d+)|(0+))$
15 非负浮点数：^\d+(\.\d+)?$ 或 ^[1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0$
16 非正浮点数：^((-\d+(\.\d+)?)|(0+(\.0+)?))$ 或 ^(-([1-9]\d*\.\d*|0\.\d*[1-9]\d*))|0?\.0+|0$
17 正浮点数：^[1-9]\d*\.\d*|0\.\d*[1-9]\d*$ 或 ^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*))$
18 负浮点数：^-([1-9]\d*\.\d*|0\.\d*[1-9]\d*)$ 或 ^(-(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*)))$
19 浮点数：^(-?\d+)(\.\d+)?$ 或 ^-?([1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0)$
```
2.校验字符
```
1 汉字：^[\u4e00-\u9fa5]{0,}$
 2 英文和数字：^[A-Za-z0-9]+$ 或 ^[A-Za-z0-9]{4,40}$
 3 长度为3-20的所有字符：^.{3,20}$
 4 由26个英文字母组成的字符串：^[A-Za-z]+$
 5 由26个大写英文字母组成的字符串：^[A-Z]+$
 6 由26个小写英文字母组成的字符串：^[a-z]+$
 7 由数字和26个英文字母组成的字符串：^[A-Za-z0-9]+$
 8 由数字、26个英文字母或者下划线组成的字符串：^\w+$ 或 ^\w{3,20}$
 9 中文、英文、数字包括下划线：^[\u4E00-\u9FA5A-Za-z0-9_]+$
10 中文、英文、数字但不包括下划线等符号：^[\u4E00-\u9FA5A-Za-z0-9]+$ 或 ^[\u4E00-\u9FA5A-Za-z0-9]{2,20}$
11 可以输入含有^%&',;=?$\"等字符：[^%&',;=?$\x22]+
12 禁止输入含有~的字符：[^~\x22]+
```
3.特殊的校验
```
1 Email地址：^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$
 2 域名：[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(/.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+/.?
 3 InternetURL：[a-zA-z]+://[^\s]* 或 ^http://([\w-]+\.)+[\w-]+(/[\w-./?%&=]*)?$
 4 手机号码：^(13[0-9]|14[0-9]|15[0-9]|166|17[0-9]|18[0-9]|19[8|9])\d{8}$
 5 电话号码("XXX-XXXXXXX"、"XXXX-XXXXXXXX"、"XXX-XXXXXXX"、"XXX-XXXXXXXX"、"XXXXXXX"和"XXXXXXXX)：^(\(\d{3,4}-)|\d{3.4}-)?\d{7,8}$ 
 6 国内电话号码(0511-4405222、021-87888822)：\d{3}-\d{8}|\d{4}-\d{7} 
 7 18位身份证号码(数字、字母x结尾)：^((\d{18})|([0-9x]{18})|([0-9X]{18}))$
 8 帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$
 9 密码(以字母开头，长度在6~18之间，只能包含字母、数字和下划线)：^[a-zA-Z]\w{5,17}$
10 强密码(必须包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间)：^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$  
11 日期格式：^\d{4}-\d{1,2}-\d{1,2}
12 一年的12个月(01～09和1～12)：^(0?[1-9]|1[0-2])$
13 一个月的31天(01～09和1～31)：^((0?[1-9])|((1|2)[0-9])|30|31)$ 
14 钱的输入格式：
15    1.有四种钱的表示形式我们可以接受:"10000.00" 和 "10,000.00", 和没有 "分" 的 "10000" 和 "10,000"：^[1-9][0-9]*$ 
16    2.这表示任意一个不以0开头的数字,但是,这也意味着一个字符"0"不通过,所以我们采用下面的形式：^(0|[1-9][0-9]*)$ 
17    3.一个0或者一个不以0开头的数字.我们还可以允许开头有一个负号：^(0|-?[1-9][0-9]*)$ 
18    4.这表示一个0或者一个可能为负的开头不为0的数字.让用户以0开头好了.把负号的也去掉,因为钱总不能是负的吧.下面我们要加的是说明可能的小数部分：^[0-9]+(.[0-9]+)?$ 
19    5.必须说明的是,小数点后面至少应该有1位数,所以"10."是不通过的,但是 "10" 和 "10.2" 是通过的：^[0-9]+(.[0-9]{2})?$ 
20    6.这样我们规定小数点后面必须有两位,如果你认为太苛刻了,可以这样：^[0-9]+(.[0-9]{1,2})?$ 
21    7.这样就允许用户只写一位小数.下面我们该考虑数字中的逗号了,我们可以这样：^[0-9]{1,3}(,[0-9]{3})*(.[0-9]{1,2})?$ 
22    8.1到3个数字,后面跟着任意个 逗号+3个数字,逗号成为可选,而不是必须：^([0-9]+|[0-9]{1,3}(,[0-9]{3})*)(.[0-9]{1,2})?$ 
23    备注：这就是最终结果了,别忘了"+"可以用"*"替代如果你觉得空字符串也可以接受的话(奇怪,为什么?)最后,别忘了在用函数时去掉去掉那个反斜杠,一般的错误都在这里
24 xml文件：^([a-zA-Z]+-?)+[a-zA-Z0-9]+\\.[x|X][m|M][l|L]$
25 中文字符的正则表达式：[\u4e00-\u9fa5]
26 双字节字符：[^\x00-\xff]    (包括汉字在内，可以用来计算字符串的长度(一个双字节字符长度计2，ASCII字符计1))
27 空白行的正则表达式：\n\s*\r    (可以用来删除空白行)
28 HTML标记的正则表达式：<(\S*?)[^>]*>.*?</\1>|<.*? />    (网上流传的版本太糟糕，上面这个也仅仅能部分，对于复杂的嵌套标记依旧无能为力)
29 首尾空白字符的正则表达式：^\s*|\s*$或(^\s*)|(\s*$)    (可以用来删除行首行尾的空白字符(包括空格、制表符、换页符等等)，非常有用的表达式)
30 腾讯QQ号：[1-9][0-9]{4,}    (腾讯QQ号从10000开始)
31 中国邮政编码：[1-9]\d{5}(?!\d)    (中国邮政编码为6位数字)
32 IP地址：\d+\.\d+\.\d+\.\d+    (提取IP地址时有用)
33 IP地址：((?:(?:25[0-5]|2[0-4]\\d|[01]?\\d?\\d)\\.){3}(?:25[0-5]|2[0-4]\\d|[01]?\\d?\\d))
```

放个彩蛋，写一些，常使用的原生校验
1.只能输入数字
```
<input type="text" onkeyup="this.value=this.value.replace(/[^\d]/g,'') " onafterpaste="this.value=this.value.replace(/[^\d]/g,'') " name="f_order" value="1"/> 
```
2.只能输入字母和下划线
```
<input onkeyup="this.value=this.value.replace(/[^_a-zA-Z]/g,'')" onpaste="this.value=this.value.replace(/[^_a-zA-Z]/g,'')"> 
```
3.只能输入数字、字母、下划线
```
<input onkeyup="this.value=this.value.replace(/[^\w]/g,'')" onpaste="this.value=this.value.replace(/[^\w]/g,'')"> 

<input onkeyup="this.value=this.value.replace(/[\W]/g,'')" onpaste="this.value=this.value.replace(/[\W]/g,'')">
```
4.只能输入数字和英文
```
<input onkeyup="value=value.replace(/[\W]/g,'') "onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^\d]/g,''))"> 
```
5.只能输入数字
```
<input onkeyup="value=value.replace(/[^\d]/g,'') "onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^\d]/g,''))"> 
```
6.只能输入全角
```
<input onkeyup="value=value.replace(/[^\uFF00-\uFFFF]/g,'')" onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^\uFF00-\uFFFF]/g,''))"> 
```
7.只能输入汉字
```
<input onkeyup="value=value.replace(/[^\u4E00-\u9FA5]/g,'')" onbeforepaste="clipboardData.setData('text',clipboardData.getData('text').replace(/[^\u4E00-\u9FA5]/g,''))"> 
```
8.只能输入数字和小数点
```
<input type=text name= caolig value ="" onblur="if (!(/^[\d]+\.?\d*$/.test(this.value)) ){alert('您的输入有误'); this.value='';this.focus();}">
```