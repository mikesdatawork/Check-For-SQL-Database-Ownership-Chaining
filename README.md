![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Check For SQL Database Ownership Chaining
**Post Date: November 2, 2017**        

## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process


![SQL Ownership Chaining]( https://mikesdatawork.files.wordpress.com/2017/11/image0011.png "Ownership Chains")
<p>Run this SQL Logic to determine if database ownership chaining is enabled. It checks both the engine configuration, and each database. Remember; some system databases cannot have the configuration set so I have excluded database_id's of certain system databases. This is completely normal.
This should save you some keystrokes.</p>



## SQL-Logic
```SQL
use [master];
set nocount on
 
-- check database chaining engine wide
select
    'sql_instance'  = @@servername
,   'configuration' = upper([name])
,   'setting'   = case when [value_in_use] = '1' then 'ON' else 'OFF' end from sys.configurations where [name] = 'cross db ownership chaining'
 
-- disable ownership chaining for engine
execute sp_configure 'show advanced', 1;  reconfigure;  
execute sp_configure 'cross db ownership chaining', 0;  reconfigure;  
 
-- disable ownership chaining per database
declare @set_db_chaining_off    varchar(max)
set @set_db_chaining_off    = ''
select  @set_db_chaining_off    = @set_db_chaining_off + 
'alter database [' + name + '] set db_chaining off;' + char(10)
from    sys.databases where [database_id] > 3
exec    (@set_db_chaining_off)
 
-- check database chaining per database
select
    'database'  = upper([name])
,   'db_chaining'   = case when [is_db_chaining_on] = '0' then 'Off' else 'On' end
from    sys.databases where [database_id] > 3 order by [is_db_chaining_on] desc

```

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

      
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

