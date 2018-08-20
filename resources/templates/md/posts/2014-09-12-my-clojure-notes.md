{:title "my clojure notes"
 :layout :post
 :tags  ["Clojure"]}

- let 解构: 顺序集合，map.  
同时作用于: fn, defn, loop, for etc.   
&: rest.   
map: :keys, :strs, :syms
- Clojure有两个假值：nil, false，任何非nil或非false的值都判断为true．
- recur: not autoboxing 
- reduce: 归约
- partial, comp：无参风格，不用显式定义它的参数
- memoize: 缓存相同的参数
- 主要抽象：Collection/Sequence/Associative/Indexed/Stack/Set/Sorted 
- seq：任何nil 或者任何空集合都返回nil.
- rest：(), next: nil
- nth：会抛出异常, get: nil, 
- 习惯用法：关键字作为函数
- list/vector : hash-set/hash-map
- transient: vector/map/set
- delay: 只执行一次
- future: 在另外一个线程执行
- pmap: 并行，用future实现
- swap! 会一直重试，直到设值成功
- atom不能存在ref里面
- meta: ^: or ^{} 
- 前置声明: declare 
- agent: 串行执行，在一个STM事务里只会执行一次
- send: 固定大小的线程池
- send-off: 不限制大小的线程池，跟future利用的是同一个线程池，适宜阻塞的(I/O)，非CPU密集型的action 
- 只有在函数满足不了需求的时候才去使用宏
- syntax quote: 语法引述: "`"
- unquote: 反引过: "~"
- splicing-unquote: 编接反引述: "~@"
- 协议提供的是基于单个参数类型的函数分派
- extend / extend-type / extend-protocol 
- deftype/defrecord: 
- 工厂函数: ->MyType, map->MyType, MyType/create 
- require: 从classpath载入命名空间, use: require + refer 
- 如果代码并不包括互操作调用，就没必要包括类型提示, 函数参数或返回值的类型提示都不是类型签名声明
- gen-class: 必须AOT
- long -> BigInt -> Rationals -> BigDecimal -> double 
