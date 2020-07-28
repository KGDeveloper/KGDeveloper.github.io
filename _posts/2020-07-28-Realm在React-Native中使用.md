---
layout: post
title: Realm在React-Native中的使用
subtitle: Realm在React-Native中的使用，数据库升迁，增删改查
date: 2020-07-21
author: KG丿夏沫
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
  - Realm
  - React-Native
  - 数据库增删改查
  - 数据库升迁
---

# Realm 在 React-Native 中的使用

## 一、介绍

> 公司项目使用 React-Native 开发的，这版本新需求需要保存数据到本地，方便下次打开直接使用上次数据，在上次数据的基础上进行操作，因为数据量比较大，不能直接存储在本地，所以使用<a href="https://realm.io/cn/">Realm</a>，这是一个很优秀的数据库，而且在`Java`、`Kotlin`、`Swift`、`Objective-C`、`Javascript`等各个平台均支持。如此优秀，那么我们优秀的 APP 也肯定使用它了。官方文档写的也很详细，所以做了一下记录，以备后续。

## 二、安装

> 我是在`React-Native`项目中使用，所以我们就用`npm`来安装了。

> 1、切换到当前项目的主目录，然后打开`vim`然后执行以下安装命令：

```
npm install --save realm
```

> 2、需要使用`link`，但是由于`React-Native`版本比较高，直接使用`react-native link`无法`link`成功，所以需要命令更加详细一些。

```
react-native link realm
```

> 3、`iOS`需要 cd 到当前项目主目录下的 iOS 文件夹，然后执行以下命令：

```
pod install
```

> 4、iOS 的就比较简单，下面是 Android 的，可能有的时候 link 不成功，那就需要我们手动 link，打开项目下 Android 文件夹下的 APP 文件夹下的 build.gradle 然后执行以下操作：

```
implementation project(':realm')
```

> 然后打开`src/main/java/...../MainApplication.java`，添加以下代码：

```
new RealmReactPackage()
```

> 如果系统提示导入 Realm 库，直接点击导入就行，如果不提示，手动导入，`import io.realm.react.RealmReactPackage;`。然后 build 以下就 OK 了。

## 三、使用

> 数据库我们在开发中无非就是增、删、改、查，还有就是版本升级。下面就按照这写要点进行介绍。

> 1、数据库创建，创建数据库的时候，我们只需要按照最新的数据库版本创建数据库表，而且程序每次进入创建数据库的方法的时候必须判断本地是否已经存在数据库。以下是示例代码。

```
const Realm = require('realm');

const ExamListSchema = {
    name: 'ExamList',
    properties: {
        workId: 'int',//作业id
        listArr: 'string',//待批阅试题序号数组，{examId:5___0___203308412}  试卷类型 试卷id 试题id
        userId: 'int',//用户id
        currIndex: 'int',//上次批阅到第几题
        addTime: 'int',//保存上次批阅结果时间
    }
};

const ExamDetailSchema = {
    name: 'ExamDetailInfo',
    properties: {
        typeId: 'int',//试卷id
        type: 'int',//试卷类型
        examId: 'int',//试题id
        examDetail: 'string',//试题详情
        userId: 'int',//用户id
        currIndex: 'int',//上次批阅到第几个学生
        addTime: 'int',//批阅结果保存时间
    }
}

var schemas = [
    {
        schemaVersion: 0, schema: [ExamDetailSchema, ExamListSchema]
    }
];

function CreateDB(succ, fail) {

    console.log(Realm.defaultPath);

    /**先获取本地版本，如果版本号为-1就是还没有创建数据库，设置为0，从版本控制列表中获取数据库配置参数 ，创建最新版本数据库*/
    var nextSchemaIndex = Realm.schemaVersion(Realm.defaultPath);
    if(nextSchemaIndex == -1){
        nextSchemaIndex = schemas.length - 1;
    }

    var migrationRealm = new Realm(schemas[nextSchemaIndex++]);
    //如果版本号小于版本控制器列表长度，遍历逐级升级版本,防止
    while (nextSchemaIndex < schemas.length) {
        var migrationRealm = new Realm(schemas[nextSchemaIndex++]);
        migrationRealm.close();
    }

    //等待数据库升级完成后，使用最新版本
    let realm = new Realm(schemas[schemas.length - 1]);
    if (realm) {
        succ(realm);
    } else {
        fail('数据库创建失败');
    }
}
```

> 里面也有注释，我想很方便阅读代码了。

