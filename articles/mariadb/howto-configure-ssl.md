---
title: Biztonságosan csatlakozhat az Azure Database for MariaDB az SSL-kapcsolat konfigurálása
description: Megfelelő konfigurálása, Azure Database for MariaDB és a társított alkalmazások SSL-kapcsolatok megfelelően használatára vonatkozó utasításokat
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 00e8479eab927acccc8f797311a0a2d440bb96da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961671"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>SSL-összekapcsolhatóság konfigurálása az alkalmazásban való kapcsolódás az Azure Database for MariaDB
Azure Database for MariaDB támogatja a Secure Sockets Layer (SSL) használó ügyfélalkalmazások az Azure Database for MariaDB-kiszolgálóhoz csatlakozik. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="obtain-ssl-certificate"></a>SSL-tanúsítvány beszerzése
Töltse le a tanúsítványt, az Azure Database for MariaDB kiszolgálót az SSL-en keresztüli kommunikációhoz szükséges [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , és mentse a tanúsítványfájlt a helyi meghajtón (ebben az oktatóanyagban c:\ssl például).
**A Microsoft Internet Explorer és Microsoft Edge:** a letöltés befejezése után nevezze át a tanúsítványt BaltimoreCyberTrustRoot.crt.pem.

## <a name="bind-ssl"></a>Az SSL BIND
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Kapcsolódás a kiszolgálóhoz a MySQL Workbench használatával SSL-en keresztül
Állítsa be a MySQL Workbench SSL-en keresztül biztonságos kapcsolatot. Az új kapcsolat beállítása párbeszédpanelen keresse meg a **SSL** fülre. Az a **SSL CA-fájl:** mezőben adja meg a fájl helyét, a **BaltimoreCyberTrustRoot.crt.pem**. 
![Mentse testre szabott csempe](./media/howto-configure-ssl/mysql-workbench-ssl.png) a meglévő kapcsolatok esetében kattintson a jobb gombbal a kapcsolat ikon az SSL bind és kattintson a Szerkesztés. Keresse meg a **SSL** lapra, és kösse a tanúsítványfájl.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Kapcsolódás a kiszolgálóhoz a MySQL parancssori felületről SSL-en keresztül
Az SSL-tanúsítvány kötése egy másik módja, a MySQL parancssori felület használatához futtassa az alábbi parancsot:
```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="enforcing-ssl-connections-in-azure"></a>Az Azure-beli SSL-kapcsolatok kikényszerítése 
### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Az Azure portal használatával, keresse fel az Azure Database for MariaDB-kiszolgálót, és kattintson **kapcsolatbiztonság**. A váltógomb segítségével engedélyezheti vagy tilthatja le a **kényszerítése SSL-kapcsolat** beállítást, és kattintson a **mentése**. Mindig engedélyezi a Microsoft javasolja a **kényszerítése SSL-kapcsolat** fokozott biztonsági beállítást.
![ssl engedélyezése](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Engedélyezheti vagy letilthatja a **ssl-kényszerítés** paraméter engedélyezett vagy letiltott értékek jelölik az Azure CLI-ben.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Az SSL-kapcsolat ellenőrzése
Hajtsa végre a mysql **állapot** paranccsal ellenőrizheti, hogy csatlakozott a MariaDB-kiszolgáló SSL-lel:
```sql
status
```
Erősítse meg a kapcsolat van titkosítva, tekintse át a kimenetet, amely kell megjelennie: **SSL: titkosító használatban az AES256-SHA** 

## <a name="sample-code"></a>Mintakód
Biztonságos kapcsolatot létesíteni az Azure Database for MariaDB SSL-en keresztül az alkalmazásból, tekintse meg a következő kód minták:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mariadb.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mariadb.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (a MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
