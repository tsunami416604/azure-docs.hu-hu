---
title: Az Azure App Service szolgáltatásban a Visual Studio használatával webalkalmazások hibáinak elhárítása
description: Ismerje meg, hogyan háríthatók el a távoli hibakeresés, a nyomkövetés és a naplózási eszközök a Visual Studio 2013 beépített Azure-webalkalmazás.
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: ''
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.openlocfilehash: 522bc9e757abeae930e47eaf53bb6da4bd9d0531
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224109"
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Az Azure App Service szolgáltatásban a Visual Studio használatával webalkalmazások hibáinak elhárítása
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan használható a Visual Studio-eszközök segítségével, egy webalkalmazás hibakeresése az [App Service-ben](http://go.microsoft.com/fwlink/?LinkId=529714),-ban történő futtatásával [hibakeresési módban](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) távolról vagy alkalmazásnaplókat és a webkiszolgáló naplóinak megtekintésével.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Az oktatóanyagból a következőket sajátíthatja el:

* Melyik Azure-alapú webes alkalmazás felügyeleti funkciók érhetők el a Visual Studióban.
* Gyors módosításokat végezhet a távoli webes alkalmazás a Visual Studio távoli nézete segítségével hogyan.
* Távolról közben a projekt hibakeresési módban futtatása az Azure-ban fut, egy webalkalmazás, és a webjobs-feladat.
* Hozza létre őket alkalmazáskövetési naplók létrehozása, és megtekintheti őket, amíg az alkalmazás.
* Megtekintése a webkiszolgáló naplófájljai, beleértve a részletes hibaüzeneteket, és a sikertelen kérelmek követésének.
* Hogyan lehet elküldeni a diagnosztikai naplók egy Azure Storage-fiók, és megtekintheti, őket.

Ha rendelkezik a Visual Studio Ultimate, akkor is használhatja [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) a hibakereséshez. IntelliTrace ebben az oktatóanyagban nem terjed ki.

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban működik együtt a fejlesztési környezet, a webes projekt és az Azure-webalkalmazást, hogy beállította- [Ismerkedés az Azure és az ASP.NET](app-service-web-get-started-dotnet-framework.md). A webjobs-feladatok szakaszokat kell az alkalmazást, amelyet létrehozott [az Azure WebJobs SDK használatának első lépései][GetStartedWJ].

Ebben az oktatóanyagban látható a Kódminták C# MVC webalkalmazás számára, de a hibaelhárítási eljárásokkal azonosak, Visual Basic és Web Forms-alkalmazások.

Az oktatóanyag feltételezi, hogy a Visual Studio 2017-et használ. 

Ez a funkció a folyamatos átviteli naplók csak akkor működik a .NET-keretrendszer 4-es vagy újabb készített alkalmazások.

## <a name="sitemanagement"></a>Webes alkalmazás konfigurálása és felügyelete
A Visual Studio egy részét a webes alkalmazás felügyeleti funkciók és a konfigurációs beállításai hozzáférést biztosít a [az Azure portal](http://go.microsoft.com/fwlink/?LinkId=529715). Ebben a szakaszban láthatja, mi érhető el a **Server Explorer**. A legújabb Azure-beli integrációs szolgáltatások megtekintéséhez próbálja ki **Cloud Explorer** is. Megnyithatja a windows a **nézet** menü.

1. Ha még nem jelentkezett be az Azure-bA a Visual Studióban, kattintson a jobb gombbal **Azure** , és válassza ki a csatlakozás **Microsoft Azure-előfizetéshez** a **Server Explorer**.

    Alternatív, hogy telepítsen egy felügyeleti tanúsítványt, amely lehetővé teszi a hozzáférést a fiókjához. Ha a tanúsítvány telepítését választja, kattintson a jobb gombbal a **Azure** csomópontja **Server Explorer**, majd válassza ki **kezelése és a szűrő előfizetések** a helyi menüben. Az a **kezelése a Microsoft Azure-előfizetések** párbeszédpanelen kattintson a **tanúsítványok** fülre, majd **importálás**. Az utasítások szerint töltse le és importálja az előfizetés fájl (más néven egy *.publishsettings* fájl) Azure-fiókját.

   > [!NOTE]
   > Ha egy előfizetés fájlt tölt le, mentse azt egy mappát a forrás kód címtárak (például a letöltések mappában) kívül, és törölje azt az importálás befejezése után. Egy rosszindulatú felhasználó fér hozzá az előfizetés-fájl szerkesztése, létrehozása és törlése az Azure-szolgáltatások.
   >
   >

    További információ a Visual studióból az Azure-erőforrások csatlakozik: [fiókok kezelése, előfizetések és rendszergazdai szerepkörök](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. A **Server Explorer**, bontsa ki a **Azure** csomópontot **App Service-ben**.
3. Bontsa ki az erőforráscsoportot, amely tartalmazza a létrehozott webalkalmazás [ASP.NET-webalkalmazás létrehozása az Azure-ban](app-service-web-get-started-dotnet-framework.md), majd kattintson a jobb gombbal a webes alkalmazás csomópontot, és kattintson a **nézetbeállítások**.

    ![A Server Explorer beállítások megjelenítése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    A **Azure Web App** lap jelenik meg, és ott a webes alkalmazás felügyeleti és konfigurációs műveleteket elérhető a Visual Studióban láthatja.

    ![Az Azure Web App ablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Ebben az oktatóanyagban a naplózás és nyomkövetés listákból fogja használni. Is használni kívánt távoli hibakeresés, de más módszert fogja használni az engedélyezéshez.

    Az alkalmazásbeállítások és a kapcsolati karakterláncok be ebben az ablakban kapcsolatos információkért lásd: [Azure Web Apps: alkalmazás-karakterláncok és a kapcsolati karakterláncok működése](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Ha szeretne egy web app feladatot, amely ebben az ablakban nem hajtható végre, kattintson a **nyissa meg a felügyeleti portálon** , nyisson meg egy böngészőablakot az Azure Portalra.

## <a name="remoteview"></a>Hozzáférés webes alkalmazás fájljai a Server Explorerben
Általában központi telepítése a webes projektet a `customErrors` jelző beállítása a Web.config fájlban `On` vagy `RemoteOnly`, ami azt jelenti, nem kap egy hasznos hiba jelenik meg, amikor valami merül fel. Sok hiba minden kap egy oldalt, például a következő eszközök egyikét:

**Kiszolgálóhiba a "/" alkalmazás:**

![Unhelpful hibalap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Hiba történt:**

![Unhelpful hibalap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**A webhely nem jeleníthető meg az oldal**

![Unhelpful hibalap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Gyakran a legegyszerűbben úgy, hogy a hiba okát, hogy engedélyezi a részletes hibaüzeneteket, amelyek az előző képernyőfelvételeken közül az első azt ismerteti, hogyan teheti. Amely követel meg a központilag telepített Web.config fájlban. Sikerült szerkeszteni a *Web.config* fájlt a projekt és ismételt üzembe helyezése a projekthez, vagy hozzon létre egy [Web.config átalakító](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) , és hogy hibakeresési buildet telepítsen, de gyorsabban: a **megoldáskezelő** , közvetlenül megtekintése és szerkesztése a távoli webes alkalmazásban fájlok használatával a *távoli nézet* funkció.

1. A **Server Explorer**, bontsa ki a **Azure**, bontsa ki a **App Service-ben**, és bontsa ki az erőforráscsoportot, amely a webes alkalmazás található, majd bontsa ki a csomópontot, a webalkalmazás számára.

    Láthatja, csomópontot, amely hozzáférést biztosít a webalkalmazás tartalmi fájlokat és naplófájlokat.
2. Bontsa ki a **fájlok** csomópontot, és kattintson duplán a *Web.config* fájlt.

    ![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio a Web.config fájl megnyitása a távoli webes alkalmazást, és bemutatja a [távoli] a címsorban látható a fájl neve melletti.
3. Adja hozzá a következő sort a `system.web` elem:

    `<customErrors mode="Off"></customErrors>`

    ![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Frissítse a böngészőt a unhelpful hibaüzenet jelenik, és most már például az alábbi példa egy részletes hibaüzenetet kap:

    ![Részletes hibaüzenet](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Adja hozzá a sort, hogy piros színnel jelenik meg a hiba létrejött *Views\Home\Index.cshtml*.)

A Web.config fájl szerkesztése csak egy példa forgatókönyv, amelyben lehetősége olvashatja és szerkesztheti a fájlokat az Azure-webalkalmazást a könnyebb legyen a hibaelhárítás.

## <a name="remotedebug"></a>Távoli hibakeresés web Apps alkalmazások
Ha a részletes hibaüzenet nem elegendő információt tartalmaznak, és nem lehet újra létrehozni a hiba helyileg, egy másik hibaelhárítása módja távoli hibakeresési módban futtatja. Állítson be töréspontokat, közvetlenül kezelheti a memória, végighaladhat a kód és a kódelérési út is módosíthatja.

Távoli hibakeresés a Visual Studio Express kiadásai nem működik.

Ez a szakasz bemutatja, hogyan segítségével távolról hoz létre a projekt hibakeresési [ASP.NET-webalkalmazás létrehozása az Azure-ban](app-service-web-get-started-dotnet-framework.md).

1. Nyissa meg a létrehozott web projektet [ASP.NET-webalkalmazás létrehozása az Azure-ban](app-service-web-get-started-dotnet-framework.md).

2. Nyissa meg *Controllers\HomeController.cs*.

3. Törölje a `About()` metódust, és insert, az alábbi kódot a helyére.

``` c#
public ActionResult About()
{
    string currentTime = DateTime.Now.ToLongTimeString();
    ViewBag.Message = "The current time is " + currentTime;
    return View();
}
```
4. [Állítson be egy töréspontot](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) a a `ViewBag.Message` sor.

5. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre, majd kattintson **közzététel**.

6. Az a **profil** legördülő listában válassza ugyanazt a használt profil [ASP.NET-webalkalmazás létrehozása az Azure-ban](app-service-web-get-started-dotnet-framework.md). Kattintson a beállítások.

7. Az a **közzététel** párbeszédpanelen kattintson a **beállítások** lapra, és módosítsa **konfigurációs** való **Debug**, és kattintson a  **Mentés**.

    ![Hibakeresési módban közzététele](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Kattintson a **Publish** (Közzététel) gombra. Üzembe helyezés befejeződik, és az Azure webalkalmazás URL-címét a böngészőben megnyílik a után zárja be a böngészőt.

9. A **Server Explorer**, kattintson jobb gombbal a webalkalmazásra, és kattintson **csatolja a hibakeresőt**.

    ![Hibakereső csatlakoztatása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    A böngésző automatikusan megnyitja a kezdőlap, az Azure-ban futó. Előfordulhat, hogy 20 másodpercet várjon, amíg Azure állítja be a kiszolgáló a hibakereséshez. Ez a késleltetés akkor fordul elő az első futtatásakor hibakeresési módban a webalkalmazás 48 órás időszakban. Amikor újra ugyanarra az időszakra vonatkozóan a hibakeresés, nincs késleltetés.

    > [!NOTE] 
    > Ha bármilyen problémája indítása a hibakeresőt, próbálja meg úgy teheti **Cloud Explorer** helyett **Server Explorer**.
    >

10. Kattintson a **kapcsolatos** menüjében.

     A Visual Studio leállítja a töréspontot, és a kód futtatása az Azure-ban, nem a helyi számítógépen.

11. A kurzort a `currentTime` változó az idő értékének megtekintéséhez.

     ![Azure-ban futó hibakeresési módban a nézet változó](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     Az idő, megjelenik az Azure-kiszolgáló ideje, lehet, mint a helyi számítógépen egy másik időzónában.

12. Adjon meg új értéket a `currentTime` változó, például az "Az Azure-ban most már fut".

13. Nyomja le az F5 billentyűt a folytatáshoz.

     Az Azure-ban futó névjegy oldal megjeleníti az új értéket, a currentTime változóhoz megadott.

     ![Megismerkedhet az új érték](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Távoli hibakeresés webjobs-feladatok
Ez a szakasz bemutatja, hogyan hibakeresése távolról használatával hoz létre a projektet és a web app [az Azure WebJobs SDK használatának első lépései](https://github.com/Azure/azure-webjobs-sdk/wiki).

Ebben a szakaszban látható funkciókat csak Visual Studio 2013 Update 4 vagy újabb verzió érhető el.

Csak a folyamatos webjobs-feladatok távoli hibakeresése működik. Ütemezett és igény szerinti WebJobs-hibakeresés nem támogatottak.

1. Nyissa meg a létrehozott web projektet [az Azure WebJobs SDK használatának első lépései][GetStartedWJ].

2. A ContosoAdsWebJob projektben nyissa meg a *Functions.cs*.

3. [Állítson be egy töréspontot](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) meg az első utasítás a `GnerateThumbnail` metódust.

    ![Töréspont beállítása](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. A **Megoldáskezelőben**, kattintson a jobb gombbal a webes projektre (nem a webjobs-feladat projekt), és kattintson a **közzététel**.

5. Az a **profil** legördülő listában válassza ugyanazt a használt profil [az Azure WebJobs SDK használatának első lépései](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Kattintson a **beállítások** lapra, és módosítsa **konfigurációs** való **Debug**, és kattintson a **közzététel**.

    A Visual Studio telepíti a webes és a Webjobs projekteket, és a böngésző megnyitja az Azure webalkalmazás URL-címét a.

7. A **Server Explorer**, bontsa ki a **Azure > App Service-ben > az erőforráscsoport > a webalkalmazás > webjobs-feladatok > folyamatos**, és kattintson a jobb gombbal **ContosoAdsWebJob**.

8. Kattintson a **Hibakereső csatlakoztatása**.

    ![Hibakereső csatlakoztatása](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    A böngésző automatikusan megnyitja a kezdőlap, az Azure-ban futó. Előfordulhat, hogy 20 másodpercet várjon, amíg Azure állítja be a kiszolgáló a hibakereséshez. Ez a késleltetés akkor fordul elő az első futtatásakor hibakeresési módban a webalkalmazás 48 órás időszakban. Amikor újra ugyanarra az időszakra vonatkozóan a hibakeresés, nincs késleltetés.

9. A webböngészőben, amely a Contoso Ads kezdőlap meg van nyitva hozzon létre egy új ad.

    Egy üzenetsor létrehozása, amely átveszi a webjobs-feladatot, és feldolgozott létrehozása egy ad okoz. Ha a WebJobs SDK meghívja a függvényt az üzenetsorban található üzenet, a kód a találatok feldolgozni a töréspont.

10. Amikor a hibakeresőt működésképtelenné válik, a töréspontot, vizsgálja meg, és változó értékeinek módosítása közben a program fut a felhőben. Az alábbi ábrán a hibakeresőt a blobInfo objektum számára átadott tartalmát jeleníti meg a `GenerateThumbnail` metódust.

     ![a hibakereső blobInfo objektum](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Nyomja le az F5 billentyűt a folytatáshoz.

     A `GenerateThumbnail` metódust végzett a létrehozással miniatűr.

12. Frissítse az Index lapot a böngészőben, és a miniatűr megjelenítéséhez.

13. A Visual Studióban nyomja le a SHIFT + F5 hibakeresés leállításához.

14. A **Server Explorer**, kattintson a jobb gombbal a ContosoAdsWebJob csomópontot, és kattintson a **irányítópult megtekintése**.

15. Jelentkezzen be Azure hitelesítő adataival, majd kattintson a webjobs-feladat neve, a webjobs-feladat a lap megnyitásához.

     ![Kattintson a ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Az irányítópult megjeleníti, hogy a `GenerateThumbnail` függvény végrehajtása a közelmúltban.

     (A legközelebb kattint **irányítópult megtekintése**, nem kell bejelentkezni, és a böngésző közvetlenül kerül az oldal a webjobs-feladat.)

16. Kattintson a függvény nevét, a függvény végrehajtási részleteinek megtekintéséhez.

     ![Függvény részletei](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Ha a függvény [okkal készítette el a naplókat](https://github.com/Azure/azure-webjobs-sdk/wiki), gombra kattintva **ToggleOutput** megtekintheti őket.

## <a name="notes-about-remote-debugging"></a>Távoli hibakeresés kapcsolatos megjegyzések

* Nem javasolt éles környezetben hibakeresési módban futtatja. Ha éles webalkalmazásra nem, több kiszolgálópéldány horizontálisan felskálázott, hibakeresés megakadályozza, hogy a webkiszolgáló egyéb kérésekre válaszol. Ha a webkiszolgálói példányok több, a hibakereső csatolása rendelkezik, egy véletlenszerű példányt kap, és hogy nem lehet annak érdekében, hogy böngésző későbbi kérelmeket ugyanazon nyissa meg. Is általában nem kell telepítenie a hibakeresési build az éles környezetben, és a kiadási buildek fordító optimalizálások előfordulhat, hogy lehetetlenné mi soronként történik a forráskód megjelenítése. Éles problémák elhárításához, a legjobb erőforrás alkalmazás nyomkövetés és a web server-naplók.
* Elkerülése érdekében hosszú leállítja a töréspontok keresése, ha a távoli hibakeresés. Az Azure kezeli a folyamat, amely a nem válaszoló folyamatként néhány percnél hosszabb ideig le van állítva, és leállítja azt.
* Hibakeresés, során a kiszolgáló küld adatokat a Visual Studióba, amelyek hatással lehetnek a sávszélesség-költségek. Sávszélesség-használati díjat kapcsolatos információkért lásd: [Azure díjszabását](https://azure.microsoft.com/pricing/calculator/).
* Győződjön meg arról, hogy a `debug` attribútuma a `compilation` eleme a *Web.config* fájl igaz értékre. A változó értéke igaz, amikor közzéteszi a hibakeresési build konfigurációját alapértelmezés szerint.

``` xml
<system.web>
  <compilation debug="true" targetFramework="4.5" />
  <httpRuntime targetFramework="4.5" />
</system.web>
```
* Ha azt tapasztalja, hogy a hibakeresőt, amelyen hibakeresést végez, a kód nem lép, előfordulhat, hogy rendelkezik a csak saját kód beállításának módosításához.  További információkért lásd: [korlátozása csak saját kód a ke krokování](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).
* A kiszolgáló időmérő elindul, amikor engedélyezi a távoli hibakeresési funkciót, és 48 óra után a szolgáltatás automatikusan ki van kapcsolva. Biztonság és teljesítmény érdekében a 48 órás korlát, készen áll. A funkció azon tetszőleges számú alkalommal próbálkozhat, egyszerűen kapcsolhatja. Azt javasoljuk, hogy hagyja azokat le van tiltva, ha nincs aktív hibakeresés.
* A hibakereső manuálisan csatlakoztathat valamilyen folyamat, nem csak a webes alkalmazás folyamata (w3wp.exe). A Visual Studio hibakeresési mód használatával kapcsolatos további információkért lásd: [hibakeresés a Visual Studióban](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Diagnosztikai naplók áttekintése
ASP.NET-alkalmazás, amely futtatja az Azure-webalkalmazás naplóit a következő típusú hozhat létre:

* **Alkalmazás nyomkövetési naplók**<br/>
  Az alkalmazás a metódusok meghívásával hozza létre ezeket a naplókat a [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) osztály.
* **Webkiszolgáló-naplókkal**<br/>
  A webkiszolgáló minden HTTP-kérelem a webalkalmazás naplóbejegyzés hoz létre.
* **Részletes hibanaplókat üzenet**<br/>
  A webkiszolgáló egy HTML-oldalt hoz létre a sikertelen HTTP-kérelmek (állapotkód: 400 vagy nagyobb eredményező kérelmek) további információkat.
* **Sikertelen kérelmek nyomkövetési naplók**<br/>
  A webkiszolgáló a sikertelen HTTP-kérelmekre részletes nyomkövetési adatok XML-fájlt hoz létre. A webalkalmazás-kiszolgáló is biztosít egy XSL-fájl, formázhatja az XML-t egy böngészőben.

Naplózás WebApp teljesítményének, hatással van, így Azure engedélyezheti vagy tilthatja le a log különböző típusú, igény szerint lehetővé teszi. Az alkalmazásnaplók megadhatja, hogy csak egy bizonyos súlyossági szint fölé naplók kell írni. Amikor hoz létre egy új webalkalmazást, alapértelmezés szerint az összes naplózás le van tiltva.

Naplók írt fájlok a *LogFiles* mappa a fájlrendszerben a webalkalmazást és FTP-n keresztül hozzáférhető. Webkiszolgáló-naplókkal és alkalmazásnaplókat is írható egy Azure Storage-fiókhoz. A nagyobb mennyiségű lehet a fájlrendszer, a tárfiókban lévő naplókat őrizheti meg. Most már egy legfeljebb 100 MB-naplók, a fájlrendszer használatakor. (Csak a rövid távú megőrzési fájl rendszer naplókat is. Azure törli a régi naplófájlokat, hogy az újaknak, a korlát elérése után.)  

## <a name="apptracelogs"></a>Hozzon létre és alkalmazáskövetési naplók megtekintése
Ebben a szakaszban a következő feladatokat végezheti el:

* Nyomkövetési utasításokat hozzáadása a webes projekthez létrehozott [Ismerkedés az Azure és az ASP.NET](app-service-web-get-started-dotnet-framework.md).
* Tekintse át a naplókat, ha a projekt helyi futtatása.
* Tekintse át a naplókat, amikor azok jönnek létre az Azure-ban futó alkalmazás.

A webjobs-feladatok naplókat alkalmazás létrehozásával kapcsolatos további információk: [használata az Azure queue storage használata a WebJobs SDK - naplókat írásával](https://github.com/Azure/azure-webjobs-sdk/wiki). Naplók megtekintése és szabályozása a tárolásuk módja az Azure-ban az alábbi utasításokat webjobs-feladatok által létrehozott alkalmazásnaplókat is vonatkozik.

### <a name="add-tracing-statements-to-the-application"></a>Nyomkövetési utasításokat hozzáadni az alkalmazáshoz
1. Nyissa meg *Controllers\HomeController.cs*, és cserélje le a `Index`, `About`, és `Contact` kódot annak érdekében, hogy adja hozzá a következő módszerek `Trace` utasítások és a egy `using` nyilatkozata `System.Diagnostics`:

```c#
public ActionResult Index()
{
    Trace.WriteLine("Entering Index method");
    ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
    Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
    Trace.WriteLine("Leaving Index method");
    return View();
}

public ActionResult About()
{
    Trace.WriteLine("Entering About method");
    ViewBag.Message = "Your app description page.";
    Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
    Trace.WriteLine("Leaving About method");
    return View();
}

public ActionResult Contact()
{
    Trace.WriteLine("Entering Contact method");
    ViewBag.Message = "Your contact page.";
    Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
    Trace.WriteLine("Leaving Contact method");
    return View();
}        
```

2. Adjon hozzá egy `using System.Diagnostics;` utasítással a fájl elejéhez.

### <a name="view-the-tracing-output-locally"></a>A nyomon követési kimenetet helyileg megtekintése
1. Nyomja le az F5 billentyűt az alkalmazás hibakeresési módban futtatja.

    Az alapértelmezett nyomkövetés-figyelő az összes nyomkövetési ír a **kimeneti** együtt más hibakeresési kimeneti ablakban. Az alábbi ábrán a korábban hozzáadott nyomkövetési utasításokat a kimenetét a `Index` metódust.

    ![Hibakeresési ablakban nyomon követése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    A következő lépésekkel nyomkövetési megjelenítése egy weblap, a hibakeresési módban fordítása nélkül.
2. Nyissa meg az alkalmazás Web.config fájljában (a projekt mappájában található egy), és adjon hozzá egy `<system.diagnostics>` elem csak a Bezárás előtt a fájl végén `</configuration>` elem:

``` xml
<system.diagnostics>
<trace>
  <listeners>
    <add name="WebPageTraceListener"
        type="System.Web.WebPageTraceListener,
        System.Web,
        Version=4.0.0.0,
        Culture=neutral,
        PublicKeyToken=b03f5f7f11d50a3a" />
  </listeners>
</trace>
</system.diagnostics>
```

A `WebPageTraceListener` lehetővé teszi a megtekintését nyomkövetési kimeneti megkeresve `/trace.axd`.
3. Adjon hozzá egy <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">nyomkövetési elem</a> alatt `<system.web>` a Web.config fájlban, például az alábbi példában:

``` xml
<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
```       

4. Az alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.
5. Adja hozzá a böngészőablak címsorában, *trace.axd* az URL-címet, és nyomja le az ENTER billentyűt (az URL-cím hasonlít a http://localhost:53370/trace.axd).
6. Az a **Trasování Aplikace** kattintson **részleteinek megtekintése** az első sorban (nem a BrowserLink sor).

    ![Trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    A **kérelem részleteinek** lap jelenik meg, majd a a **nyomkövetési adatok** szakaszban láthatja a hozzáadott nyomkövetési utasításokat a kimenetét a `Index` metódus.

    ![Trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Alapértelmezés szerint `trace.axd` csak akkor érhető el helyileg. Ha szeretné a távoli webes alkalmazás elérhető legyen, hozzáadhat `localOnly="false"` , a `trace` eleme a *Web.config* fájljához a következő példában látható módon:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Azonban engedélyezése `trace.axd` egy éles webes biztonsági okokból nem ajánlott alkalmazás. A következő szakaszokban láthatja megkönnyítő olvassa el az Azure-webalkalmazás nyomkövetési naplóit.

### <a name="view-the-tracing-output-in-azure"></a>A nyomon követési kimenetet megtekintése az Azure-ban
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a webes projektre, és kattintson a **közzététel**.
2. Az a **Publish Web** párbeszédpanelen kattintson a **közzététel**.

    A Visual Studio közzéteszi a frissítést, megnyílik egy böngészőablak a kezdőlapra (feltéve, hogy nem törli **cél URL-címe** a a **kapcsolat** lap).
3. A **Server Explorer**, kattintson jobb gombbal a webalkalmazásra, és válassza ki **folyamatos átviteli naplók megtekintése**.

    ![Folyamatos átviteli naplók megtekintése a helyi menü](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    A **kimeneti** ablak mutatja, hogy a naplóstreamelési szolgáltatáshoz csatlakozik, és hozzáad egy értesítési sorban, ami megjeleníthető napló nélkül percenként.

    ![Folyamatos átviteli naplók megtekintése a helyi menü](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Az alkalmazás kezdőlapját megjelenítő böngészőablakban kattintson **Contact**.

    Néhány másodpercen belül a hibaszintet kimenete nyomkövetési amelyet hozzáadott a `Contact` metódus megjelenik a **kimeneti** ablak.

    ![Hiba történt a nyomkövetési kimeneti ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    A Visual Studio csak jelenik hibaszintet nyomkövetéseket, mert a szolgáltatás figyelésére napló engedélyezése esetén ez az alapértelmezett beállítás. Amikor létrehoz egy új Azure-webalkalmazást, az összes naplózás le van tiltva alapértelmezés szerint a beállítások lapon korábban megnyitásakor, mint:

    ![Kijelentkezés alkalmazás](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Azonban ha kiválasztott **folyamatos átviteli naplók megtekintése**, automatikusan megváltoztatta a Visual Studio **alkalmazás Logging(File System)** való **hiba**, ami azt jelenti, hogy hiba szintű naplók a jelentett beolvasása. Annak érdekében, hogy az összes a nyomkövetési naplók megtekintéséhez módosíthatja ezt a beállítást **részletes**. Amikor kiválaszt egy súlyossági szintje alacsonyabb, mint a hiba, az összes napló magasabb fontossági szintek is jelenti. Tehát amikor kiválasztja a részletes, is megjelenik információkat, a figyelmeztetési és a hibanaplók.  

1. A **Server Explorer**, kattintson jobb gombbal a webalkalmazásra, és kattintson **nézetbeállítások** ahogy korábban is tette.
2. Változás **Application Logging (fájlrendszer)** való **részletes**, és kattintson a **mentése**.

    ![Részletes nyomkövetési szint](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. A böngészőablakban, amely most már láthatók a **forduljon** kattintson **kezdőlap**, majd kattintson **kapcsolatos**, és kattintson a **forduljon**.

    Néhány másodpercen belül a **kimeneti** ablak megjeleníti az összes a nyomon követési kimenetet.

    !["Részletes" nyomkövetési kimeneti](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Ebben a szakaszban engedélyezve van, és le van tiltva a naplózás az Azure webalkalmazás-beállítások használatával. Is engedélyezése és letiltása nyomkövetési figyelői a Web.config fájl módosításával. Azonban a Web.config fájl módosítása újrahasznosítást, miközben a webalkalmazás konfigurációjának keresztül naplózás engedélyezése nem végez, amely alkalmazástartomány okoz. Ha a probléma reprodukálásának, hosszú időt vesz igénybe, vagy időszakos, a alkalmazástartomány újrahasznosítási előfordulhat, hogy "javítás", és annak kikényszerítéséhez, hogy Várjon, amíg újra jelentkezik. Diagnosztika engedélyezése az Azure lehetővé teszi a hibával kapcsolatos információk azonnal rögzítés alkalmazástartomány újrahasznosítási nélkül.

### <a name="output-window-features"></a>Kimeneti ablak Funkciók
A **a Microsoft Azure-naplók** lapján a **kimeneti** ablakoknál több gombok és a egy szövegbeviteli mező:

![Naplók gombok lapra](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Ezek hajtsa végre a következő funkciókat:

* Törölje a **kimeneti** ablak.
* Engedélyezi vagy letiltja a sortörés.
* Indítsa el vagy leállíthatja a figyelést a naplókat.
* Adja meg, amely naplózza a figyelése.
* Töltse le a naplókat.
* A keresési karakterláncot vagy egy reguláris kifejezés alapján naplók szűrése.
* Zárja be a **kimeneti** ablak.

Ha megad egy keresési karakterláncot vagy reguláris kifejezést, a Visual Studio szűri a naplózási információk az ügyfélen. Azt jelenti, hogy a feltételeket adhatja meg, után jelennek meg a naplókat a **kimeneti** ablakban és szűrési feltételeket módosíthatja újragenerálja a naplók nélkül.

## <a name="webserverlogs"></a>Webkiszolgáló-naplók megtekintése
Webkiszolgáló-naplókkal a webalkalmazáshoz tartozó összes HTTP-tevékenységet rögzíti. Annak érdekében, hogy azok a a **kimeneti** ablakban engedélyezze azokat a webalkalmazás és megadhatja a Visual Studióban, hogy szeretne-e megfigyelheti őket.

1. Az a **Azure webalkalmazás konfigurációjának** lapon, a megnyitott **Server Explorer**, módosítsa a Web Server naplózását **a**, és kattintson a **mentése**.

    ![Webkiszolgálói naplózás engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Az a **kimeneti** ablakban kattintson a **adja meg, amely a Microsoft Azure figyelése** gombra.

    ![Adja meg, amely az Azure figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Az a **Microsoft Azure-naplózás lehetőségeket** párbeszédpanelen jelölje ki **webalkalmazás-naplók**, és kattintson a **OK**.

    ![A figyelő webkiszolgáló-naplókkal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. A webalkalmazás megjelenítő böngészőablakban kattintson **kezdőlap**, majd kattintson a **kapcsolatos**, és kattintson a **ügyfél**.

    Az alkalmazásnaplók általában az első, a webkiszolgáló-naplókkal követ. Előfordulhat, hogy megjelenjenek a naplók várnia.

    ![Webkiszolgáló naplózza a kimeneti ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Alapértelmezés szerint amikor webkiszolgáló-naplók engedélyezésére a Visual Studio használatával Azure írja a naplókat a fájlrendszerben. Alternatív megoldásként az Azure portal segítségével adja meg a webkiszolgáló egy blobtárolót a tárfiókban lévő naplókat kell írni.

Ha a portál használatával engedélyezi a naplózási egy Azure storage-fiókot a webkiszolgálón, és tiltsa le a Visual Studióban, naplózás, ha újra engedélyezi a Visual Studio-naplózás a tárfiók-beállítások visszaállnak.

## <a name="detailederrorlogs"></a>Részletes hibanaplókat üzenet megtekintése
Részletes hibanaplókat bizonyos HTTP-kéréseket eredményező hiba válaszkódot (400 vagy újabb) további információt nyújtanak. Annak érdekében, hogy azok a a **kimeneti** ablakban kell engedélyezéséhez a web app, és mondja el a Visual Studióban, hogy szeretné-e megfigyelheti őket.

1. Az a **Azure webalkalmazás konfigurációjának** lapon, a megnyitott **Server Explorer**, módosítsa **részletes hibaüzenetek** való **a**, majd Kattintson a **mentése**.

    ![Részletes hibaüzenetek engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Az a **kimeneti** ablakban kattintson a **adja meg, amely a Microsoft Azure figyelése** gombra.

3. Az a **Microsoft Azure-naplózás lehetőségeket** párbeszédpanelen kattintson a **az összes napló**, és kattintson a **OK**.

    ![Az összes napló figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. A böngészőablak címsorában, adjon hozzá egy extra karakter 404-es hibát okozhat az URL-cím (például `http://localhost:53370/Home/Contactx`), és nyomja le az Enter billentyűt.

    Néhány másodperc elteltével megjelenik a hibával kapcsolatos részletes napló a Visual Studio **kimeneti** ablak.

    ![Részletes hibanapló - kimeneti ablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Szabályozhatja, és kattintson a hivatkozásra kattintva megtekintheti a böngészőben formátumú kimenet:

    ![Részletes hibanapló - böngészőablakot](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Töltse le a fájl rendszernaplók
Bármely figyelemmel kísérheti a naplók a **kimeneti** ablakban is letölthető, mint egy *.zip* fájlt.

1. Az a **kimeneti** ablakban kattintson a **folyamatos átviteli naplók letöltése**.

    ![Naplók gombok lapra](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Fájlkezelő megnyílik a *letölti* a letöltött fájlt a kiválasztott mappában.

    ![Letöltött fájl](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Bontsa ki a *.zip* fájlt, és tekintse meg a következő mappastruktúra:

    ![Letöltött fájl](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Alkalmazás nyomkövetési naplók *.txt* található fájlokat a *LogFiles\Application* mappát.
   * Webkiszolgáló-naplókkal szerepelnek *.log* található fájlokat a *LogFiles\http\RawLogs* mappát. Használhatja például egy eszköz [naplóelemző](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) megtekintésére és kezelésére, ezeket a fájlokat.
   * Részletes hibanaplókat üzenet szerepelnek *.html* található fájlokat a *LogFiles\DetailedErrors* mappát.

    (A *központi telepítések* mappa a következő közzétételi; verziókövetés által létrehozott fájlok nem rendelkezik a Visual Studio közzététel kapcsolódó egyik elemet sem. A *Git* mappa van a forráskezelőhöz kapcsolódó nyomok közzétételt és a log fájl adatfolyam-szolgáltatást.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="failedrequestlogs"></a>Sikertelen kérelmek nyomkövetési naplók megtekintése
Sikertelen kérelmek nyomkövetési naplók hasznosak, amikor szüksége van az IIS hogyan kezeli, az URL-cím újraírását vagy hitelesítési problémák – egyéb felhasználási helyzetek egy HTTP-kérés részleteinek megismeréséhez.

Az Azure web apps a sikertelen kérelmek nyomkövetésének funkcióit az IIS 7.0 és újabb verziói óta használja. Nem kell az IIS-beállítások, amelyek konfigurálása, hogy mely hibákat naplózza, azonban a hozzáférést. Ha engedélyezi a sikertelen kérelmek nyomkövetése, hibákat lesznek rögzítve.

A Visual Studio használatával engedélyezheti a sikertelen kérelmek nyomkövetése, de nem tekintheti meg őket a Visual Studióban. Ezek a naplók olyan XML-fájlok. A streamelési naplózási szolgáltatás csak olvasható egyszerű szöveges módban beállításkulcsoknak fájlok figyeli: *.txt*, *.html*, és *.log* fájlokat.

Egy böngészőben, közvetlenül az FTP-n keresztül, vagy helyileg egy FTP-eszköz használatával töltse le azokat a helyi számítógépre után megtekintheti a sikertelen kérelmek nyomkövetési naplóit. Ebben a szakaszban fog megtekintheti őket egy böngészőben közvetlenül.

1. Az a **konfigurációs** lapján a **Azure Web App** ablak, amely a megnyitott **Server Explorer**, módosítsa **sikertelen kérelmek nyomkövetését** , **a**, és kattintson a **mentése**.

    ![Sikertelen kérelmek követésének engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Egy extra karaktert ad hozzá az URL-címet, amely a webes alkalmazás látható a böngészőablak címsorában, és lenyomja az Enter billentyűt a 404-es hiba miatt.

    Ennek hatására a sikertelen kérelmek nyomkövetési napló létrehozni, és a következő lépések bemutatják, hogyan lehet megtekinteni vagy letölteni a napló.

3. A Visual Studióban az a **konfigurációs** lapján a **Azure Web App** ablakban kattintson a **nyissa meg a felügyeleti portál**.

4. Az a [az Azure portal](https://portal.azure.com) **beállítások** a webalkalmazás oldalára, kattintson **üzembe helyezési hitelesítő adatok**, majd adja meg egy új felhasználónevet és jelszót.

    ![Új FTP-felhasználónév és jelszó](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Amikor bejelentkezik, akkor a webalkalmazás neve elé, használja a teljes felhasználói nevét. Például ha "myid" Adjon meg egy felhasználónevet, és a hely "myexample", akkor jelentkezzen "myexample\myid".
    >

5. Egy új böngészőablakban nyissa meg az URL-címe alatt megjelenő **FTP-gazdagépnév** vagy **FTPS-állomásnév** a a **áttekintése** lap webalkalmazása számára.

6. Jelentkezzen be a korábban (beleértve a webes alkalmazás-előtagja a felhasználó nevét) létrehozott FTP hitelesítő adataival.

    A böngészőben a webalkalmazás gyökérmappájában jeleníti meg.

7. Nyissa meg a *LogFiles* mappát.

    ![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Nyissa meg a W3SVC és a egy numerikus érték nevű mappát.

    ![Nyissa meg a W3SVC mappa](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A mappában található hibaüzeneteket, miután engedélyezte a sikertelen kérelmek nyomkövetése naplózott XML-fájlok és a egy XSL-fájl, amellyel egy böngészőben az XML formátumban tartalmazza.

    ![W3SVC mappa](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Kattintson a sikertelen kérések, amelyek meg szeretné tekinteni a nyomkövetési adatok XML-fájljának.

    Az alábbi ábrán látható egy minta hiba történt a nyomkövetési adatok egy részét.

    ![Sikertelen kérelmek követésének böngészőben](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Következő lépések
Megtudhatta, hogyan Visual Studio megkönnyíti az Azure-alapú webes alkalmazás által létrehozott naplók megtekintéséhez. Az alábbi szakaszok nyújtanak további forrásokra mutató hivatkozásokat a kapcsolódó témakörök:

* Azure-alapú webes alkalmazás hibaelhárítása
* Hibakeresés a Visual Studióban
* Távoli hibakeresése az Azure-ban
* Az ASP.NET alkalmazásokban nyomon követése
* Webkiszolgáló-naplók elemzéséhez
* Kérelem nyomkövetési naplók elemzése nem sikerült.
* Felhőszolgáltatások hibakeresése

### <a name="azure-web-app-troubleshooting"></a>Azure-alapú webes alkalmazás hibaelhárítása
Az Azure App Service web Apps-alkalmazások hibaelhárításával kapcsolatos további információkért lásd a következőket:

* [Web Apps alkalmazások figyelése](/manage/services/web-sites/how-to-monitor-websites/)
* [Az Azure Web Apps szolgáltatásban a Visual Studio 2013 memóriavesztés kivizsgálása](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). A Microsoft ALM blogbejegyzés felügyelt memória problémák elemzése a Visual Studio funkcióit.
* [Az Azure web apps online eszközök kell tudni](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). A blogbejegyzés, Amit az Apple által.

Egy adott hibaelhárítási kérdés segítség elindítani egy szálat a következő fórumok egyikében:

* [Az Azure-fórum az ASP.NET-hely](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Az Azure-fórum az MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Hibakeresés a Visual Studióban
A Visual Studio hibakeresési mód használatával kapcsolatos további információkért lásd: [hibakeresés a Visual Studióban](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) és [tippek hibakeresés a Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Távoli hibakeresése az Azure-ban
Az Azure web apps és webjobs-feladatok távoli hibakereséssel kapcsolatban további információkért lásd a következőket:

* [Bevezetés a távoli hibakeresése az Azure App Service Web Apps](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Távoli hibakeresés az Azure App Service Web Apps 2. rész - távoli hibakeresés belül bemutatása](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Az Azure App Service Web Apps 3. rész - többpéldányos környezet és a GIT a távoli hibakeresés bemutatása](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs-hibakeresés (videó)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Ha a webalkalmazás az Azure webes API-t vagy a Mobile Services háttérbeli használ, és van szükség, hibakeresés, lásd: [.NET-háttérrendszer hibakeresés a Visual Studióban](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Az ASP.NET alkalmazásokban nyomon követése
Nincsenek nem naprakész, és alapos tudnivalókra az ASP.NET nyomkövetési elérhető az interneten. A legjobb teheti van Ismerkedés a Web Forms MVC nem még létezik, és az újabb blog kiegészíti, mert tesz közzé, amelyek a konkrét problémák írt régi bevezető anyagok. Néhány hasznos helyek a indítsa el a következő források:

* [Monitoring és Telemetria (valós felhőalapú alkalmazások létrehozása az Azure-ral)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  E-könyv fejezet ajánlásokkal nyomkövetés az Azure felhőalapú alkalmazásokat.
* [Az ASP.NET nyomkövetési](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Régi azonban továbbra is a leghasznosabb, az alapszintű bevezetést a tulajdonosnak.
* [Nyomkövetési figyelők](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Információ a nyomkövetési figyelői nem említik, de a [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Forgatókönyv: Az ASP.NET nyomkövetési integrálása System.Diagnostics nyomkövetés](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Ez a cikk is régi, de nem vonatkozik a bevezető cikkben további információkat tartalmaz.
* [Az ASP.NET MVC Razor nézetekben nyomon követése](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Nyomon követését a Razor-nézetekben, amellett a hozzászólás is hozzon létre egy olyan hiba szűrőt, és minden nem kezelt kivételek naplózásához az MVC alkalmazások módját ismerteti. Minden nem kezelt kivételek jelentkezzen be egy Web Forms-alkalmazást kapcsolatos információkért lásd: Global.asax példa [hibakezelők teljes példa](http://msdn.microsoft.com/library/bb397417.aspx) az MSDN Webhelyén. MVC vagy Web Forms bizonyos kivételek naplózásához, de lehetővé teszik az alapértelmezett keretrendszer kezelése gyakorolt hatását, ha akkor is feltárhatja és újbóli kiváltásához a következő példához hasonlóan:

``` c#
try
{
   // Your code that might cause an exception to be thrown.
}
catch (Exception ex)
{
    Trace.TraceError("Exception: " + ex.ToString());
    throw;
}
```

* [Streamelési diagnosztikai nyomkövetési naplózása az Azure parancssori felületével (plusz ízelítőt kaphatnak abból!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Milyen Ez az oktatóanyag elvégzéséhez a parancssor használatával a Visual Studióban végrehajtását ismerteti. [Ízelítőt kaphatnak abból](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) olyan eszköz, az ASP.NET-alkalmazások a hibakereséshez.
* [Web Apps-naplózás és diagnosztika – használata David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) és [folyamatos átviteli naplók, a Web Apps – David Ebbo](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Scott Hanselman és David Ebbo videókat.

Hiba történt a naplózást, a saját nyomkövetési kód írása helyett egy nyílt forráskódú naplózási keretrendszer használandó [ELMAH](http://nuget.org/packages/elmah/). További információkért lásd: [házigazdája, Scott Hanselman kapcsolatos blogbejegyzéseket ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Emellett nem kell használnia az ASP.NET vagy `System.Diagnostics` le adatfolyam-továbbítási nyomkövetési naplók az Azure-ból. A naplózási szolgáltatás Azure web app streameli bármely *.txt*, *.html*, vagy *.log* fájlt, amely megjeleníti a *LogFiles* mappát. Ezért létrehozhatja a saját naplózási rendszerrel, amely a fájlrendszer, a webalkalmazás ír, és a fájl automatikusan streamelt adatok és letöltött. Ehhez csak írási alkalmazáskóddal, amelyhez a fájlokat hozza létre a *d:\home\logfiles* mappát.

### <a name="analyzing-web-server-logs"></a>Webkiszolgáló-naplók elemzéséhez
Webkiszolgáló-naplók elemzésével kapcsolatos további információkért lásd a következőket:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Egy eszköz a adatok megtekintése a webkiszolgáló-naplókkal (*.log* fájlok).
* [Hibaelhárítási IIS teljesítménybeli problémák vagy az alkalmazáshibák LogParser használatával ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Bevezetés a naplóelemző eszköz, amellyel webkiszolgáló-naplók elemzése.
* [Blogbejegyzések által Robert McMurray LogParser használatával](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Az IIS 7.0, IIS 7.5 és IIS 8.0-s HTTP-állapotkód](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Kérelem nyomkövetési naplók elemzése nem sikerült.
A Microsoft TechNet webhelyen tartalmaz egy [használatával sikertelen kérelmek nyomkövetését](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) rész, amely hasznos lehet az ismertetése, hogyan használhatja ezeket a naplókat. Azonban ez a dokumentáció összpontosít főként sikertelen kérelmek nyomkövetése konfigurálása az IIS-ben, amely az Azure Web Apps nem hajtható végre.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
