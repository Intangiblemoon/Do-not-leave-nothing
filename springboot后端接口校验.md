# springboot后端接口校验

#前言
前端收集页面信息发送请求给后端接口以调用资源，当前端界面校验不完全或通 过抓包工具获得请求信息伪装信息包发送一些不符合规则的数据进入后台时，为了效率和数据安全应当对其进行拦截，而不是放进后台进行处理。
Springboot中**'spring-boot-starter-web'**中包含**'hibernate-validator'**可以使用该依赖对不符合规范的数据进行拦截。
#内容
##依赖
>&lt;dependency&gt;
      &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
      &lt;artifactId&gt;spring-boot-starter-web&lt;/artifactId&gt;
&lt;/dependency&gt;

##所用注解
springboot舒服的一点在于快速启动以及注解的使用。
接口校验使用到的注解有：
- @valid
- @NotBlank(message = "")
- @Length(max = 10, message = "")
- @Values(value = {"0", "1"}, message = "")

##用法
###@valid
添加在接收数据的参数之前，用来表明该参数需进行校验。传入的数据会根据该参数对应的类中标注的注解信息进行校验
eg:
> public MyResult<MyRes> create(@Valid @RequestBody MyRes myRes)

###@Length
此注解用来校验长度，如不符合长度要求将不允许通过。
eg:

> @Length(max = 10, message = "长度不可超过10位")

###@Values
如果对属性的值有要求可使用此注解，将限制属性值。
eg:
> @Values(value = {"0", "1"}, message = "属性值只能为0或者1")

###@NotBlank
限制字符串类型，必须不为空、trim()后不为空（除空格外必须有字符）
eg:
> @NotBlank(message = "不能为空")

注意：
**此注解只能标记在字符串类型上**
如果标记在别的类型属性上，如Date，会报错。
>"HV000030: No validator could be found for constraint 'javax.validation.constraints.NotBlank' validating type 'java.util.Date'. Check configuration for 'expireDate'"

对其他属性可使用@NotNull注解进行标记

##拦截异常进行封装
在不进行处理的形况下，如果发现参数异常将抛出**'MethodArgumentNotValidException'**，为了保证返回的信息结构一致建议封装一层自定义异常。
可以采取在全局异常捕获时捕获该异常，对该异常进行处理。
eg:
>    
    @ExceptionHandler(value = MethodArgumentNotValidException.class)
    @ResponseBody
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public MyResult txnArgumentExceptionHandler(Exception e) {
        if (e instanceof MethodArgumentNotValidException){
            return methodArgumentNotValidExceptionHandler((MethodArgumentNotValidException) e);
        }else{
            return new MyResult<>().getFail(99999, "参数检查错误");
        }
    }
    private MyResult methodArgumentNotValidExceptionHandler(MethodArgumentNotValidException ex){
        MyResult result = new MyResult();
        // 参数校验异常
        List<ObjectError> errors = ex.getBindingResult().getAllErrors();
        if(!errors.isEmpty()) {
            // 获取所有出错字段及错误信息，FieldName -> ErrorMessage (字段名称 -> 对应的错误信息)
            ArrayList<Map<String, String>> errorMsgList = new ArrayList<>();
            errors.forEach(error->{
                if(error instanceof FieldError) {
                    FieldError fieldError = (FieldError) error;

                    Map<String, String> fieldErrorMap = new HashMap<>(1);
                    fieldErrorMap.put(fieldError.getField(), fieldError.getDefaultMessage());
                    errorMsgList.add(fieldErrorMap);

                    logger.warn("参数校验失败, field={}, errorMsg={}, fieldValue={}",
                            fieldError.getField(), fieldError.getDefaultMessage(), fieldError.getRejectedValue());
                }
            });

            // 将所有校验失败的字段及信息返回
            result.setData(errorMsgList);
            // 将第一个校验失败的字段的错误信息放到result的message中
            if (!errorMsgList.isEmpty()) {
                String firstErrorFieldMsg = "";
                Map<String, String> firstErrorField = errorMsgList.get(0);
                for (Map.Entry<String, String> field : firstErrorField.entrySet()) {
                    firstErrorFieldMsg = field.getValue();
                    break;
                }
                result.getFail( 0, firstErrorFieldMsg);
            }
        }
        return result;
    }

#结语
后端校验非常重要，但是写起来也是无聊的很。不过想想到如果有人抓包搞破坏会对工程造成多大的破坏。该写的还是要写上