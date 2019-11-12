# yuniql ![yuniql-build-status](https://ci.appveyor.com/api/projects/status/e6hqrhqa6d1lnma0?svg=true) [![AppVeyor tests (branch)](https://img.shields.io/appveyor/tests/rdagumampan/yuniql)](https://ci.appveyor.com/project/rdagumampan/yuniql/build/tests) [![Gitter](https://img.shields.io/gitter/room/yuniql/yuniql)](https://gitter.im/yuniql/yuniql) [![Stack Overflow](https://img.shields.io/badge/stack%20overflow-yuniql-green.svg)](http://stackoverflow.com/questions/tagged/yuniql) [![Download latest build](https://ci.appveyor.com/api/projects/status/32r7s2skrgm9ubva?svg=true&passingText=Download%20win-x64)](https://ci.appveyor.com/api/projects/rdagumampan/yuniql/artifacts/yuniql-nightly.zip) [![Download latest build](https://ci.appveyor.com/api/projects/status/32r7s2skrgm9ubva?svg=true&passingText=Download%20linux-x64)](https://ci.appveyor.com/api/projects/rdagumampan/yuniql/artifacts/yuniql-nightly-linux-x64.zip)


>*** Disclaimer: **`yuniql`** is not yet officially released. Much of the claims here are still work in progress but nightly build have major features available.

<img align="right" src="assets/yuniql-logo.png" width="200">

**yuniql** is a schema versioning and database migration tool for sql server. All versions are organized as series of ordinary directories and scripts are stored transparently as `.sql` files. yuniql simply automates what you would normally do by hand and executes all scripts in an orderly and transactional fashion.

