---
title: "Az Azure App Service szolgáltatásban a Visual Studio használatával webes alkalmazás hibaelhárítása"
description: "Megtudhatja, hogyan hibáinak elhárítása az Azure-webalkalmazás távoli hibakeresés, a nyomkövetés és a Visual Studio 2013-bA beépített naplózási eszközök segítségével."
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: 
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.openlocfilehash: 6b1d5694c4d80a4db584b0c76a044dd596c5d553
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Az Azure App Service szolgáltatásban a Visual Studio használatával webes alkalmazás hibaelhárítása
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag segítségére hibakeresésben egy webalkalmazásba a Visual Studio eszközök használatával történő [App Service](http://go.microsoft.com/fwlink/?LinkId=529714), való futtatásával [hibakeresési mód](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) távolról vagy alkalmazásnaplók és a webkiszolgáló naplóinak megtekintésével.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Az oktatóanyagból a következőket sajátíthatja el:

* Az Azure web app felügyeleti funkciókról a Visual Studio érhetők el.
* Hogyan gyors módosításokat végezhet a távoli webes alkalmazás a Visual Studio távoli nézet használatával.
* Az Azure távolról közben a projekt hibakeresési mód futtatásával fut a webes alkalmazás és a webjobs-feladat.
* Hozza létre őket alkalmazás nyomkövetési naplók létrehozása, és megtekinthetők az alkalmazás közben.
* Megtekintése a webkiszolgáló naplóinak, beleértve a részletes hibaüzeneteket, és a sikertelen kérelmek nyomkövetése.
* Hogyan lehet elküldeni a diagnosztikai naplók az Azure Storage fiókot, és megtekintheti, azokat.

Ha a Visual Studio Ultimate, használhatja [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) a hibakereséshez. IntelliTrace ebben az oktatóanyagban nem jelez.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag együttműködve biztosítja a fejlesztési környezet, a webes projekt és az Azure-webalkalmazásban a beállított [Ismerkedés az Azure és az ASP.NET][GetStarted]. A webjobs-feladatok szakaszokat, szüksége lesz az alkalmazás a létrehozott [Ismerkedés az Azure WebJobs SDK-val][GetStartedWJ].

Ebben az oktatóanyagban szereplő Kódminták C# MVC webalkalmazás, de a hibaelhárítási lépései megegyeznek a Visual Basic és a Web Forms-alkalmazásokhoz.

Az oktatóanyag feltételezi, hogy a Visual Studio 2017 használata. 

A folyamatos átviteli naplók csak akkor működik a .NET-keretrendszer 4-es vagy újabb alkalmazások funkció.

## <a name="sitemanagement"></a>Webes alkalmazás konfigurálása és kezelése
A Visual Studio web app felügyeleti funkció és konfigurációs beállításai egy részét, hozzáférést biztosít a [Azure-portálon](http://go.microsoft.com/fwlink/?LinkId=529715). Ebben a szakaszban láthatja, mi érhető el a **Server Explorer**. A legújabb Azure integrációs szolgáltatások megtekintéséhez próbálja ki **Cloud Explorer** is. Mindkét windows megnyithatja a **nézet** menü.

1. Ha még nem jelentkezett be az Azure-ba, a Visual Studióban, kattintson a jobb gombbal **Azure** válassza ki a kapcsolódás **Microsoft Azure-előfizetés** a **Server Explorer**.

    Alternatív egy felügyeleti tanúsítványt, amely lehetővé teszi, hogy hozzáfér a fiókjához telepíteni fogja. Ha a tanúsítvány telepítését választja, kattintson a jobb gombbal a **Azure** csomópontja **Server Explorer**, majd válassza ki **kezelése és a szűrő előfizetések** a helyi menü. Az a **kezelése a Microsoft Azure-előfizetések** párbeszédpanel, kattintson a **tanúsítványok** fülre, majd **importálási**. Kövesse az utasításokat, töltse le, és importálja az előfizetés-fájl (más néven a *.publishsettings* fájl) Azure-fiókját.

   > [!NOTE]
   > Ha egy előfizetés fájl letöltése mentse azt a mappát a forrás kód könyvtárak (például a Letöltések mappába) kívül, és törölje azt az importálás befejezése után. Egy rosszindulatú felhasználó, aki hozzáfér az előfizetési fájl szerkesztése, létrehozása és törlése az Azure-szolgáltatások.
   >
   >

    Azure-erőforrások csatlakozik a Visual Studio eszközből kapcsolatos további információkért lásd: [fiókok kezelése, előfizetések és rendszergazdai szerepkörök](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. A **Server Explorer**, bontsa ki a **Azure** csomópontot **App Service**.
3. Bontsa ki az erőforráscsoportot, amely tartalmazza a létrehozott webalkalmazás [az ASP.NET-webalkalmazás létrehozása az Azure][app-service-web-get-started-dotnet.md], majd kattintson a jobb gombbal a webes alkalmazás csomópont, és kattintson a **nézetbeállítások**.

    ![A Server Explorer beállításainak megjelenítése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    A **Azure Web Apps** lap jelenik meg, és ott a webes alkalmazás felügyeleti és konfigurációs műveleteket a Visual Studio elérhető láthatja.

    ![Az Azure Web App ablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Ebben az oktatóanyagban a naplózás és nyomkövetés legördülő listákat fogja használni. Is alkalmazhatja távoli hibakeresés, de más módszert fogja használni az engedélyezéshez.

    Az alkalmazásbeállítások és kapcsolati karakterláncok jelölőnégyzetéből, ebben az ablakban kapcsolatos információkért lásd: [Azure Web Apps alkalmazások: hogyan alkalmazás karakterláncok és a kapcsolati karakterláncok munkahelyi](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Ha szeretné elvégezni egy webes alkalmazás-felügyeleti feladatot, amely nem hajtható végre ebben az ablakban, kattintson a **nyissa meg a felügyeleti portálon** való nyisson meg egy böngészőablakot az Azure portálon.

## <a name="remoteview"></a>Web Server Explorer alkalmazás fájlok elérése
A webes projekt általában telepítése a `customErrors` jelző beállítása a Web.config fájlban `On` vagy `RemoteOnly`, ami azt jelenti, nem kap egy hasznos hibaüzenet, ha valami hibás működés vagy művelet. Sok hiba összes kap egy oldal, például a következő néhányat a meglévők közül egyik:

**Kiszolgálóhiba a "/" alkalmazás:**

![Unhelpful hibaüzenetet megjelenítő lap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Hiba történt:**

![Unhelpful hibaüzenetet megjelenítő lap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**A lap nem jeleníthető meg, a webhely**

![Unhelpful hibaüzenetet megjelenítő lap](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Gyakran a legegyszerűbb módja, ha a hiba okát az engedélyezi a részletes hibaüzenetek, amelyből az első az előző képernyőfelvételeken megtudhatja, hogyan kell végrehajtani. A telepített Web.config fájl változása igényel. Módosíthatja a *Web.config* a projekt fájlt, és telepítse újra a projektet, vagy hozzon létre egy [Web.config transzformálása](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) és hibakeresési buildet telepítsen, de gyorsabban: a **megoldáskezelő** , közvetlenül megtekintése és szerkesztése a fájlok a távoli webes alkalmazás használatával az *távoli nézet* szolgáltatás.

1. A **Server Explorer**, bontsa ki a **Azure**, bontsa ki a **App Service**, és bontsa ki az erőforráscsoportot, amely a webalkalmazás található, majd bontsa ki a csomópontot, a webalkalmazás.

    Megjelenik a csomópontot, amely hozzáférést biztosít a webalkalmazás tartalmi fájlokat és naplófájlokat.
2. Bontsa ki a **fájlok** csomópontját, és kattintson duplán a *Web.config* fájlt.

    ![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    A Visual Studio a Web.config fájl megnyitása a távoli webes alkalmazás, és a címsorában található a fájl neve melletti [távoli] mutatja.
3. Adja hozzá a következő sort a `system.web` elem:

    `<customErrors mode="Off"></customErrors>`

    ![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Frissítse a böngészőt, amely a unhelpful hibaüzenet látható, és most kap egy részletes hibaüzenetet, például az alábbi példa:

    ![Részletes hibaüzenet](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (A hiba látható hozta létre a vörösen láthatók a sor hozzáadása *Views\Home\Index.cshtml*.)

A Web.config fájl szerkesztése csak a csak egy példa a forgatókönyvet, amelyben elolvashassák és szerkeszthessék az Azure-webalkalmazásban fájlokat képes könnyebb legyen a hibaelhárítás.

## <a name="remotedebug"></a>Távoli hibakeresési webalkalmazások
Ha a részletes hibaüzenet nem elegendő információt tartalmaznak, és nem lehet újra létrehozni a hiba helyileg, egy másik hibaelhárítása módja távolról hibakeresési módban fusson. Állítson be töréspontokat, kezelheti közvetlenül a memória, kód teljesítéséhez, és még a kód elérési útjának módosítása.

Távoli hibakeresés nem működik a Visual Studio Express kiadásait.

Ez a szakasz bemutatja, hogyan segítségével távolról hoz létre a projekt hibakeresési [egy ASP.NET-webalkalmazás létrehozása az Azure-ban](app-service-web-get-started-dotnet.md).

1. Nyissa meg a webes projektet a [egy ASP.NET-webalkalmazás létrehozása az Azure-ban](app-service-web-get-started-dotnet.md).

2. Nyissa meg *Controllers\HomeController.cs*.

3. Törölje a `About()` metódus és a beszúrás helyére az alábbi kódot.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }
4. [Állítson be egy töréspontot](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) a a `ViewBag.Message` sor.

5. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, majd kattintson **közzététel**.

6. Az a **profil** legördülő listában válassza azonos profilját, amikor a használt [egy ASP.NET-webalkalmazás létrehozása az Azure-ban](app-service-web-get-started-dotnet.md). Kattintson a beállítások.

7. Az a **közzététel** párbeszédpanel, kattintson a **beállítások** lapra, és módosítsa **konfigurációs** való **Debug**, és kattintson a  **Mentés**.

    ![Tegye közzé ezt a hibakeresési mód](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Kattintson a **Publish** (Közzététel) gombra. A telepítés utáni befejeződik, és a böngésző megnyitja a webalkalmazás Azure URL-címét, zárja be a böngészőt.

9. A **Server Explorer**, kattintson a jobb gombbal a webalkalmazást, és kattintson a **csatolása hibakereső**.

    ![Hibakereső csatlakoztatása](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    A böngésző automatikusan megnyitja a kezdőlapra, Azure-beli. Lehetséges, hogy 20 másodperc vagy, várjon, amíg Azure állít be a kiszolgáló a hibakereséshez. Ez a késés csak akkor fordul elő, az első futtatásakor hibakeresési módban egy webalkalmazásban 48 órás időszakban. Újra az azonos időszakban hibakeresési indításakor nincs késleltetés.

    > [!NOTE] 
    > Ha bármilyen problémát tapasztal a hibakereső indítása, megpróbálja használatával teheti **Cloud Explorer** helyett **Server Explorer**.
    >

10. Kattintson a **kapcsolatos** a menüben.

     A Visual Studio leállítja a töréspont, és a kódot nem a helyi számítógépen fut, az Azure-ban.

11. Vigye a `currentTime` változó az idő értékének megtekintéséhez.

     ![Azure-beli hibakeresési módban nézet változó](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     Látni ideje az Azure-kiszolgáló ideje, ami esetleg egy másik időzónába, mint a helyi számítógépen.

12. Adjon meg új értéket a `currentTime` változó, például az "Azure-ban már futó".

13. Nyomja le az F5 billentyűt továbbra is futnak.

     Az Azure-beli jogi tudnivalók megjelenítése Névjegy oldalon az új érték, akkor a currentTime változó való megadott jeleníti meg.

     ![Új értékkel rendelkező oldalról](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a>Távoli hibakeresési webjobs-feladatok
Ez a szakasz bemutatja, hogyan segítségével távolról hoz létre a projekt és a webes alkalmazás hibakeresése [Ismerkedés az Azure WebJobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).

Az itt látható szolgáltatások váltak elérhetővé, csak a Visual Studio 2013 Update 4 vagy újabb.

Távoli hibakeresés csak működik együtt a folyamatos webjobs-feladatok. Ütemezett és igény szerinti webjobs-feladatok nem támogatják a hibakeresést.

1. Nyissa meg a webes projektet a [Ismerkedés az Azure WebJobs SDK-val][GetStartedWJ].

2. A ContosoAdsWebJob projektben nyissa meg a *Functions.cs*.

3. [Állítson be egy töréspontot](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) a következő utasításnak elsőként a `GnerateThumbnail` metódust.

    ![Set töréspont](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. A **Megoldáskezelőben**, kattintson a jobb gombbal a webes projekt (ne a webjobs-feladat projektre), és kattintson a **közzététel**.

5. Az a **profil** legördülő listában válassza azonos profilját, amikor a használt [Ismerkedés az Azure WebJobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Kattintson a **beállítások** lapon, és módosítsa **konfigurációs** való **Debug**, és kattintson a **közzététel**.

    A Visual Studio telepíti a webkiszolgáló és a webjobs-feladat projektek, és a böngészőben megnyílik a webalkalmazás Azure URL-címét.

7. A **Server Explorer**, bontsa ki a **Azure > az App Service > az erőforráscsoport > a webalkalmazás > WebJobs > Continuous**, és kattintson a jobb gombbal **ContosoAdsWebJob**.

8. Kattintson a **Hibakereső csatlakoztatása**.

    ![Hibakereső csatlakoztatása](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    A böngésző automatikusan megnyitja a kezdőlapra, Azure-beli. Lehetséges, hogy 20 másodperc vagy, várjon, amíg Azure állít be a kiszolgáló a hibakereséshez. Ez a késés csak akkor fordul elő, az első futtatásakor hibakeresési módban egy webalkalmazásban 48 órás időszakban. Újra az azonos időszakban hibakeresési indításakor nincs késleltetés.

9. A webböngészőben, amely a Contoso Ads kezdőlap meg van nyitva hozzon létre egy új ad.

    Egy üzenetsor-üzenetet létrehozni, amely a webjobs-feladat észlelnie és feldolgozott létrehozása az ad okoz. Ha a WebJobs SDK a függvényt hívja az üzenetsorban lévő üzenetet, a kód találatok feldolgozni a töréspont megjelenését.

10. Ha a hibakereső megsérti a töréspont megjelenését, vizsgálja meg, és változók értékeinek módosítása, miközben a program fut a felhőben. Az alábbi ábrán látható, a hibakereső blobInfo objektum lett átadva tartalmát jeleníti meg a `GenerateThumbnail` metódust.

     ![a hibakereső blobInfo objektum](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Nyomja le az F5 billentyűt továbbra is futnak.

     A `GenerateThumbnail` metódus létrehozta a miniatűr.

12. A böngészőben frissítse az Index lapot, és a miniatűr megjelenítéséhez.

13. A Visual Studio nyomja le az ENTER SHIFT + F5 billentyűkombinációt a állítsa le a hibakeresést.

14. A **Server Explorer**, kattintson a jobb gombbal a ContosoAdsWebJob csomópont, és kattintson a **irányítópult nézet**.

15. Jelentkezzen be Azure hitelesítő adataival, és kattintson a webjobs-feladat neve az a webjobs-feladat a lap megnyitásához.

     ![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Az irányítópult azt mutatja, hogy a `GenerateThumbnail` nemrég végrehajtott függvény.

     (A legközelebb kattint **irányítópult nézet**, nem kell bejelentkezni, és a böngésző közvetlenül a lapra ugrik a webjobs-feladat a.)

16. Kattintson a függvény nevét, a függvény végrehajtása kapcsolatos részletek megtekintéséhez.

     ![Függvény részletei](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Ha a függvény [naplók megírt](https://github.com/Azure/azure-webjobs-sdk/wiki), sikerült kattintson **ToggleOutput** megtekintheti őket.

## <a name="notes-about-remote-debugging"></a>Távoli hibakereséssel kapcsolatos megjegyzések

* Éles hibakeresési módban nem ajánlott. Az éles webes alkalmazás nem horizontálisan több server-példány, ha hibakeresési megakadályozza, hogy a webkiszolgáló egyéb kérésekre válaszol. Ha több web server-példány, a hibakereső csatolása rendelkezik, egy véletlenszerű példányt kap, és nincs mód annak ellenőrzésére, hogy a későbbi böngésző kérelmek Ugrás ugyanazon rendelkezik. Is általában nem kell telepíteni a hibakeresési buildet üzemi környezetben, és a fordító optimalizálásokat kiadás buildek esetében előfordulhat, hogy lehetetlenné megjelenítése, mi történik a forráskód soronként. A termelési problémák elhárításához, a legjobb erőforrás alkalmazás nyomkövetés és web server-naplók.
* Hosszú leáll, amikor távoli töréspontok elkerülése hibakeresést. Azure értékként kezelje-e a folyamat, amely le van állítva, több mint egy nem válaszoló folyamatként néhány percig, és leállítja azt.
* Hibakeresése, közben a kiszolgáló küld adatokat a Visual Studio, amelyek hatással lehetnek a sávszélesség-költségek. Sávszélesség díjszabás kapcsolatos információkért lásd: [Azure díjszabása](https://azure.microsoft.com/pricing/calculator/).
* Győződjön meg arról, hogy a `debug` attribútuma a `compilation` eleme a *Web.config* fájl értéke TRUE. Az értéke igaz, ha hibakeresési buildet konfiguráció közzéteszi alapértelmezés szerint.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>
* Ha talál meg, hogy a hibakereső a kódot, amelyet szeretne debug nem lép, lehetséges, hogy a csak saját kód beállítását.  További információkért lásd: [korlátozása csak saját kód léptetési](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).
* Egy felszabadításakor kezdődik a kiszolgálón, ha engedélyezi a távoli hibakeresési szolgáltatást, és 48 óra elteltével a szolgáltatás automatikusan ki van kapcsolva. Ezt a határt 48 órán biztonsági és teljesítménynövelő okokból kell elvégezni. Könnyen bekapcsolása a szolgáltatás ismét annyi alkalommal. Azt javasoljuk, hogy le van tiltva, akkor nem aktívan hibakeresése esetén hagyja.
* A hibakereső semmilyen teendője, nem csak a webes alkalmazás folyamat (w3wp.exe) manuális csatolása. A Visual Studio hibakeresési mód használatával kapcsolatos további információkért lásd: [a Visual Studio hibakeresési](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Diagnosztikai naplók – áttekintés
Az ASP.NET-alkalmazások az Azure-webalkalmazásban futó naplók a következő típusú hozhat létre:

* **Alkalmazás nyomkövetési naplók**<br/>
  Az alkalmazás létrehozza a naplók módszerek meghívásával a [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) osztály.
* **Webkiszolgáló naplóinak**<br/>
  A webkiszolgáló minden HTTP-kérelem a webalkalmazás a naplóbejegyzés hoz létre.
* **Részletes hibanaplókat üzenet**<br/>
  A webkiszolgáló HTML-lapot hoz létre a sikertelen HTTP-kérelmek (a kérelmek állapotkód: 400 vagy nagyobb eredményező) további információkra.
* **Sikertelen kérelmek nyomkövetési naplóit**<br/>
  A webkiszolgáló egy XML-fájlt hoz létre a részletes nyomkövetési adatok a sikertelen HTTP-kérelmekre. A webalkalmazás-kiszolgáló is biztosít egy XSL fájlt a böngészőben XML formátumú.

Naplózási befolyásolja a webes alkalmazás teljesítménye, így Azure lehetővé teszi a engedélyezhető vagy tiltható le egyes naplókhoz, igény szerint. Alkalmazásnaplók megadhatja, hogy csak egy bizonyos súlyossági szint fölé naplók kell írni. Amikor hoz létre egy új webalkalmazást, alapértelmezés szerint az összes naplózás le van tiltva.

Írja a naplókat a fájlokat egy *naplófájlok* mappa a fájlrendszer a webalkalmazást és FTP-n keresztül érhető el. Webkiszolgáló naplóinak és az alkalmazásnaplók is is beírhatók az Azure Storage-fiók. A storage-fiók, mint ha a fájlrendszer-naplók nagyobb mennyiségű őrizheti meg. Ön egy legfeljebb 100 megabájtos naplói a fájlrendszer használatakor. (Csak a rövid távú megőrzési fájl rendszer naplóit is. Azure törli a régi naplófájlokat, és ellenőrizze az újaknak, a határérték elérése után.)  

## <a name="apptracelogs"></a>Hozzon létre, és az alkalmazás nyomkövetési naplók megtekintése
Ebben a szakaszban a következő feladatokat hajthatnak végre:

* Nyomkövetési utasítások hozzáadása a webes projekthez létrehozott [Ismerkedés az Azure és az ASP.NET][GetStarted].
* A naplók megtekintéséhez a projekt helyi futtatásakor.
* A naplók megtekintéséhez, amikor azok jönnek létre az Azure-ban futó alkalmazás.

Alkalmazás létrehozásával kapcsolatos információkat naplózza a webjobs-feladatok, a következő témakörben: [hogyan működnek az Azure queue storage használata a WebJobs SDK - naplókat írásával](https://github.com/Azure/azure-webjobs-sdk/wiki). Az alábbi utasításokat a naplók megtekintése, és azzal kapcsolatban, hogyan vannak tárolva az Azure webjobs-feladatok által létrehozott alkalmazásnaplók is vonatkozik.

### <a name="add-tracing-statements-to-the-application"></a>Nyomkövetési utasítások hozzáadni az alkalmazáshoz
1. Nyissa meg *Controllers\HomeController.cs*, és cserélje le a `Index`, `About`, és `Contact` kódot hozzáadásához a következő módszerek `Trace` utasítások és egy `using` nyilatkozata `System.Diagnostics`:

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
2. Adja hozzá a `using System.Diagnostics;` nyilatkozatot, így a fájl elejéhez.

### <a name="view-the-tracing-output-locally"></a>A nyomkövetési kimeneti helyileg megtekintése
1. Nyomja le az F5 billentyűt az alkalmazás hibakeresési módban.

    Az alapértelmezett nyomkövetés-figyelő minden nyomkövetési kimenetet ír a **kimeneti** együtt más hibakeresési kimeneti ablakban. Az alábbi ábrán a nyomkövetési utasítások hozzáadott kimenetét a `Index` metódust.

    ![Nyomkövetés hibakeresési ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    A következő lépések bemutatják a nyomkövetési kimeneti megtekintése egy weblapon hibakeresési módban fordítása nélkül.
2. Nyissa meg az alkalmazás Web.config fájljában (a egy, a projekt mappában található), és adja hozzá a `<system.diagnostics>` elem csak a Bezárás előtt a fájl végén `</configuration>` elem:

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

    A `WebPageTraceListener` lehetővé megtekintését megkeresésével nyomon követése kimeneti `/trace.axd`.
3. Adja hozzá a <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">nyomkövetési elem</a> alatt `<system.web>` a Web.config fájlban, például az alábbi példa:

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
4. Az alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.
5. Adja hozzá a böngészőablak címsorában, *trace.axd* az URL-címet, és nyomja le az Enter (az URL-cím hasonlít http://localhost:53370/trace.axd).
6. Az a **alkalmazás nyomkövetési** kattintson **részleteinek megtekintése** az első sorban (nem a BrowserLink).

    ![Trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    A **kérelem részleteinek** lap jelenik meg, majd a a **nyomkövetési adatok** szakasz kimenetét a hozzáadott nyomkövetési utasítások látja a `Index` metódust.

    ![Trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Alapértelmezés szerint `trace.axd` csak helyileg nem érhető el. Ha elérhetővé tétele egy távoli webalkalmazásról szeretne, hozzáadhatja `localOnly="false"` számára a `trace` eleme a *Web.config* fájl, a következő példában látható módon:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Azonban engedélyezése `trace.axd` egy éles webes biztonsági okokból nem ajánlott alkalmazást. A következő szakaszokban láthatja a egyszerűbb az Azure-webalkalmazás nyomkövetési naplók olvasását.

### <a name="view-the-tracing-output-in-azure"></a>Nyomkövetés eredményének megtekintéséhez az Azure-ban
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a webes projektet, és kattintson a **közzététel**.
2. Az a **webhely közzététele** párbeszédpanel, kattintson a **közzététel**.

    Után a Visual Studio közzéteszi a frissítést, a kezdőlapra egy böngészőablakban nyílik meg (feltéve, hogy ezt nem kapcsolhatja **URL-címre** a a **kapcsolat** lap).
3. A **Server Explorer**, kattintson a jobb gombbal a webalkalmazást, és válassza ki **folyamatos átviteli naplók megtekintése**.

    ![Folyamatos átviteli naplók megtekintése a helyi menü](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    A **kimeneti** ablak mutatja, hogy a napló-adatfolyam-szolgáltatáshoz csatlakozik, és hozzáad egy értesítési sor minden perce, amely a napló megjelenítése nélkül kerül.

    ![Folyamatos átviteli naplók megtekintése a helyi menü](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Az alkalmazás kezdőlapját megjelenítő böngészőablakban kattintson **forduljon**.

    Néhány másodpercen belül a hibaszintet kimenete nyomkövetési meg hozzáadni a `Contact` metódus megjelenik a **kimeneti** ablak.

    ![Hiba történt a nyomkövetési kimeneti ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio van a hibaszintet nyomkövetések csak jelennek meg, mert a szolgáltatás figyelése napló engedélyezése esetén ez az alapértelmezett beállítás. Amikor létrehoz egy új Azure-webalkalmazásban, az összes naplózás le van tiltva alapértelmezés szerint ahogy azt a beállítások lapon korábban megnyitásakor:

    ![Kijelentkezés alkalmazás](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Azonban ha kijelölt **folyamatos átviteli naplók megtekintése**, Visual Studio automatikusan **alkalmazás Logging(File System)** való **hiba**, ami azt jelenti, hogy hiba szintű naplók jelentett beolvasása. A nyomkövetési naplók megjelenítéséhez ezt a beállítást módosíthatja **részletes**. Amikor kiválaszt egy súlyossági szintje alacsonyabb, mint a hiba, magasabb súlyossági szintek összes naplójának is jelenti. Ezért részletes kiválasztásakor is látni információ, figyelmeztetés és hibanaplókat.  

1. A **Server Explorer**, kattintson a jobb gombbal a webalkalmazást, és kattintson a **nézetbeállítások** úgy, ahogy korábban.
2. Változás **Alkalmazásnaplózást (fájlrendszer)** való **részletes**, és kattintson a **mentése**.

    ![Részletes nyomkövetési szint](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. Most megjelenítő böngészőablakban a **forduljon** lapján kattintson **Home**, majd kattintson a **kapcsolatos**, és kattintson a **forduljon**.

    Néhány másodpercen belül a **kimeneti** ablakban láthatók a nyomkövetési kimenetet.

    ![Részletes nyomkövetés](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Ebben a szakaszban engedélyezve van, és le van tiltva a naplózás Azure webalkalmazás-beállítások használatával. Is engedélyezése és letiltása nyomkövetésfigyelők a Web.config fájl módosításával. Azonban a Web.config fájl módosítása hatására újrahasznosítása, amíg az alkalmazás konfigurációját keresztül naplózás engedélyezése nem végez, amely az alkalmazástartományban. Ha a probléma reprodukálásához szükséges, hosszú ideig tart vagy időszakos, az alkalmazástartomány újrahasznosítási előfordulhat, hogy "javítás", és várjon, amíg ez akkor fordul elő újra kényszerítése. Az Azure diagnostics engedélyezése lehetővé teszi hibainformációk azonnal rögzítés alkalmazástartomány újrahasznosítási nélkül.

### <a name="output-window-features"></a>A kimeneti ablakban szolgáltatások
A **Microsoft Azure naplók** lapján a **kimeneti** ablak több gombok és szövegmező rendelkezik:

![Naplófájlok lap gombok](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Ezek a hajtsa végre a következő funkciókat:

* Törölje a jelet a **kimeneti** ablak.
* Engedélyezi vagy letiltja a sortörés.
* Indítsa el, vagy állítsa le a naplók figyelése.
* Adja meg, amelyek figyelése naplózza.
* Naplók letöltéséhez.
* A keresési karakterláncot vagy reguláris kifejezést alapján szűrni.
* Zárja be a **kimeneti** ablak.

Ha megad egy keresési karakterláncot vagy reguláris kifejezést, a Visual Studio szűrők naplóinformációk az ügyfélen. Ez azt jelenti, hogy a feltételeket adhatja meg, miután jelennek meg a naplókat a **kimeneti** ablakban és szűrési feltételeket módosíthatja anélkül, hogy a naplók újragenerálja.

## <a name="webserverlogs"></a>Web server naplók megtekintése
Webkiszolgáló naplóinak jegyezze fel a webalkalmazáshoz tartozó összes HTTP-tevékenység. Ahhoz, hogy azok a a **kimeneti** ablakban kell lehetővé teszi a webalkalmazás, és kérje meg a Visual Studio, hogy szeretné-e a figyelheti azokat.

1. Az a **Azure Web App konfigurációs** lapon, a megnyitott **Server Explorer**, módosítsa a Web Server naplózásának **a**, és kattintson a **mentése**.

    ![Web server naplózásának engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Az a **kimeneti** ablak, kattintson a **adja meg, mely a Microsoft Azure naplózza a figyelheti** gombra.

    ![Adja meg, hogy az Azure naplói figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Az a **Microsoft Azure naplózási beállítások** párbeszédpanelen jelölje ki **webalkalmazás-naplói**, és kattintson a **OK**.

    ![Webkiszolgáló naplóinak figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. A webalkalmazás megjelenítő böngészőablakban kattintson **Home**, kattintson a **kapcsolatos**, és kattintson a **forduljon**.

    Az alkalmazás naplóiban általában az első, a webkiszolgáló naplóinak követ. Lehetséges, hogy Várjon egy ideig, míg a naplók.

    ![Webkiszolgáló naplózza a kimeneti ablakban](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Alapértelmezés szerint amikor a webkiszolgáló naplóinak engedélyezi a Visual Studio használatával Azure írja a naplókat a fájlrendszerben. Alternatív megoldásként az Azure portál segítségével adja meg a naplók lehet írni a tárfiók a blob tárolóhoz webkiszolgálóra.

Ha a portál használatával lehetővé teszi egy Azure storage-fiókot a naplózás a webkiszolgálón, és tiltsa le a Visual Studio naplózás, ha újra engedélyezi a Visual Studio-naplózás a tárolási fiók beállításainak állítja vissza.

## <a name="detailederrorlogs"></a>Részletes hibanaplókat üzenet megtekintése
Részletes hibanaplókat néhány további információval szolgálnak a válasz hibakódok (400 vagy újabb) eredményező HTTP-kérelmekre. Ahhoz, hogy azok a a **kimeneti** ablakban kell lehetővé teszi a webalkalmazás számára, és közölje a Visual Studio, hogy szeretné-e a figyelheti azokat.

1. Az a **Azure Web App konfigurációs** lapon, a megnyitott **Server Explorer**, módosítsa **részletes hibaüzenetek** való **a**, majd Kattintson a **mentése**.

    ![A részletes hibaüzenetek engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Az a **kimeneti** ablak, kattintson a **adja meg, mely a Microsoft Azure naplózza a figyelheti** gombra.

3. Az a **Microsoft Azure naplózási beállítások** párbeszédpanel, kattintson a **összes napló**, és kattintson a **OK**.

    ![Összes napló figyelése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. A böngészőablak címsorában, adja hozzá egy extra karakter 404-es hiba okozza az URL-címet (például `http://localhost:53370/Home/Contactx`), és nyomja le az ENTER billentyűt.

    Néhány másodperc, a részletes hibanapló jelenik meg a Visual Studio **kimeneti** ablak.

    ![Részletes hibanapló - kimeneti ablak](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    CTRL + a hivatkozásra kattintva tekintse meg a böngészőben formátumú kimenet:

    ![Részletes hibanapló - böngészőablakot](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Fájl rendszer naplók letöltése
A naplófájlokat, amelyek alapján a figyelheti a **kimeneti** ablak is letölthető a *.zip* fájlt.

1. Az a **kimeneti** ablak, kattintson a **folyamatos átviteli naplók letöltése**.

    ![Naplófájlok lap gombok](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    A Fájlkezelőben nyitja meg a *letölti* a letöltött fájlt a kijelölt mappában.

    ![A letöltött fájl](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Bontsa ki a *.zip* fájlt, és tekintse meg a következő gyökérmappa-szerkezetében:

    ![A letöltött fájl](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Alkalmazás nyomkövetési naplók vannak *.txt* -fájlok a *LogFiles\Application* mappa.
   * Nincsenek a webkiszolgáló naplói a *.log* -fájlok a *LogFiles\http\RawLogs* mappa. Használhatja például egy eszközt [napló elemző](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) történő megtekintését és kezelését ezeket a fájlokat.
   * Üzenet részletes hibanaplókat a rendszer *.html* -fájlok a *LogFiles\DetailedErrors* mappa.

    (A *központi telepítések* mappa van közzététele; verziókezelő által létrehozott fájlok nem tartalmaz semmit a Visual Studio közzététel kapcsolódik. A *Git* mappa a verziókövetési rendszerrel kapcsolatos nyomainak van a közzététel, és a napló fájl adatfolyam-szolgáltatásra.)  

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
Sikertelen kérelmek nyomkövetési naplóit hasznosak, ha meg kell ismernie, hogyan kezeli az IIS a HTTP-kérések, például az URL-címet írja át vagy hitelesítési problémák forgatókönyvekben részleteit.

Azure-webalkalmazásokban használja a sikertelen kérelmek nyomkövetésére vonatkozó funkcióját, amelyet az IIS 7.0 és újabb verziói. Nincs hozzáférése az IIS-beállítások, mely hibák naplózásra, azonban konfigurál. Ha engedélyezi a sikertelen kérelmek nyomon követése, a rendszer rögzíti az összes hiba.

Visual Studio használatával engedélyezheti a sikertelen kérelmek nyomon követése, de nem lehet megtekinteni azokat a Visual Studióban. Ezek a naplók olyan XML-fájlok. Az adatfolyam a naplózási szolgáltatás csak figyeli fontosságúnak ítélt olvasható egyszerű szöveges módban fájlok: *.txt*, *.html*, és *.log* fájlokat.

Sikertelen kérelmek nyomkövetési naplóit egy böngészőben, közvetlenül az FTP-n keresztül, vagy helyileg után letöltheti a fájlokat a helyi számítógépen egy FTP eszközzel tekintheti meg. Ebben a szakaszban lesz meg egy böngészőt közvetlenül.

1. A a **konfigurációs** lapján a **Azure Web Apps** ablak, amely a megnyitott **Server Explorer**, módosítsa **sikertelen kérelmek nyomkövetése** számára **a**, és kattintson a **mentése**.

    ![A sikertelen kérelmek nyomkövetésének engedélyezése](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. A böngészőablakban, amely a webes alkalmazás a címsorba egy extra karakter hozzáadása az URL-címet, majd kattintson a 404-es hiba okozza, és adjon meg.

    Ennek hatására létrejön egy sikertelen kérelmek nyomkövetési naplóját, és a következő lépések bemutatják, hogyan lehet megtekinteni, vagy töltse le a naplót.

3. A Visual Studio a a **konfigurációs** lapján a **Azure Web Apps** ablak, kattintson a **nyissa meg a felügyeleti portál**.

4. Az a [Azure-portálon](https://portal.azure.com) **beállítások** a webalkalmazás lapján kattintson **üzembe helyezési hitelesítő adatok**, majd adja meg egy új felhasználónevet és jelszót.

    ![Új FTP-felhasználónév és jelszó](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Bejelentkezéskor, akkor használja a teljes felhasználói nevét tartalmazó előtaggal azt a webalkalmazás neve. Például ha "myid" Adjon meg egy felhasználónevet, és a hely "myexample", hogy jelentkezzen be "myexample\myid".
    >

5. Egy új böngészőablakban nyissa meg a mezőben látható URL-cím **FTP-állomásnév** vagy **FTPS állomásnév** a a **áttekintése** a webes alkalmazás lapját.

6. Jelentkezzen be az FTP hitelesítő adatait, amelyet korábban (beleértve a webes alkalmazás nevének előtagját a felhasználónév) hozott létre.

    A böngészőben a webalkalmazás gyökérmappájában jeleníti meg.

7. Nyissa meg a *naplófájlok* mappát.

    ![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Nyissa meg a W3SVC plusz egy numerikus érték nevű mappát.

    ![Nyissa meg a W3SVC mappa](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A mappa tartalmazza XML-fájlokat, miután engedélyezte a sikertelen kérelmek nyomkövetése naplózott hibákat, és az XSL-fájl, amellyel a böngésző XML formátumú.

    ![W3SVC mappa](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Kattintson a sikertelen kérelmek, melyet szeretne megjeleníteni a nyomkövetési adatok XML-fájljának.

    A következő ábrán egy minta hibához nyomkövetési adatok egy részét.

    ![Sikertelen kérelmek követésének böngészőben](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>További lépések
Megtudhatta, hogyan Visual Studio segítségével egyszerűen egy Azure webalkalmazás által létrehozott naplófájlok megtekintéséhez. Az alábbi szakaszokban további forrásanyagokra mutató hivatkozásokat tartalmaz a kapcsolódó témaköröket:

* Az Azure web app hibaelhárítása
* A Visual Studio hibakereső
* Távoli hibakeresés az Azure-ban
* Nyomkövetés az ASP.NET alkalmazásokban.
* Webkiszolgáló naplóinak elemzésekor
* Elemzése sikertelen kérelmek nyomkövetési naplóit.
* Hibakeresési Cloud Services csomag

### <a name="azure-web-app-troubleshooting"></a>Az Azure web app hibaelhárítása
Az Azure App Service web apps hibaelhárítással kapcsolatos további információkért lásd a következőket:

* [Webalkalmazások figyelése](/manage/services/web-sites/how-to-monitor-websites/)
* [Vizsgálja meg az Azure Web Apps a Visual Studio 2013 memóriavesztés](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Visual Studio szolgáltatások elemzése a Microsoft ALM blogbejegyzésben felügyelt memóriaproblémák léptek fel.
* [Az Azure web apps online eszközök kell tudni](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Amit Apple blogbejegyzést.

Egy adott hibaelhárítási kérdés kapcsolatban elindítani egy szálat a következő fórumok egyikében:

* [Az ASP.NET-webhely Azure fórummal](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Az MSDN Webhelyén az Azure fórumra](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>A Visual Studio hibakereső
A Visual Studio hibakeresési mód használatával kapcsolatos további információkért lásd: [a Visual Studio hibakeresési](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) és [hibakeresési tippeket a Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Távoli hibakeresés az Azure-ban
Az Azure-webalkalmazásokban és webjobs-feladatok távoli hibakereséssel kapcsolatos további információkért lásd a következőket:

* [Bevezetés az Azure App Service Web Apps hibakeresés távoli](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Távoli hibakeresés Azure App Service Web Apps 2. rész - belül távoli hibakeresés bemutatása](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Az Azure App Service Web Apps 3. rész - többpéldányos környezet és a GIT a távoli hibakeresés bemutatása](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs-hibakeresés (videó)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Ha a webalkalmazás az Azure Web API-t vagy a Mobile Services háttér-használ, és végezzen azon hibakeresést, tekintse meg kell [.NET-háttérrendszer hibakeresés a Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Nyomkövetés az ASP.NET alkalmazásokban.
Nincsenek nem alapos, naprakész állapotban Bevezetés az ASP.NET nyomkövetési elérhető az interneten. A legjobb elvégezhető van Ismerkedés a régi bevezető anyagok írt Web Forms mert MVC még létezik, és nem kiegészítése, amely az újabb blog visszaküldés, amelyek arra utalnak a konkrét problémák. Néhány legalkalmasabb helyek indítsa el a rendszer a következőket:

* [Figyelés és Telemetriai (valós felhőalapú alkalmazások összeállítása az Azure-ral)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  E-könyv fejezet az ajánlások a nyomkövetés az Azure felhőalapú alkalmazásokhoz.
* [ASP.NET Tracing](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Régi, de továbbra is a tulajdonos alapszintű bevezetést helyes erőforrás.
* [Nyomkövetésfigyelők](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Információ a nyomkövetésfigyelők, de nem említse meg a [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Forgatókönyv: Az ASP.NET nyomkövetési integrálása System.Diagnostics nyomkövetése](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Ez a cikk is régi, de, amely nem fedi le a bevezető cikkben további információkat tartalmaz.
* [Az ASP.NET MVC Razor nézetekben nyomkövetése](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Nyomkövetés Razor nézetekben, mellett a feladás egy vagy több is bemutatja, hogyan ahhoz, hogy az összes nem kezelt kivétel jelentkezzen be egy MVC alkalmazás egy hiba-szűrő létrehozásához. Összes kezelt kivételek jelentkezzen be egy webes űrlapalkalmazás kapcsolatos információkért lásd: a Global.asax példát [hibakezelők teljes példa](http://msdn.microsoft.com/library/bb397417.aspx) az MSDN Webhelyén. MVC, vagy a Web Forms keretrendszerre Ha azt szeretné, hogy bizonyos kivételeket, de az alapértelmezett keretrendszer hatását kezelése, lehetővé lehet catch és újrakiváltható kivétel a következő példában látható módon:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        }
* [Adatfolyam-továbbítási diagnosztikai nyomkövetési naplózása az Azure parancssori (plusz futó!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  A parancssor használatával milyen Ez az oktatóanyag elvégzéséhez a Visual Studio feladatok végrehajtását ismerteti. [Futó](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) ASP.NET-alkalmazások hibakereséshez eszköz.
* [Webalkalmazások naplózás és diagnosztika - használata a David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) és [a folyamatos átviteli naplók, a webalkalmazások - David Ebbo](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Videók Scott Hanselman és David Ebbo.

A hibák naplózásának, ahelyett, hogy a saját nyomkövetési kód írása, hogy egy nyílt forráskódú naplózási keretrendszert használja, mint [ELMAH](http://nuget.org/packages/elmah/). További információkért lásd: [Scott Hanselman kapcsolatos blogbejegyzéseket ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Is, nem kell használnia az ASP.NET vagy `System.Diagnostics` le adatfolyam-továbbítási nyomkövetési naplók az Azure-ból. A naplózási szolgáltatás streaming Azure web app az adatfolyamokat, bármely *.txt*, *.html*, vagy *.log* fájlt talál, a *naplófájlok* mappát. Ezért létrehozhatja a saját naplózási rendszer, amely a fájlrendszer a webalkalmazás ír, és a fájl automatikusan folyamatos átviteli és letöltve. Ehhez mindössze hoz létre a fájlok írási alkalmazáskód a *d:\home\logfiles* mappa.

### <a name="analyzing-web-server-logs"></a>Webkiszolgáló naplóinak elemzésekor
A webkiszolgáló naplóinak elemzésével kapcsolatos további információkért lásd a következőket:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Egy eszköz a adatok megtekintése a webkiszolgáló naplóinak (*.log* fájlok).
* [IIS teljesítményproblémák vagy LogParser használó alkalmazás-hibák elhárítása](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  A napló-elemző eszköz, amely segítségével elemezheti a webkiszolgáló naplóinak bemutatása.
* [Blogbejegyzéseket által Robert McMurray LogParser használatával](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Az IIS 7.0, IIS 7.5 és IIS 8.0-s HTTP-állapotkód:](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Elemzése sikertelen kérelmek nyomkövetési naplóit.
A Microsoft TechNet webhely tartalmaz egy [használatával sikertelen kérelmek nyomkövetése](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) rész, amely segít annak megértésében, ezek a naplók használata lehet. Azonban ez a dokumentáció összpontosít főként sikertelen kérelmek nyomkövetése konfigurálása az IIS-ben, amely az Azure Web Apps nem hajtható végre.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
