---
title: Hibaelhárítás a Visual Studióval
description: Megtudhatja, hogy miként háríthatja el az App Service-alkalmazásokat a Visual Studio 2013 beépített távoli hibakeresési, nyomkövetési és naplózási eszközeivel.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: seodec18
ms.openlocfilehash: 3305cfb81980984574961b2a84a056f5d1879ead
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280052"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Alkalmazás hibaelhárítása az Azure App Service szolgáltatásban a Visual Studio használatával
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan használható a Visual Studio eszközei vel az applikátor hibakereséséhez az [App Service szolgáltatásban,](https://go.microsoft.com/fwlink/?LinkId=529714) [távolról,](https://docs.microsoft.com/visualstudio/debugger/) illetve alkalmazásnaplók és webkiszolgálói naplók megtekintésével.

Az oktatóanyagból a következőket sajátíthatja el:

* Mely alkalmazáskezelési funkciók érhetők el a Visual Studióban.
* A Visual Studio távoli nézetének használata gyors módosításokat egy távoli alkalmazásban.
* A hibakeresési mód távoli futtatása, miközben egy projekt fut az Azure-ban, mind egy alkalmazás, mind a WebJob esetében.
* Alkalmazás-nyomkövetési naplók létrehozása és megtekintése, miközben az alkalmazás létrehozza őket.
* A webkiszolgálói naplók megtekintése, beleértve a részletes hibaüzeneteket és a sikertelen kérelmek nyomon követését.
* Diagnosztikai naplók küldése egy Azure Storage-fiókba, és ott megtekintheti őket.

Ha rendelkezik Visual Studio Ultimate-el, az [IntelliTrace-et](/visualstudio/debugger/intellitrace) is használhatja hibakeresésre. Az IntelliTrace nem tartozik ebbe az oktatóanyagba.

## <a name="prerequisites"></a><a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag a ASP.NET alkalmazás létrehozása az [Azure App Service-ben](app-service-web-get-started-dotnet-framework.md)beállított fejlesztői környezettel, webes projekttel és App Service-alkalmazással működik. A WebJobs szakaszokhoz szüksége lesz az [Azure WebJobs SDK első lépések][GetStartedWJ]című szakaszában létrehozott alkalmazásra.

Az oktatóanyagban látható kódminták egy C# MVC webalkalmazáshoz tartoznak, de a hibaelhárítási eljárások megegyeznek a Visual Basic és a Web Forms alkalmazások esetében.

Az oktatóanyag feltételezi, hogy a Visual Studio 2019-et használja. 

A streamelési naplók szolgáltatás csak a .NET Framework 4 vagy újabb célrendszert célzó alkalmazások esetében működik.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>Alkalmazás konfigurációja és kezelése
A Visual Studio hozzáférést biztosít az [Azure Portalon](https://go.microsoft.com/fwlink/?LinkId=529715)elérhető alkalmazáskezelési funkciók és konfigurációs beállítások egy részhalmazához. Ebben a szakaszban láthatja, hogy mi érhető el a **Kiszolgálókezelő**vel. A legújabb Azure-integrációs funkciók megtekintéséhez próbálja ki a **Cloud Explorert** is. Mindkét ablakot megnyithatja a **Nézet** menüből.

1. Ha még nem jelentkezett be az Azure-ba **Azure** a Visual Studióban, kattintson a jobb gombbal az Azure-ra, és válassza a Csatlakozás a **Microsoft Azure-előfizetéshez** parancsot a **Kiszolgálókezelőben.**

    Egy másik lehetőség egy felügyeleti tanúsítvány telepítése, amely lehetővé teszi a hozzáférést a fiókjához. Ha úgy dönt, hogy telepít egy **Azure** tanúsítványt, kattintson a jobb gombbal az Azure-csomópontra a **Kiszolgálókezelőben,** majd válassza az **Előfizetések kezelése és szűrése** parancsot a helyi menüben. A **Microsoft Azure-előfizetések kezelése** párbeszédpanelen kattintson a **Tanúsítványok** fülre, majd az **Importálás gombra.** Kövesse az Azure-fiókjához az előfizetési fájl (más néven *.publishsettings* fájl) letöltésének és importálásának utasításokat.

   > [!NOTE]
   > Ha letölt egy előfizetési fájlt, mentse azt a forráskódkönyvtárakon kívüli mappába (például a Letöltések mappába), majd törölje azt, miután az importálás befejeződött. Az előfizetési fájlhoz hozzáférést megkapó rosszindulatú felhasználók szerkeszthetik, létrehozhatják és törölhetik az Azure-szolgáltatásokat.
   >
   >

    Az Azure-erőforrásokhoz a Visual Studio-ból való csatlakozásról a [Fiókok, előfizetések és felügyeleti szerepkörök kezelése című témakörben talál](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)további információt.
2. A **Kiszolgálókezelőben**bontsa ki az **Azure** csomópontot, és bontsa ki **az App Service csomópontot.**
3. Bontsa ki azt az erőforráscsoportot, amely a ASP.NET alkalmazás létrehozása az [Azure App Service-ben](app-service-web-get-started-dotnet-framework.md)alkalmazásban létrehozott alkalmazást tartalmazza, majd kattintson a jobb gombbal az alkalmazáscsomópontra, és kattintson a **Beállítások megtekintése parancsra.**

    ![Beállítások megtekintése a Kiszolgálókezelőben](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Megjelenik **az Azure Web App** lap, és láthatja a Visual Studio-ban elérhető alkalmazáskezelési és konfigurációs feladatokat.

    ![Az Azure Web App ablaka](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Ebben az oktatóanyagban a naplózási és nyomkövetési legördülő legördülő lista használható. Távoli hibakeresést is használni fog, de más módszert fog használni az engedélyezéséhez.

    Ebben az ablakban az alkalmazásbeállításokés a kapcsolati karakterláncok mezőiről az [Azure App Service: Hogyan működnek az alkalmazáskarakterláncok és a kapcsolati karakterláncok.](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)

    Ha olyan alkalmazáskezelési feladatot szeretne végrehajtani, amely ebben az ablakban nem hajtható végre, kattintson **a Megnyitás a Felügyeleti portálon** gombra az Azure Portal böngészőablakának megnyitásához.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Alkalmazásfájlok elérése a Kiszolgálókezelőben
Általában olyan webes projektet `customErrors` telepít, amelynek jelzője a `On` `RemoteOnly`Web.config fájlban vagy a vagy , ami azt jelenti, hogy nem kap hasznos hibaüzenetet, ha valami elromlik. Sok hiba esetén csak egy olyan oldalt kapsz, mint az alábbiak egyike:

**Kiszolgálóhiba a '/' alkalmazásban:**

![Nem hasznos hibalap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Hiba történt:**

![Nem hasznos hibalap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**A webhely nem tudja megjeleníteni a lapot**

![Nem hasznos hibalap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Gyakran a legegyszerűbb módja annak, hogy megtalálja a hiba okát, hogy részletes hibaüzeneteket engedélyez, amelyek az előző első képernyőképek ismertetik, hogyan kell csinálni. Ehhez módosítani kell a telepített Web.config fájlt. Szerkesztheti a *Web.config* fájlt a projektben, újratelepítheti a projektet, vagy létrehozhat egy [Web.config átalakítót,](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) és telepíthet egy hibakeresési buildet, de van egy gyorsabb módja: a **Megoldáskezelőben**közvetlenül megtekintheti és szerkesztheti a távoli alkalmazásban lévő fájlokat a *távoli nézet* szolgáltatás használatával.

1. A **Kiszolgálókezelőben**bontsa ki az **Azure**csomópontot, bontsa ki az **App Service**csomópontot, bontsa ki azt az erőforráscsoportot, amelyben az alkalmazás található, majd bontsa ki az alkalmazás csomópontját.

    Olyan csomópontokat lát, amelyek hozzáférést biztosítanak az alkalmazás tartalomfájljaihoz és naplófájljaihoz.
2. Bontsa ki a **Fájlok csomópontot,** és kattintson duplán a *Web.config* fájlra.

    ![Web.config megnyitása](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    A Visual Studio megnyitja a Web.config fájlt a távoli alkalmazásból, és a címsorban a fájlneve mellett a [Remote] látható.
3. Adja hozzá a `system.web` következő sort az elemhez:

    `<customErrors mode="Off"></customErrors>`

    ![Web.config szerkesztése](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Frissítse a nem hasznos hibaüzenetet megjelenítő böngészőt, és most részletes hibaüzenetet kap, például a következő példát:

    ![Részletes hibaüzenet](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (A megjelenített hibát úgy hozták létre, hogy a piros színnel jelzett sort hozzáadta a *Views\Home\Index.cshtml*mappához.)

A Web.config fájl szerkesztése csak egy példa olyan esetekre, amikor az App Service-alkalmazásban lévő fájlok olvasása és szerkesztése megkönnyíti a hibaelhárítást.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Távoli hibakereső alkalmazások
Ha a részletes hibaüzenet nem ad elegendő információt, és nem tudja helyileg újra létrehozni a hibát, a hibaelhárítás másik módja a hibakeresési módban történő távoli futtatás. Beállíthat töréspontokat, közvetlenül kezelheti a memóriát, végigléphet a kódon, és akár módosíthatja is a kód elérési útját.

A távoli hibakeresés nem működik a Visual Studio Express kiadásaiban.

Ez a szakasz bemutatja, hogyan távolról hibakeresést készíthet az [Azure App Service](app-service-web-get-started-dotnet-framework.md)ASP.NET alkalmazás létrehozása szolgáltatásában létrehozott projekt használatával.

1. Nyissa meg a létrehozott webes projektet a [Create a ASP.NET app ban az Azure App Service alkalmazásban.](app-service-web-get-started-dotnet-framework.md)

1. Nyissa *meg a Vezérlők\HomeController.cs*.

1. Törölje `About()` a metódust, és szúrja be a helyére a következő kódot.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Állítson be egy töréspontot](https://docs.microsoft.com/visualstudio/debugger/) a `ViewBag.Message` vonalon.

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, majd kattintson a **Közzététel parancsra.**

1. A **Profil** legördülő listában válassza ki ugyanazt a profilt, amelyet a ASP.NET alkalmazás létrehozása az [Azure App Service-ben.](app-service-web-get-started-dotnet-framework.md) Ezután kattintson a Beállítások gombra.

1. A **Közzététel** párbeszédpanelen kattintson a **Beállítások** fülre, majd módosítsa a **Konfiguráció** beállítást **Hibakeresés**re, majd a **Mentés gombra.**

    ![Közzététel hibakeresési módban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Kattintson a **Publish** (Közzététel) gombra. Miután a telepítés befejeződik, és a böngésző megnyílik az alkalmazás Azure URL-címét, zárja be a böngészőt.

1. A **Kiszolgálókezelőben**kattintson a jobb gombbal az alkalmazásra, majd kattintson **a Hibakereső csatolása parancsra.**

    ![Hibakereső csatolása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    A böngésző automatikusan megnyílik az Azure-ban futó kezdőlapra. Előfordulhat, hogy 20 másodpercet kell várnia, amíg az Azure beállítja a kiszolgálót a hibakereséshez. Ez a késleltetés csak akkor történik meg, amikor először futtat hibakeresési módban egy alkalmazásban 48 óra alatt. Ha ugyanabban az időszakban újra elkezdi a hibakeresést, nincs késés.

    > [!NOTE] 
    > Ha bármilyen problémája van a hibakereső elindításával, próbálja meg a **Kiszolgálókezelő**helyett a **Cloud Explorer** használatával.
    >

1. A menüben kattintson a **Tanmenü Be** gombjára.

    A Visual Studio leáll a törésponton, és a kód az Azure-ban fut, nem a helyi számítógépen.

1. Az időérték megtekintéséhez vigye az egérmutatót a `currentTime` változó fölé.

    ![Az Azure-ban futó hibakeresési módban futó változó megtekintése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    A látható idő az Azure-kiszolgáló idáig, amely lehet, hogy egy másik időzónában, mint a helyi számítógépen.

1. Adjon meg egy `currentTime` új értéket a változóhoz, például "Most fut az Azure-ban".

1. A futás folytatásához nyomja le az F5 billentyűt.

     Az Azure-ban futó Betama lap a currentTime változóba megadott új értéket jeleníti meg.

     ![Új értékkel rendelkező lap –](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a>WebJobs távoli hibakeresés
Ez a szakasz bemutatja, hogyan távolról hibakeresést az [Azure WebJobs SDK első lépések](https://github.com/Azure/azure-webjobs-sdk/wiki)című szakaszában létrehozott projekt és alkalmazás használatával.

Az ebben a szakaszban látható funkciók csak a Visual Studio 2013 4- es vagy újabb frissítését tartalmazza.

A távoli hibakeresés csak folyamatos WebJobs programokkal működik. Az ütemezett és az igény szerinti WebJobs nem támogatja a hibakeresést.

1. Nyissa meg az Azure [WebJobs SDK][GetStartedWJ]első lépések szolgáltatásában létrehozott webes projektet.

2. A ContosoAdsWebJob projektben nyissa meg *a Functions.cs.*

3. [Állítson be egy töréspontot](https://docs.microsoft.com/visualstudio/debugger/) `GnerateThumbnail` a metódus első utasítására.

    ![Töréspont beállítása](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. A **Megoldáskezelőben**kattintson a jobb gombbal a webes projektre (ne a WebJob projektre), majd kattintson a **Közzététel parancsra.**

5. A **Profil** legördülő listában válassza ki ugyanazt a profilt, amelyet [az Azure WebJobs SDK – Első lépések az Azure WebJobs SDK használatával](https://github.com/Azure/azure-webjobs-sdk/wiki)című listában használt.

6. Kattintson a **Beállítások** fülre, módosítsa a **Konfiguráció** beállítást **Hibakeresés**úrra, majd a **Közzététel gombra.**

    A Visual Studio telepíti a webes és a WebJob-projekteket, és a böngésző megnyílik az alkalmazás Azure-URL-címére.

7. A **Server Explorerben**bontsa ki az **Azure > App Service > az erőforráscsoportot, > az alkalmazás > WebJobs > Folyamatos**, majd kattintson a jobb gombbal a **ContosoAdsWebJob parancsra.**

8. Kattintson **a Hibakereső csatolása gombra.**

    ![Hibakereső csatolása](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    A böngésző automatikusan megnyílik az Azure-ban futó kezdőlapra. Előfordulhat, hogy 20 másodpercet kell várnia, amíg az Azure beállítja a kiszolgálót a hibakereséshez. Ez a késleltetés csak akkor történik meg, amikor először futtat hibakeresési módban egy alkalmazásban 48 óra alatt. Ha ugyanabban az időszakban újra elkezdi a hibakeresést, nincs késés.

9. A Contoso Ads kezdőlapjára megnyitott webböngészőben hozzon létre egy új hirdetést.

    A hirdetés létrehozása esetén létrejön egy üzenetsor-üzenet, amelyet a WebJob felvesz és feldolgoz. Amikor a WebJobs SDK meghívja a függvényt a várólistaüzenet feldolgozásához, a kód eléri a töréspontot.

10. Amikor a hibakereső eltörik a töréspontnál, megvizsgálhatja és módosíthatja a változóértékeket, miközben a program a felhőt futtatja. A következő ábrán a hibakereső a `GenerateThumbnail` metódusnak átadott blobInfo objektum tartalmát jeleníti meg.

     ![blobInfo objektum a hibakeresőben](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. A futás folytatásához nyomja le az F5 billentyűt.

     A `GenerateThumbnail` módszer befejezi a miniatűr létrehozását.

12. A böngészőben frissítse az Index lapot, és megjelenik a miniatűr.

13. A Visual Studio alkalmazásban nyomja le a SHIFT+F5 billentyűkombinációt a hibakeresés leállításához.

14. A **Kiszolgálókezelőben**kattintson a jobb gombbal a ContosoAdsWebJob csomópontra, és válassza az **Irányítópult megtekintése parancsra.**

15. Jelentkezzen be az Azure-hitelesítő adatokkal, majd kattintson a WebJob nevére a webfeladat lapjának megugrásához.

     ![Kattintson a ContosoAdsWebJob elemre](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Az irányítópult azt `GenerateThumbnail` mutatja, hogy a függvény a közelmúltban hajtott végre.

     (Amikor legközelebb az **Irányítópult megtekintése**gombra kattint, nem kell bejelentkezned, és a böngésző közvetlenül a WebJob lapjára kerül.)

16. Kattintson a függvény nevére a függvény végrehajtásával kapcsolatos részletek megtekintéséhez.

     ![A funkció részletei](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Ha a függvény [naplókat írt,](https://github.com/Azure/azure-webjobs-sdk/wiki)a **ToggleOutput** gombra kattintva megtekintheti őket.

## <a name="notes-about-remote-debugging"></a>Megjegyzések a távoli hibakereséssel kapcsolatban

* Éles környezetben nem ajánlott hibakeresési módban futni éles környezetben. Ha az éles alkalmazás nincs több kiszolgálópéldányra horizontálisan kiméretezve, a hibakeresés megakadályozza, hogy a webkiszolgáló más kérésekre válaszoljon. Ha több webkiszolgáló-példánya van, amikor a hibakeresőhöz csatolja, véletlenszerű példányt kap, és nincs módja annak, hogy a következő böngészőkérelmek ugyanarra a példányra jussanak. Emellett általában nem telepít hibakeresési buildet éles környezetben, és a fordító optimalizálása a kiadási buildek lehetetlenné teheti, hogy megmutassa, mi történik sord-tól sorda a forráskódban. A termelési problémák elhárításához a legjobb erőforrás az alkalmazásnyomkövetés és a webkiszolgáló-naplók.
* Távolról történő hibakeresés kor kerülje a töréseknél a hosszú megállókat. Az Azure egy néhány percnél hosszabb ideig leállított folyamatot nem válaszoló folyamatként kezel, és leállítja azt.
* Hibakeresés közben a kiszolgáló adatokat küld a Visual Studiónak, ami hatással lehet a sávszélesség-díjakra. A sávszélesség-díjakról az [Azure díjszabása](https://azure.microsoft.com/pricing/calculator/)című témakörben talál további információt.
* Győződjön meg `debug` arról, `compilation` hogy a *Web.config* fájl elemének attribútuma igaz értékre van állítva. Hibakeresési buildkonfiguráció közzétételekor alapértelmezés szerint igaz értékre van állítva.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Ha úgy találja, hogy a hibakereső nem lép be a hibakereséshez kívánt kódba, előfordulhat, hogy módosítania kell a Just My Code beállítást.  További [információ: Adja meg, hogy csak a felhasználói kódot szeretné-e hibakeresést használni a Visual Studio Just My Code használatával.](https://docs.microsoft.com/visualstudio/debugger/just-my-code)
* Az időzítő akkor indul el a kiszolgálón, amikor engedélyezi a távoli hibakeresési szolgáltatást, és 48 óra elteltével a szolgáltatás automatikusan kikapcsol. Ez a 48 órás korlát biztonsági és teljesítménybeli okokból történik. Könnyedén visszakapcsolhatja a funkciót, ahányszor csak szeretné. Azt javasoljuk, hogy hagyja letiltva, ha nem aktív anamezentatimát.
* A hibakeresőt manuálisan is csatolhatja bármely folyamathoz, nem csak az alkalmazásfolyamathoz (w3wp.exe). A Hibakeresési mód Visual Studio-ban való használatáról a [Hibakeresés a Visual Studióban](/visualstudio/debugger/debugging-in-visual-studio)című témakörben talál további információt.

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Diagnosztikai naplók – áttekintés
Az App Service-alkalmazásban futó ASP.NET alkalmazások a következő típusú naplókat hozhatják létre:

* **Alkalmazásnyomkövetési naplók**<br/>
  Az alkalmazás ezeket a naplókat a [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) osztály metódusainak hívásával hozza létre.
* **Webkiszolgáló-naplók**<br/>
  A webkiszolgáló naplóbejegyzést hoz létre az alkalmazás minden HTTP-kéréséhez.
* **Részletes hibaüzenetnaplók**<br/>
  A webkiszolgáló létrehoz egy HTML-lapot, amely további információkat tartalmaz a sikertelen HTTP-kérelmekről (a 400-as vagy újabb állapotkódot eredményező kérelmekről).
* **Sikertelen kérelmek nyomkövetési naplói**<br/>
  A webkiszolgáló létrehoz egy XML-fájlt, amely részletes nyomkövetési adatokat tartalmaz a sikertelen HTTP-kérelmekhez. A webkiszolgáló egy XSL-fájlt is biztosít az XML böngészőben való formázásához.

A naplózás hatással van az alkalmazások teljesítményére, így az Azure lehetővé teszi az egyes naplótípusok szükség szerint történő engedélyezését vagy letiltását. Az alkalmazásnaplókhoz megadhatja, hogy csak egy bizonyos súlyossági szint feletti naplókat kell írni. Új alkalmazás létrehozásakor alapértelmezés szerint minden naplózás le van tiltva.

A naplók az alkalmazás fájlrendszerében lévő *LogFiles* mappában lévő fájlokba vannak írva, és FTP-n keresztül érhetők el. Webkiszolgáló-naplók és alkalmazásnaplók is írhatók egy Azure Storage-fiókba. A tárfiókban a fájlrendszerben lehetségesnél nagyobb mennyiségű napló tárolható. A fájlrendszer használata esetén legfeljebb 100 megabájt nyi naplót használhat. (A fájlrendszer naplói csak rövid távú megőrzésre szolgálnak. Az Azure törli a régi naplófájlokat, hogy helyet adjon az újaknak a korlát elérése után.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Alkalmazás-nyomkövetési naplók létrehozása és megtekintése
Ebben a szakaszban a következő feladatokat kell elvégeznie:

* Nyomkövetési utasítások hozzáadása az Azure és a ASP.NET című ismerkedés című ismerkedés i [és ASP.NET](app-service-web-get-started-dotnet-framework.md)létrehozott webes projekthez.
* A naplók megtekintése a projekt helyi futtatásakor.
* Tekintse meg a naplókat, ahogy azok az Azure-ban futó alkalmazás által létrehozott.

Az alkalmazásnaplók WebJobs-ban történő létrehozásáról a [WebJobs SDK használatával az Azure várólista-tárterület teljében – Naplók írása](https://github.com/Azure/azure-webjobs-sdk/wiki)című témakörben talál további információt. A naplók megtekintésére és az Azure-ban való tárolásuk szabályozására vonatkozó alábbi utasítások a WebJobs által létrehozott alkalmazásnaplókra is vonatkoznak.

### <a name="add-tracing-statements-to-the-application"></a>Nyomkövetési utasítások hozzáadása az alkalmazáshoz
1. Nyissa *meg a Controllers\HomeController.cs webhelyet,* és cserélje le a `Index`, `About`és `Contact` a módszereket a következő kódra cserélve, hogy kivonatokat és utasítást `Trace` `using` adjon `System.Diagnostics`hozzá:

    ```csharp
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

1. Adjon `using System.Diagnostics;` hozzá egy utasítást a fájl tetejéhez.

### <a name="view-the-tracing-output-locally"></a>A nyomkövetési kimenet helyi megtekintése
1. Nyomja le az F5 billentyűt az alkalmazás hibakeresési módban történő futtatásához.

    Az alapértelmezett nyomkövetési figyelő az összes nyomkövetési kimenetet a **Kimenet ablakba** írja, más dehiba-kimenettel együtt. A következő ábra a `Index` metódushoz hozzáadott nyomkövetési utasítások kimenetét mutatja be.

    ![Nyomkövetés a Hibakeresés ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    A következő lépések bemutatják, hogyan tekintheti meg a nyomkövetési kimenetet egy weblapon, hibakeresési módban való összeállítás nélkül.
1. Nyissa meg az alkalmazást Web.config fájlt (a projekt `<system.diagnostics>` mappában találhatófájlt), és adjon `</configuration>` hozzá egy elemet a fájl végén közvetlenül a záró elem előtt:

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

A `WebPageTraceListener` segítségével megtekintheti a nyomkövetési `/trace.axd`kimenetet a böngészéssel a-ra.
1. Nyomkövetési <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elem</a> `<system.web>` hozzáadása a Web.config fájl alatt, például a következő példa:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Az alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.
1. A böngészőablak címsorában adja hozzá a *trace.axd címet* az URL-címhez, `http://localhost:53370/trace.axd`majd nyomja le az Enter billentyűt (az URL hasonló a következőhöz).
1. Az **Alkalmazáskövetés** lapon kattintson a **Részletek megtekintése** gombra az első sorban (nem a BrowserLink sorban).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Megjelenik **a Részletek kérése** lap, és a **Nyomkövetési információ** szakaszban a `Index` metódushoz hozzáadott nyomkövetési utasítások kimenete látható.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Alapértelmezés szerint `trace.axd` csak helyileg érhető el. Ha egy távoli alkalmazásból szeretné elérhetővé tenni, `localOnly="false"` `trace` hozzáadhatja a *Web.config* fájl eleméhez, ahogy az a következő példában látható:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Az éles `trace.axd` alkalmazásokban való engedélyezés azonban biztonsági okokból nem ajánlott. A következő szakaszokban egy app-szolgáltatásalkalmazásban a nyomkövetési naplók olvasásának egyszerűbb módját láthatja.

### <a name="view-the-tracing-output-in-azure"></a>A nyomkövetési kimenet megtekintése az Azure-ban
1. A **Megoldáskezelőben**kattintson a jobb gombbal a webes projektre, és válassza a **Közzététel (Közzététel) parancsra.**
2. A **Webhely közzététele** párbeszédpanelen kattintson a **Közzététel gombra.**

    Miután a Visual Studio közzétette a frissítést, megnyit egy böngészőablakot a kezdőlapra (feltéve, hogy nem törölte a **Cél URL-címét** a **Kapcsolat** lapon).
3. A **Kiszolgálókezelőben**kattintson a jobb gombbal az alkalmazásra, és válassza **a Streamelési naplók megtekintése parancsot.**

    ![Adatfolyam-naplók megtekintése a helyi menüben](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    A **Kimenet** ablak ban látható, hogy csatlakozik a napló-streamelési szolgáltatáshoz, és minden percben hozzáad egy értesítési sort, amely napló nélkül jelenik meg.

    ![Adatfolyam-naplók megtekintése a helyi menüben](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Az alkalmazás kezdőlapját megjelenítő böngészőablakban kattintson a **Kapcsolat gombra.**

    Néhány másodpercen belül a `Contact` metódushoz hozzáadott hibaszintű nyomkövetés kimenete megjelenik a Kimenet **ablakban.**

    ![Hibakövetés a Kimenet ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    A Visual Studio csak hibaszintű nyomkövetéseket jelenít meg, mert ez az alapértelmezett beállítás a naplófigyelési szolgáltatás engedélyezésekor. Amikor új App Service-alkalmazást hoz létre, alapértelmezés szerint minden naplózás le van tiltva, ahogy azt a beállítási lap korábbi megnyitásakor látta:

    ![Alkalmazás kijelentkezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Ha azonban a **Streamelési naplók megtekintése**lehetőséget választotta, a Visual Studio automatikusan **hiba**állapotúra módosította **az alkalmazásnaplózást (fájlrendszer),** ami azt jelenti, hogy hibaszintű naplók jelennek meg. Az összes nyomkövetési napló megtekintéséhez ezt a beállítást **részletesre módosíthatja.** Ha a hibánál alacsonyabb súlyossági szintet választ, a rendszer a magasabb súlyossági szintekre vonatkozó összes naplót is jelenti. Így amikor a részletes et választja, akkor az információk, a figyelmeztetés és a hibanaplók is megjelennek.  

5. A **Kiszolgálókezelőben**kattintson a jobb gombbal az alkalmazásra, majd kattintson a **Beállítások megtekintése** parancsra, ahogy korábban tette.
6. Módosítsa **az alkalmazásnaplózást (fájlrendszert)** részletesre, majd kattintson a **Mentés** **gombra.**

    ![A nyomkövetési szint részletesre állítása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. A most az **Ügyfél** lapot megjelenítő böngészőablakban kattintson a **Kezdőlap**menü **Névjegy parancsára,** majd a **Névjegy**parancsra.

    Néhány másodpercen belül a **Kimenet** ablak az összes nyomkövetési kimenetet megjeleníti.

    ![Részletes nyomkövetési kimenet](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Ebben a szakaszban engedélyezte és letiltotta a naplózást az alkalmazásbeállítások használatával. A web.config fájl módosításával a nyomfigyelők is engedélyezhetők és letilthatók. A Web.config fájl módosítása azonban az alkalmazástartomány újrahasznosítását eredményezi, miközben az alkalmazás konfigurációin keresztüli naplózás engedélyezése nem teszi ezt lehetővé. Ha a probléma sokáig tart a reprodukálása, vagy időszakos, az alkalmazástartomány újrahasznosítása "kijavíthatja" azt, és arra kényszerítheti, hogy várjon, amíg újra megtörténik. A diagnosztika engedélyezése az Azure-ban lehetővé teszi, hogy azonnal, az alkalmazástartomány újrahasznosítása nélkül kezdje el a hibaadatok rögzítését.

### <a name="output-window-features"></a>Kimeneti ablak jellemzői
A **Kimeneti** ablak **Microsoft Azure-naplók** lapján több gomb és egy szövegdoboz található:

![Naplók lap gombjai](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Ezek a következő feladatokat hajtják végre:

* Törölje a **Kimenet** ablakot.
* A szótördelés engedélyezése vagy letiltása.
* A naplók figyelésének indítása vagy leállítása.
* Adja meg, hogy mely naplókat szeretné figyelni.
* Töltse le a naplókat.
* Keresési karakterlánc vagy reguláris kifejezés alapján szűrheti a naplókat.
* Zárja be a **Kimenet** ablakot.

Ha keresési karakterláncot vagy reguláris kifejezést ad meg, a Visual Studio szűri a naplózási adatokat az ügyfélen. Ez azt jelenti, hogy megadhatja a feltételeket, miután a naplók megjelennek a **Kimenet** ablakban, és módosíthatja a szűrési feltételeket anélkül, hogy újra kellene generálnia a naplókat.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Webkiszolgáló-naplók megtekintése
A webkiszolgáló naplózza az alkalmazás összes HTTP-tevékenységét rögzítve. Ahhoz, hogy a **Kimenet** ablakban láthassa őket, engedélyeznie kell őket az alkalmazáshoz, és meg kell mondania a Visual Studio-nak, hogy figyelni szeretné őket.

1. A **Kiszolgálókezelőből**megnyitott **Azure Web App konfigurációja** lapon módosítsa a Webkiszolgáló bejelentkezését **Be**állapotúrra, majd kattintson a **Mentés gombra.**

    ![Webkiszolgáló naplózásának engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. A **Kimenetablakban** kattintson a **Figyelési Microsoft Azure-naplók megadása** gombra.

    ![Adja meg, hogy mely Azure-naplókat kell figyelni](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. A **Microsoft Azure naplózási beállításai** párbeszédpanelen válassza a **Webkiszolgáló-naplók**lehetőséget, majd kattintson az **OK**gombra.

    ![Webkiszolgáló-naplók figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Az alkalmazást megjelenítő böngészőablakban kattintson a **Kezdőlap**menü **Névjegy parancsára,** majd a **Névjegy**parancsra.

    Általában az alkalmazásnaplók jelennek meg először, majd a webkiszolgáló naplói. Előfordulhat, hogy várnia kell egy kicsit, amíg a naplók megjelennek.

    ![Webkiszolgáló-naplók a Kimenet ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Alapértelmezés szerint, amikor először engedélyezi a webkiszolgálói naplókat a Visual Studio használatával, az Azure a naplókat a fájlrendszerbe írja. Alternatív megoldásként használhatja az Azure Portalon, hogy adja meg, hogy a webkiszolgáló naplókat kell írni egy blob tároló egy tárfiókban.

Ha a portál segítségével engedélyezi a webkiszolgáló naplózását egy Azure-tárfiókba, majd letiltja a naplózást a Visual Studióban, amikor újra engedélyezi a naplózást a Visual Studióban, a tárfiók beállításai visszaállnak.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Részletes hibaüzenetnaplók megtekintése
A részletes hibanaplók további információkat nyújtanak a HTTP-kérelmekről, amelyek hibaválaszkódokat eredményeznek (400 vagy újabb). Ahhoz, hogy a **Kimenet** ablakban láthassa őket, engedélyeznie kell őket az alkalmazáshoz, és meg kell mondania a Visual Studio-nak, hogy figyelni szeretné őket.

1. A **Kiszolgálókezelőből**megnyitott **Azure Web App konfigurációja** lapon módosítsa a **részletes hibaüzenetek beállítását** Be , majd kattintson **a** **Mentés gombra.**

    ![Részletes hibaüzenetek engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. A **Kimenetablakban** kattintson a **Figyelési Microsoft Azure-naplók megadása** gombra.

3. A **Microsoft Azure naplózási beállításai** párbeszédpanelen kattintson a **Minden napló gombra,** majd az **OK**gombra.

    ![Az összes napló figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. A böngészőablak címsorában adjon hozzá egy további karaktert az URL-címhez, `http://localhost:53370/Home/Contactx`hogy 404-es hibát okozzon (például ) és nyomja le az Enter billentyűt.

    Néhány másodperc múlva a részletes hibanapló megjelenik a Visual Studio **kimeneti** ablakában.

    ![Részletes hibanapló – Kimeneti ablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Control+kattintson a hivatkozásra a böngészőben formázott naplókimenet megtekintéséhez:

    ![Részletes hibanapló - böngészőablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Fájlrendszer-naplók letöltése
A **Kimenet** ablakban figyelhető naplók *.zip* fájlként is letölthetők.

1. A **Kimenet ablakban** kattintson a **Streamelési naplók letöltése gombra.**

    ![Naplók lap gombjai](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    A Fájlkezelő a letöltött fájl kijelölésével nyílik meg a *Letöltések* mappába.

    ![Letöltött fájl](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Bontsa ki a *.zip* fájlt, és a következő mappastruktúra jelenik meg:

    ![Letöltött fájl](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Az alkalmazásnyomkövetési naplók a *LogFiles\Application* mappában *találhatók.*
   * A webkiszolgálói naplók a *LogFiles\http\RawLogs* *mappában* találhatók. A fájlok megtekintéséhez és kezeléséhez használhat egy eszközt, például [a Log Parsert.](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659)
   * A részletes hibaüzenetnaplók a *LogFiles\DetailedErrors* mappában *találhatók.*

     (A *központi telepítési* mappa a forrásvezérlő közzétételével létrehozott fájlokra vonatkozik; a Visual Studio közzétételével kapcsolatban semmi nem kapcsolódik. A *Git* mappa a forrásvezérlő közzététellel és a naplófájl-adatfolyam-szolgáltatással kapcsolatos nyomkövetésekhez szolgál.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

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

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Sikertelen kérelmek nyomkövetési naplóinak megtekintése
A sikertelen kérelmek nyomkövetési naplói akkor hasznosak, ha meg kell értenie, hogy az IIS hogyan kezeli a HTTP-kérelmeket, például URL-újraírásvagy hitelesítési problémák esetén.

Az App Service-alkalmazások ugyanazokat a sikertelen kérelmek nyomkövetési funkcióit használják, mint az IIS 7.0-s és újabb verzióiban. Nem fér hozzá azonban az IIS-beállításokhoz, amelyek azt szeretnék, hogy mely hibák legyenek naplózva. Ha engedélyezi a sikertelen kérelmek nyomkövetését, a rendszer rögzíti az összes hibát.

A sikertelen kérelmek nyomkövetését a Visual Studio segítségével engedélyezheti, de a Visual Studióban nem tekintheti meg őket. Ezek a naplók XML-fájlok. A folyamatos átvitelű naplószolgáltatás csak azokat a fájlokat figyeli, amelyek egyszerű szöveges módban olvashatónak minősülnek: *.txt*, *.html*és *.log* fájlok.

Megtekintheti a sikertelen kérelmek nyomkövetési naplók at a böngészőben közvetlenül FTP-n keresztül, vagy helyileg használata után egy FTP eszköz letölteni őket a helyi számítógépre. Ebben a szakaszban közvetlenül a böngészőben tekintheti meg őket.

1. A **Kiszolgálókezelőből**megnyitott **Azure Web App** ablak **konfigurációlapján** módosítsa a **Sikertelen kérelmek nyomkövetését** be , majd kattintson **a** **Mentés gombra.**

    ![Sikertelen kérelmek nyomkövetésének engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Az alkalmazást megjelenítő böngészőablak címsorában adjon hozzá egy további karaktert az URL-címhez, és 404-es hibát okozzon az Enter gombra.

    Ez egy sikertelen kérelemnyomkövetési napló létrehozását eredményezi, és a következő lépések bemutatják a napló megtekintésének vagy letöltésének módját.

3. A Visual Studio **konfigurációlapján** **kattintson** a Megnyitás a **Felügyeleti portálon**gombra.

4. Az [Alkalmazás Azure portalának](https://portal.azure.com) **beállításai** lapon kattintson a Telepítési hitelesítő **adatok**elemre, majd adjon meg egy új felhasználónevet és jelszót.

    ![Új FTP-felhasználónév és -jelszó](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Bejelentkezéskor a teljes felhasználónevet kell használnia az alkalmazás előtaggal. Ha például a "myid" nevet adja meg felhasználónévként, és a webhely "saját példa", akkor "myexample\myid" néven jelentkezik be.
    >

5. Egy új böngészőablakban nyissa meg az **FTP-állomásnév** vagy **FTPS-állomásnév** alatt megjelenő URL-címet az **alkalmazás Áttekintés lapján.**

6. Jelentkezzen be a korábban létrehozott FTP-hitelesítő adatokkal (beleértve a felhasználónév alkalmazásnév-előtagot is).

    A böngésző az alkalmazás gyökérmappáját jeleníti meg.

7. Nyissa meg a *LogFiles* mappát.

    ![LogFiles mappa megnyitása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Nyissa meg a W3SVC nevű mappát és egy numerikus értéket.

    ![W3SVC mappa megnyitása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A mappa tartalmaz XML-fájlokat a sikertelen kérelmek nyomon követésének engedélyezése után naplózott hibákhoz, valamint egy XSL-fájlt, amelyet a böngésző az XML formázására használhat.

    ![W3SVC mappa](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Kattintson annak a sikertelen kérelemnek az XML-fájljára, amelyhez nyomkövetési információkat szeretne látni.

    A következő ábra egy mintahiba nyomkövetési információinak egy részét mutatja be.

    ![Sikertelen kérelmek nyomon követése a böngészőben](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Következő lépések
Látta, hogy a Visual Studio hogyan teszi lehetővé az App Service-alkalmazások által létrehozott naplók megtekintését. Az alábbi szakaszok a kapcsolódó témakörökhöz kapcsolódó további forrásokra mutató hivatkozásokat tartalmaznak:

* Az App Service hibaelhárítása
* Hibakeresés a Visual Studióban
* Távoli hibakeresés az Azure-ban
* Nyomkövetés ASP.NET alkalmazásokban
* Webkiszolgáló-naplók elemzése
* Sikertelen kérelmek nyomkövetési naplóinak elemzése
* Felhőszolgáltatások hibakeresése

### <a name="app-service-troubleshooting"></a>Az App Service hibaelhárítása
Az Azure App Service-ben az alkalmazások hibaelhárításáról az alábbi forrásokban talál további információt:

* [Az alkalmazások figyelése](web-sites-monitor.md)
* [Memóriavesztések vizsgálata az Azure App Service szolgáltatásban a Visual Studio 2013-mal.](https://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx) Microsoft ALM blogbejegyzés a Visual Studio felügyelt memóriaproblémák elemzésére szolgáló szolgáltatásairól.
* [Az Azure App Service online eszközeiről, amelyekről tudnia kell.](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/) Blogbejegyzést amit Alma.

Ha segítségre van szüksége egy adott hibaelhárítási kérdéssel kapcsolatban, indítson el egy hozzászólásláncot az alábbi fórumok egyikén:

* [Az Azure-fórum a ASP.NET webhelyen](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Az Azure fórum a Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-webapps.html).
* [StackOverflow.com.](https://www.stackoverflow.com)

### <a name="debugging-in-visual-studio"></a>Hibakeresés a Visual Studióban
A Hibakeresési mód Visual Studio-ban való használatáról a [Hibakeresés a Visual Studio ban](/visualstudio/debugger/debugging-in-visual-studio) és a Tippek a Visual Studio [2010-ben](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)című témakörben olvashat bővebben.

### <a name="remote-debugging-in-azure"></a>Távoli hibakeresés az Azure-ban
Az App Service-alkalmazások és a WebJobs távoli hibakereséséről az alábbi forrásokban talál további információt:

* [Bevezetés az Azure App Service távoli hibakeresésébe.](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/)
* [Bevezetés az Azure App Service távoli hibakeresésének 2.](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Bevezetés a távoli hibakeresésbe az Azure App Service 3.](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs hibakeresés (videó)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Ha az alkalmazás Azure-web API-t vagy mobilszolgáltatások-háttérszolgáltatást használ, és ezt hibakereséssel kell ellátnia, olvassa el [a .NET háttérhiba](https://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx)a Visual Studio ban.

### <a name="tracing-in-aspnet-applications"></a>Nyomkövetés ASP.NET alkalmazásokban
Nincsenek alapos és naprakész bemutatkozások ASP.NET nyomon követéshez az interneten. A legjobb, amit tehetünk, hogy az induláshoz a régi bevezető anyagokat írt webes űrlapok, mert MVC még nem létezett, és kiegészíti, hogy az újabb blogbejegyzések, amelyek középpontjában a konkrét kérdéseket. Néhány jó kiindulópont a következő források:

* [Figyelés és telemetria (Valós felhőalapú alkalmazások létrehozása az Azure-ral).](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)<br>
  E-book fejezet javaslatokat az Azure felhőalapú alkalmazások nyomon követésére.
* [ASP.NET nyomkövetés](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Régi, de még mindig jó forrás egy alapvető bevezetése a témához.
* [Nyomon hallgatók](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Információ a nyomfigyelőkről, de nem említi a [WebPageTraceListener t.](/dotnet/api/system.web.webpagetracelistener)
* [Forgatókönyv: A ASP.NET követés integrálása a System.Diagnostics Nyomkövetésbe](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Ez a cikk is régi, de tartalmaz néhány további információt, hogy a bevezető cikk nem terjed ki.
* [Nyomkövetés ASP.NET MVC Razor Nézetek](https://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Mellett nyomon követése Razor nézetek, a post is elmagyarázza, hogyan lehet létrehozni egy hibaszűrő annak érdekében, hogy jelentkezzen be az összes kezeletlen kivételek egy MVC alkalmazás. A nem kezelt kivételek webűrlap-alkalmazásokban való naplózásáról az MSDN [Hibakezelők című teljes példa](/previous-versions/bb397417(v=vs.140)) című témakörglobális.asax példájában talál információt. Ha az MVC-ben vagy a webes űrlapokon bizonyos kivételeket szeretne naplózni, de hagyja, hogy az alapértelmezett keretrendszer-kezelés érvénybe lépjen, a következő példában láthatómódon foghatja el és dobhatja újra:

    ```csharp
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

* [Streamelési diagnosztika nyomkövetési naplózása az Azure parancssorából (plusz pillantás!)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  A parancssor használata az oktatóanyag működéséhez a Visual Studio programban. [A Glimpse](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) ASP.NET alkalmazások hibakeresésének eszköze.
* [Web Apps naplózásés diagnosztika használata - David Ebbo-val](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) és [streaming naplók webalkalmazásokból - David Ebbo-val](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Scott Hanselman és David Ebbo videói.

A hibanaplózáshoz a saját nyomkövetési kód írásának alternatívája egy nyílt forráskódú naplózási keretrendszer, például [az ELMAH](https://nuget.org/packages/elmah/)használata. További információ: [Scott Hanselman blogbejegyzései az ELMAH-ról.](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)

Emellett nem kell használnia ASP.NET `System.Diagnostics` vagy nyomkövetést a streamelési naplók azure-ból való lehívásához. Az App Service alkalmazás streamelési naplószolgáltatása a *LogFiles* mappában található *.txt*, *.html*vagy *.log* fájlokat streameli. Ezért létrehozhat saját naplózási rendszert, amely az alkalmazás fájlrendszerébe ír, és a fájl automatikusan streamelésre és letöltésre kerül. Mindössze annyit kell tennie, hogy olyan alkalmazáskódot ír, amely fájlokat hoz létre a *d:\home\logfiles* mappában.

### <a name="analyzing-web-server-logs"></a>Webkiszolgáló-naplók elemzése
A webkiszolgálói naplók elemzéséről az alábbi forrásokban talál további információt:

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Az adatok webkiszolgálói naplókban (*.log* fájlok) való megtekintésére szolgáló eszköz.
* [Az IIS teljesítményproblémáinak vagy alkalmazáshibáinak elhárítása a LogParser használatával](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Bevezetés a Naplóelemző eszközbe, amely a webkiszolgálónaplóinak elemzésére használható.
* [Blogbejegyzések Robert McMurray segítségével LogParser](https://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Az IIS 7.0, az IIS 7.5 és az IIS 8.0 HTTP-állapotkódja](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Sikertelen kérelmek nyomkövetési naplóinak elemzése
A Microsoft TechNet webhelye tartalmaz egy [sikertelen kérelmekre vonatkozó nyomkövetést tartalmazó szakaszt,](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) amely hasznos lehet a naplók használatának megértéséhez. Ez a dokumentáció azonban elsősorban a sikertelen kérelmek nyomkövetésének konfigurálására összpontosít az IIS-ben, amelyet az Azure App Service-ben nem tehet meg.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
