---
title: – Az Azure App Service-alkalmazások konfigurálása
description: Az Azure App Service-alkalmazás konfigurálása
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fb8dedac8b795ec127d7b4a14728d73c9397a1dd
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807405"
---
# <a name="configure-apps-in-azure-app-service"></a>Alkalmazások konfigurálása az Azure App Service-ben

Ez a témakör azt ismerteti, hogyan konfigurálhatja egy webalkalmazás, a mobil háttérrendszer vagy az API-alkalmazás használatával az [Azure Portal].

## <a name="application-settings"></a>Alkalmazásbeállítások
1. Az a [Azure Portal], az alkalmazás paneljének megnyitásához.
2. Válassza az **Alkalmazásbeállítások** elemet.

![Alkalmazásbeállítások][configure01]

A **Alkalmazásbeállítások** panel alatt különböző kategóriákba vannak csoportosítva beállításokkal rendelkezik.

### <a name="general-settings"></a>Általános beállítások
**Keretrendszer-verziókat**. Állítsa be ezeket a beállításokat, ha az alkalmazás használja, minden ezek a keretrendszerek: 

* **.NET-keretrendszer**: Állítsa be a .NET-keretrendszer verzióját. 
* **PHP**: A PHP-verzió beállítása vagy **OFF** PHP letiltása. 
* **Java**: Válassza ki a Java-verzió vagy **OFF** Java letiltásához. Használja a **webes tároló** választhat a Tomcat és a Jetty-verziót.
* **Python**: Válassza ki a Python-verzió vagy **OFF** Python letiltása.

Technikai okokból Java engedélyezése az alkalmazás letiltja a .NET, PHP és Python-beállításokat.

