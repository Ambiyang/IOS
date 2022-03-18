jsvalue is a universal type for swift to use value in js, you can evaluate the variant's type by isNumber. 
invokeMethod,forProperty,setvalue:e.g article.forProperty("title") = context.evaluateScript("seticle.title").  
```
@convention(block) (String) -> Void = { NSLog("\($0)")}  
context.setObject(output,forKeyedSubscript:"output" as NSString)  
context.evaluateScript("output('msg')").  
```
```
context.evaluateScript("""
  function getA(name){
    return name + "san"
  }
""")  
context.evaluateScript("getA('you')").  
```
JSExport:function of swift callable from js.
