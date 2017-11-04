---
title: "Konfigurálja az SSL-kapcsolatot való biztonságos kapcsolódás Azure-adatbázis a MySQL |} Microsoft Docs"
description: "Útmutatást megfelelően konfigurálni az Azure-adatbázis MySQL és a társított alkalmazások megfelelően az SSL-kapcsolat használata"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 10/25/2017
ms.openlocfilehash: 83830e4776eaa7c4f10bc14dcefd47c6eaf25997
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Az alkalmazásokhoz való biztonságos kapcsolódás Azure-adatbázis a MySQL SSL-kapcsolat konfigurálása
Azure MySQL-adatbázis támogatja az Azure-adatbázis MySQL-kiszolgáló csatlakozik a Secure Sockets Layer (SSL) használó ügyfélalkalmazások. Az adatbázis-kiszolgáló és az ügyfél alkalmazások közötti SSL-kapcsolatok kényszerítése segít lánctámadások elleni védelem érdekében "man a középső" az adatfolyamot a kiszolgáló és az alkalmazás közötti titkosításával.

## <a name="step-1-obtain-ssl-certificate"></a>1. lépés: SSL-tanúsítvány beszerzése
Töltse le a tanúsítványt, az Azure-adatbázis a MySQL-kiszolgáló az SSL protokollt használó kommunikációra szükséges [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) és mentette a tanúsítványfájlt (a helyi meghajtójára Ebben az oktatóanyagban használtuk c:\ssl).
**A Microsoft Internet Explorer és a Microsoft Edge:** a letöltés befejezése után nevezze át a tanúsítvány BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>2. lépés: A kötés SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Kapcsolódás a kiszolgálóhoz a MySQL munkaterület használata az SSL-en keresztül
Konfigurálja a MySQL munkaterület biztonságos SSL Csatornán keresztül csatlakozni. Új kapcsolat beállítása párbeszéd navigáljon a **SSL** fülre. Az a **SSL-hitelesítésszolgáltató fájl:** mezőbe írja be a fájl helyét a **BaltimoreCyberTrustRoot.crt.pem**. 
![Mentse testre szabott csempe](./media/howto-configure-ssl/mysql-workbench-ssl.png) meglévő kapcsolatok esetén SSL kötni a kapcsolat ikon a jobb gombbal kattintva és kattintson a Szerkesztés. Keresse meg a **SSL** lapra, majd kösse a tanúsítványfájl.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Kapcsolódás a kiszolgálóhoz SSL-en keresztül a MySQL parancssori felület használatával
Másik módja, hogy az SSL-tanúsítvány kötését használ a MySQL parancssori felület szerint hajtsa végre a következő parancsot:
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>3. lépés: SSL-kapcsolatok az Azure-ban kényszerítése 
### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Az Azure portál használatával, látogasson el a MySQL-kiszolgálóhoz tartozó Azure-adatbázis, és kattintson **kapcsolatbiztonsági**. A váltógomb segítségével engedélyezheti vagy tilthatja le a **kényszerítése SSL-kapcsolat** beállításával, és kattintson a **mentése**. A Microsoft azt javasolja, hogy ahhoz, hogy mindig a **kényszerítése SSL-kapcsolat** vonatkozó fokozott biztonsági beállításait.
![ssl engedélyezése](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Engedélyezheti vagy letilthatja a **ssl-kényszerítési** engedélyezve vagy letiltva értékek használatával rendre az Azure CLI paraméter.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>4. lépés: Az SSL-kapcsolat ellenőrzése
A mysql végrehajtása **állapot** parancs futtatásával ellenőrizze, hogy rendelkezik-e csatlakozik a MySQL-kiszolgálóval SSL használatával:
```dos
mysql> status
```
Ellenőrizze a kapcsolat a kimeneti, amelyhez meg kell jelennie megtekintésével titkosított: **SSL: titkosító használatban AES256-SHA** 

## <a name="sample-code"></a>Mintakód
Biztonságos kapcsolatot létesíteni az Azure Database-MySQL az SSL-en keresztül az alkalmazásról, tekintse meg a következő példakódok.
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```
try:
    conn=mysql.connector.connect(user='myadmin@myserver4demo', 
        password='yourpassword', 
        database='quickstartdb', 
        host='myserver4demo.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```
conn = pymysql.connect(user = 'myadmin@myserver4demo', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'myserver4demo.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>Ruby
```
client = Mysql2::Client.new(
        :host     => 'myserver4demo.mysql.database.azure.com', 
        :username => 'myadmin@myserver4demo',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool, InsecureSkipVerify: true})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@myserver4demo' , 'yourpassword', 'myserver4demo.mysql.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
```
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>Java(MariaDB)
```
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'myserver4demo.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@myserver4demo');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

## <a name="next-steps"></a>Következő lépések
Tekintse át a következő különböző alkalmazás kapcsolati lehetőségek [adatkapcsolattárak MySQL az Azure-adatbázis](concepts-connection-libraries.md)
