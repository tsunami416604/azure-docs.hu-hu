---
title: "App Service-webapp csatlakoztatása a Redishez a Memcache protokoll segítségével – Azure | Microsoft Docs"
description: "Webes alkalmazás csatlakoztatása az Azure App Service-ben a Redis Cache-hoz a Memcache protokoll segítségével"
services: app-service\web
documentationcenter: php
author: SyntaxC4
manager: wpickett
editor: riande
ms.assetid: 0fcdf9fa-2995-4839-ba4d-cfa389c4ba06
ms.service: app-service-web
ms.devlang: php
ms.topic: get-started-article
ms.tgt_pltfrm: windows
ms.workload: na
ms.date: 02/29/2016
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 550b4154f0fe510cb37ec4f8bdef251b06f0b495
ms.openlocfilehash: 4d867f80cfbe5360962550fbda0091f88784b4af


---
# <a name="connect-a-web-app-in-azure-app-service-to-redis-cache-via-the-memcache-protocol"></a>Webes alkalmazás csatlakoztatása az Azure App Service-ben a Redis Cache-hoz a Memcache protokoll segítségével
Ebből a cikkből megtudhatja, hogyan csatlakoztathat egy WordPress webalkalmazást az [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) szolgáltatásban az [Azure Redis Cache][12] szolgáltatáshoz a [Memcache][13] protokollal. Ha van egy létező webalkalmazása, amely egy Memcached-kiszolgálót használ a memórián belüli gyorsítótárazáshoz, azt áttelepítheti az Azure App Service-be, és az alkalmazás kódjának minimális módosításával vagy módosítása nélkül használhatja a Microsoft Azure saját belső gyorsítótárazási megoldását. Ezenkívül használhatja a Memcache meglévő szakértelmét jól méretezhető, terjesztett alkalmazások létrehozására az Azure App Service-ben az Azure Redis Cache szolgáltatással memórián belüli gyorsítótárazáshoz, olyan népszerű alkalmazás-keretrendszerek használata mellett, mint a .NET, a PHP, a Node.js, a Java és a Python.  

Az App Service Web Apps a webalkalmazások Memcache segédkódjával teszi lehetővé ezt a forgatókönyvet. Ez lényegében egy helyi Memcached-kiszolgáló, amely Memcache-proxyként viselkedik a hívások Azure Redis Cache szolgáltatásba való gyorsítótárazásnál. Ez lehetővé teszi, hogy bármilyen Memcache-protokollal kommunikáló alkalmazás a Redis Cache segítségével gyorsítótárazzon. A Memcache-segédkód a protokoll szintjén működik, tehát bármilyen alkalmazás vagy alkalmazás-keretrendszer képes használni, ha a Memcache protokoll segítségével kommunikál.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="prerequisites"></a>Előfeltételek
A webalkalmazások Memcache-segédkódja bármilyen alkalmazással használható, feltéve, hogy az a Memcache protokoll segítségével kommunikál. Ebben a konkrét példában a referenciaalkalmazás egy méretezhető WordPress-webhely, amely az Azure Piactérről szerezhető be.

Hajtsa végre az alábbi cikkekben leírt lépéseket:

* [Az Azure Redis Cache szolgáltatás egy példányának kiosztása][0]
* [Egy méretezhető WordPress-webhely telepítése az Azure-ban][1]

Ha már telepítette a méretezhető WordPress-webhelyet, és beszerezte a Redis Cache példányt, folytathatja a Memcache-segédkód engedélyezését az Azure App Service Web Apps használatával.

## <a name="enable-the-web-apps-memcache-shim"></a>A webalkalmazások Memcache-segédkódjának engedélyezése
A Memcache-segédkód konfigurálásához létre kell hoznia három alkalmazásbeállítást. Ez különböző módszerekkel hajtható végre, ilyen például az [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715), a [klasszikus portál][3], az [Azure PowerShell-parancsmagok][5] vagy az [Azure parancssori felület][5]. Ebben a bejegyzésben az [Azure Portal][4] használatával fogom megadni az alkalmazásbeállításokat. Az alábbi értékek a Redis Cache példány **Beállítások** paneljéről kérhetők le.

