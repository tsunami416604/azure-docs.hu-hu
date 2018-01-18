---
title: "Webalkalmazások konfigurálása az Azure App Service-ben"
description: "A webes alkalmazás beállítása az Azure App Service szolgáltatások"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.openlocfilehash: 3f735b8742f22110b4de264bfb5661651a899afb
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="configure-web-apps-in-azure-app-service"></a>Webalkalmazások konfigurálása az Azure App Service-ben

Ez a témakör ismerteti, hogyan konfigurálhatja a web app használatával a [Azure Portal].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Alkalmazásbeállítások
1. Az a [Azure Portal], nyissa meg a webalkalmazás a panelt.
3. Válassza az **Alkalmazásbeállítások** elemet.

![Alkalmazásbeállítások][configure01]

A **Alkalmazásbeállítások** panel több kategóriák szerint csoportosítva beállításokkal rendelkezik.

### <a name="general-settings"></a>Általános beállítások
**Keretrendszer-verziók**. Állítsa be ezeket a beállításokat, ha az alkalmazás használja, minden ezen keretrendszerek: 

* **.NET-keretrendszer**: állítsa be a .NET-keretrendszer verziója. 
* **A PHP**: állítsa be a PHP verzióját vagy **OFF** PHP letiltása. 
* **Java**: válassza ki a Java verzióját vagy **OFF** Java letiltása. Használja a **webes tároló** lehetőséget választhat a Tomcat- és Jetty-verziót.
* **Python**: Jelölje ki a Python-verziót, vagy **OFF** Python letiltása.

Technikai okokból Java az alkalmazás engedélyezése letiltja a .NET, PHP és Python beállításokat.

