```
//
//  ViewController.swift
//  jscore
//
//  Created by yang yatuo on 2022/03/18.
//

import UIKit
import JavaScriptCore

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        let context = JSContext()!

        //1.warming up
        context.evaluateScript("var v1 = 10")
        context.evaluateScript("var v2 = v1 + 10")

        let answer = context.evaluateScript("v1 + v2")
        print("Answer =",answer)
        
        //2.swift use js variant
        let n = context.objectForKeyedSubscript("v1")!.toInt32()
        print(n)
        
        //3.4 swift use js object
        context.evaluateScript("""
            class Article{
                constructor(title,body){
                    this._title = title;
                    this._body = body;
                }
                
                get title() {
                    return this._title
                }

                get body() {
                    return this._body
                }

                getOutline(length) {
                    return this.title + ":" + this.body.substr(0,length)
                }
            }
        """)
        
        context.evaluateScript("var article = new Article('Title','Description')")
        let article = context.objectForKeyedSubscript("article")!
        
        print(article.forProperty("title")!)
        print(article.invokeMethod("getOutline", withArguments: [4])!)
        print(context.evaluateScript("article.getOutline(4)")!)
        
        
        context.evaluateScript("""
            var a = 1;
            function onPlayerReady() {
                return "567"
            }
        """)
        print(context.evaluateScript("onPlayerReady()"))
        
        //4.API
        let a = context.evaluateScript("Object.getOwnPropertyNames(this)").toArray() as! [String]
//        print("4.\(a)")
        
        //4.4 js use swift variable
        let array = ["Apple","Orange","Grape"]
        let dict = ["Apple":5,"Orange":2,"Grape":3]
        
        context.setObject(array, forKeyedSubscript: "array1" as NSString)
        context.setObject(dict, forKeyedSubscript: "dict1" as NSString)
        
        let value1 = context.evaluateScript("array1[1]")!
        let value2 = context.evaluateScript("dict1['Grape']")!
        print("4.\(value1),\(value2)")
        
        //4.6 js use swift closure or function
        let account = "@kumagai"
        func printIdx(idx:String) {
            print("\(idx)")
        }
        let output : @convention(block) (String) -> Void = {
            print("\(account):\($0)")
            printIdx(idx: "func:\(account):\($0)")
        }
        context.setObject(output, forKeyedSubscript: "output" as NSString)
        context.evaluateScript("output('msg')")

        
        // js share swift class instance with swift
        let image = Color(r: 111, g: 111, b: 111)
        context.setObject(image, forKeyedSubscript: "icon" as NSString)
        context.evaluateScript("icon.r = 222")
        print("sharing eg:\(image.r)")
        
        // js use swift class
        context.setObject(Color.self, forKeyedSubscript: "Color" as NSString)
        context.evaluateScript("var color = Color.makeWithRGB(200,150,100)")
        print(context.evaluateScript("color.r")!)
        // or
        let color = context.objectForKeyedSubscript("color").toObject() as! Color
        print("color eg:\(color.r)")
        // oe jsValue
        let color1 = context.objectForKeyedSubscript("color")!
        print("color1 eg:\(color1.forProperty("r")!)")
        
        //5.11 jsvalue array
        context.evaluateScript("var article = [new Article('Title','Description')]")
        let array1 = context.objectForKeyedSubscript("article")!
        let article1 = array1.objectAtIndexedSubscript(0)!
        print("jsvalue array:\(article1.invokeMethod("getOutline", withArguments: [4])!)")
        
        
        //7.2 exception
        context.exceptionHandler = {
            context,error in
            
            let msg = error!.description
            let line = error!.objectForKeyedSubscript("line")!
            let stack = error!.objectForKeyedSubscript("stack")!
            print("ERROR:",msg,line,stack)
        }
        
        context.evaluateScript("throw new TypeError('error')")
        
        
        
        
    }
    
    
}

@objc protocol ColorInterface: JSExport{
    var r:Double { get set }
    var g:Double { get set }
    var b:Double { get set }
    
    static func make(r:Double,g:Double,b:Double) ->Self
}

class Color:NSObject,ColorInterface{
    var r: Double
    var g: Double
    var b: Double
    
    required init(r:Double,g:Double,b:Double){
        self.r = r
        self.g = g
        self.b = b
    }
    
    class func make(r:Double,g:Double,b:Double) ->Self{
        return Self(r:r,g:g,b:b)
    }
}

// for easy read js
extension JSContext {
    @discardableResult
    func evaluateScript(src name:String, withExtension ext:String) throws -> JSValue!{
        let path = Bundle.main.url(forResource: name, withExtension: ext)!
        let source = try String(contentsOf: path)
        
        return evaluateScript(source)
    }
}

// for CGSize.CGRect,CGPoint,NSRange auto wrapper
extension JSContext {
    @nonobjc
    func setObject(_ size:CGSize, forKeyedSubscript key:(NSCopying & NSObjectProtocol)!){
        setObject(JSValue(size: size, in: self)!, forKeyedSubscript: key)
    }
    @nonobjc
    func setObject(_ rect:CGRect, forKeyedSubscript key:(NSCopying & NSObjectProtocol)!){
        setObject(JSValue(rect: rect, in: self)!, forKeyedSubscript: key)
    }
    @nonobjc
    func setObject(_ point:CGPoint, forKeyedSubscript key:(NSCopying & NSObjectProtocol)!){
        setObject(JSValue(point: point, in: self)!, forKeyedSubscript: key)
    }
    @nonobjc
    func setObject(_ range:NSRange, forKeyedSubscript key:(NSCopying & NSObjectProtocol)!){
        setObject(JSValue(range: range, in: self)!, forKeyedSubscript: key)
    }
}

// subscript
extension JSContext {
    @nonobjc
    subscript(key:String) -> Any {
        get {
            objectForKeyedSubscript(key as NSString)!.toObject()!
        }
        set(object) {
            setObject(object, forKeyedSubscript: key as NSString)
        }
    }
    
    @nonobjc
    subscript(size key:String) -> CGSize {
        get {
            objectForKeyedSubscript(key as NSString)!.toSize()
        }
        set(size) {
            setObject(JSValue(size: size, in: self), forKeyedSubscript: key as NSString)
        }
    }
}

// external js return failure
extension JSContext {
    func invoke(_ script: String) -> JSValue!{
        evaluateScript("(function(){\(script)})();")
    }
}

```