**yuniql** is released as self-contained .NET Core 3.0 application (no need for JVM or .NET CLR) for Windows. Docker container images are also [available](https://cloud.docker.com/u/rdagumampan/repository/docker/rdagumampan/yuniql) for seamless continuous delivery of database changes in Linux environments.

## To start using **`yuniql`**

1. Clone sample project
	```bash
	git clone https://github.com/rdagumampan/yuniql c:\temp\yuniql
	cd c:\temp\yuniql\sqlserver-samples\visitph-db
	```

2. Download latest `yuniql` build<br>

	```bash
	powershell Invoke-WebRequest -Uri https://ci.appveyor.com/api/projects/rdagumampan/yuniql/artifacts/yuniql-nightly.zip -OutFile  "c:\temp\yuniql\yuniql-nightly.zip"
	powershell Expand-Archive "c:\temp\yuniql\yuniql-nightly.zip" -DestinationPath "c:\temp\yuniql\sqlserver-samples\visitph-db"
	```

	>`Expand-Archive` requires at least powershell v5.0+ running on your machine. You may also [download manually here](https://ci.appveyor.com/api/projects/rdagumampan/yuniql/artifacts/yuniql-nightly.zip) and extract to desired directory.

3. Set default connection string to target database<br>
	- Using an sql account<br>
	`Server=<server-instance>,[<port-number>];Database=VisitorDB;User Id=<sql-user-name>;Password=<sql-user-password>`	
	- Using trusted connection<br>
	`Server=<server-instance>,[<port-number>];Database=VisitorDB;Trusted_Connection=True;`<br><br>

	```bash
	SETX YUNIQL_CONNECTION_STRING "Server=.\;Database=VisitorDB;Trusted_Connection=True;"
	```

4. Run migration<br>
The following commands `yuniql` to discover the project directory, creates the target database if it doesn't exist and runs all migration steps in the order they are listed. These includes `.sql` files, directories, subdirectories, and csv files. Tokens are also replaced via `-k` parameters.
	```bash
	yuniql run -a -k "VwColumnPrefix1=Vw1,VwColumnPrefix2=Vw2,VwColumnPrefix3=Vw3,VwColumnPrefix4=Vw4"
	yuniql info

	Version         Created                         CreatedBy
	v0.00           2019-11-03T16:29:36.0130000     DESKTOP-ULR8GDO\rdagumampan
	v1.00           2019-11-03T16:29:36.0600000     DESKTOP-ULR8GDO\rdagumampan
	v1.01           2019-11-03T16:29:36.1130000     DESKTOP-ULR8GDO\rdagumampan
	```

5. Verify results<br>
Query tables with SSMS or your preferred SQL client
	```sql
	//SELECT * FROM [dbo].[Visitor]
	VisitorID   FirstName   LastName    Address  Email
	----------- ----------- ----------- ------------------------------------------
	1000        Jack        Poole       Manila   jack.poole@never-exists.com
	1001        Diana       Churchill   Makati   diana.churchill@never-exists.com
	1002        Rebecca     Lyman       Rizal    rebecca.lyman@never-exists.com
	1003        Sam         Macdonald   Batangas sam.macdonald@never-exists.com
	1004        Matt        Paige       Laguna   matt.paige@never-exists.com
	```

	<br>
	<img align="center" src="assets/visitordb-screensot-ssms.png" width="700">

## To start working with **`yuniql`** CLI
See how it works here https://github.com/rdagumampan/yuniql/wiki/How-yuniql-works

```bash
yuniql init
yuniql init -p c:\temp\demo | --path c:\temp\demo
yuniql vnext
yuniql vnext -p c:\temp\demo | --path c:\temp\demo
yuniql vnext -M | --major
yuniql vnext -m | --minor
yuniql vnext -f "your-script-file.sql"
yuniql verify
yuniql run
yuniql run -a true | --auto-create-db true
yuniql run -p c:\temp\demo | --path c:\temp\demo
yuniql run -t v1.05 | --target-version v1.05
yuniql run -c "<connectiong-string>"
yuniql run -k "Token1=TokenValue1,Token2=TokenValue2,Token3=TokenValue3"
yuniql -v | --version
yuniql -h | --help
yuniql -d | --debug
```

## To dig deeper for advanced use cases

* [How yuniql works](https://github.com/rdagumampan/yuniql/wiki/How-yuniql-works)
* [How to setup new database](https://github.com/rdagumampan/yuniql/wiki/How-to-baseline-your-database)
* [How to replace tokens in script files](https://github.com/rdagumampan/yuniql/wiki/How-to-apply-token-replacement)
* [How to bulk import data](https://github.com/rdagumampan/yuniql/wiki/How-to-bulk-import-data-during-migration)
* [How to migrate from docker container](https://github.com/rdagumampan/yuniql/wiki/How-to-run-migration-from-a-docker-container)
* [Known issues](https://github.com/rdagumampan/yuniql/wiki/Known-issues)
* [Best practices](https://github.com/rdagumampan/yuniql/wiki/Best-practices)

## Supported databases

* SQL Server
* Azure SQL Database
* PostgreSQL ***

*** planned or being evaluated/developer/tested

## To contribute or ask for help

You may submit ideas for improvement or report a bug by [creating an issue](https://github.com/rdagumampan/yuniql/issues/new). <br>
If you have questions, talk to us on [gitter chat](https://gitter.im/yuniql/community). Alternatively, tag [#yuniql](https://twitter.com/) on Twitter.

## To track docker build status
For running migration from docker container, [see instructions here](https://github.com/rdagumampan/yuniql/wiki/How-to-run-migration-from-a-docker-container)

|Platform|Build Status|Docker Images|
|---|---|---|
|![win-x64](assets/win_med.png) **linux-x64**|![yuniql-build-status](https://ci.appveyor.com/api/projects/status/cje10k5mqx4v4emd?svg=true)|`docker pull rdagumampan/yuniql:linux-x64-latest`|
|![linux-x64](assets/linux_med.png) **windows-x64**|![yuniql-build-status](https://ci.appveyor.com/api/projects/status/jn7f7r64rx6a2ujk?svg=true)|`docker pull rdagumampan/yuniql:win-x64-latest`|

## License

Copyright (C) 2019 Rodel E. Dagumampan

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

## Maintainers

[//]: contributor-faces

<a href="https://github.com/rdagumampan"><img src="https://avatars.githubusercontent.com/u/5895952?v=3" title="rdagumampan" width="80" height="80"></a>

[//]: contributor-faces
