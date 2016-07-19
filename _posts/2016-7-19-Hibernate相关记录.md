**hibernate相关记录**
1， list是一次查所有记录， iterator需要查2次，1次查出ID，返回代理对象， 当对象get属性时，会搜索二级缓存，没有会再查一次数据库。

如果iterator查出的obj get前调用了session.evict(obj)后， 会报：
LazyInitializationException: could not initialize proxy - no Session

2. get和load： load先从一级缓存找对应的对象，构建并返回一个代理对象，对象get属性时，才会找二级缓存或数据库加载， 数据库找不到抛异常；  get也是先从一级缓存找，找不到，马上找二级缓存或数据库加载，没有返回null。

