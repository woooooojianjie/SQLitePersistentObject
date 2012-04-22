Introduction
======================

Persistent Objects for Cocoa &amp; Cocoa Touch that use SQLite3. <br>
Forked from http://code.google.com/p/sqlitepersistentobjects/ <br>
http://code.google.com/p/sqlitepersistentobjects/source/browse/trunk/ReadMe.txt


Setup
=====================

1. Add all sources to your Xcode projects. 
2. Link the `libsqlite3.dylib` framework.


Usage
======================

Every subclass of SQLitePersistentObject gets its own table in the database. 
Every property that's not a collection class (NSDictionary, NSArray, NSSet or 
mutable variants) will get persisted into a column in the database. Properties
that are pointers to other objects that are also subclasses of 
SQLitePersistentObject will get stored as a reference to the right 
row in that object's corresponding table. Collection classes gets stored as 
child tables, and are capable of storing either a foreign key reference (when 
the object they hold is a subclass of SQLitePersistentObject) or in a field on 
the child table. 

1.数据类继承自SQLitePersistentObject
    属性名字必须是驼峰命名法，如firstName, 查询时全小写并添加下划线，如first_name.
    数据库字段名字是遇到大写字母即添加一个下划线，然后把大写字母改成小写，如msgXHTMLContent
    在数据库里字段名为 msg_x_h_t_m_l_content .
    
2.调用 -save 保存数据库

3.调用 [[PersonModel class] allObjects]; 获取所有条目。

4.查询：
```objc
NSArray *people = [PersonModel findByCriteria:@"WHERE name = 'John Smith' AND nickname = 'Johnny'"];
```

5.创建索引：实现indices方法即可：<br>
```objc
+(NSArray *) indices {
    NSArray *index1 = [NSArray arrayWithObject:@"name"];
    NSArray *indices = [NSArray arrayWithObject:index1];
    return indices;
} 
```

6.过滤不需要保存到数据库中的属性：<br>
```objc
+(NSArray *)transients {
    return [NSArray arrayWithObject:@"nickname"];
}
```

7.检查是否存在，-existsInDB 方法

8.删除 -deleteObject <br>
```
SPO provides 2 methods for deleting objects: 
`deleteObject` and `deleteObjectCascade`. 
The `deleteObjectCascade` also allows us to specify whether the 
child rows associated with the deleted object should also be deleted. 
```

9.恢复:`revert`, `revertProperty`, and `revertProperties`<br>
```
Often, it is necessary to revert changes that have been made to an object, 
but not saved to the database yet. SPO provides us with 3 methods for 
performing an "undo", of sorts. 
These are: `revert`, `revertProperty`, and `revertProperties`.
The revert method will revert all changes made to an objects since 
the last save to the database. The `revertProperty` and `revertProperties` allow
us to revert changes to one or more properties, respectively, by name.
```

10.clear cache<br>
`+ (void)clearCache;`


Wrannings Fix
======================


1.
SQLitePersistentObject.m:1280
- (void)dealloc{
    // -elf, fix observer leaked
    for (NSString *oneProp in [[self class] propertiesWithEncodedTypes])
    {
        [self removeObserver:self forKeyPath:oneProp];
    }
    // end
  
     [[self class] unregisterObject:self];
     [super dealloc];
}

2.NSMutableArray-MultipleSort.m:60
while ((eachObject = va_arg(argumentList, id)))

3.NSObject-MissingKV.h:16
#endif//;

4.SQLiteInstanceManager.m:73
- (id)autorelease
{
     return self;
}

5.SQLiteInstanceManager.h:57
@property (readwrite,retain,nonatomic) NSString *databaseFilepath;