> 2、数据库添加数据，由于我是直接封装了一下，所以贴上代码以及操作步骤。

```
function Open(schema, succ, fail) {
    Realm.open(schema)
        .then(realm => {
            succ(realm);
        })
        .catch(error => {
            fail(error);
        })
}

export { CreateDB, Open, ExamDetailSchema, ExamListSchema };
```

> 上面是封装的打开数据库的方法，以下为新增数据

```
Open([ExamListSchema, ExamDetailSchema], succ => {
    succ.write(() => {
        succ.create(('ExamList'), {
            workId: workId,
            listArr: JSON.stringify(this.sortView.state.sortArr),
            userId: userId,
            currIndex: index,
            addTime: Date.parse(new Date()),
        });
        var arr = examId.split('___');
        succ.create(('ExamDetailInfo'), {
            examId: parseInt(arr[2]),
            typeId: parseInt(arr[1]),
            type: parseInt(arr[0]),
            examDetail: JSON.stringify(this.state.data),
            userId: userId,
            currIndex: this.state.currIndex,
            addTime: Date.parse(new Date()),
        });
    })
    succ.close();
}, fail => {
    console.log(fail)
})
```

>3、删除数据

```
Open([ExamListSchema, ExamDetailSchema], succ => {
    succ.write(() => {
        let examList = succ.objects('ExamList');
        let examArr = examList.filtered('workId = ' + workId);
        if (examArr) {
            succ.delete(examArr);
        }
        let examDetail = succ.objects('ExamDetailInfo');
        var arr = examId.split('___');
        let examInfo = examDetail.filtered(`examId = ${parseInt(arr[2])} AND typeId = ${parseInt(arr[1])} AND type = ${parseInt(arr[0])}`);
        if (examInfo) {
            succ.delete(examInfo);
        }
    })
    succ.close();
}, fail => {
    console.log(fail)
})
```

>4、修改数据

```
Open([ExamListSchema, ExamDetailSchema], succ => {
    succ.write(() => {
        let examList = succ.objects('ExamList');
        let examArr = examList.filtered('workId = ' + workId);
        let examDetail = succ.objects('ExamDetailInfo');
        var arr = examId.split('___');
        let examInfo = examDetail.filtered(`examId = ${parseInt(arr[2])} AND typeId = ${parseInt(arr[1])} AND type = ${parseInt(arr[0])}`);
        if (examInfo) {
            succ.delete(examInfo);
        }
        if(index == sortArr.length -1){
            if (examArr) {
                succ.delete(examArr);
            }
        }else{
            if(examArr.length > 0){
                succ.create('ExamList',{workId:workId,currIndex:index},true);
            }
        }
    })
    succ.close();
}, fail => {
    console.log(fail)
})
```

>5、查询数据

```
Open([ExamDetailSchema, ExamListSchema], succ => {
            
    //先查询是否有待批阅试题列表
    let examListDB = succ.objects('ExamList');
    //根据workId过滤是否有当前作业待批阅存档
    let dbExamArr = examListDB.filtered('workId = ' + workId);
    //如果本地存在存档
    if (dbExamArr.length > 0) {
        //取最新一份存档信息
        let examObject = dbExamArr[dbExamArr.length - 1];
        //获取到当前待批阅试题列表json串，以及当前批阅到第几题
        const { listArr, currIndex } = examObject;
        //根据数据库查询出来的结果，修改顶部试题序号展示
        this.sortView.setState({
            sortArr: JSON.parse(listArr),
            currIndex: currIndex
        })
        //只有当待批阅列表存在时，才根据examId去查询当前试题详情，所以放在内部
        let arr = JSON.parse(listArr);
        //根据当期待批阅试题列表以及批阅中下标，去除相应examId
        const {examId} = arr[currIndex];
        //因为examId是个拼接字符串，所以数据库中不支持存储，只能裁剪分隔
        let tmpArr = examId.split('___');
        //查询试题详情列表
        let examInfoDB = succ.objects('ExamDetailInfo');
        //根据examid分隔字段查询当前试题详情是否存在
        let examInfo = examInfoDB.filtered(`examId = ${parseInt(tmpArr[2])} AND typeId = ${parseInt(tmpArr[1])} AND type = ${parseInt(tmpArr[0])}`);
        //如果存在，获取最新一份存档
        if(examInfo.length > 0){
            let examDetailObject = examInfo[examInfo.length-1];
            //获取存档对象中试题详情json串以及当前已批阅学生人数
            const {examDetail,currIndex} = examDetailObject;
            var data = JSON.parse(examDetail);
            //判断是否是复合题
            if (data.hasNode == 0) {
                count = data.stuList.length;
            } else {
                const { stuList } = data.examTree[0];
                count = stuList.length;
            }
            //更新页面
            this.setState({
                data: data,
                showAnalysis: false,
                stuCnt: count,
                currIndex: currIndex,
                remarkCut: currIndex,
                showSubBtu: false
            })
        }else{
            //如果不存在试题详情存档，根据examId请求服务器获取
            this.requestNext(examId);
        }
    } else {
        //如果数据库不存在试题待批阅列表，从服务器请求获取
        this.requestExamInfo();
    }
}, fail => {

})
```

