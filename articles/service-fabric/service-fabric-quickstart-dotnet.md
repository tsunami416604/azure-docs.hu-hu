---
title: .NET-alkalmazás létrehozása a Service Fabricben az Azure-ban | Microsoft Docs
description: Ez a rövid útmutató azt ismerteti, hogyan lehet .NET-alkalmazást létrehozni az Azure-ban a Service Fabric Reliable Services mintaalkalmazásának használatával.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.custom: mvc, devcenter, vs-azure
ms.openlocfilehash: 8e83da53d0b2f71abc1f74a0ca8fbc2405e75bda
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736583"
---
# <a name="quickstart-deploy-a-net-reliable-services-application-to-service-fabric"></a>Gyors útmutató: Egy .NET Service fabric reliable services-alkalmazás üzembe helyezése

Az Azure Service Fabric egy elosztott rendszerplatform, amely skálázható és megbízható mikroszolgáltatások és tárolók üzembe helyezésére és kezelésére szolgál.

Ez a rövid útmutató bemutatja, hogyan helyezheti üzembe első .NET-alkalmazását a Service Fabricben. Az útmutató elvégzése után rendelkezni fog egy ASP.NET Core webes kezelőfelületes szavazóalkalmazással, amely egy, a fürtben található állapotalapú háttérszolgáltatásba menti a szavazati adatokat.

