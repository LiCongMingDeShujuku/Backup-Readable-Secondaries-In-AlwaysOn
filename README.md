![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 备份AlwaysOn中的可读辅助节点
### Backup Readable Secondaries In AlwaysOn
**发布-日期: 2016年08月18日 (评论)**



## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
**问题：** 对于AlwaysOn ，你可以备份可读的辅助副本吗？

**回答：**
完整的数据库备份？	不可以
事务日志备份？		可以

以下是在SQL AlwaysOn环境中尝试为可读辅助节点运行完整数据库备份时会发生的情况。在此方案中，我们将Sharepoint 2013数据库留在SQL 2014 AlwaysOn环境中。我基本上是在磁盘上运行完整的数据库备份。这里并不难。。


## English
**Question：** For AlwaysOn… Can you backup readable secondary replicas?

**Answer:**
Full Database Backups? 		NO
Transaction Log Backups? 	YES

Here’s what happens whenever you try to run Full Database Backups for readable secondaries in an SQL AlwaysOn environment. In this scenario we have Sharepoint 2013 databases residing in an SQL 2014 AlwaysOn environment. I’m basically running a full database backup to disk. Nothing crazy here.

![#](images/backup-errors-for-readable-secondaries.png?raw=true "#")

我收到这些报错 (I get these errors)

```
This BACKUP or RESTORE command is not supported on a database mirror or secondary replica.
Msg 3013, Level 16, State 1, Line 1
BACKUP DATABASE is terminating abnormally.
Msg 3059, Level 16, State 1, Line 2
This BACKUP or RESTORE command is not supported on a database mirror or secondary replica.
Msg 3013, Level 16, State 1, Line 2
BACKUP DATABASE is terminating abnormally.
Msg 3059, Level 16, State 1, Line 3
This BACKUP or RESTORE command is not supported on a database mirror or secondary replica.
Msg 3013, Level 16, State 1, Line 3
BACKUP DATABASE is terminating abnormally.
Msg 3059, Level 16, State 1, Line 4
This BACKUP or RESTORE command is not supported on a database mirror or secondary replica.
Msg 3013, Level 16, State 1, Line 4
BACKUP DATABASE is terminating abnormally.
Msg 3059, Level 16, State 1, Line 5
This BACKUP or RESTORE command is not supported on a database mirror or secondary replica.
```

Here is what happens when I try to run transaction log backups.  It works. 

![#](images/transaction-log-backups-for-readable-secondaries.png?raw=true "#")

因为你就会发现，
完整数据库备份不可行，事务日志备份工作正常。
如果你需要编写一个进程来检查数据库服务器是否是主服务器，那么你可以使用以下内容：

So there you have it.
Full Database Backups DO NOT WORK, however; Transaction Log Backups work perfectly normal.

## Logic
```SQL
use master;
set nocount on
 
if exists(select is_local, role_desc from sys.dm_hadr_availability_replica_states where role = 1 and role_desc = 'PRIMARY') 
    begin
        print 'This server [' + upper(@@servername) + '] is the primary.'
        backup database [MyDatabase] to disk = 'E:\MyPath\MyDatabase.bak'
    end
```

![#](images/working-backup-logic-for-readable-secondaries.png?raw=true "#")


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

