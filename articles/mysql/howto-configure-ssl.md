---
title: SSL konfigurálása - Azure-adatbázis a MySQL-hez
description: Útmutató az Azure Database MySQL és a kapcsolódó alkalmazások megfelelő konfigurálásához az SSL-kapcsolatok helyes használatához
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: a68b63fa190aa67125b2bf8c09ae7f31aedc3905
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770267"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>SSL-kapcsolat konfigurálása az alkalmazásban a MySQL Azure Database for MySQL biztonságos csatlakozásához
Az Azure Database for MySQL támogatja az Azure Database for MySQL-kiszolgálóhoz való csatlakoztatását a Secure Sockets Layer (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="step-1-obtain-ssl-certificate"></a>1. lépés: SSL-tanúsítvány beszerzése
Töltse le az SSL-en keresztüli kommunikációhoz szükséges [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) tanúsítványt az Azure Database for MySQL-kiszolgálóval, és mentse a tanúsítványfájlt a helyi meghajtóra (ez az oktatóanyag például a c:\ssl-t használja).
**Microsoft Internet Explorer és Microsoft Edge esetén:** A letöltés befejezése után nevezze át a tanúsítványt BaltimoreCyberTrustRoot.crt.pem névre.

## <a name="step-2-bind-ssl"></a>2. lépés: Kötés SSL

Az egyes programozási nyelvi kapcsolati karakterláncok esetében tekintse meg az alábbi [mintakódot.](howto-configure-ssl.md#sample-code)

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Csatlakozás kiszolgálóhoz a MySQL Workbench ssl-en keresztüli használatával
Konfigurálja a MySQL Workbench-et úgy, hogy biztonságosan csatlakozzon SSL-en keresztül. 

1. Az Új kapcsolat beállítása párbeszédablakban lépjen az **SSL** lapra. 

1. Frissítse az **SSL használata** mezőt "Require" állapotra.

1. Az **SSL hitelesítéshitelesítési fájl:** mezőbe írja be a **BaltimoreCyberTrustRoot.crt.pem**fájlhelyét. 
    
    ![SSL-konfiguráció mentése](./media/howto-configure-ssl/mysql-workbench-ssl.png)

Meglévő kapcsolatok esetén úgy kötheti az SSL-t, hogy a jobb gombbal a kapcsolat ikonjára kattint, és a szerkesztés parancsot választja. Ezután keresse meg az **SSL** lapot, és kösse össze a tanúsítványfájlt.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Csatlakozás a szerverhez a MySQL CLI ssl-en keresztül
Az SSL-tanúsítvány kötésének másik módja a MySQL parancssori felület használata a következő parancsok végrehajtásával. 

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> A Windows MySQL parancssori felületének használatakor `SSL connection error: Certificate signature check failed`hibaüzenet jelenhet meg. Ebben az esetben cserélje `--ssl-mode=REQUIRED --ssl-ca={filepath}` le `--ssl`a paramétereket a-ra.

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>3. lépés: SSL-kapcsolatok érvényesítése az Azure-ban 
### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Az Azure Portalon keresse fel az Azure Database for MySQL-kiszolgálót, majd kattintson a **Kapcsolat biztonsága**elemre. A váltógombbal engedélyezheti vagy letilthatja az **SSL-kapcsolat kényszerítése** beállítást, majd kattintson a **Mentés gombra.** A Microsoft azt javasolja, hogy a fokozott biztonság érdekében mindig engedélyezze az **SSL-kapcsolat kényszerítése** beállítást.
![engedélyezés-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Engedélyezheti vagy letilthatja az **ssl-kényszerítési** paraméter t i. Azure CLI engedélyezett vagy letiltott értékek használatával.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>4. lépés: Az SSL-kapcsolat ellenőrzése
A mysql **status** paranccsal ellenőrizze, hogy ssl-rel csatlakozott-e a MySQL-kiszolgálóhoz:
```dos
mysql> status
```
A kapcsolat titkosításának ellenőrzése a kimenet áttekintésével, amely nek a következőket kell mutatnia: **SSL: A használatban lévő titkosítás a AES256-SHA** 

## <a name="sample-code"></a>Mintakód
Ha biztonságos kapcsolatot szeretne létesíteni az Azure Database for MySQL ssl-en keresztül ia alkalmazásból, olvassa el a következő kódmintákat:

Tekintse meg az Azure Database for MySQL szolgáltatás által támogatott [kompatibilis illesztőprogramok](concepts-compatibility.md) listáját.

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (ODO használatával)
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
                       ssl={'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
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
            'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
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
### <a name="java-mysql-connector-for-java"></a>Java (MySQL Connector Java-hoz)
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
### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB-csatlakozó Java-hoz)
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

## <a name="next-steps"></a>További lépések
Tekintse át a különböző alkalmazáscsatlakozási lehetőségeket [a MySQL-hez készült Azure Database for MySQL kapcsolattárak](concepts-connection-libraries.md) at követve
