---
title: Azure Service Fabric vs kódgal az első lépésekhez
description: Ez a cikk áttekintést nyújt a Service Fabric-alkalmazások Visual Studio-kód használatával történő létrehozásáról.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 17bff459a0399d39c6bfdd772ad16d0b5b2f6771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258485"
---
# <a name="service-fabric-for-visual-studio-code"></a>Szolgáltatásfabric visual studio kód

A [Service Fabric Reliable Services bővítmény vs kód](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) biztosítja a szükséges eszközöket a Service Fabric alkalmazások létrehozásához, létrehozásához és hibakereséséhez Windows, Linux és macOS operációs rendszereken.

Ez a cikk áttekintést nyújt a bővítmény követelményeiről és beállításáról, valamint a bővítmény által biztosított különböző parancsok használatáról. 

> [!IMPORTANT]
> A Service Fabric Java-alkalmazások fejleszthetők Windows-gépeken, de csak Azure Linux-fürtökre telepíthetők. A Java-alkalmazások hibakeresését a Windows nem támogatja.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételeket minden környezetben telepíteni kell.

* [Visual Studio kód](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Szolgáltatás fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman generátorok - telepítse a megfelelő generátorok az alkalmazás

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

A Java-fejlesztéshez a következő előfeltételeket kell telepíteni:

* [Java SDK](https://aka.ms/azure-jdks) (1.8-as verzió)
* [Gradle](https://gradle.org/install/)
* [Hibakereső a Java VS-kód bővítményhez](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Java-szolgáltatások hibakereséséhez szükséges. A Java-szolgáltatások hibakeresése csak Linuxon támogatott. A telepítést a VS-kód **tevékenységsávján** található Bővítmények ikonra kattintva, a bővítmény keresésével, vagy a VS Code Piactérről telepítheti.

A .NET Core/C# fejlesztéshez a következő előfeltételeket kell telepíteni:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (2.0.0-s vagy újabb verzió)
* [C# a Visual Studio Code (powered by OmniSharp) VS Kód kiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) A C# szolgáltatások hibakereséséhez szükséges. A telepítést a VS-kód **tevékenységsávján** található Bővítmények ikonra kattintva, a bővítmény keresésével, vagy a VS Code Piactérről telepítheti.

## <a name="setup"></a>Telepítés

1. Nyissa meg a VS-kódot.
2. Kattintson a Vs-kód bal oldalán lévő **Tevékenységsáv Bővítmények** ikonjára. Keressen rá a "Service Fabric" kifejezésre. Kattintson a **Telepítés** a Service Fabric reliable services bővítményhez.

## <a name="commands"></a>Parancsok
A Service Fabric Reliable Services bővítmény a VS-kód számos parancsot biztosít a fejlesztők számára a Service Fabric-projektek létrehozásához és üzembe helyezéséhez. A **parancspalettából** úgy hívhat `(Ctrl + Shift + p)`ja a parancsokat, hogy megnyomja a parancsot, beírja a parancs nevét a beviteli sávba, és kiválasztja a kívánt parancsot a sorlistából. 

* Szolgáltatásfabric: Alkalmazás létrehozása 
* Szolgáltatásháló: Alkalmazás közzététele 
* Szolgáltatásfabric: Alkalmazás telepítése 
* Szolgáltatásfabric: Alkalmazás eltávolítása  
* Szolgáltatásfabric: Build alkalmazás 
* Szolgáltatás fabric: Tiszta alkalmazás 

### <a name="service-fabric-create-application"></a>Szolgáltatásfabric: Alkalmazás létrehozása

A **Service Fabric: Application létrehozása** parancs létrehoz egy új Service Fabric-alkalmazást az aktuális munkaterületen. Attól függően, hogy mely yeoman-generátorok vannak telepítve a fejlesztői gépen, többféle Service Fabric-alkalmazást hozhat létre, beleértve a Java, C#, Container és Vendég projekteket. 

1.  Válassza ki a **Service Fabric: Create Application** parancsot
2.  Válassza ki az új Service Fabric-alkalmazás típusát. 
3.  Adja meg a létrehozni kívánt alkalmazás nevét.
3.  Válassza ki a Service Fabric-alkalmazáshoz hozzáadni kívánt szolgáltatás típusát. 
4.  A szolgáltatás elnevezéséhez kövesse az utasításokat. 
5.  Az új Service Fabric-alkalmazás megjelenik a munkaterületen.
6.  Nyissa meg az új alkalmazásmappát, hogy az legyen a munkaterület gyökérmappája. Innen folytathatja a parancsok végrehajtásának folytatását.

### <a name="service-fabric-add-service"></a>Szolgáltatás fabric: Szolgáltatás hozzáadása
A **Service Fabric: Add Service** parancs egy új szolgáltatást ad hozzá egy meglévő Service Fabric-alkalmazáshoz. A szolgáltatáshoz hozzáadandó alkalmazásnak a munkaterület gyökérkönyvtárának kell lennie. 

1.  Válassza ki a **Service Fabric: Add Service** parancsot.
2.  Válassza ki az aktuális Service Fabric-alkalmazás típusát. 
3.  Válassza ki a Service Fabric-alkalmazáshoz hozzáadni kívánt szolgáltatás típusát. 
4.  A szolgáltatás elnevezéséhez kövesse az utasításokat. 
5.  Az új szolgáltatás megjelenik a projektkönyvtárban. 

### <a name="service-fabric-publish-application"></a>Szolgáltatásháló: Alkalmazás közzététele
A **Service Fabric: Publish Application** parancs telepíti a Service Fabric-alkalmazást egy távoli fürtre. A célfürt lehet biztonságos vagy nem biztonságos. Ha a paraméterek nincsenek beállítva a Cloud.json ban, az alkalmazás a helyi fürtre lesz telepítve.

1.  Az alkalmazás első létrehozásakor egy Cloud.json fájl jön létre a projekt könyvtárában.
2.  Adja meg annak a fürtnek az értékeit, amelyhez csatlakozni szeretne a Cloud.json fájlban.
3.  Válassza ki a **Service Fabric: Publish Application** parancsot.
4.  Tekintse meg a célfürta Service Fabric Explorer annak megerősítéséhez, hogy az alkalmazás telepítve van. 

### <a name="service-fabric-deploy-application-localhost"></a>Szolgáltatásfabric: Alkalmazás telepítése (Localhost)
A **Service Fabric: Deploy Application** parancs telepíti a Service Fabric-alkalmazást a helyi fürtre. A parancs használata előtt győződjön meg arról, hogy a helyi fürt fut. 

1. Válassza ki a **Service Fabric: Deploy Application** parancsot
2. Tekintse meg a helyi fürt szolgáltatás\/fabric explorer (http: /localhost:19080/Explorer) annak megerősítéséhez, hogy az alkalmazás telepítve van. Ez eltarthat egy ideig, ezért légy türelmes.
3. A Service **Fabric: Publish Application** parancs a Cloud.json fájlban beállított paraméterek nélkül is használható egy helyi fürtre való üzembe helyezéshez.

> [!NOTE]
> Windows-gépek en nem támogatott a Java-alkalmazások telepítése a helyi fürtre.

### <a name="service-fabric-remove-application"></a>Szolgáltatásfabric: Alkalmazás eltávolítása
A **Service Fabric: Remove Application** parancs eltávolítja a Service Fabric-alkalmazást a fürtből, amely korábban telepítve volt a VS Code bővítmény használatával. 

1.  Válassza ki a **Service Fabric: Application eltávolítása** parancsot.
2.  Tekintse meg a fürt szolgáltatás hálókezelővel annak megerősítéséhez, hogy az alkalmazás el lett távolítva. Ez eltarthat egy ideig, ezért légy türelmes.

### <a name="service-fabric-build-application"></a>Szolgáltatásfabric: Build alkalmazás
A **Service Fabric: Build Application** parancs java vagy C# Service Fabric-alkalmazásokat hozhat létre. 

1.  A parancs végrehajtása előtt győződjön meg arról, hogy az alkalmazás gyökérmappájában van. A parancs azonosítja az alkalmazás típusát (C# vagy Java), és ennek megfelelően építi fel az alkalmazást.
2.  Válassza ki a **Service Fabric: Build Application** parancsot.
3.  A létrehozási folyamat kimenete az integrált terminálhoz van írva.

### <a name="service-fabric-clean-application"></a>Szolgáltatás fabric: Tiszta alkalmazás
A **Service Fabric: Tiszta alkalmazás** parancs törli az összes jar fájlokat és natív könyvtárak, amelyek a build által létrehozott. Csak Java alkalmazásokra érvényes. 

1.  A parancs végrehajtása előtt győződjön meg arról, hogy az alkalmazás gyökérmappájában van. 
2.  Válassza ki a **Service Fabric: Tiszta alkalmazás** parancsot.
3.  A tiszta folyamat kimenete az integrált terminálhoz van írva.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [fejleszthet és debugolhat C# Service Fabric-alkalmazásokat a VS Code segítségével.](./service-fabric-develop-csharp-applications-with-vs-code.md)
* Ismerje meg, hogyan [fejleszthet és debugolhat Java Service Fabric-alkalmazásokat a VS Code segítségével.](./service-fabric-develop-java-applications-with-vs-code.md)
