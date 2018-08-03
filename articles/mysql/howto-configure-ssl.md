---
title: A biztonságosan csatlakozhat az Azure Database for MySQL-hez SSL-kapcsolatok konfigurálása
description: Megfelelően konfigurálni az Azure Database for MySQL és a társított alkalmazások SSL-kapcsolatok megfelelően használatára vonatkozó utasításokat
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f18510e83d4e7d6498f34012b68368552399c806
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439575"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>SSL-összekapcsolhatóság konfigurálása az alkalmazásban való kapcsolódás az Azure Database for MySQL-hez
Azure Database for MySQL támogatja a Secure Sockets Layer (SSL) használó ügyfélalkalmazások az Azure Database for MySQL-kiszolgálóhoz csatlakozik. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="step-1-obtain-ssl-certificate"></a>1. lépés: SSL-tanúsítvány beszerzése
Töltse le a tanúsítványt, az Azure Database for MySQL-kiszolgáló az SSL-en keresztüli kommunikációhoz szükséges [ https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem ](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , és mentse a tanúsítványfájlt a helyi meghajtón (ebben az oktatóanyagban c:\ssl például).
**A Microsoft Internet Explorer és Microsoft Edge:** a letöltés befejezése után nevezze át a tanúsítványt BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>2. lépés: SSL Bind
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Kapcsolódás a kiszolgálóhoz a MySQL Workbench használatával SSL-en keresztül
Állítsa be a MySQL Workbench SSL-en keresztül biztonságos kapcsolatot. Az új kapcsolat beállítása párbeszédpanelen keresse meg a **SSL** fülre. Az a **SSL CA-fájl:** mezőben adja meg a fájl helyét, a **BaltimoreCyberTrustRoot.crt.pem**. 
![Mentse testre szabott csempe](./media/howto-configure-ssl/mysql-workbench-ssl.png) a meglévő kapcsolatok esetében kattintson a jobb gombbal a kapcsolat ikon az SSL bind és kattintson a Szerkesztés. Keresse meg a **SSL** lapra, és kösse a tanúsítványfájl.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Kapcsolódás a kiszolgálóhoz a MySQL parancssori felületről SSL-en keresztül
Az SSL-tanúsítvány kötése egy másik módja, a MySQL parancssori felület használatához futtassa az alábbi parancsot:
```dos
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>3. lépés: Az Azure-beli SSL-kapcsolatok kényszerítése 
### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Az Azure portal használatával, keresse fel az Azure Database for MySQL-kiszolgálóhoz, és kattintson **kapcsolatbiztonság**. A váltógomb segítségével engedélyezheti vagy tilthatja le a **kényszerítése SSL-kapcsolat** beállítást, és kattintson a **mentése**. Mindig engedélyezi a Microsoft javasolja a **kényszerítése SSL-kapcsolat** fokozott biztonsági beállítást.
![ssl engedélyezése](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Engedélyezheti vagy letilthatja a **ssl-kényszerítés** paraméter engedélyezett vagy letiltott értékek jelölik az Azure CLI-ben.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>4. lépés: Az SSL-kapcsolat ellenőrzése
Hajtsa végre a mysql **állapot** paranccsal ellenőrizheti, hogy csatlakozott a MySQL-kiszolgáló SSL-lel:
```dos
mysql> status
```
Erősítse meg a kapcsolat van titkosítva, tekintse át a kimenetet, amely kell megjelennie: **SSL: titkosító használatban az AES256-SHA** 

## <a name="sample-code"></a>Mintakód
Biztonságos kapcsolatot létesíteni az Azure Database for MySQL-hez SSL-en keresztül az alkalmazásból, tekintse meg a következő kód minták:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
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
        host='mydemoserver.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com', 
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>JAVA(MariaDB)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

## <a name="next-steps"></a>További lépések
Tekintse át a különböző alkalmazás kapcsolódási lehetőségekről a következő [adatkapcsolattárak az Azure Database for MySQL-hez](concepts-connection-libraries.md)