>6、数据库升级

```
schemaVersion: 2, schema: [ExamDetailSchema, ExamListSchema], migration: function (oldRealm, newRealm) {
    if (oldRealm.schemaVersion < 2) {
        var oldObjects = oldRealm.objects('ExamList');
        var newObjects = newRealm.objects('ExamList');
        for (let i = 0; i < oldObjects.length; i++) {
            newObjects[i].workId = oldObjects[i].workId;
            newObjects[i].listArr = oldObjects[i].listArr;
            newObjects[i].userId = 0;
        }
    }
},
schemaVersion: 3, schema: [ExamDetailSchema, ExamListSchema], migration: function (oldRealm, newRealm) {
    if (oldRealm.schemaVersion < 3) {
        var oldObjects1 = oldRealm.objects('ExamList');
        var newObjects1 = newRealm.objects('ExamList');
        for (let i = 0; i < oldObjects1.length; i++) {
            newObjects1[i].workId = oldObjects1[i].workId;
            newObjects1[i].listArr = oldObjects1[i].listArr;
        }

        var oldObjects = oldRealm.objects('ExamDetailInfo');
        var newObjects = newRealm.objects('ExamDetailInfo');
        for (let i = 0; i < oldObjects.length; i++) {
            newObjects[i].typeId = oldObjects[i].typeId;
            newObjects[i].type = oldObjects[i].type;
            newObjects[i].examId = oldObjects[i].examId;
            newObjects[i].examDetail = oldObjects[i].examDetail;
        }
    }
},
schemaVersion: 4, schema: [ExamDetailSchema, ExamListSchema], migration: function (oldRealm, newRealm) {
    if (oldRealm.schemaVersion < 4) {
        var oldObjects1 = oldRealm.objects('ExamList');
        var newObjects1 = newRealm.objects('ExamList');
        for (let i = 0; i < oldObjects1.length; i++) {
            newObjects1[i].workId = oldObjects1[i].workId;
            newObjects1[i].listArr = oldObjects1[i].listArr;
            newObjects1[i].userId = 0;
        }

        var oldObjects = oldRealm.objects('ExamDetailInfo');
        var newObjects = newRealm.objects('ExamDetailInfo');
        for (let i = 0; i < oldObjects.length; i++) {
            newObjects[i].typeId = oldObjects[i].typeId;
            newObjects[i].type = oldObjects[i].type;
            newObjects[i].examId = oldObjects[i].examId;
            newObjects[i].examDetail = oldObjects[i].examDetail;
            newObjects[i].userId = 0;
        }
    }
},
schemaVersion: 5, schema: [ExamDetailSchema, ExamListSchema], migration: function (oldRealm, newRealm) {
    if (oldRealm.schemaVersion < 4) {
        var oldObjects1 = oldRealm.objects('ExamList');
        var newObjects1 = newRealm.objects('ExamList');
        for (let i = 0; i < oldObjects1.length; i++) {
            newObjects1[i].workId = oldObjects1[i].workId;
            newObjects1[i].listArr = oldObjects1[i].listArr;
            newObjects1[i].userId = 0;
            newObjects1[i].currIndex = 0;
            newObjects1[i].addTime = Date.parse(new Date());
        }

        var oldObjects = oldRealm.objects('ExamDetailInfo');
        var newObjects = newRealm.objects('ExamDetailInfo');
        for (let i = 0; i < oldObjects.length; i++) {
            newObjects[i].typeId = oldObjects[i].typeId;
            newObjects[i].type = oldObjects[i].type;
            newObjects[i].examId = oldObjects[i].examId;
            newObjects[i].examDetail = oldObjects[i].examDetail;
            newObjects[i].userId = 0;
            newObjects[i].currIndex = 0;
            newObjects[i].addTime = Date.parse(new Date());
        }
    }
}
```

>就是这么简单吧，防止忘记