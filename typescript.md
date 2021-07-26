## 装饰器

@expression形式，表达式求值后必须为函数，运行时被调用.target参数就是被装饰的声明信息

例子：

```typescript
function sealed(target){}
@sealed x
```

方法装饰器

```typescript
定义：
function enumeable(value: boolean){
  return function(target, property, description){
    
  }
}

使用：
@enumerable（false）
function greet(){
  return "hello"
}
```

