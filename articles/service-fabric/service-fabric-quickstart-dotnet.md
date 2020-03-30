---
title: Gyorsan létrehozhat egy .NET alkalmazást az Azure Service Fabric szolgáltatásában
description: Ez a rövid útmutató azt ismerteti, hogyan lehet .NET-alkalmazást létrehozni az Azure-ban a Service Fabric Reliable Services mintaalkalmazásának használatával.
author: mikkelhegn
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: mikhegn
ms.custom: mvc, devcenter, vs-azure
ms.openlocfilehash: e3d984fee75dcdb8d4e14e7b454e74a3f7c629f2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75730139"
---
# <a name="quickstart-deploy-a-net-reliable-services-application-to-service-fabric"></a>Rövid útmutató: .NET Reliable Services-alkalmazás üzembe helyezése a Service Fabricben

Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe első .NET-alkalmazását a Service Fabricben. Az útmutató elvégzése után rendelkezni fog egy ASP.NET Core webes kezelőfelületes szavazóalkalmazással, amely egy, a fürtben található állapotalapú háttérszolgáltatásba menti a szavazati adatokat.

![Képernyőkép az alkalmazásról](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Az alkalmazás használatával a következőkkel ismerkedhet meg:

* Alkalmazás létrehozása a .NET és a Service Fabric használatával
* Használja ASP.NET a magot webes előtérként
* Alkalmazásadatok tárolása állapotalapú szolgáltatásban
* Alkalmazás helyi hibakeresése
* Az alkalmazás horizontális felskálázása több csomópontra
* Alkalmazás frissítése működés közben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [Telepítse a Visual Studio 2019-et](https://www.visualstudio.com/) az **Azure fejlesztési** és **ASP.NET és webfejlesztési** munkaterhelésekkel.
2. [A Git telepítése](https://git-scm.com/)
3. [A Microsoft Azure Service Fabric SDK telepítése](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Futtassa a következő parancsot, amellyel engedélyezheti a Visual Studio üzembe helyezését a helyi Service Fabric-fürtön:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
   ```
    
## <a name="build-a-cluster"></a>Fürt létrehozása

A futtatókörnyezet telepítése után SDK-k, Visual Studio-eszközök, Docker, és a Docker futása, hozzon létre egy öt csomópontos helyi fejlesztési fürt.

> [!Note]
> Az ok, hogy a Docker fut, amikor létrehozza a fürtet, hogy a fürt jön létre a tároló szolgáltatások engedélyezve van. Ha a Docker nem fut, újra létre kell hoznia a fürtöt a tárolószolgáltatások engedélyezéséhez.
> Bár ez a konkrét rövid útmutató szükségtelen, a Docker-fürt létrehozásakor futó utasítást a legjobb gyakorlatként tartalmazza.
> A Docker futásának ellenőrzéséhez nyisson meg egy terminálablakot, majd a `docker ps` parancs futtatásával ellenőrizze, hogy történik-e hiba. Ha a válasz nem jelez hibát, akkor a Docker fut, és készen áll a fürt létrehozására.
>
> [A Windows 10 vagy a Windows Server beállítása tárolókhoz](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-10-Client)

1. Nyisson meg egy új, emelt szintű PowerShell-ablakot rendszergazdaként.
2. Futtassa az alábbi PowerShell-parancsot egy fejlesztési fürt létrehozásához:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
   ```
3. A helyi fürtkezelő eszköz elindításához futtassa a következő parancsot:

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
   ```

>[!NOTE]
> A jelen rövid útmutatóban szereplő mintaalkalmazás olyan funkciókat használ, amelyek Windows 7 rendszeren nem érhetők el.
>

## <a name="download-the-sample"></a>A minta letöltése

Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

A Start menüben kattintson a jobb gombbal a Visual Studio ikonra, majd válassza a **Futtatás rendszergazdaként** lehetőséget. Ha a hibakeresőt a szolgáltatásokhoz szeretné csatolni, rendszergazdaként kell futtatnia a Visual Studio alkalmazást.

Nyissa meg a **Voting.sln** Visual Studio-megoldást a klónozott adattárból.

Alapértelmezés szerint a Szavazás alkalmazás a 8080-as porton hallgatja.  Az alkalmazásport a */VotingWeb/PackageRoot/ServiceManifest.xml* fájlban van beállítva.  Az alkalmazásport a **Végpont** elem **Port** attribútumának átírásával módosítható.  Az alkalmazás helyi üzembe helyezéséhez és futtatásához az alkalmazásportnak a számítógépen megnyitva és elérhető állapotban kell lennie.  Ha módosítja az alkalmazásportot, cserélje le a cikkben szereplő "8080" új alkalmazásportértékét.

Az alkalmazás üzembe helyezéséhez nyomja le az **F5** billentyűt.

> [!NOTE]
> A Visual Studio kimeneti ablakában a következő üzenet jelenik meg: "Az alkalmazás URL-címe nincs beállítva, vagy nem HTTP/HTTPS URL-cím, így a böngésző nem nyílik meg az alkalmazás számára."  Ez az üzenet nem utal hibára, csak arra, hogy a böngésző nem fog automatikusan elindulni.

Amikor a központi telepítés befejeződött, `http://localhost:8080` indítson el egy böngészőt, és nyissa meg az alkalmazás webes előterének megtekintéséhez.

![Alkalmazás kezelőfelülete](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

Most hozzáadhat szavazási lehetőségeket az alkalmazáshoz, és megkezdheti a szavazatok gyűjtését. Az alkalmazás különálló adatbázis nélkül futtatja és tárolja az összes adatot a Service Fabric-fürtben.

## <a name="walk-through-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás bemutatása

A szavazóalkalmazás két szolgáltatásból áll:

* Webes kezelőfelületi szolgáltatás (VotingWeb) – Olyan ASP.NET Core webes kezelőfelületi szolgáltatás, amely kiszolgálja a weboldalt, és webes API-kat biztosít a háttérszolgáltatással folytatott kommunikációhoz.
* Háttérszolgáltatás (VotingData) – Olyan ASP.NET Core webszolgáltatás, amely egy API-t biztosít a szavazási eredmények tárolásához egy megbízható, lemezen őrzött szótárban.

![Alkalmazásdiagram](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Amikor a jelentkezésben szavaz, a következő események következnek be:

1. A JavaScript HTTP PUT kérelemként elküldi a szavazati kérést a webes API-nak a webes kezelőfelületi szolgáltatásban.

2. A webes kezelőfelületi szolgáltatás egy proxy segítségével megkeresi és továbbítja a HTTP PUT kérelmet a háttérszolgáltatásnak.

3. A háttérszolgáltatás fogadja a bejövő kérelmet, és egy megbízható szótárban tárolja a frissített eredményt, amelyet a fürt több csomópontjára is replikál, és egy lemezen őriz. Az alkalmazás összes adata a fürtön tárolódik, így nincs szükség adatbázisra.

## <a name="debug-in-visual-studio"></a>Hibakeresés a Visual Studióban

Az alkalmazásnak megfelelően kellene futnia, de a hibakeresővel megtekintheti, hogyan működnek az alkalmazás fő részei. Amikor a Visual Studio ban hibakeresés, egy helyi Service Fabric fejlesztői fürt öt. A hibakeresési élményt a forgatókönyvhöz igazíthatja. Ebben az alkalmazásban az adatokat a háttérszolgáltatás tárolja egy megbízható szótárban. A Visual Studio alapértelmezés szerint a hibakereső leállításakor eltávolítja az alkalmazást. Az alkalmazás eltávolításával a háttérszolgáltatásban tárolt adatok is el lesznek távolítva. Ha szeretné megtartani az adatokat a hibakeresési munkamenetek között, akkor módosítania kell az **Application Debug Mode** (Alkalmazás hibakeresési módja) tulajdonságot a **Voting** (Szavazás) projektben a Visual Studióban.

Ha szeretné megtekinteni, hogy mi történik a kódban, hajtsa végre a következő lépéseket:

1. Nyissa meg a **/VotingWeb/Controllers/VotesController.cs** fájlt, és állítson be egy töréspontot a webes API **Put** metódusában (69. sor). A fájlt megkeresheti a Visual Studio Megoldáskezelőjében.

2. Nyissa meg a **/VotingData/Controllers/VoteDataController.cs** fájlt, és állítson be egy töréspontot a webes API **Put** metódusában (54. sor).

3. Térjen vissza a böngészőhöz, és kattintson egy szavazási lehetőségre vagy adjon meg egy újat. Az első töréspont a webes kezelőfelület API-vezérlőjében jelentkezik.
   * Ez a lépés az, ahol a JavaScript a böngészőben küld egy kérést a webes API-vezérlő az előtér-szolgáltatás.

     ![Szavazási kezelőfelületi szolgáltatás hozzáadása](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

   * Elsőként hozza létre a ReverseProxyra mutató URL-címet a háttérszolgáltatás számára **(1)**.
   * Ezután küldje el a HTTP PUT kérelmet a ReverseProxyhoz **(2)**.
   * Végül küldje vissza a választ a háttérszolgáltatásból az ügyfélhez **(3)**.

4. A folytatáshoz nyomja le az **F5 billentyűt**
   - Ha a böngésző kéri adjon olvasási és végrehajtási engedélyeket a ServiceFabricAllowedUsers csoportnak a hibakeresési módban.
   - Most már a töréspontnál vagy a háttérszolgáltatásban.

     ![Szavazási háttérszolgáltatás hozzáadása](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

   - A metódus első sorában **(1)** a `StateManager` egy `counts` nevű megbízható szótárat kér le vagy ad meg.
   - A megbízható szótárakban tárolt értékekkel folytatott mindennemű interakcióhoz tranzakcióra van szükség, amelyet ez a using utasítás **(2)** hoz létre.
   - A tranzakcióban frissítse a szavazási lehetőséghez tartozó kulcs értékét, majd véglegesítse a műveletet **(3)**. Ha a véglegesítési metódus visszatért, az adatok frissülnek a szótárban, és a fürt egyéb csomópontjaira is replikálódnak. Az adatok ettől fogva biztonságosan tárolódnak a fürtön, és a háttérszolgáltatás feladatait más csomópontok is átvehetik, míg az adatok továbbra is elérhetők maradnak.
5. A folytatáshoz nyomja le az **F5 billentyűt**

A hibakeresési munkamenet leállításához nyomja le a **Shift+F5** billentyűkombinációt.

## <a name="perform-a-rolling-application-upgrade"></a>Alkalmazás frissítése működés közben

Ha új frissítéseket telepít az alkalmazásban, a Service Fabric biztonságosan elvégzi a frissítést. A működés közbeni frissítésnek köszönhetően állásidő nélkül végezhet frissítéseket, és hiba esetén a rendszer automatikusan visszaállítást kezdeményez.

Az alkalmazás frissítéséhez tegye a következőket:

1. Nyissa meg a **/VotingWeb/Views/Home/Index.cshtml** fájlt a Visual Studióban.
2. Módosítsa az oldal fejlécét szöveg hozzáadásával vagy a meglévő szöveg frissítésével. Módosítsa például a fejlécet a következőre: „Service Fabric Voting Sample v2”.
3. Mentse a fájlt.
4. A Megoldáskezelőben kattintson a jobb gombbal a **Voting** (Szavazás) elemre, majd válassza a **Publish** (Közzététel) lehetőséget. Ekkor megjelenik a Publish (Közzététel) párbeszédpanel.
5. Kattintson a **Manifest Version** (Jegyzékverzió) gombra a szolgáltatás és az alkalmazás verziójának módosításához.
6. Módosítsa a **Code** (Kód) elemet a **VotingWebPkg** résznél például „2.0.0”-ra, majd kattintson a **Save** (Mentés) gombra.

    ![Verzió módosítása párbeszédpanel](./media/service-fabric-quickstart-dotnet/change-version.png)
7. A **Szolgáltatásháló-alkalmazás közzététele** párbeszédpanelen jelölje be **az Alkalmazás frissítése jelölőnégyzetet.**
8.  Módosítsa **a célprofilt** **PublishProfiles\Local.5Node.xml értékre,** és győződjön meg arról, hogy a **kapcsolatvégpont** **beállítása Helyi fürt**. 
9. Válassza **az Alkalmazás frissítése**lehetőséget.

    ![A Közzététel párbeszédpanel frissítési beállításai](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. Kattintson a **Publish** (Közzététel) gombra.

    Frissítés közben az alkalmazás továbbra is használható. Mivel a fürtön a szolgáltatás két példánya fut, a kérelmek egy része már az alkalmazás frissített verziójával találkozik, míg mások továbbra is a régebbivel.

11. Nyissa meg a böngészőt, és keresse meg a fürtcímét az 19080-as porton. Például: `http://localhost:19080/`.
12. Kattintson az **Alkalmazások** csomópontra a fanézetben, majd a jobb oldali ablaktáblán található **Folyamatban lévő frissítések** elemre. Ekkor láthatja, hogy a frissítés hogyan lépked végig a fürt frissítési tartományain, miközben továbblépés előtt minden tartomány állapotát ellenőrzi. A frissítési tartomány zöld színnel jelenik meg a folyamatjelzőn, ha a tartomány állapota ellenőrizve lett.
    ![Frissítési nézet a Service Fabric Explorerben](./media/service-fabric-quickstart-dotnet/upgrading.png)

    A Service Fabric a biztonságos frissítés érdekében a fürt minden egyes csomópontjának frissítése után két percet várakozik. A teljes frissítés körülbelül nyolc percet vesz igénybe.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Alkalmazás létrehozása a .NET és a Service Fabric használatával
* Használja ASP.NET a magot webes előtérként
* Alkalmazásadatok tárolása állapotalapú szolgáltatásban
* Alkalmazás helyi hibakeresése
* Az alkalmazás horizontális felskálázása több csomópontra
* Alkalmazás frissítése működés közben

A Service Fabrickel és a .NET-tel kapcsolatos további tudnivalókért tekintse át a következő oktatóanyagot:
> [!div class="nextstepaction"]
> [.NET alkalmazás a Service Fabric szolgáltatásban](service-fabric-tutorial-create-dotnet-app.md)
