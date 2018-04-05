---
title: Nyílt forráskódú technológiák gyakori kérdések az Azure web apps |} Microsoft Docs
description: Adott válaszok nyílt forráskódú technológiákkal kapcsolatos gyakori kérdések az Azure App Service Web Apps szolgáltatása.
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
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 4fb443691e216169dd1322b96d77139ffde752d4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Gyakori kérdések az Azure Web Apps nyílt forráskódú technológiák

Ez a cikk gyakran ismételt kérdések (GYIK) választ rendelkezik tudnivalók a nyílt forráskódú technológiák a [az Azure App Service Web Apps szolgáltatásának](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-cleardb-database-is-down-how-do-i-resolve-this"></a>A ClearDB adatbázist nem működik. Hogyan lehet elhárítani ezt?

Adatbázissal kapcsolatos problémák esetén kérje [ClearDB támogatási](https://www.cleardb.com/developers/help/support). 

A ClearDB kapcsolatos gyakori kérdésekre adott válaszok, lásd: [ClearDB – gyakori kérdések](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="why-wasnt-my-cleardb-database-migrated-during-my-subscription-migration"></a>A ClearDB adatbázist miért nem volt át az előfizetést az áttelepítés során?

Erőforrás-áttelepítés végrehajtása előfizetések között, bizonyos korlátozások vonatkoznak. A ClearDB MySQL-adatbázis egy külső szolgáltatás, és nem telepíti át az Azure-előfizetés az áttelepítés során.

Ha nem Ön a MySQL-adatbázis áttelepítése az Azure-erőforrások áttelepítése előtt, előfordulhat, hogy a ClearDB MySQL-adatbázis nem elérhető. Ennek elkerülése érdekében először, manuálisan telepítse át a ClearDB adatbázist, majd utána áttelepíteni a webalkalmazás az Azure-előfizetés.

További információkért lásd: [ClearDB MySQL-adatbázisok – gyakori kérdések az Azure App Service](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hogyan PHP PHP kapcsolatos problémák elhárítása naplózás bekapcsolása?

PHP-naplózás bekapcsolása:

1. Jelentkezzen be a [Kudu webhely](https://*yourwebsitename*.scm.azurewebsites.net).
2. A felső menüben válassza ki **Debug konzol** > **CMD**.
3. Válassza ki a **hely** mappát.
4. Válassza ki a **wwwroot** mappát.
5. Válassza ki a **+** ikonra, és válassza **új fájl**.
6. A fájl neve **. user.ini**.
7. Jelölje be a ceruza ikonra a **. user.ini**.
8. A fájlban adja hozzá ezt a kódot: `log_errors=on`
9. Kattintson a **Mentés** gombra.
10. Jelölje be a ceruza ikonra a **wp-config.php**.
11. Módosítsa a szöveg a következő kódot:
   ```
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Supress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Supress PHP errors to screenini_set('display_errors', 0);
   ```
12. Az Azure portálon, a webes alkalmazás menü indítsa újra a webes alkalmazást.

További információkért lásd: [engedélyezése WordPress hibanaplókat](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hogyan Python-alkalmazások hibáinak jelentkezni az App Service szolgáltatásban üzemeltetett alkalmazásokat?

Rögzítheti a Python-alkalmazások hibáinak:

1. Válassza ki az Azure portálon, a web app alkalmazásban **beállítások**.
2. Az a **beállítások** lapon jelölje be **Alkalmazásbeállítások**.
3. A **Alkalmazásbeállítások**, adja meg a következő kulcs/érték pár:
    * Key : WSGI_LOG
    * Érték: D:\home\site\wwwroot\logs.txt (adja meg a kívánt fájlnevet)

Meg kell jelennie a logs.txt fájlban a wwwroot hibáit.

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>A Node.js-alkalmazás az App Service-ben futtatott verziójának módosítása?

Ha módosítani szeretné a Node.js-alkalmazás verziója, az alábbi lehetőségek egyikét használhatja:

*   Az Azure-portálon használni **Alkalmazásbeállítások**.
    1. Az Azure-portálon keresse meg a webes alkalmazást.
    2. Az a **beállítások** panelen válassza **Alkalmazásbeállítások**.
    3. A **Alkalmazásbeállítások**, WEBSITE_NODE_DEFAULT_VERSION is megadható a kulcsot, valamint a Node.js értékeként kívánt verzióját.
    4. Lépjen a [Kudu konzol](https://*yourwebsitename*.scm.azurewebsites.net).
    5. A Node.js-verzió ellenőrzéséhez írja be a következő parancsot:  
   ```
   node -v
   ```
*   Módosítsa a iisnode.yml fájlt. A Node.js-verzió az iisnode.yml fájlt módosítása csak állítja be a futtatási környezetet, hogy az iisnode használja. A Kudu cmd, míg mások továbbra is használhatják a Node.js-verzió az beállított **Alkalmazásbeállítások** az Azure portálon.

    Állítsa be manuálisan a iisnode.yml fájlt, hozzon létre egy iisnode.yml fájlt az alkalmazás gyökérmappájában lévő mappának. A fájlban a következő sort a következők:
   ```
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Be iisnode.yml fájlt package.json forrás vezérlő üzembe helyezése során.
    Az Azure forrás vezérlő telepítési folyamat a következő lépésekből áll:
    1. Tartalom áthelyezése az Azure-webalkalmazásban.
    2. Egy alapértelmezett telepítési parancsfájl hoz létre, ha nincs a webes alkalmazás gyökérmappájában (Deploy.cmd fájl, .deployment fájlok) ilyen.
    3. A telepítési parancsfájlt, amelyben létrehoz egy iisnode.yml fájlt Ha, említse meg a package.json fájlban a Node.js-verzió fut > motor `"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. A iisnode.yml fájlt a következő kódsort rendelkezik:
        ```
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>A WordPress alkalmazás az App Service-ben üzemeltetett "Error egy adatbázis-kapcsolatot létesít." hibaüzenet jelenik meg. Hogyan hibaelhárítása Ez?

Ha ez a hiba jelenik meg az Azure WordPress-alkalmazás, php_errors.log és debug.log, hogy teljes a lépések részletes leírást talál [engedélyezése WordPress hibanaplókat](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

A naplókat, ha engedélyezve vannak Reprodukálja a hibát, és ellenőrizze a naplókat, ha elfogy kapcsolatok megjelenítéséhez:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Ha ez a hiba a debug.log vagy php_errors.log fájlok jelenik meg, az alkalmazás túllépte a kapcsolatok száma. Ha a ClearDB, ellenőrizze a rendelkezésre álló kapcsolatok száma a [service-csomag](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hogyan debug a Node.js-alkalmazás az App Service-ben üzemeltetett?

1.  Lépjen a [Kudu konzol](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Nyissa meg az alkalmazás naplók mappába (D:\home\LogFiles\Application).
3.  A logging_errors.txt fájlban ellenőrizze a tartalom.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Natív Python-modulok telepítése egy App Service webalkalmazás vagy API-alkalmazás

Néhány csomag Előfordulhat, hogy telepítse az Azure-ban a pip használatával. A csomag nem feltétlenül érhető el a Python-Csomagindexben, vagy lehet, hogy egy fordító szükséges (a fordító nem érhető el a számítógépen, hogy fut a webes alkalmazás az App Service szolgáltatásban). Az App Service web apps és API-alkalmazások natív modulok telepítésével kapcsolatos információkért lásd: [telepítése Python-modulok az App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hogyan telepíthetem a Django-alkalmazást az App Service a Git szoftver, a Python új verziójának használatával?

A Django telepítésével kapcsolatos információkért lásd: [központi telepítése egy Django-alkalmazást az App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>A Tomcat naplófájlok helyét?

Az Azure piactér és egyéni telepítésekhez:

* Mappájának helye: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Fontos fájlok:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost. *éééé-hh-nn*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log


A portál **Alkalmazásbeállítások** központi telepítések:

* Mappájának helye: D:\home\LogFiles
* Fontos fájlok:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost. *éééé-hh-nn*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hogyan hibáinak elhárítása JDBC kapcsolat hibái?

A következő üzenetet láthatja a Tomcat naplókban:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

A hiba elhárításához:

1. Távolítsa el a sqljdbc*.jar fájlt az alkalmazás/lib mappából.
2. Ha az egyéni Tomcat- vagy Azure piactér Tomcat webkiszolgáló használ, másolja a .jar fájlt a Tomcat lib mappába.
3. Ha engedélyezi az Azure portálról Java (válasszon **Java 1.8** > **Tomcat kiszolgálót**), másolja a sqljdbc.* jar-fájlra a mappában, az alkalmazás párhuzamos. Ezt követően adja hozzá a következő classpath a web.config fájlban

    ```
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Miért látom hibák, amikor élő naplófájlok másolása?

Ha megpróbálja élő naplófájlok Java-alkalmazások (például Tomcat) másolja, láthatja az FTP-hiba:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A hibaüzenet a következő változhat, attól függően, hogy az FTP-ügyfél.

Minden Java-alkalmazások rendelkeznek a zárolási problémát. Csak a Kudu támogatja, a fájl letöltése, az alkalmazás futtatása közben.

Az alkalmazás leállítása lehetővé teszi az FTP-hozzáférést a fájlokhoz.

Egy másik megoldás, a webjobs-feladat ütemezés szerint fut, és másolja át ezeket a fájlokat egy másik címtárba írható. Egy minta-projekt tekintse meg a [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projekt.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Hol található a naplófájlok a Jetty?

Piactér-vagy egyéni telepítések esetén a naplófájl a D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs mappában van. Vegye figyelembe, hogy a mappa helyét a Jetty használ verziójától függ. Például az elérési út az itt megadott a Jetty 9.1.2. Keresse meg jetty_*YYYY_MM_DD*. stderrout.log.

A portál Alkalmazásbeállítás telepítések esetében a naplófájl D:\home\LogFiles van. Keresse meg jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Küldhetek e-mailt a saját Azure-webalkalmazás?

App Service beépített e-mail-szolgáltatás nem rendelkezik. Az e-mailek küldése az alkalmazásból, néhány jó alternatíva ez című [Stack Overflow vitafórum](http://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Miért nem saját WordPress-webhely átirányítja egy másik URL-CÍMRE?

Ha nemrég áttelepítette az Azure-ba, WordPress előfordulhat, hogy átirányítja a régi tartomány URL-CÍMRE. Ezt a MySQL-adatbázis beállítása okozza.

WordPress ismerős + az Azure hely bővítménye által az átirányítási URL-címet közvetlenül az adatbázis frissítéséhez használhatja. WordPress ismerős + használatával kapcsolatos további információkért lásd: [WordPress eszközök és a MySQL-áttelepítés a WordPress ismerős +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Azt is megteheti, ha szeretné-e manuálisan frissítse a átirányítási URL-címet az SQL-lekérdezések vagy PHPMyAdmin, lásd: [WordPress: rossz URL-cím átirányítása](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>A WordPress bejelentkezési jelszó módosítása

Ha elfelejtette a jelszavát WordPress-bejelentkezés, segítségével WordPress ismerős + a frissítést. A jelszó alaphelyzetbe állításához, a WordPress ismerős + Azure hely-kiterjesztés telepítése, és fejezze be a leírt lépéseket [WordPress eszközök és a MySQL-áttelepítés a WordPress ismerős +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nem lehet bejelentkezni WordPress. Hogyan lehet elhárítani ezt?

Ha nemrég a beépülő modul telepítése után WordPress tévedéssel, lehetséges, hogy egy hibás beépülő modult. WordPress ismerős +, amelyek segítségével Azure hely bővítmény letiltása a WordPress beépülő modulok. További információkért lásd: [WordPress eszközök és a MySQL-áttelepítés a WordPress ismerős +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hogyan telepítse át a WordPress adatbázist?

Amely csatlakozik a WordPress webhely MySQL-adatbázis áttelepítéséhez több lehetőség közül választhat:

* A fejlesztők: Használja a [parancssort vagy PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Nem-Fejlesztők: [WordPress ismerős +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hogyan segít, WordPress biztonságosabbá?

Ajánlott biztonsági eljárások a WordPress, lásd: [ajánlott eljárások az Azure-ban WordPress biztonsági](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Kísérlet PHPMyAdmin használja, és "Hozzáférés megtagadva." a következő üzenet jelenik meg Hogyan lehet elhárítani ezt?

A probléma tapasztalhat, ha a MySQL alkalmazásbeli funkció még nem fut. Ez App Service-példányban. A probléma megoldása érdekében próbálkozzon a webhely eléréséhez. A szükséges eljárásokat, beleértve a MySQL alkalmazáson belüli folyamat elindul. Annak ellenőrzése, hogy MySQL alkalmazásbeli fut-e, a Process Explorert, hogy mysqld.exe jelleggel szerepel-e.

Miután meggyőződött arról, hogy MySQL alkalmazásbeli fut-e, próbálja meg PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>A HTTP 403-as hiba jelenik meg: való importálására vagy exportálására alkalmazásbeli MySQL adatbázis PHPMyadmin használatával. Hogyan lehet elhárítani ezt?

Ha a Látványelem egy régebbi verzióját használja, akkor léptek fel egy ismert hiba. A probléma megoldása érdekében váltson a Chrome egy újabb verziója. Is próbálkozzon másik böngészővel, például az Internet Explorer vagy Edge, ha a probléma nem történik meg.