![Azure Redis Cache beállításpanel](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### <a name="add-redishost-app-setting"></a>REDIS_HOST alkalmazásbeállítás hozzáadása
Az első alkalmazás-beállítás, amelyet létre kell hoznia, az a **REDIS\_HOST** alkalmazásbeállítás. Ez a beállítás megadja a célt, ahová a segédkód továbbítja a gyorsítótárazott adatokat. A REDIS_HOST alkalmazásbeállításhoz szükséges érték a Redis Cache példány **Tulajdonságok** paneljéről kérhető le.

![Azure Redis Cache gazdanév](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Az alkalmazásbeállítás kulcsát állítsa a következőre: **REDIS\_HOST**, az alkalmazásbeállítás értéke pedig legyen a Redis Cache példány**gazdaneve**.

![Webalkalmazás AppSetting REDIS_HOST](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### <a name="add-rediskey-app-setting"></a>REDIS_KEY alkalmazásbeállítás hozzáadása
A második alkalmazásbeállítás, amelyet létre kell hoznia, az a **REDIS\_KEY**. Ez a beállítás biztosítja a Redis Cache biztonságos eléréséhez szükséges hitelesítési tokent. A REDIS_KEY alkalmazásbeállításhoz szükséges értéket a Redis Cache példány **Hozzáférési kulcsok** paneljéről kérheti le.

![Azure Redis Cache elsődleges kulcs](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Az alkalmazásbeállítás kulcsát állítsa a következőre: **REDIS\_KEY**, az alkalmazásbeállítás értéke pedig legyen a Redis Cache példány**Elsődleges kulcsa**.

![Azure-webhely alkalmazásbeállítás REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### <a name="add-memcacheshimredisenable-app-setting"></a>A MEMCACHESHIM_REDIS_ENABLE alkalmazásbeállítás hozzáadása
Az utolsó alkalmazásbeállítás segítségével engedélyezhető a Memcache-segédkód a webalkalmazásokban, amelyek a REDIS-HOST és a REDIS-KEY segítségével csatlakoznak az Azure Redis Cache-hoz, és továbbítják a gyorsítótárhívásokat. Az alkalmazásbeállítás kulcsát állítsa a következőre: **MEMCACHESHIM\_REDIS\_ENABLE**, az értéke pedig legyen **true**

![Webalkalmazás alkalmazásbeállítás MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

Ha hozzáadta a három (3) alkalmazásbeállítást, kattintson a **Mentés** elemre.

## <a name="enable-memcache-extension-for-php"></a>Memcache bővítmény engedélyezése PHP-hoz
Ahhoz, hogy az alkalmazás képes legyen alkalmazni a Memcache protokollt, telepíteni kell a Memcache bővítményt a PHP-be (a WordPress webhelyének a nyelvi keretrendszerébe).

### <a name="download-the-phpmemcache-extension"></a>A php_memcache bővítmény letöltése
Tallózással keresse meg a következőt: [PECL][6]. A gyorsítótárazás kategóriájában kattintson a [memcache][7] elemre. A letöltések oszlopában kattintson a DLL hivatkozásra.

![PHP PECL webhely](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Töltse le a Non-Thread Safe (NTS) x86 hivatkozást a Webalkalmazásokban engedélyezett PHP verzióért. (Az alapértelmezett a PHP 5.4)

![PHP PECL-webhely Memcache-csomagja](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### <a name="enable-the-phpmemcache-extension"></a>A php_memcache bővítmény engedélyezése
Miután letöltötte a fájlt, csomagolja ki, és töltse fel a **php\_memcache.dll** fájlt a **d:\\home\\site\\wwwroot\\bin\\ext\\** könyvtárba. Ha a php_memcache.dll már fel van töltve a webalkalmazásba, engedélyeznie kell a bővítményt a PHP futtatókörnyezetben. A Memcache bővítménynek az Azure Portalon való engedélyezéséhez nyissa meg a webalkalmazás **Alkalmazásbeállítások** paneljét, majd adjon hozzá egy új alkalmazásbeállítást a **PHP\_EXTENSIONS** kulccsal és a **bin\\ext\\php_memcache.dll** értékkel.

> [!NOTE]
> Ha a webalkalmazásnak több PHP-bővítményt kell betöltenie, a PHP_EXTENSIONS értékének a DLL-fájlokra mutató relatív útvonalak vesszővel elválasztott listájának kell lennie.
> 
> 

![Webalkalmazás alkalmazásbeállítás PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

Ha kész, kattintson a **Mentés**gombra.

## <a name="install-memcache-wordpress-plugin"></a>A Memcache WordPress beépülő modul telepítése
> [!NOTE]
> A Wordpress.org webhelyről letöltheti a [Memcached Object Cache beépülő modult](https://wordpress.org/plugins/memcached/).
> 
> 

A WordPress beépülő modulok oldalon kattintson az **Új hozzáadása** lehetőségre.

![WordPress beépülő modul oldal](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

A keresőmezőbe írja be a **memcached** kifejezést, és nyomja le az **Enter** billentyűt.

![WordPress Új hozzáadása beépülő modul](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Keresse meg a **Memcached Object Cache** elemet a listában, majd kattintson a**Telepítés most** lehetőségre.

![WordPress – A Memcache telepítése beépülő modul](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### <a name="enable-the-memcache-wordpress-plugin"></a>A Memcache WordPress beépülő modul engedélyezése
> [!NOTE]
> A Visual Studio Team Services telepítéséhez kövesse a blog útmutatását: [Webhelybővítmény engedélyezése a Web Apps szolgáltatásban][8].
> 
> 

A `wp-config.php` fájlban adja hozzá az alábbi kódot a szerkesztésbefejezési megjegyzés fölött, majdnem a fájl végénél.

```php
$memcached_servers = array(
    'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

Ha beillesztette ezt a kódot, a monaco automatikusan menti a dokumentumot.

A következő lépés az objektum-gyorsítótár beépülő modul engedélyezése. Ezt úgy teheti meg, ha az **object-cache.php** fájlt áthúzza a **wp-content/plugins/memcached** mappából a **wp-content** mappába, és így engedélyezi a Memcached Object Cache működését.

![A memcache object-cache.php beépülő modul megkeresése](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

Most, hogy az **object-cache.php** fájl a **wp-content** mappában van, a Memcached Object Cache engedélyezve van.

![A memcache object-cache.php beépülő modul engedélyezése](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## <a name="verify-the-memcache-object-cache-plugin-is-functioning"></a>A Memcache Object Cache beépülő modul működésének ellenőrzése
A webalkalmazások Memcache-segédkódjának engedélyezéséhez szükséges összes lépést elvégezte. Egy dolog maradt: ellenőrizni kell, hogy az adatok bekerülnek-e a Redis Cache példányába.

### <a name="enable-the-non-ssl-port-support-in-azure-redis-cache"></a>Nem SSL portok támogatása az Azure Redis Cache-ben
> [!NOTE]
> A cikk írásának pillanatában a Redis CLI nem támogatja az SSL-kapcsolatot, így végre kell hajtani a következő lépéseket.
> 
> 

Az Azure Portalon keresse meg a webalkalmazáshoz létrehozott Redis Cache példányt. Ha a gyorsítótár panelje meg van nyitva, kattintson a **Beállítások** ikonra.

![Azure Redis Cache beállításgomb](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

A listáról válassza a **Hozzáférési portok** lehetőséget.

![Azure Redis Cache hozzáférési port](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Kattintson a **Nem** lehetőségre a **Hozzáférés engedélyezése csak SSL-en keresztül** beállításnál.

![Azure Redis Cache hozzáférési port csak SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

Látni fogja, hogy most a NEM SSL port van beállítva. Kattintson a **Save** (Mentés) gombra.

![Azure Redis Cache Redis hozzáférési portál nem SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### <a name="connect-to-azure-redis-cache-from-redis-cli"></a>Az Azure Redis Cache elérése a Redis CLI-ről
> [!NOTE]
> A lépés feltételezi, hogy a Redis helyben telepítve van a fejlesztési számítógépén. [A Redist ezen utasításokat követve telepítheti helyben][9].
> 
> 

Nyissa meg az Önnek megfelelő parancssori konzolt, és írja be az alábbi parancsot:

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

Cserélje ki a **&lt;hostname-for-redis-cache&gt;** elemet a tényleges xxxxx.redis.cache.windows.net gazdanévre, és a **&lt;primary-key-for-redis-cache&gt;** elemet a gyorsítótár hozzáférési kulcsára, majd nyomja le az **Enter** billentyűt. Ha a CLI csatlakozott a Redis Cache példányhoz, adjon ki egy redis parancsot. Az alábbi képernyőképen a kulcsok listázását választottam.

![Az Azure Redis Cache elérése a Redis CLI-ről a Terminálon](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

A kulcsok listázását kérő hívásnak értéket kell visszaadnia. Ha nem, lépjen ismét a webalkalmazáshoz, és próbálja meg újra.

## <a name="conclusion"></a>Összegzés
Gratulálunk! A WordPress alkalmazás mostantól központosított memórián belüli gyorsítótárral segít a teljesítmény növelésében. Ne feledje, a webalkalmazások Memcache-segédkódja bármilyen Memcache-ügyféllel használható, függetlenül a programnyelvtől vagy az alkalmazás keretrendszerétől. Ha visszajelzést szeretne küldeni, vagy kérdése van a Web Apps Memcache-segédkódjáról, küldje el azt az [MSDN Forums][10] vagy a [Stackoverflow][11] fórumra.

> [!NOTE]
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra, ahol azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="whats-changed"></a>A változások
* Információk a Websites szolgáltatásról az App Service-re való váltásról: [Az Azure App Service és a hatása a meglévő Azure-szolgáltatásokra](http://go.microsoft.com/fwlink/?LinkId=529714)

[0]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: /powershell/azureps-cmdlets-docs
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org



<!--HONumber=Jan17_HO4-->


