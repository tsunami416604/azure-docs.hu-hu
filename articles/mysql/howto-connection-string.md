---
title: Csatlakozás a MySQL az Azure Database-alkalmazások
description: Ez a dokumentum a jelenleg támogatott kapcsolati karakterláncok az alkalmazások az Azure Database-MySQL, beleértve az ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python vagy Ruby kapcsolati sorolja fel.
services: mysql
author: mswutao
ms.author: wuta
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e7b200fd1de79f0bca680bdedc34fa376cf07d68
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>A MySQL az Azure Database-alkalmazások összekapcsolása
Ez a témakör felsorolja a karakterláncot kapcsolattípusokat által támogatott Azure-adatbázis a MySQL, sablonok és példákkal együtt. Lehetséges, hogy különböző paraméterek és beállítások a kapcsolati karakterláncban.

- A tanúsítvány beszerzéséről [SSL konfigurálása](./howto-configure-ssl.md).
- {your_host} = <servername>.mysql.database.azure.com
- {your_user}@{servername} megfelelően = userID formátum a hitelesítéshez.  Használata esetén a felhasználói azonosítóját, a hitelesítés sikertelen lesz.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Ebben a példában a kiszolgáló neve, `mydemoserver`, az adatbázisnév `wpdb`, a felhasználónév `WPAdmin`, és a jelszó `mypassword!2`. Ennek eredményeképpen a kapcsolati karakterláncot kell lennie:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Ismerje meg a kapcsolati karakterlánc részleteket az Azure-portálon
Az a [Azure-portálon](https://portal.azure.com), nyissa meg a MySQL-kiszolgáló Azure-adatbázishoz, és kattintson a **kapcsolati karakterláncok** karakterlánc listájának lekérdezése a példányhoz: ![a kapcsolati karakterláncok ablaktáblán az Azure-ban portál](./media/howto-connection-strings/connection-strings-on-portal.png)

A karakterlánc részletesen bemutatja a például az illesztőprogram, a kiszolgáló és a más adatbázis kapcsolat paramétereit. Módosítsa a példákat a saját paraméterekkel, például az adatbázis nevét, jelszó és így tovább. Ez a karakterlánc segítségével majd csatlakozni a kiszolgálóhoz, a kódot és az alkalmazások.

## <a name="next-steps"></a>További lépések
- Kapcsolat szalagtárakkal kapcsolatos további információkért lásd: [fogalmak - adatkapcsolattárak](./concepts-connection-libraries.md).
