# GORM Sqlite Mock Driver

## DEMO
https://github.com/Lichmaker/gorm-sqlite-sqlmock-demo

## WHY
自从升级到 `gorm v2` 之后， `sqlite` 使用 `gorm` 时如下：
```

    // ......

    import (
        "gorm.io/driver/sqlite"
        "gorm.io/gorm"
    )

    //......

    // 初始化数据库链接
	dbSqlite, err := gorm.Open(sqlite.Open("/tmp/mydbfile.db"), &gorm.Config{
		SkipDefaultTransaction: true,
	})
	if err != nil {
		panic("failed to connect database")
	}
```

当使用 `sqlmock` 时，需要把mock出来的 `*sql.DB` 注入到 `gorm` 中。但即使使用 `gorm.Config` 中的 `ConnPool` 也是无法起效的。 一个失败的例子：

```
    // A wrong example

	db, mock, err = sqlmock.New()
	if err != nil {
		panic(err)
	}

	dbSqlite, err := gorm.Open(sqlite.Open("/tmp/mydbfile.db"), &gorm.Config{
        ConnPool: db,
		SkipDefaultTransaction: true,
	})
	if err != nil {
		panic("failed to connect database")
	}
```

因此，通过改写 `gorm-sqlite-driver` ,强行把 `*sql.DB` 注入进去。

## USE
```

    // ......
    
    import (
        "github.com/DATA-DOG/go-sqlmock"
        sqlitemock "github.com/lichmaker/sqlite-mock"
        "gorm.io/gorm"
    )

    // ......

	db, mock, err = sqlmock.New()
	if err != nil {
		panic(err)
	}

	myMockDriver := sqlitemock.Open(db)
	dbSqlite, err := gorm.Open(myMockDriver, &gorm.Config{
		SkipDefaultTransaction: true,
	})
	if err != nil {
		panic("failed to connect database")
	}
```
