---
title: Nyílt forráskódú technológiák – gyakori kérdések
description: Válaszok az Azure App Service nyílt forráskódú technológiáival kapcsolatos gyakori kérdésekre.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 33590f9fc7e6c4d46123cbc7088086a3197d52ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672430"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Nyílt forráskódú technológiák – gyakori kérdések az Azure-beli webalkalmazásokhoz

Ez a cikk választ ad az [Azure App Service webalkalmazások szolgáltatásának](https://azure.microsoft.com/services/app-service/web/)nyílt forráskódú technológiáival kapcsolatos gyakori kérdésekre .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Hogyan tudom bekapcsolni a PHP naplózást a PHP problémák elhárításához?

A PHP naplózásának bekapcsolása:

1. Jelentkezzen be a [Kudu webhelyére.](https://*yourwebsitename*.scm.azurewebsites.net)
2. A felső menüben válassza a **Debug Console** > **CMD**lehetőséget.
3. Jelölje ki a **Webhely** mappát.
4. Jelölje ki a **wwwroot** mappát.
5. Jelölje **+** ki az ikont, majd az **Új fájl**lehetőséget.
6. Állítsa a fájl nevet **.user.ini**.
7. Jelölje ki a **.user.ini**jel melletti ceruza ikont.
8. A fájlban adja hozzá ezt a kódot:`log_errors=on`
9. Kattintson a **Mentés** gombra.
10. Válassza a ceruza ikont a **wp-config.php**fájl mellett.
11. Módosítsa a szöveget a következő kódra:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Az Azure Portalon a webalkalmazás menüjében indítsa újra a webalkalmazást.

További információt a [WordPress hibanaplók engedélyezése című témakörben talál.](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/)

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Hogyan naplózhatom a Python-alkalmazáshibákat az App Service-ben tárolt alkalmazásokban?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Hogyan módosíthatom az App Service-ben üzemeltetett Node.js alkalmazás verzióját?

A Node.js alkalmazás verziójának módosításához az alábbi lehetőségek közül választhat:

* Az Azure Portalon használja az **alkalmazásbeállításokat.**
  1. Az Azure Portalon nyissa meg a webalkalmazást.
  2. A **Beállítások** panelen válassza az **Alkalmazásbeállítások lehetőséget**.
  3. Az **Alkalmazásbeállítások**ban WEBSITE_NODE_DEFAULT_VERSION vehet fel kulcsként, és a Node.js kívánt verzióját értékként.
  4. Nyissa meg a [Kudu konzolt.](https://*yourwebsitename*.scm.azurewebsites.net)
  5. A Node.js verzió ellenőrzéséhez írja be a következő parancsot:  
     ```
     node -v
     ```
* Módosítsa az iisnode.yml fájlt. Az iisnode.yml fájl Node.js verziójának módosítása csak az iisnode által használt futásidejű környezetet állítja be. A Kudu cmd és mások továbbra is a Node.js verziót használják, amely az **Azure Portalalkalmazás-beállításokban** van beállítva.

  Az iisnode.yml fájl manuális beállításához hozzon létre egy iisnode.yml fájlt az alkalmazás gyökérmappájában. A fájlban adja meg a következő sort:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Állítsa be az iisnode.yml fájlt a package.json használatával a forrásvezérlés telepítése során.
  Az Azure forrásvezérlés telepítési folyamata a következő lépéseket foglalja magában:
  1. Tartalmat helyezi át az Azure webappba.
  2. Létrehoz egy alapértelmezett központi telepítési parancsfájlt, ha nincs egy (deploy.cmd, .deployment fájlok) a webalkalmazás gyökérmappájában.
  3. Futtat egy központi telepítési parancsfájlt, amelyben létrehoz egy iisnode.yml fájlt, ha a Node.js verziót a package.json fájlban említi > motorban`"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Az iisnode.yml fájl kódsora a következő:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Az App Service szolgáltatásban üzemeltetett WordPress alkalmazásban a "Hiba az adatbázis-kapcsolat létrehozása" üzenet jelenik meg. Hogyan háríthatók el a hibaelhárítás?

Ha ezt a hibát látja az Azure WordPress alkalmazásban, a php_errors.log és a debug.log engedélyezéséhez hajtsa végre a [WordPress hibanaplók engedélyezése](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/)című témakörben ismertetett lépéseket.

Ha a naplók engedélyezve vannak, reprodukálja a hibát, majd ellenőrizze a naplókat, hogy elfogynak-e a kapcsolatok:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Ha ezt a hibát a debug.log vagy a php_errors.log fájlokban látja, az alkalmazás túllépi a kapcsolatok számát. Ha a ClearDB szolgáltatásban üzemelteti a szolgáltatást, ellenőrizze, hogy hány kapcsolat érhető el a [szolgáltatási csomagban.](https://www.cleardb.com/pricing.view)

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Hogyan debug egy Node.js app, amely az App Service-ben található?

1.  Nyissa meg a [Kudu konzolt.](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole)
2.  Nyissa meg az alkalmazásnaplók mappáját (D:\home\LogFiles\Application).
3.  A logging_errors.txt fájlban ellenőrizze a tartalom szempontjából.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Hogyan telepíthetem a natív Python-modulokat egy App Service-webalkalmazásban vagy API-alkalmazásban?

Előfordulhat, hogy egyes csomagok nem települnek az Azure-ban lévő pip használatával. Előfordulhat, hogy a csomag nem érhető el a Python csomagindexben, vagy fordítóra van szükség (a fordító nem érhető el azon a számítógépen, amelyen a webalkalmazás fut az App Service-ben). A natív modulok App Service-webalkalmazásokban és API-alkalmazásokban történő telepítéséről a [Python-modulok telepítése az App Service szolgáltatásban](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/)című témakörben talál további információt.

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Hogyan telepíthetek egy Django-alkalmazást az App Service-be a Git és a Python új verziójával?

A Django telepítéséről a [Django alkalmazás telepítése az App Service szolgáltatásba című](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/)témakörben talál.

## <a name="where-are-the-tomcat-log-files-located"></a>Hol találhatók a Tomcat naplófájlok?

Azure Piactérhez és egyéni telepítésekhez:

* Mappa helye: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Érdekes fájlok:
    * Catalina. *yyyy-mm-dd*.log
    * gazda-menedzser. *yyyy-mm-dd*.log
    * Localhost. *yyyy-mm-dd*.log
    * Manager. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log


A **portálalkalmazás-beállítások** központi telepítései hez:

* Mappa helye: D:\home\LogFiles
* Érdekes fájlok:
    * Catalina. *yyyy-mm-dd*.log
    * gazda-menedzser. *yyyy-mm-dd*.log
    * Localhost. *yyyy-mm-dd*.log
    * Manager. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Hogyan háríthatók el a JDBC illesztőprogram csatlakozási hibái?

A Tomcat-naplókban a következő üzenet jelenhet meg:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

A hiba elhárítása:

1. Távolítsa el az sqljdbc*.jar fájlt az alkalmazás/könyvtár mappából.
2. Ha az egyéni Tomcat vagy az Azure Marketplace Tomcat webkiszolgálót használja, másolja ezt a .jar fájlt a Tomcat lib mappába.
3. Ha engedélyezi a Java-t az Azure Portalon (válassza a **Java 1.8** > **Tomcat-kiszolgálót),** másolja az sqljdbc.* jar fájlt az alkalmazással párhuzamos mappába. Ezután adja hozzá a következő osztályelérési beállítást a web.config fájlhoz:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Miért jelennek meg hibák, amikor élő naplófájlokat próbálok másolni?

Ha egy Java alkalmazás (például Tomcat) élő naplófájljait próbálja másolni, a következő FTP-hiba jelenhet meg:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A hibaüzenet az FTP-ügyféltől függően változhat.

Minden Java-alkalmazásrendelkezik ezzel a zárolási problémával. Csak a Kudu támogatja a fájl letöltését az alkalmazás futása közben.

Leállítása az alkalmazás lehetővé teszi az FTP hozzáférést ezekhez a fájlokhoz.

Egy másik megoldás az, hogy írni egy WebJob, hogy fut az ütemezés, és másolja ezeket a fájlokat egy másik könyvtárba. Mintaprojektről a [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projekt ben.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Hol találom a Jetty naplófájljait?

A Marketplace és az egyéni telepítések esetében a naplófájl a D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs mappában található. Ne feledje, hogy a mappa helye a használt móló verziójától függ. Az itt megadott elérési út például a 9.1.2-es mólóhoz. Keresse meg a jetty_*YYYY_MM_DD*.stderrout.log fájlt.

A portálalkalmazás-beállítások központi telepítései esetén a naplófájl d:\home\LogFiles mappában található. Keresse meg a jetty_ YYYY_MM_DD .stderrout.log*jetty_*

## <a name="can-i-send-email-from-my-azure-web-app"></a>Küldhetek e-mailt az Azure-webalkalmazásomból?

Az App Service nem rendelkezik beépített e-mail funkcióval. Az alkalmazásból küldött e-mailek néhány jó alternatívát lásd a [Veremtúlcsordulás ról.](https://stackoverflow.com/questions/17666161/sending-email-from-azure)

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Miért irányít át a WordPress oldalam egy másik URL-re?

Ha nemrég áttért az Azure-ra, előfordulhat, hogy a WordPress átirányítja a régi domain URL-re. Ezt a MySQL adatbázis egyik beállítása okozza.

A WordPress Buddy+ egy Azure-webhelybővítmény, amelynek segítségével közvetlenül az adatbázisban frissítheti az átirányítási URL-címet. A WordPress Buddy+ használatával kapcsolatos további információkért lásd: [WordPress eszközök és MySQL-áttelepítés a WordPress Buddy+ segítségével.](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

Ha az átirányítási URL-t manuálisan szeretné frissíteni SQL-lekérdezések vagy PHPMyAdmin használatával, olvassa el a [WordPress: Rossz URL-re való átirányítás című témakört.](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/)

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Hogyan változtathatom meg a WordPress bejelentkezési jelszavamat?

Ha elfelejtette a WordPress bejelentkezési jelszavát, a WordPress Buddy+ segítségével frissítheti azt. A jelszó visszaállításához telepítse a WordPress Buddy+ Azure Site Extension alkalmazást, majd hajtsa végre a [WordPress eszközökben és a MySQL-áttelepítésben](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)leírt lépéseket a WordPress Buddy+ segítségével.

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Nem tudok bejelentkezni a WordPress programba. Hogyan oldhatom meg ezt?

Ha úgy találja magát zárva a WordPress után a közelmúltban beiktatás egy plugin, lehet, hogy egy hibás plugin. WordPress Buddy + egy Azure Site Extension, amely segít letiltani dugó a WordPress. További információ: [WordPress eszközök és MySQL migráció wordpress buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Hogyan lehet áttelepíteni a WordPress adatbázis?

Több lehetősége van a WordPress webhelyéhez kapcsolódó MySQL adatbázis áttelepítésére:

* Fejlesztők: Használja a [parancssort vagy a PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Nem fejlesztők: Használja [WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Hogyan segíthetek biztonságosabbá tenni a WordPress-t?

A WordPress biztonsági gyakorlati tanácsairól az [Azure-beli WordPress-biztonság gal kapcsolatos gyakorlati tanácsok című témakörben](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/)olvashat.

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>A PHPMyAdmin szolgáltatást próbálom használni, és a "Hozzáférés megtagadva" üzenet jelenik meg. Hogyan oldhatom meg ezt?

Előfordulhat, hogy ez a probléma, ha a MySQL alkalmazáson belüli funkció még nem fut ebben az App Service-példányban. A probléma megoldásához próbáljon meg hozzáférni a webhelyhez. Ez elindítja a szükséges folyamatokat, beleértve a MySQL alkalmazáson belüli folyamatot. Annak ellenőrzéséhez, hogy a MySQL alkalmazáson belüli futása, a Process Explorer, győződjön meg arról, hogy mysqld.exe szerepel a folyamatokban.

Miután biztosította, hogy a MySQL az alkalmazáson belüli fut, próbálja meg használni PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>HTTP 403-as hibaüzenet jelenik meg, amikor a PHPMyadmin használatával megpróbálom importálni vagy exportálni a MySQL alkalmazáson belüli adatbázisomat. Hogyan oldhatom meg ezt?

Ha a Chrome régebbi verzióját használja, előfordulhat, hogy egy ismert hibát tapasztal. A probléma megoldásához frissítsen a Chrome újabb verziójára. Próbáljon meg egy másik böngészőt is használni, például az Internet Explorert vagy a Microsoft Edge-et, ahol a probléma nem jelentkezik.
