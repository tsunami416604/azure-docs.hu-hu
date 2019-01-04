---
title: Csatlakozás az alkalmazások az Azure Database for MySQL-hez
description: Ez a dokumentum a jelenleg támogatott kapcsolati karakterlánc, a csatlakozás az Azure Database for MySQL, beleértve a (C#) ADO.NET, JDBC, Node.js, ODBC, PHP, Python és Ruby-alkalmazások listája.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 721950160974ef21f88791e64a41cfe0dd2b23ba
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546759"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Kapcsolódás az alkalmazások az Azure Database for MySQL-hez
Ez a témakör felsorolja a kapcsolati karakterlánc típusú által támogatott Azure Database for MySQL, példák és sablonok. Lehetséges, hogy különböző paraméterek és beállítások a kapcsolati karakterláncban.

- A tanúsítvány beszerzéséről [SSL konfigurálása](./howto-configure-ssl.md).
- {your_host} = <servername>.mysql.database.azure.com
- {your_user}@{servername} = megfelelően a felhasználói azonosító formátuma a hitelesítéshez.  Ha csak a felhasználói azonosítót használja, a hitelesítés sikertelen lesz.

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

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>A kapcsolati karakterlánc adatait lekérése az Azure Portalról
Az a [az Azure portal](https://portal.azure.com), nyissa meg az Azure Database for MySQL-kiszolgálóhoz, és kattintson **kapcsolati karakterláncok** karakterlánc lekéréséhez a példány: ![A kapcsolati karakterlánc ablaktáblán az Azure Portalon](./media/howto-connection-strings/connection-strings-on-portal.png)

A karakterláncban biztosít például az illesztőprogram, a kiszolgáló és az egyéb adatbázis kapcsolódási paraméterek. Módosítsa a példákat a saját paramétereivel, például az adatbázis nevét, jelszó és így tovább. Ezután használhatja ezt a karakterláncot a kód és az alkalmazások csatlakozni a kiszolgálóhoz.

## <a name="next-steps"></a>További lépések
- Csatlakozási kódtárak kapcsolatos további információkért lásd: [alapelvei – adatkapcsolattárak](./concepts-connection-libraries.md).