![Képernyőkép az alkalmazásról](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Az alkalmazás használatával a következőkkel ismerkedhet meg:

* Alkalmazás létrehozása a .NET és a Service Fabric használatával
* Az ASP.NET Core használata webes kezelőfelületként
* Alkalmazásadatok tárolása állapotalapú szolgáltatásban
* Alkalmazás helyi hibakeresése
* Az alkalmazás horizontális felskálázása több csomópontra
* Alkalmazás frissítése működés közben

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

1. [Telepítse a Visual Studio 2017-et](https://www.visualstudio.com/) az **Azure-fejlesztési**, valamint az **ASP.NET- és webfejlesztési** számítási feladatokkal.
2. [A Git telepítése](https://git-scm.com/)
3. [Telepítse a Microsoft Azure Service Fabric SDK-t](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Futtassa a következő parancsot, amellyel engedélyezheti a Visual Studio üzembe helyezését a helyi Service Fabric-fürtön:
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
    ```
    
## <a name="build-a-cluster"></a>Fürt létrehozása

Miután telepítette a futtatókörnyezet, az SDK-k, a Visual Studio tools, Docker, és Docker rendszert, hozzon létre egy öt csomópontot számláló helyi fejlesztési fürtöt.

> [!Note]
> A Docker futó programot, ha a fürt létrehozása az oka, hogy a fürt létrehozása a tároló-funkciók engedélyezve. Ha Docker nem fut, akkor újra létre kell hozni a fürtöt, tároló-szolgáltatások engedélyezése.
> Bár nem szükséges ehhez a rövid, az útmutatót követve Docker futtatja, a fürt létrehozásakor elérhető az ajánlott eljárásokat.
> A Docker futásának ellenőrzéséhez nyisson meg egy terminálablakot, majd a `docker ps` parancs futtatásával ellenőrizze, hogy történik-e hiba. Ha a válasz nem jelez hibát, akkor a Docker fut, és készen áll a fürt létrehozására.

1. Nyisson meg egy új, emelt szintű PowerShell-ablakot rendszergazdaként.
2. Futtassa az alábbi PowerShell-parancsot egy fejlesztési fürt létrehozásához:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
3. Futtassa a következő parancsot a helyi cluster manager eszköz elindításához:

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

A Start menüben kattintson a jobb gombbal a Visual Studio ikonra, majd válassza a **Futtatás rendszergazdaként** lehetőséget. Ha csatlakoztatni szeretné a hibakeresőt a szolgáltatásaihoz, rendszergazdaként kell futtatnia a Visual Studiót.

Nyissa meg a **Voting.sln** Visual Studio-megoldást a klónozott adattárból.

Alapértelmezés szerint a szavazóalkalmazás a 8080-as portot figyeli.  Az alkalmazásport a */VotingWeb/PackageRoot/ServiceManifest.xml* fájlban van beállítva.  Az alkalmazásport a **Végpont** elem **Port** attribútumának átírásával módosítható.  Az alkalmazás helyi üzembe helyezéséhez és futtatásához az alkalmazásportnak a számítógépen megnyitva és elérhető állapotban kell lennie.  Ha megváltoztatja az alkalmazásportot, a „8080”-at az egész cikkben helyettesítse be az új alkalmazásport értékével.

Az alkalmazás üzembe helyezéséhez nyomja le az **F5** billentyűt.

> [!NOTE]
> A Visual Studio kimeneti ablakában megjelenik az üzenet "Az alkalmazás URL-címe nincs beállítva vagy nem HTTP/HTTPS URL-címet, a böngésző nem lehet megnyitni az alkalmazáshoz."  Ez az üzenet nem utal hibára, csak arra, hogy a böngésző nem fog automatikusan elindulni.

Az üzembe helyezés befejeződése után elindít egy böngészőt, és nyissa meg a `http://localhost:8080` a webes kezelőfelület az alkalmazás megtekintéséhez.

![Alkalmazás kezelőfelülete](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

Most hozzáadhat szavazási lehetőségeket az alkalmazáshoz, és megkezdheti a szavazatok gyűjtését. Az alkalmazás különálló adatbázis nélkül futtatja és tárolja az összes adatot a Service Fabric-fürtben.

## <a name="walk-through-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás bemutatása

A szavazóalkalmazás két szolgáltatásból áll:

* Webes kezelőfelületi szolgáltatás (VotingWeb) – Olyan ASP.NET Core webes kezelőfelületi szolgáltatás, amely kiszolgálja a weboldalt, és webes API-kat biztosít a háttérszolgáltatással folytatott kommunikációhoz.
* Háttérszolgáltatás (VotingData) – Olyan ASP.NET Core webszolgáltatás, amely egy API-t biztosít a szavazási eredmények tárolásához egy megbízható, lemezen őrzött szótárban.

![Alkalmazásdiagram](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Amikor szavazatot adnak le az alkalmazásban, az alábbi eseményekre kerül sor:

1. A JavaScript HTTP PUT kérelemként elküldi a szavazati kérést a webes API-nak a webes kezelőfelületi szolgáltatásban.

2. A webes kezelőfelületi szolgáltatás egy proxy segítségével megkeresi és továbbítja a HTTP PUT kérelmet a háttérszolgáltatásnak.

3. A háttérszolgáltatás fogadja a bejövő kérelmet, és egy megbízható szótárban tárolja a frissített eredményt, amelyet a fürt több csomópontjára is replikál, és egy lemezen őriz. Az alkalmazás összes adata a fürtön tárolódik, így nincs szükség adatbázisra.

## <a name="debug-in-visual-studio"></a>Hibakeresés a Visual Studióban

Az alkalmazásnak megfelelően kellene futnia, de a hibakeresővel megtekintheti, hogyan működnek az alkalmazás fő részei. Az alkalmazás Visual Studióban történő hibakeresése során egy helyi Service Fabric fejlesztési fürtöt használ. Arra is lehetősége van, hogy a hibakeresési folyamatot a saját forgatókönyvéhez igazítsa. Ebben az alkalmazásban az adatokat a háttérszolgáltatás tárolja egy megbízható szótárban. A Visual Studio alapértelmezés szerint a hibakereső leállításakor eltávolítja az alkalmazást. Az alkalmazás eltávolításával a háttérszolgáltatásban tárolt adatok is el lesznek távolítva. Ha szeretné megtartani az adatokat a hibakeresési munkamenetek között, akkor módosítania kell az **Application Debug Mode** (Alkalmazás hibakeresési módja) tulajdonságot a **Voting** (Szavazás) projektben a Visual Studióban.

Ha szeretné megtekinteni, hogy mi történik a kódban, hajtsa végre a következő lépéseket:

1. Nyissa meg a **/VotingWeb/Controllers/VotesController.cs** fájlt, és állítson be egy töréspontot a webes API **Put** metódusában (69. sor). A fájlt megkeresheti a Visual Studio Megoldáskezelőjében.

2. Nyissa meg a **/VotingData/Controllers/VoteDataController.cs** fájlt, és állítson be egy töréspontot a webes API **Put** metódusában (54. sor).

3. Térjen vissza a böngészőhöz, és kattintson egy szavazási lehetőségre vagy adjon meg egy újat. Az első töréspont a webes kezelőfelület API-vezérlőjében jelentkezik.
    * A böngészőben futó JavaScript ekkor küld kérelmet a webes kezelőfelületi szolgáltatás API-vezérlőjének.

    ![Szavazási kezelőfelületi szolgáltatás hozzáadása](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

    * Elsőként hozza létre a ReverseProxyra mutató URL-címet a háttérszolgáltatás számára **(1)**.
    * Ezután küldje el a HTTP PUT kérelmet a ReverseProxyhoz **(2)**.
    * Végül küldje vissza a választ a háttérszolgáltatásból az ügyfélhez **(3)**.

4. A folytatáshoz nyomja le az **F5** billentyűt.
    - Ha a böngésző kéri adjon olvasási és végrehajtási engedélyeket a ServiceFabricAllowedUsers csoportnak a hibakeresési módban.
    - Ezzel elérte a háttérszolgáltatás töréspontját.

    ![Szavazási háttérszolgáltatás hozzáadása](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

    * A metódus első sorában **(1)** a `StateManager` egy `counts` nevű megbízható szótárat kér le vagy ad meg.
    * A megbízható szótárakban tárolt értékekkel folytatott mindennemű interakcióhoz tranzakcióra van szükség, amelyet ez a using utasítás **(2)** hoz létre.
    * A tranzakcióban frissítse a szavazási lehetőséghez tartozó kulcs értékét, majd véglegesítse a műveletet **(3)**. Ha a véglegesítési metódus visszatért, az adatok frissülnek a szótárban, és a fürt egyéb csomópontjaira is replikálódnak. Az adatok ettől fogva biztonságosan tárolódnak a fürtön, és a háttérszolgáltatás feladatait más csomópontok is átvehetik, míg az adatok továbbra is elérhetők maradnak.
5. A folytatáshoz nyomja le az **F5** billentyűt.

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
7. Az a **Service Fabric-alkalmazás közzététele** párbeszédpanelen ellenőrizze a **frissíteni az alkalmazás jelölőnégyzet**.
8.  Változás **célprofilt** való **PublishProfiles\Local.5Node.xml** és ellenőrizze, hogy **kapcsolati végpont** értékre van állítva **helyi fürt**. 
9. Válassza ki **Upgradovat Aplikaci**.

    ![A Közzététel párbeszédpanel frissítési beállításai](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. Kattintson a **Publish** (Közzététel) gombra.

    Frissítés közben az alkalmazás továbbra is használható. Mivel a fürtön a szolgáltatás két példánya fut, a kérelmek egy része már az alkalmazás frissített verziójával találkozik, míg mások továbbra is a régebbivel.

11. Nyissa meg a böngészőt, és keresse meg a fürt címét az 19080-as portot. Például: `http://localhost:19080/`.
12. Kattintson az **Alkalmazások** csomópontra a fanézetben, majd a jobb oldali ablaktáblán található **Folyamatban lévő frissítések** elemre. Ekkor láthatja, hogy a frissítés hogyan lépked végig a fürt frissítési tartományain, miközben továbblépés előtt minden tartomány állapotát ellenőrzi. A frissítési tartomány zöld színnel jelenik meg a folyamatjelzőn, ha a tartomány állapota ellenőrizve lett.
    ![Frissítési nézet a Service Fabric Explorerben](./media/service-fabric-quickstart-dotnet/upgrading.png)

    A Service Fabric a biztonságos frissítés érdekében a fürt minden egyes csomópontjának frissítése után két percet várakozik. A teljes frissítés körülbelül nyolc percet vesz igénybe.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Alkalmazás létrehozása a .NET és a Service Fabric használatával
* Az ASP.NET Core használata webes kezelőfelületként
* Alkalmazásadatok tárolása állapotalapú szolgáltatásban
* Alkalmazás helyi hibakeresése
* Az alkalmazás horizontális felskálázása több csomópontra
* Alkalmazás frissítése működés közben

A Service Fabrickel és a .NET-tel kapcsolatos további tudnivalókért tekintse át a következő oktatóanyagot:
> [!div class="nextstepaction"]
> [.NET alkalmazás a Service Fabric szolgáltatásban](service-fabric-tutorial-create-dotnet-app.md)
