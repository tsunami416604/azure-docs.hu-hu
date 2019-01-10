---
title: Nyílt forráskódú technológiák – gyakori kérdések – Azure App Service-ben |} A Microsoft Docs
description: Az Azure App Service Web Apps funkcióját a nyílt forráskódú technológiákkal kapcsolatos gyakori kérdésekre választ kaphat.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 3057368ca76a0a9ad90c3c1890cffd139b6b13a2
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159808"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Nyílt forráskódú technológiákat az Azure Web Apps vonatkozó gyakori kérdések

Ez a cikk rendelkezik (GYIK) kapcsolatos gyakori kérdésekre adott válaszok a nyílt forráskódú technológiákkal kapcsolatos a [Azure App Service Web Apps funkciójával](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hogyan PHP PHP-hibáinak elhárítása naplózás bekapcsolása?

PHP-naplózás bekapcsolása:

1. Jelentkezzen be a [Kudu-webhely](https://*yourwebsitename*.scm.azurewebsites.net).
2. A felső menüben válassza ki a **hibakeresési konzolt** > **CMD**.
3. Válassza ki a **hely** mappát.
4. Válassza ki a **wwwroot** mappát.
5. Válassza ki a **+** ikonra, és válassza ki **új fájl**.
6. Állítsa be a fájl nevét **. user.ini**.
7. Kattintson a ceruza ikonra a **. user.ini**.
8. A fájlban adja hozzá ezt a kódot: `log_errors=on`
9. Kattintson a **Mentés** gombra.
10. Kattintson a ceruza ikonra a **wp-config.php**.
11. Módosítsa a szöveget az alábbi kódot:
   ```php
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
   ```
12. Az Azure Portalon, a webes alkalmazás menüjében indítsa újra a webalkalmazást.

További információkért lásd: [engedélyezése WordPress-hibanaplók](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hogyan Python-alkalmazáshibák bejelentkezés App Service-ben üzemeltetett alkalmazások?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hogyan változtatható az App Service-ben üzemeltetett Node.js-alkalmazás verziója?

A Node.js-alkalmazás verziójának módosításához használhatja az alábbi lehetőségek közül:

*   Az Azure Portalon használata **Alkalmazásbeállítások**.
    1. Az Azure Portalon lépjen a webalkalmazáshoz.
    2. Az a **beállítások** panelen válassza ki **Alkalmazásbeállítások**.
    3. A **Alkalmazásbeállítások**, hozzáadhatja a WEBSITE_NODE_DEFAULT_VERSION a kulcsot, valamint értékként a kívánt Node.js verziója.
    4. Nyissa meg a [Kudu konzol](https://*yourwebsitename*.scm.azurewebsites.net).
    5. A Node.js-verzió ellenőrzéséhez írja be a következő parancsot:  
   ```
   node -v
   ```
*   Módosítsa a iisnode.yml fájlt. A Node.js verzió iisnode.yml fájlt a módosítása csak beállítja a futtatási környezetet, amelyet az iisnode használja. A Kudu cmd parancsot, és másokkal is használható a Node.js-verzió az **Alkalmazásbeállítások** az Azure Portalon.

    Manuálisan állítsa be az iisnode.yml fájlt, hozzon létre egy iisnode.yml fájlt az alkalmazás gyökérmappájában lévő mappának. A fájl tartalmazza a következő sort:
   ```yml
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Állítsa be az iisnode.yml fájlt a package.json forrás vezérlő telepítése során.
    Az Azure forrás vezérlőelem üzembe helyezési folyamat az alábbi lépésekből áll:
    1. Tartalom áthelyezi az Azure-webalkalmazást.
    2. Egy alapértelmezett üzembe helyezési parancsfájlt hoz létre, ha nincs a webes alkalmazás gyökérmappájában (Deploy.cmd fájl, .deployment fájl) ilyen.
    3. A telepítési parancsfájlt, amelyben létrehoz egy iisnode.yml fájlt. Ha már javítva az a package.json fájlt a Node.js-verzió fut > motor `"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. Iisnode.yml fájlt a következő kódsort rendelkezik:
        ```yml
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>A saját WordPress-alkalmazás az App Service-ben üzemeltetett "Hiba történt egy adatbázis-kapcsolat létrehozásakor" hibaüzenet jelenik meg. Hogyan háríthatom Ez?

Ha ez a hiba az Azure a WordPress alkalmazás jelenik meg, php_errors.log és Debug.log-ot, teljes a lépések részletes [engedélyezése WordPress-hibanaplók](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Ha engedélyezve vannak a naplók, reprodukálni a hibát, és ellenőrizze a naplókat, ha elfogy kapcsolatok megjelenítéséhez:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Ha ezt a hibaüzenetet a Debug.log-ot vagy php_errors.log fájlokban, az alkalmazás meghaladja a kapcsolatok száma. Ha a ClearDB, ellenőrizze a rendelkezésre álló kapcsolatok száma a [service-csomag](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hogyan végezhetek hibakeresést egy App Service-ben üzemeltetett Node.js-alkalmazást?

1.  Nyissa meg a [Kudu konzol](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Nyissa meg az alkalmazás naplók mappájában (D:\home\LogFiles\Application).
3.  A logging_errors.txt fájlban ellenőrizze a tartalomhoz.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hogyan kell telepíteni a natív Python-modulok az App Service-webalkalmazás vagy API-alkalmazást?

Néhány csomag Előfordulhat, hogy telepítse az Azure-ban a pip használatával. A csomag nem állnak rendelkezésre a Python-Csomagindexet, vagy lehet, hogy a fordító szükséges (fordító nem áll rendelkezésre a számítógépen, hogy fut-e a webalkalmazást az App Service-ben). Az App Service web apps és API-alkalmazások natív modulok telepítésével kapcsolatos információkért lásd: [telepítse a Python-modulok az App Service-ben](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hogyan helyezhetek üzembe egy Django-alkalmazás az App Service a Git és az új verziót a Python használatával?

Django telepítésével kapcsolatos információkért lásd: [Django-alkalmazás telepítése az App Service-](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Hol találhatók a Tomcat naplófájlok?

Az Azure Marketplace-en, és egyéni telepítésekhez:

* Mappájának helye: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* A lényeges fájlok:
    * catalina. *éééé-hh-nn*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost. *éééé-hh-nn*.log
    * kezelő. *éééé-hh-nn*.log
    * site_access_log.*yyyy-mm-dd*.log


A portál **Alkalmazásbeállítások** központi telepítések:

* Mappájának helye: D:\home\LogFiles
* A lényeges fájlok:
    * catalina. *éééé-hh-nn*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost. *éééé-hh-nn*.log
    * kezelő. *éééé-hh-nn*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hogyan háríthatom el a JDBC-illesztőprogram csatlakozási hibák?

A Tomcat naplókban jelenhetnek meg a következő üzenetet:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

A hiba megoldásához:

1. Távolítsa el a sqljdbc*.jar fájlt az alkalmazás/lib mappájába.
2. Ha az egyéni Tomcat vagy az Azure Marketplace-en Tomcat webkiszolgáló használ, másolja a .jar-fájl a Tomcat lib mappájába.
3. Ha engedélyezi a Java az Azure Portalon (kiválasztása **Java 1.8-as** > **Tomcat kiszolgálót**), másolja a sqljdbc.* jar-fájlt abban a mappában, párhuzamos, és az alkalmazás. Ezután adja hozzá a következő osztályútvonal beállítást a web.config fájlt:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Miért látok hibákat, amikor élő naplófájlok másolása?

Ha egy Java-alkalmazás (például a Tomcat) élő naplófájlok másolása, előfordulhat, hogy ezt a hibaüzenetet FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A hibaüzenet az FTP-ügyfél függően változhat.

Java-alkalmazások rendelkeznek a zárolási problémát. Csak a Kudu támogatja, a fájl letöltése, az alkalmazás futása közben.

Az alkalmazás leállítása lehetővé teszi az FTP-hozzáférést a fájlokhoz.

Egy másik megoldás, hogy a webjobs-feladat ütemezés szerint fut, és a egy másik könyvtárba másolja ezeket a fájlokat írja. A mintaprojekt, lásd: a [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projekt.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Hol találom a naplófájlokat a Jetty?

A Marketplace-en és az egyéni telepítésekhez a naplófájl a D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs mappában van. Vegye figyelembe, hogy a mappa helyét, használja a Jetty verziójától függ. Például az elérési út megadva itt a Jetty 9.1.2. Keressen jetty_*YYYY_MM_DD*. stderrout.log.

A portál alkalmazás beállítás-környezetek a naplófájl D:\home\LogFiles szerepel. Keressen jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Küldhetek e-mailben a saját Azure-webalkalmazást?

App Service-ben egy beépített e-mail-szolgáltatás nem rendelkezik. További néhány jó alternatíva az e-mailek küldése az alkalmazásból: Ez [Stack Overflow vitafórum](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Miért nem egy másik URL-cím átirányítása WordPress-webhelyemet?

Ha nemrégiben áttelepítette az Azure-ba, előfordulhat, hogy a WordPress irányítsa át a régi tartomány URL-címe. A MySQL-adatbázis beállítása okozza.

WordPress ismerős + az Azure-webhely bővítmény frissítése az adatbázisban közvetlenül az átirányítási URL-cím használható. WordPress ismerős + használatával kapcsolatos további információkért lásd: [WordPress eszközöket és a MySQL-migrálása a WordPress ismerős +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Azt is megteheti, ha inkább manuálisan frissíteni az átirányítási URL-cím, az SQL-lekérdezések vagy PHPMyAdmin, lásd: [WordPress: Helytelen URL-cím átirányítása](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hogyan változtatható meg a bejelentkezési jelszó WordPress?

Ha elfelejtette a jelszót a WordPress jelentkezzen be, használhatja a WordPress ismerős + frissíteni. A jelszó alaphelyzetbe állításához, a WordPress ismerős + Azure Webhelybővítmény telepítése, és kövesse az ismertetett lépéseket [WordPress eszközöket és a MySQL-migrálása a WordPress ismerős +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nem lehet bejelentkezni a WordPress. Hogyan oldhatom fel ez?

Ha saját maga a közelmúltban a beépülő modul telepítése után a WordPress kizárva, lehetséges, hogy egy hibás beépülő modult. A WordPress ismerős +, egy Azure-Webhelybővítményt, amelyek segítségével a WordPress beépülő modulok letiltása. További információkért lásd: [WordPress eszközöket és a MySQL-migrálása a WordPress ismerős +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hogyan migrálhatok WordPress-adatbázisom?

A MySQL-adatbázis, amely csatlakozik a WordPress-webhely áttelepítése több lehetősége van:

* Fejlesztők számára: Használja a [parancssort vagy a PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Nem-fejlesztők számára: Használat [WordPress ismerős +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hogyan segíti meg, hogy még biztonságosabbá teheti a WordPress-e?

Ajánlott biztonsági eljárások a WordPress, lásd: [ajánlott eljárások az Azure biztonsági WordPress](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>A tapasztalataimat szeretném PHPMyAdmin használatával, és a "Hozzáférés megtagadva." a következő üzenet jelenik meg Hogyan oldhatom fel ez?

A probléma tapasztalhat, ha az alkalmazáson belüli MySQL szolgáltatás nem fut még az App Service-példányhoz. A probléma megoldásához próbálja meg elérni a webhelyet. A szükséges eljárásokat, beleértve az alkalmazáson belüli MySQL folyamat elindul. Annak ellenőrzéséhez, hogy – alkalmazáson belüli MySQL fut-e, a Process Explorert, győződjön meg arról, hogy mysqld.exe szerepel a folyamatok.

Miután meggyőződött arról, hogy – alkalmazáson belüli MySQL fut-e, próbálja meg használni a PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Amikor importálása és exportálása az alkalmazáson belüli MySQL-adatbázis PHPMyadmin használatával a HTTP 403-as hibaüzenetet kapok. Hogyan oldhatom fel ez?

Ha egy régebbi verziója Chrome használ, akkor tapasztalt egy ismert hiba. A probléma megoldásához frissítse a Chrome egy újabb verziója. Emellett próbálja meg egy másik böngészővel, például az Internet Explorer vagy Edge, ahol a hiba nem történik meg.
