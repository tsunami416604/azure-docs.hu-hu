---
title: .NET Core alkalmazások fejlesztése visual studio kóddal
description: Ez a cikk bemutatja, hogyan hozhat létre, telepíthet és debugolhatosak .T.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614523"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>C# Service Fabric-alkalmazások fejlesztése visual studio kóddal

A [Service Fabric Reliable Services bővítmény vs kód](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) megkönnyíti a .NET Core Service Fabric alkalmazások létrehozása Windows, Linux és macOS operációs rendszereken.

Ez a cikk bemutatja, hogyan hozhat létre, telepíthet és debugolhatja a .NET Core Service Fabric-alkalmazásokat a Visual Studio-kód használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepítette a VS-kódot, a Service Fabric reliable services bővítményt a VS-kódhoz, és a fejlesztői környezethez szükséges függőségeket. További információ: [Első lépések](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>A minta letöltése
Ez a cikk a CounterService-alkalmazást használja a [Service Fabric .NET Core első lépésekben a GitHub-tárházból.](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) 

A tárház fejlesztői gépre történő klónozásához futtassa a következő parancsot egy terminálablakból (parancsablak a Windows rendszeren):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Az alkalmazás megnyitása a VS-kódban

### <a name="windows"></a>Windows
Kattintson a jobb gombbal a Start menü VS-kód ikonjára, és válassza a **Futtatás rendszergazdaként parancsot.** A hibakereső szolgáltatásokhoz való csatolásához rendszergazdaként kell futtatnia a VS-kódot.

### <a name="linux"></a>Linux
A terminál használatával keresse meg a /service-fabric-dotnet-core-getting-started/Services/CounterService elérési utat abból a könyvtárból, amelybe az alkalmazást helyileg klónozták.

Futtassa a következő parancsot a VS-kód gyökérfelhasználóként való megnyitásához, hogy a hibakereső csatolható a szolgáltatásokhoz.
```
sudo code . --user-data-dir='.'
```

Az alkalmazásnak most meg kell jelennie a VS Code munkaterületen.

![Szolgáltatásszámláló-alkalmazás a munkaterületen](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Az alkalmazás létrehozása
1. Nyomja meg a (Ctrl + Shift + p) billentyűkombinációt a **Parancspaletta** VS-kódban való megnyitásához.
2. Keresse meg és válassza ki a **Service Fabric: Build Application** parancsot. A buildkimenet az integrált terminálra kerül.

   ![Alkalmazás buildelése parancs a VS-kódban](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Az alkalmazás telepítése a helyi fürtre
Miután elkészítette az alkalmazást, üzembe helyezheti azt a helyi fürtre. 

1. A **parancspalettán**válassza ki a **Service Fabric: Deploy Application (Localhost) parancsot.** A telepítési folyamat kimenete az integrált terminálra kerül.

   ![Alkalmazás telepítése parancs a VS-kódban](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Amikor a központi telepítés befejeződött, indítson el egy\/böngészőt, és nyissa meg a Service Fabric Explorer: http: /localhost:19080/Explorer. Látnia kell, hogy az alkalmazás fut. Ez eltarthat egy ideig, ezért légy türelmes. 

   ![Számlálószolgáltatás-alkalmazás a Service Fabric Intézőben](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Miután ellenőrizte, hogy az alkalmazás fut,indítson el egy böngészőt, és nyissa meg ezt az oldalt: http:\//localhost:31002. Ez az alkalmazás webes előtér- kiszolgálója. Frissítse a lapot, hogy a számláló aktuális értéke növekszik.

   ![Számlálószolgáltatás-alkalmazás a böngészőben](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Az alkalmazás közzététele egy Azure Service Fabric-fürtben
Az alkalmazás helyi fürtre való üzembe helyezése mellett az alkalmazást egy távoli Azure Service Fabric-fürtre is közzéteheti. 

1. Győződjön meg arról, hogy a fenti utasítások nak megfelelően építette fel az alkalmazást. Frissítse a létrehozott `Cloud.json` konfigurációs fájlt annak a távoli fürtnek a részleteivel, amelynek közzé szeretné tenni.

2. A **parancspalettán**válassza a **Service Fabric: Publish Application parancsot.** A telepítési folyamat kimenete az integrált terminálra kerül.

   ![Alkalmazás közzététele parancs a VS-kódban](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Amikor a központi telepítés befejeződött, indítson el `https:<clusterurl>:19080/Explorer`egy böngészőt, és nyissa meg a Service Fabric Explorert: . Látnia kell, hogy az alkalmazás fut. Ez eltarthat egy ideig, ezért légy türelmes. 

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése
Alkalmazások vs kódban történő hibakeresésekénél az alkalmazásnak helyi fürtön kell futnia. Ezután töréspontokadhatók hozzá a kódhoz.

Töréspont és hibakeresés beállításához hajtsa végre az alábbi lépéseket:
1. Az Explorer ben nyissa meg a */src/CounterServiceApplication/CounterService/CounterService.cs* fájlt, és `RunAsync` állítson be egy töréspontot a 62.
3. A **Hibakeresés** ikonra kattintva nyissa meg a hibakereső nézetet a VS-kódban. Kattintson a hibakereső nézet tetején lévő fogaskerék ikonra, és válassza a **.NET Core** lehetőséget a legördülő környezet menüből. Megnyílik a launch.json fájl. Bezárhatja ezt a fájlt. Most meg kell jelennie a konfigurációs lehetőségeka hibakeresési konfigurációs menü mellett található a futtatás gomb (zöld nyíl).

   ![Hibakeresési ikon a VS code workspace-ben](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Válassza a hibakeresési konfigurációs menü **.NET Core Attach parancsát.**

   ![Hibakeresési ikon a VS code workspace-ben](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Nyissa meg a Service Fabric\/Explorer böngészőt: http: /localhost:19080/Explorer. Kattintson **az Alkalmazások gombra,** és részletezze a leásást a CounterService által futtatott elsődleges csomópont meghatározásához. A counterservice elsődleges csomópontja alatti képen a 0 csomópont.

   ![A CounterService elsődleges csomópontja](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. A VS Code játékban kattintson a futtatás ikonra (zöld nyíl) a **.NET Core Attach** hibakeresési konfiguráció mellett. A folyamatkijelölési párbeszédpanelen jelölje ki azt a CounterService folyamatot, amely a 4.

   ![Elsődleges folyamat](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. A *töréspont* a CounterService.cs fájlban lesz hit nagyon gyorsan. Ezután megismerheti a helyi változók értékeit. A VS-kód tetején található Debug eszköztár segítségével folytathatja a végrehajtást, átléphet a sorokon, módszerekbe léphet, vagy kiléphet az aktuális módszerből. 

   ![Hibakeresési értékek](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. A hibakeresési munkamenet befejezéséhez kattintson a VS-kód tetején lévő Debug eszköztár dugóikonjára..
   
   ![Kapcsolat bontása a hibakeresővel](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Miután befejezte a hibakeresést, használhatja a **Service Fabric: Application eltávolítása** parancsot a CounterService alkalmazás eltávolításához a helyi fürtből. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [fejleszthet és debugolhat Java Service Fabric-alkalmazásokat a VS Code segítségével.](./service-fabric-develop-java-applications-with-vs-code.md)



