# sp_GenerateTableDDLScript

This stored procedure can be used to generate a CREATE TABLE script in T-SQL for a given table in Microsoft SQL Server.

[Releases Download Page](https://github.com/EitanBlumin/sp_GenerateTableDDLScript/releases)

## Download & Installation

You may install this procedure in your SQL Server instance by downloading (or copy-and-pasting) the script [sp_GenerateTableDDLScript.sql](https://raw.githubusercontent.com/EitanBlumin/sp_GenerateTableDDLScript/master/sp_GenerateTableDDLScript.sql) and running it in the master database, it will also use the following command to turn it into a system stored procedure, thus making it usable anywhere in the instance:

`EXECUTE sp_MS_marksystemobject 'sp_GenerateTableDDLScript'`

## Syntax

```
EXEC sp_GenerateTableDDLScript
	    [ @TableName = ] 'TableName'
	[ , [ @NewTableName = ] 'NewTableName ]
	[ , [ @Result = ] @Result OUTPUT ]
	[ , [ @IncludeDefaults = ] 1 | 0 ]
	[ , [ @IncludeCheckConstraints = ] 1 | 0 ]
	[ , [ @IncludeForeignKeys = ] 1 | 0 ]
	[ , [ @IncludeIndexes = ] 1 | 0 ]
	[ , [ @IncludePrimaryKey = ] 1 | 0 ]
	[ , [ @IncludeIdentity = ] 1 | 0 ]
	[ , [ @IncludeUniqueIndexes = ] 1 | 0 ]
	[ , [ @IncludeComputedColumns = ] 1 | 0 ]
	[ , [ @UseSystemDataTypes = ] 1 | 0 ]
	[ , [ @ConstraintsNameAppend = ] 'ConstraintsNameAppend' ]
	[ , [ @Verbose = ] 1 | 0 ]
```

## Arguments

`[ @TableName = ] 'TableName'`

 Is the name of the source table. This parameter is mandatory and is of type **SYSNAME**. If the table's schema is not default (dbo), then please specify the schema name as well, as part of the parameter.
 
`[ @NewTableName = ] 'NewTableName'`

 Is the name of the new (target) table. It is of type **SYSNAME** with a default of NULL. You may also include database and schema as part of the name. If not specified, same name as source table will be used.
 
`[ @Result = ] @Result OUTPUT`

 Is an output textual parameter of type **NVARCHAR(MAX)** that will contain the result TSQL command for creating the table.
 
`[ @IncludeDefaults = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 1. Sets whether to include default constraints.
 
`[ @IncludeCheckConstraints = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 1. Sets whether to include check constraints.
 
`[ @IncludeForeignKeys = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 1. Sets whether to include foreign key constraints.
 
`[ @IncludeIndexes = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 1. Sets whether to include indexes.
 
`[ @IncludePrimaryKey = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 1. Sets whether to include primary key constraints.
 
`[ @IncludeIdentity = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 1. Sets whether to include identity property.
 
`[ @IncludeUniqueIndexes = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 1. Sets whether to include unique index constraints.
 
`[ @IncludeComputedColumns = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 1. Sets whether to include computed columns (if not, they will also be automatically ignored by constraints and indexes).
 
`[ @UseSystemDataTypes = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 0. Sets whether to use system data type names instead of user data type names.
 
`[ @ConstraintsNameAppend = ] 'ConstraintsNameAppend'`

 Is a parameter of type **SYSNAME** with a default of '' (empty string). This is an optional text string to append to constraint names, in order to avoid the duplicate object name exception. This is useful when creating the new table within the same database.
 
`[ @Verbose = ] 1 | 0`

 Is a parameter of type **BIT** with a default of 0 (false). This is an optional parameter which, when set to 1 (true), will cause the procedure to print informative messages, and also output a result set containing the discovered table field metadata.

## Examples

**A. Creating a table in an archive database, without foreign keys and identity property:**

```
DECLARE @CMD NVARCHAR(MAX)
EXEC sp_GenerateTableDDLScript 'Sales.OrderDetails', 'ArchiveDB.Sales.OrderDetails', @CMD OUTPUT, @IncludeForeignKeys = 0, @IncludeIdentity = 0
SELECT @CMD
```

**B. Duplicating a table within the same database:**

```
DECLARE @CMD NVARCHAR(MAX)
EXEC sp_GenerateTableDDLScript 'Sales.OrderDetails', 'Sales.OrderDetails_New', @CMD OUTPUT, @ConstraintsNameAppend = '_New'
SELECT @CMD
```

**C. Duplicating a table as a temporary table, without computed columns:**

```
DECLARE @CMD NVARCHAR(MAX)
EXEC sp_GenerateTableDDLScript 'Sales.OrderDetails', '#temp_OrderDetails', @CMD OUTPUT, @ConstraintsNameAppend = '_Temp', @IncludeComputedColumns = 0
SELECT @CMD
```

## Remarks

- The **source table must exist**, otherwise an exception will be raised.
- The script **does not check whether the target table already exists**,
  it falls on you to make sure that it doesn't before running the result script.
- The script does not check whether constraint names already exist,
  it falls on you to use the `@ConstraintsNameAppend` parameter to generate unique names.
- The script (at the moment) does NOT support the following ([more info at the issues page](https://github.com/EitanBlumin/sp_GenerateTableDDLScript/issues)):
	- Column Sets
	- Collations different from Database Default
	- Filestream columns
	- Sparse columns
	- Not for replication property
	- XML document collections
	- Rule objects
	- Non-default Filegroups
	- In-Memory tables

## Supported Versions

At this time, this script was tested on the following Microsoft SQL Server versions:

- 2014
- 2016
- 2017

If you wish to contribute by testing on additional versions, you're more than welcome to do so, and submit your results to our [GitHub Issues page](https://github.com/EitanBlumin/sp_GenerateTableDDLScript/issues).

## License

The sp_GenerateTableDDLScript procedure is licensed under the Mozilla Public License 2.0.

For more info [Click Here](https://github.com/EitanBlumin/sp_GenerateTableDDLScript/blob/master/LICENSE)

## Contribution & Issue Submission

This is an open-source project, and therefore you may freely submit your own issue and pull requests, if you wish to contribute.

Any contribution is welcome.

You may view the currently opened issues at the [GitHub Issues page](https://github.com/EitanBlumin/sp_GenerateTableDDLScript/issues).

## Acknowledgements

The script is mainly based off of the sp_ScriptTable stored procedure originally published by Tim Chapman in this URL:

[https://www.techrepublic.com/blog/the-enterprise-cloud/script-table-definitions-using-tsql/](https://www.techrepublic.com/blog/the-enterprise-cloud/script-table-definitions-using-tsql/)