<a name="platform"></a>
**Platform**. Kiválasztja az alkalmazás 32 bites vagy 64-bit-es környezetben fut-e. A 64 bites környezet alapszintű vagy Standard csomag szükséges. Az ingyenes és közös szint mindig 32-bit-es környezetben futnak.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Webes szoftvercsatornák**. Állítsa be **ON** a WebSocket protokoll; például, ha az alkalmazás által használt [ASP.NET SignalR] vagy [a Socket.IO kódtár](https://socket.io/).

<a name="alwayson"></a>
**Always On**. Alapértelmezés szerint alkalmazások törlődnek a memóriából, ha bizonyos ideig inaktív azok. Ez lehetővé teszi, hogy a rendszer erőforrások megőrzése. Alap vagy Standard módban engedélyezheti **Always On** , hogy az alkalmazás betöltése a folyamatosan. Ha az alkalmazás fut a folyamatos webjobs-feladatok vagy futtatása webjobs-feladatok által aktivált egy CRON-kifejezés használatával, akkor engedélyezze a **Always On**, vagy a webjobs-feladatok futása nem megbízható.

**Kezelt Folyamatverzió**. Beállítja az IIS [folyamatkezelési mód]. Hagyja meg a set integrált (alapértelmezett), kivéve, ha egy örökölt alkalmazás, amely az IIS egy régebbi verziója szükséges.

**HTTP-verzió**. Állítsa be **2.0** támogatásának engedélyezéséhez [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokollt. 

> [!NOTE]
> Legtöbb modern böngésző támogatja a HTTP/2 protokoll TLS protokollon keresztüli csak, miközben továbbra is használja a HTTP/1.1-es nem titkosított forgalmat. Győződjön meg arról, hogy az ügyfél böngészők csatlakoznia kell a HTTP/2, alkalmazások vagy [egy App Service-tanúsítvány vásárlása](web-sites-purchase-ssl-web-site.md) az alkalmazás egyéni tartomány vagy [harmadik féltől származó tanúsítvány kötése](app-service-web-tutorial-custom-ssl.md).

**Az ARR-affinitás**. Az alkalmazás, amely horizontálisan a Virtuálisgép-példányok, az ARR-affinitás cookie-k biztosítása, hogy az ügyfél lesz irányítva a munkamenet élettartamára ugyanezen példányában. Állapot nélküli alkalmazások teljesítményének javítása érdekében ehhez a beállításhoz **ki**.   

**Automatikus felcserélés**. Ha engedélyezi az automatikus felcserélés egy üzembe helyezési ponthoz, az App Service lesz automatikusan felcserélni az alkalmazás éles környezetbe amikor leküld egy frissítést, hogy a tárolóhely. További információkért lásd: [üzembe helyezés előkészítési ponton az Azure App Service-alkalmazások](deploy-staging-slots.md).

### <a name="debugging"></a>Hibakeresés
**Távoli hibakeresés**. Lehetővé teszi a távoli hibakeresésről. Ha engedélyezve van, használhatja a távoli hibakeresőt a Visual Studióban közvetlenül kapcsolódni az alkalmazáshoz. Távoli hibakeresés továbbra is engedélyezett marad 48 órán át. 

### <a name="app-settings"></a>Alkalmazásbeállítások
Ebben a szakaszban be az alkalmazás betölti a Start menüben név-érték párokat tartalmaz. 

* A .NET-alkalmazások esetén ezek a beállítások elhelyezte a .NET-konfiguráció `AppSettings` futásidőben, felülírva a meglévő beállításokat. 
* Az App Service Linux vagy a Web App for Containers, ha a beágyazott json struktúra neve például `ApplicationInsights:InstrumentationKey` rendelkeznie kell `ApplicationInsights__InstrumentationKey` kulcs neveként. Ezért figyelje meg, hogy bármely `:` kell helyettesíteni `__` (azaz dupla aláhúzásjelet lehet).
* A PHP, Python, Java és csomópont alkalmazásai is elérhetik ezeket a beállításokat a környezeti változók futásidőben. Az egyes alkalmazás-beállításoknál két környezeti változó jön létre; az egyik az alkalmazás beállítás-bejegyzést, és egy másik APPSETTING_ előtaggal rendelkező által megadott névvel. Mindkettő ugyanazt az értéket tartalmazza.

Alkalmazásbeállítások mindig titkosított tárolt (titkosított inaktív).

A Key Vault használatával App beállításai feloldható [Key Vault hivatkozik](app-service-key-vault-references.md).

### <a name="connection-strings"></a>Kapcsolati sztringek
Kapcsolati karakterláncok társított erőforrások. 

A .NET-alkalmazások esetén ezek a kapcsolati karakterláncok elhelyezte a .NET-konfiguráció `connectionStrings` beállításokat futásidőben, felülírva a meglévő bejegyzést, ahol a kulcs megegyezik-e a társított adatbázis neve. 

A PHP, Python, Java és a Node-alkalmazások ezeket a beállításokat futásidőben, a kapcsolat típusa előtaggal környezeti változókként érhetők el lesz. A környezeti változó előtagok a következők: 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL-adatbázis: `SQLAZURECONNSTR_`
* Egyéni: `CUSTOMCONNSTR_`

Például, ha egy MySql-kapcsolati karakterlánc lett nevű `connectionstring1`, azt szeretné, a környezeti változó keresztül érhetők el `MYSQLCONNSTR_connectionString1`.

Kapcsolati karakterláncok mindig titkosított tárolt (titkosított inaktív).

Kapcsolati karakterláncok feloldható a Key Vault használatával [Key Vault hivatkozik](app-service-key-vault-references.md).

### <a name="default-documents"></a>Alapértelmezett dokumentumok
Az alapértelmezett dokumentum a weblap, amelyen egy webhely gyökérszintű URL-címen jelenik meg.  A lista első egyeztetési fájlt használja. 

Alkalmazások modult, hogy útvonal URL-cím alapján. ahelyett, hogy szolgáltató statikus tartalmat, ebben az esetben nincs ilyen nincs alapértelmezett dokumentum előfordulhat, hogy használja.    

### <a name="handler-mappings"></a>Leírók leképezése
Ez a terület használatával adja hozzá az egyéni szkriptek processzorok az adott kérések kezelésére. 

* **Bővítmény**. A fájl kiterjesztése például *.php vagy handler.fcgi kezelni. 
* **Processzor elérési útja parancsfájl**. A parancsprogram-feldolgozó abszolút elérési útja. A parancsprogram-feldolgozó fogja feldolgozni, fájlok, amelyek megfelelnek a fájlkiterjesztés kérelmeket. Az elérési utat használja `D:\home\site\wwwroot` , tekintse meg az alkalmazás gyökérkönyvtárában.
* **További argumentumok**. Parancssori argumentumokat használni a parancsprogram-feldolgozó 

### <a name="virtual-applications-and-directories"></a>Virtuális alkalmazások és könyvtárak
Konfigurálja a virtuális alkalmazások és könyvtárak, adja meg minden egyes virtuális könyvtár és az annak megfelelő fizikai elérési út a webhely gyökeréhez viszonyítva. Másik lehetőségként kiválaszthatja a **alkalmazás** jelölőnégyzetet, hogy egy alkalmazás egy virtuális könyvtárat.

## <a name="enabling-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése
Diagnosztikai naplók engedélyezése:

1. Az alkalmazás paneljén kattintson **minden beállítás**.
2. Kattintson a **Diagnosztikai naplók** elemre. 

Diagnosztikai naplók írása egy webalkalmazás, amely támogatja a naplózási beállítások: 

* **Alkalmazásnaplózás**. A fájlrendszer a naplófájlba írja az alkalmazásnaplókat. A naplózás 12 órán át aktiválást. 

**Szint**. Ha az alkalmazásadatok naplózása engedélyezve van, ezzel a beállítással megadhatja, kívánt adatok mennyiségétől (hiba, figyelmeztetés, információ vagy részletes) rögzíti.

**Webkiszolgálói naplózás**. A W3C bővített naplófájlformátum naplók lesznek mentve. 

**A részletes hibaüzeneteket**. Menti a hiba részletei üzenetek .htm fájl. A fájlok /LogFiles/DetailedErrors alapján kerülnek mentésre. 

**Sikertelen kérelmek követésének**. Naplók sikertelen kérelmeket, XML-fájlok. A fájlok kerülnek mentésre alatt/LogFiles/W3SVC*xxx*, ahol a xxx egyedi azonosítója. Ez a mappa tartalmaz egy XSL-fájl és a egy vagy több XML-fájlt. Ügyeljen arra, hogy töltse le a XSL-fájlt, mert funkciókat biztosít a formázás és szűrés az XML-fájlok tartalmát.

Naplófájljainak megtekintéséhez, létre kell hoznia az FTP-hitelesítő adatokat, a következő:

1. Az alkalmazás paneljén kattintson **minden beállítás**.
2. Kattintson a **üzembe helyezési hitelesítő adatok**.
3. Adjon meg egy felhasználónevet és jelszót.
4. Kattintson a **Save** (Mentés) gombra.

![Telepítési hitelesítő adatok beállítása][configure03]

Az FTP-felhasználó teljes neve "app\username" hol *alkalmazás* az alkalmazás neve. A felhasználónév alatt szerepel a panelén **Essentials**.

![FTP telepítési hitelesítő adatok][configure02]

## <a name="other-configuration-tasks"></a>Egyéb konfigurációs feladatok
### <a name="ssl"></a>SSL
Alap vagy Standard módban feltölthet egy egyéni tartomány SSL-tanúsítványok. További információkért lásd: [HTTPS engedélyezése az alkalmazás](app-service-web-tutorial-custom-ssl.md). 

A feltöltött tanúsítványok megtekintéséhez kattintson **minden beállítás** > **egyéni tartományok és SSL**.

### <a name="domain-names"></a>Tartománynevek
Az alkalmazás egyéni tartományneveket adhat hozzá. További információkért lásd: [egy alkalmazás egy egyéni tartománynév konfigurálása az Azure App Service](app-service-web-tutorial-custom-domain.md).

A tartománynevek megtekintéséhez kattintson **minden beállítás** > **egyéni tartományok és SSL**.

### <a name="deployments"></a>Központi telepítés
* Folyamatos üzembe helyezés beállítása. Lásd: [Git használatával az Azure App Service-alkalmazások telepítéséhez](deploy-local-git.md).
* Üzembe helyezési pontok. Lásd: [átmeneti környezetek, az Azure App Service üzembe helyezése].

Az üzembe helyezési pontok megtekintéséhez kattintson **minden beállítás** > **üzembe helyezési pontok**.

### <a name="monitoring"></a>Figyelés
Alap vagy Standard módban tesztelheti a rendelkezésre állási HTTP vagy HTTPS-végpontot, legfeljebb három földrajzilag elosztott helyekről. Egy figyelési teszt sikertelen, ha HTTP-válaszkódot (4xx vagy 5xx) hiba vagy a válasz több mint 30 másodperc. A végpont érhető el, ha a figyelési tesztek összes meghatározott helyeiről származó sikeres számít. 

További információkért lásd: [Útmutató: Webes végpont állapotának figyelése].

## <a name="next-steps"></a>További lépések
* [Egyéni tartománynév konfigurálása az Azure App Service-ben]
* [HTTPS engedélyezése az Azure App Service-alkalmazás]
* [Alkalmazások skálázása az Azure App Service-ben]
* [Megfigyelési alapismeretek az Azure App Service-ben]
* [ApplicationHost.xdt az applicationHost.config fájl beállításainak módosítása](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Egyéni tartománynév konfigurálása az Azure App Service-ben]: ./app-service-web-tutorial-custom-domain.md
[Átmeneti környezetek, az Azure App Service üzembe helyezése]: ./deploy-staging-slots.md
[HTTPS engedélyezése az Azure App Service-alkalmazás]: ./app-service-web-tutorial-custom-ssl.md
[Útmutató: Webes végpont állapotának figyelése]: https://go.microsoft.com/fwLink/?LinkID=279906
[Megfigyelési alapismeretek az Azure App Service-ben]: ./web-sites-monitor.md
[folyamatkezelési mód]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Alkalmazások skálázása az Azure App Service-ben]: ./web-sites-scale.md

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
