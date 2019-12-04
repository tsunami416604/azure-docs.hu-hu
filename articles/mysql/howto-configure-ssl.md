---
title: Az SSL konfigurálása – Azure Database for MySQL
description: Útmutatás a Azure Database for MySQL és a társított alkalmazások megfelelő konfigurálásához az SSL-kapcsolatok megfelelő használatához
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 590e2e7e735af55f8c7ad96f87f947115f8783fc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770560"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Az SSL-kapcsolat konfigurálása az alkalmazásban a Azure Database for MySQLhoz való biztonságos csatlakozáshoz
A Azure Database for MySQL támogatja a Azure Database for MySQL-kiszolgáló SSL (SSL) használatával történő ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="step-1-obtain-ssl-certificate"></a>1\. lépés: SSL-tanúsítvány beszerzése
Töltse le az SSL protokollon keresztüli kommunikációhoz szükséges tanúsítványt a Azure Database for MySQL-kiszolgálóról [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) és mentse a tanúsítványt a helyi meghajtóra (ez az oktatóanyag az c:\ssl-t használja például).
**A Microsoft Internet Explorer és a Microsoft Edge esetében:** A letöltés befejezése után nevezze át a tanúsítványt a BaltimoreCyberTrustRoot. CRT. PEM névre.

## <a name="step-2-bind-ssl"></a>2\. lépés: SSL kötése

Adott programozási nyelvi kapcsolódási karakterláncok esetén tekintse meg az alábbi [mintakód](howto-configure-ssl.md#sample-code) .

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench használatával SSL-en keresztül
Konfigurálja a MySQL Workbench-t a biztonságos SSL-kapcsolathoz. 

1. Az új kapcsolat beállítása párbeszédablakban navigáljon az **SSL** lapra. 

1. Frissítse az **SSL használata** mezőt a kötelező értékre.

1. Az **SSL-hitelesítésszolgáltató fájlja:** mezőben adja meg a **BaltimoreCyberTrustRoot. CRT. PEM**fájljának helyét. 
    
    ![SSL-konfiguráció mentése](./media/howto-configure-ssl/mysql-workbench-ssl.png)

A meglévő kapcsolatok esetében a jobb gombbal a kapcsolat ikonjára kattintva, majd a szerkesztés lehetőséget választva lehet kötni az SSL-t. Ezután navigáljon az **SSL** lapra, és kösse a tanúsítvány-fájlt.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Kapcsolódás a kiszolgálóhoz a MySQL CLI-vel SSL használatával
Az SSL-tanúsítvány kötésének másik módja a MySQL parancssori felület használata a következő parancsok végrehajtásával. 

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> A MySQL parancssori felület Windows rendszeren való használatakor hibaüzenetet kaphat `SSL connection error: Certificate signature check failed`. Ha ez történik, cserélje le a `--ssl-mode=REQUIRED --ssl-ca={filepath}` paramétereket a `--ssl`ra.

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>3\. lépés: az SSL-kapcsolatok kényszerítése az Azure-ban 
### <a name="using-the-azure-portal"></a>Az Azure Portal használata
A Azure Portal használatával keresse fel a Azure Database for MySQL-kiszolgálót, majd kattintson a **kapcsolatbiztonsági**elemre. A váltógomb használatával engedélyezheti vagy letilthatja az **SSL-kapcsolat érvényesítése** beállítást, majd kattintson a **Mentés**gombra. A Microsoft azt javasolja, hogy mindig engedélyezze az **SSL-kapcsolat betartatása** beállítást a fokozott biztonság érdekében.
![engedélyezése – SSL](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Az **SSL-kényszerítési** paramétert engedélyezheti vagy letilthatja az Azure CLI-ben engedélyezett vagy letiltott értékek használatával.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>4\. lépés: az SSL-kapcsolat ellenőrzése
Futtassa a MySQL **status** parancsot annak ellenőrzéséhez, hogy az SSL használatával csatlakozott-e a MySQL-kiszolgálóhoz:
```dos
mysql> status
```
Ellenőrizze, hogy a kapcsolat titkosítva van-e a kimenet áttekintésével, amelynek a következőnek kell megjelennie: **SSL: a használatban lévő REJTJEL AES256-SHA** 

## <a name="sample-code"></a>Mintakód
Ha biztonságos kapcsolatot szeretne létesíteni az alkalmazásból az SSL-kapcsolaton keresztül Azure Database for MySQL, tekintse meg az alábbi kódrészleteket:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (OEM használatával)
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
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
### <a name="java-mysql-connector-for-java"></a>Java (MySQL-összekötő Java-hoz)
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
### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB-összekötő Java-hoz)
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

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Következő lépések
Tekintse át a különböző alkalmazás-csatlakozási lehetőségeket [a Azure Database for MySQLhoz tartozó kapcsolati kódtárak](concepts-connection-libraries.md) között