<a name="platform"></a>
**Platform**. Választja ki, hogy a webalkalmazás fut, 32 bites vagy 64 bites környezetben. A 64 bites környezet Basic vagy Standard módot igényel. Ingyenes, és megosztott mód mindig 32-bit-es környezetben fusson.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Webes szoftvercsatornák**. Állítsa be **ON** ahhoz, hogy a WebSocket protokoll; például, ha a webes alkalmazás használ [ASP.NET SignalR] vagy [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Always On**. Alapértelmezés szerint a webalkalmazások a memóriából, ha bizonyos ideig inaktív. Ez lehetővé teszi, hogy a rendszer, ezért az erőforrások megőrzése. Basic vagy Standard módban, ahol engedélyezheti **mindig a** az alkalmazás mindig betöltve a folyamatosan. Ha az alkalmazás fut a folyamatos webjobs-feladatok webjobs-feladatok futtatása indított CRON-kifejezés használatával, vagy engedélyezze **mindig a**, vagy a webes feladatok nem futtatható megbízhatóan.

**Felügyelt folyamatkezelési verzió**. Beállítja az IIS [folyamatkezelési mód]. Ne módosítsa integrált (alapértelmezett) kivéve, ha egy örökölt alkalmazás, amely az IIS egy régebbi verziója szükséges.

**Az automatikus felcserélés**. Ha engedélyezte az automatikus felcserélés egy üzembe helyezési tárhelyet, App Service lesz automatikusan felcserélni a webalkalmazás éles környezetben ha egy frissítés leküldése a tárolóhely. További információkért lásd: [központi telepítése az átmeneti üzembe helyezési ponti az Azure App Service web Apps](web-sites-staged-publishing.md).

### <a name="debugging"></a>Hibakeresés
**Távoli hibakeresés**. Távoli hibakeresésének engedélyezése. Ha engedélyezve van, használhatja a távoli hibakereső a Visual Studio közvetlenül kapcsolódni a webes alkalmazást. Távoli hibakeresés továbbra is engedélyezett marad 48 órán keresztül. 

### <a name="app-settings"></a>Alkalmazásbeállítások
Ez a szakasz olyan név/érték párok, amelyek a webalkalmazás a kezdőlapon tölti fel. 

* .NET-alkalmazások esetén ezek a beállítások vannak be a nézetmodellbe, a .NET-konfiguráció `AppSettings` futásidőben, felülírva a meglévő beállításokat. 
* A PHP, Python, Java és csomópont alkalmazások férhetnek hozzá ezeket a beállításokat a környezeti változók futásidőben. Minden egyes alkalmazás-beállítás két környezeti változók jönnek létre; egy alkalmazás beállítási bejegyzésre, és egy másik APPSETTING_ előtaggal rendelkező által a megadott néven. Is tartalmaznak ugyanarra az értékre.

### <a name="connection-strings"></a>Kapcsolati karakterláncok
Kapcsolati karakterláncok kapcsolt erőforrásokban. 

.NET-alkalmazások esetén a kapcsolati karakterláncok vannak be a nézetmodellbe, a .NET-konfiguráció `connectionStrings` beállításokat futásidőben, felülírva a meglévő bejegyzéseket, ahol a kulcs értéke a hivatkozott adatbázis neve. 

A PHP, Python, Java és csomópont alkalmazások ezek a beállítások használhatók környezeti változók futásidőben, a kapcsolat típusa előtagként. A környezeti változó előtagok a következők: 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL-adatbázis:`SQLAZURECONNSTR_`
* Egyéni:`CUSTOMCONNSTR_`

Például, ha a MySql-kapcsolati karakterlánc lett nevű `connectionstring1`, akkor elérhetőek a környezeti változó `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Alapértelmezett dokumentumok
Az alapértelmezett dokumentum egy a weblap, akkor jelenik meg, a webhely a gyökér URL-címen.  A listán szereplő első egyező fájlok szolgál. 

Webalkalmazások használhatja a modulok, hogy útvonal URL-címe alapján. ahelyett, hogy nincs alapértelmezett dokumentum ilyen szolgáltató statikus tartalmat, ebben az esetben nincs.    

### <a name="handler-mappings"></a>Leírók leképezése
Ez a terület segítségével egyéni parancsfájl processzorok, a tanúsítványigénylések meghatározott fájlnév-kiterjesztések hozzáadása. 

* **Bővítmény**. A fájl kiterjesztése például *.php vagy handler.fcgi kezelni. 
* **Parancsfájl-feldolgozó elérési útja**. A parancsprogram-feldolgozó abszolút elérési utat. A parancsprogram-feldolgozó által a fájlokat, amelyek megfelelnek a fájlnévkiterjesztés kérelmek dolgoz fel. Az elérési utat használja `D:\home\site\wwwroot` utal, hogy az alkalmazás gyökérkönyvtárában.
* **További argumentumok**. Parancssori argumentumokat használni a parancsprogram-feldolgozó 

### <a name="virtual-applications-and-directories"></a>Virtuális alkalmazások és könyvtárak
Konfigurálja a virtuális alkalmazások és könyvtárak, adja meg az egyes virtuális könyvtárakat és az annak megfelelő fizikai elérési út a webhely gyökeréhez viszonyítva. Ha, akkor jelölje be a **alkalmazás** jelölőnégyzetet, hogy egy alkalmazás egy virtuális könyvtárat.

## <a name="enabling-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése
Diagnosztikai naplók engedélyezése:

1. A webalkalmazás panelen kattintson **összes beállítás**.
2. Kattintson a **diagnosztikai naplók**. 

Diagnosztikai naplók írása egy webalkalmazás, amely támogatja a naplózási beállítások: 

* **Alkalmazásnaplózás**. A fájlrendszer alkalmazásnaplók ír. A naplózás 12 óra ideig tart. 

**Szint**. Ha alkalmazás-naplózás engedélyezve van, ezzel a beállítással megadhatja, amely lesz adatmennyiség rögzített (hiba, figyelmeztetés, információ, vagy a részletes).

**Webkiszolgáló naplózásának**. A W3C bővített naplófájlformátum naplók lesznek mentve. 

**A részletes hibaüzeneteket**. Menti a hiba részleteit a .htm fájl üzenetek. A fájlok a /LogFiles/DetailedErrors kerülnek mentésre. 

**Sikertelen kérelmek követésének**. Naplók sikertelen kérelmek XML-fájlok számára. A fájlok fájl/naplófájlok/W3SVC*xxx*, ahol a xxx egyedi azonosítója. Ez a mappa tartalmaz egy XSL-fájlt és egy vagy több XML-fájlokat. Ügyeljen arra, hogy az XSL-fájl letöltése, mert formázására és az XML-fájlok tartalmát szűrés funkciókat biztosít.

A naplófájlban, létre kell hoznia FTP hitelesítő adatokat, az alábbiak szerint:

1. A webalkalmazás panelen kattintson **összes beállítás**.
2. Kattintson a **üzembe helyezési hitelesítő adatok**.
3. Adjon meg egy felhasználónevet és jelszót.
4. Kattintson a **Save** (Mentés) gombra.

![Telepítési hitelesítő adatok beállítása][configure03]

A teljes FTP-felhasználó neve "app\username" hol *app* a webes alkalmazás neve. A felhasználónév, szerepel a webalkalmazás panelen, a **Essentials**.

![FTP telepítési hitelesítő adatok][configure02]

## <a name="other-configuration-tasks"></a>Egyéb konfigurációs feladatok
### <a name="ssl"></a>SSL
Basic vagy Standard módban SSL-tanúsítványokat az egyéni tartományt is feltölthet. További információ: [HTTPS engedélyezése az webalkalmazáshoz]. 

A feltöltött tanúsítványok megtekintéséhez kattintson **összes beállítás** > **egyéni tartományok és SSL**.

### <a name="domain-names"></a>Tartománynevek
A webalkalmazás egyéni tartományneveket adhat hozzá. További információ: [egy webalkalmazást az Azure App Service szolgáltatásban az egyéni tartománynév konfigurálása].

A tartománynevek megtekintéséhez kattintson **összes beállítás** > **egyéni tartományok és SSL**.

### <a name="deployments"></a>Központi telepítés
* Folyamatos üzembe helyezést beállítani. Lásd: [Git használatával is telepíthet webalkalmazásokat az Azure App Service](app-service-deploy-local-git.md).
* Üzembe helyezési pontok. Lásd: [előkészítési környezetek telepítése az Azure App Service Web Apps].

Az üzembe helyezési megtekintéséhez kattintson **összes beállítás** > **üzembe helyezési**.

### <a name="monitoring"></a>Figyelés
Basic vagy Standard módban tesztelheti a HTTP vagy HTTPS-végpontokkal, legfeljebb három földrajzilag elosztott helyekről rendelkezésre állását. A figyelési teszt sikertelen, ha a HTTP válaszkódot (4xx vagy 5xx) hiba vagy a választ több mint 30 másodpercet vesz igénybe. A végpont tekinthető érhető el, ha a figyelési tesztek sikeres legyen, az összes meghatározott helyeiről. 

További információkért lásd: [Útmutató: webes végpont állapotának figyelése].

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása] oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

## <a name="next-steps"></a>További lépések
* [Egyéni tartománynév konfigurálása az Azure App Service-ben]
* [HTTPS engedélyezése az alkalmazásoknak az Azure App Service-ben]
* [A webalkalmazás skálázása az Azure App Service-ben]
* [Az Azure App Service Web Apps figyelési alapjai]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Egyéni tartománynév konfigurálása az Azure App Service-ben]: ./app-service-web-tutorial-custom-domain.md
[Az Azure App Service Web Apps előkészítési környezetek telepítése]: ./web-sites-staged-publishing.md
[HTTPS engedélyezése az alkalmazásoknak az Azure App Service-ben]: ./app-service-web-tutorial-custom-ssl.md
[Útmutató: webes végpont állapotának figyelése]: http://go.microsoft.com/fwLink/?LinkID=279906
[Az Azure App Service Web Apps figyelési alapjai]: ./web-sites-monitor.md
[feldolgozási sor üzemmódban]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[A webalkalmazás skálázása az Azure App Service-ben]: ./web-sites-scale.md
[Az App Service kipróbálása]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
