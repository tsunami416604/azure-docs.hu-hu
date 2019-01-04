---
title: Alkalmazások az Azure Database for MariaDB csatlakoztatása
description: Ez a dokumentum a jelenleg támogatott kapcsolati karakterlánc, a csatlakozás az Azure Database for MariaDB, beleértve a (C#) ADO.NET, JDBC, Node.js, ODBC, PHP, Python és Ruby-alkalmazások listája.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 70cd25ff63101fa2a477cde2502d5d286b289366
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536574"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Alkalmazások az Azure Database for MariaDB csatlakoztatása
Ez a témakör felsorolja a kapcsolati karakterlánc típusú által támogatott Azure Database for MariaDB, sablonok és a példák együtt. Lehetséges, hogy különböző paraméterek és beállítások a kapcsolati karakterláncban.

- A tanúsítvány beszerzéséről [SSL konfigurálása](./howto-configure-ssl.md).
- {your_host} = [servername].mariadb.database.azure.com
- {your_user}@{servername} = megfelelően a felhasználói azonosító formátuma a hitelesítéshez.  Ha csak a felhasználói azonosítót használja, a hitelesítés sikertelen lesz.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

Ebben a példában a kiszolgáló neve, `mydemoserver`, az adatbázisnév `wpdb`, a felhasználónév `WPAdmin`, és a jelszó `mypassword!2`. Ennek eredményeképpen a kapcsolati karakterláncot kell lennie:

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>A kapcsolati karakterlánc adatait lekérése az Azure Portalról
Az a [az Azure portal](https://portal.azure.com), nyissa meg az Azure Database for MariaDB-kiszolgálót, és kattintson **kapcsolati karakterláncok** karakterlánc lekéréséhez a példány: ![A kapcsolati karakterlánc ablaktáblán az Azure Portalon](./media/howto-connection-strings/connection-strings-on-portal.png)

A karakterláncban biztosít például az illesztőprogram, a kiszolgáló és az egyéb adatbázis kapcsolódási paraméterek. Módosítsa a példákat a saját paramétereivel, például az adatbázis nevét, jelszó és így tovább. Ezután használhatja ezt a karakterláncot a kód és az alkalmazások csatlakozni a kiszolgálóhoz.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
