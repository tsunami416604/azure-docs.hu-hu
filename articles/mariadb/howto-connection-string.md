---
title: Kapcsolati karakterláncok – Azure-adatbázis a MariaDB-hez
description: Ez a dokumentum felsorolja a MariaDB Azure Database-hez való csatlakozáshoz jelenleg támogatott kapcsolati karakterláncokat, beleértve a ADO.NET (C#), a JDBC, a Node.js, az ODBC, a PHP, a Python és a Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 74574fb905ae4ebd2552ef97cd0b5430eea6363a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530223"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Alkalmazások csatlakoztatása az Azure Database for MariaDB-hez
Ez a témakör az Azure Database for MariaDB által támogatott kapcsolati karakterlánc-típusokat, valamint sablonokat és példákat sorolja fel. Előfordulhat, hogy a kapcsolati karakterláncban különböző paraméterek és beállítások vannak.

- A tanúsítvány beszerzéséről az [SSL konfigurálása](./howto-configure-ssl.md)című témakörben látható.
- {your_host} = [kiszolgálónév].mariadb.database.azure.com
- {your_user}@{servername} = felhasználóazonosító formátum a megfelelő hitelesítéshez.  Ha csak a userID azonosítót használja, a hitelesítés sikertelen lesz.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Ebben a példában a `mydemoserver`kiszolgáló neve `wpdb`, az adatbázis `WPAdmin`neve , `mypassword!2`a felhasználónév a , a jelszó pedig . Ennek eredményeképpen a kapcsolati karakterláncnak a következőnek kell lennie:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>A kapcsolati karakterlánc részleteinek beszereznie az Azure Portalról
Az [Azure Portalon](https://portal.azure.com)nyissa meg az Azure Database for MariaDB-kiszolgálót, majd kattintson ![a **Kapcsolati karakterláncok** elemre a példány karakterlánclistájának lekérni: A Kapcsolati karakterláncok ablaktábla az Azure Portalon](./media/howto-connection-strings/connection-strings-on-portal.png)

A karakterlánc olyan részleteket tartalmaz, mint például az illesztőprogram, a kiszolgáló és az adatbázis egyéb kapcsolati paraméterei. Módosítsa ezeket a példákat saját paramétereinek , például adatbázisnevének, jelszavának és így tovább. Ezután ezzel a karakterlánccal csatlakozhat a kiszolgálóhoz a kódból és az alkalmazásokból.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
