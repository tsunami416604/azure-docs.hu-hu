<properties
    pageTitle="在 Windows 上使用 Python 連接到 SQL Database"
    description="提供可用來從 Windows 用戶端連接到 Azure SQL Database 的 Python 程式碼範例。 這個範例使用 pymssql 驅動程式。"
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="meetb"/>


# 在 Windows 上使用 Python 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供以 Python 撰寫的程式碼範例。 這個範例會在 Windows 電腦上執行。 此範例會使用連接到 Azure SQL Database 和 **pymssql** 驅動程式。


## 必要條件


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)


### 安裝必要的模組


安裝 [pymssql](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql)。

確定您選擇正確的 whl 檔案。

例如：如果您在 64 位元電腦上使用 Python 2.7，請選擇：pymssql‑2.1.1‑cp27‑none‑win_amd64.whl。
下載 .whl 檔案之後，請將它放在 C:/Python27 資料夾中。

現在安裝 pymssql 驅動程式使用 pip 從命令列。 切換至 c: / Python27 並執行下列

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

若要啟用以使用 pip 的指示，請參閱 [這裡](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)


### SQL Database

請參閱 [入門頁面](sql-database-get-started.md) 以了解如何建立範例資料庫。  請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 只有如下所示的範例適用於 **AdventureWorks 結構描述**。

## 步驟 1：取得連線詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## 步驟 2 ︰ 連接


 [Pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) 函式用來連接到 SQL 資料庫。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## 步驟 3 ︰ 執行查詢

 [Cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) 函式可用來擷取對 SQL Database 設定從查詢的結果。 此函式基本上會接受任何查詢，並傳回結果集，這可以藉由使用反覆 [cursor.fetchone （)](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone)。


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


## 步驟 4 ︰ 插入資料列

在您將了解如何執行此範例 [插入](https://msdn.microsoft.com/library/ms174335.aspx) 陳述式，將保護您的應用程式的參數傳遞 [SQL 資料隱碼](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 弱點，以及擷取自動產生 [主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx) 值。  


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


## 步驟 5 ︰ 回復交易


這個程式碼範例示範如何使用交易，您將：


-開始交易

-插入一列資料

-回復您的交易以復原插入


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## 後續步驟

如需詳細資訊，請參閱 [Python 開發人員中心](/develop/python/)。


