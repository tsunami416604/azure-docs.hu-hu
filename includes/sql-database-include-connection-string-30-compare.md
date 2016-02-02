


### 比較連接字串

下表比較 C# 程式連接到內部部署 SQL Server，以及連接到雲端中的 Azure SQL Database 時，所需要的連接字串。 差異之處會以粗體顯示。


| 連接字串<br/>Azure SQL Database| 連接字串<br/>Microsoft SQL Server|
| :-- | :-- |
| Server =**tcp:**{your_serverName_here}**。 database.windows.net,1433**;<br/>使用者識別碼 = {your_loginName_here}**@{your_serverName_here}**;<br/>密碼 = {your_password_here};<br/>**資料庫 = {your_databaseName_here};**<br/>**連接逾時 = 30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate = False**;| Server = {your_serverName_here};<br/>使用者識別碼 = {your_loginName_here};<br/>密碼 = {your_password_here};|


**Database=** 對 SQL Server 而言是選用的，但對 SQL Database 而言則是必要的。


[.NET ADO SqlConnectionStringBuilder 屬性](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) -討論詳細資料中的所有參數。








