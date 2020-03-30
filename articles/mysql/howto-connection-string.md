---
title: Kapcsolati karakterláncok – Azure Database for MySQL
description: Ez a dokumentum felsorolja a jelenleg támogatott kapcsolati karakterláncok alkalmazások csatlakozni az Azure Database for MySQL, beleértve a ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python és Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ff257e101ca925a554f2343d357e2042d2930b45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062393"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Alkalmazások csatlakoztatása az Azure Database for MySQL-adatbázishoz
Ez a témakör az Azure Database for MySQL által támogatott kapcsolati karakterlánc-típusokat, valamint sablonokat és példákat sorolja fel. Előfordulhat, hogy a kapcsolati karakterláncban különböző paraméterek és beállítások vannak.

- A tanúsítvány beszerzéséről az [SSL konfigurálása](./howto-configure-ssl.md)című témakörben látható.
- {your_host} \<= kiszolgálónév>.mysql.database.azure.com
- {your_user}@{servername} = felhasználóazonosító formátum a megfelelő hitelesítéshez.  Ha csak a userID azonosítót használja, a hitelesítés sikertelen lesz.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Ebben a példában a `mydemoserver`kiszolgáló neve `wpdb`, az adatbázis `WPAdmin`neve , `mypassword!2`a felhasználónév a , a jelszó pedig . Ennek eredményeképpen a kapcsolati karakterláncnak a következőnek kell lennie:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>A kapcsolati karakterlánc részleteinek beszereznie az Azure Portalról
Az [Azure Portalon](https://portal.azure.com)nyissa meg az Azure Database for MySQL-kiszolgálót, majd kattintson ![a **Kapcsolati karakterláncok** elemre a példány karakterlánclistájának lekérni: A Kapcsolati karakterláncok ablaktábla az Azure Portalon](./media/howto-connection-strings/connection-strings-on-portal.png)

A karakterlánc olyan részleteket tartalmaz, mint például az illesztőprogram, a kiszolgáló és az adatbázis egyéb kapcsolati paraméterei. Módosítsa ezeket a példákat saját paramétereinek , például adatbázisnevének, jelszavának és így tovább. Ezután ezzel a karakterlánccal csatlakozhat a kiszolgálóhoz a kódból és az alkalmazásokból.

## <a name="next-steps"></a>További lépések
- A kapcsolattárakról további információt a [Fogalmak – Kapcsolattárak](./concepts-connection-libraries.md)című témakörben talál.
