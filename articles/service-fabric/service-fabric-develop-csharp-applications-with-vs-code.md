---
title: Visual Studio Code használatával .NET Core az Azure Service Fabric-alkalmazások fejlesztéséhez |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre, üzembe helyezése és hibakeresése a Visual Studio Code használatával .NET Core Service Fabric-alkalmazásokat.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 9da735f10063649222a38498af17e0404137a706
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315351"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Fejlesztés C# Service Fabric-alkalmazásokat a Visual Studio Code-dal

A [Service Fabric Reliable Services bővítmény a VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) megkönnyíti a .NET Core a Service Fabric-alkalmazások Windows, Linux és macOS operációs rendszereken.

Ez a cikk bemutatja, hogyan hozhat létre, üzembe helyezése és hibakeresése a Visual Studio Code használatával .NET Core Service Fabric-alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már telepítette a VS Code, VS Code a Service Fabric Reliable Services-bővítmény és a fejlesztési környezet számára szükséges függőségek. További tudnivalókért lásd: [bevezetés](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>A minta letöltése
Ez a cikk a CounterService alkalmazást használja a [Bevezetés a Service Fabric .NET Core-minták GitHub-adattár](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Klónozza az adattárat a fejlesztői gépén, futtassa a következő parancsot egy terminálablakból (a Windows parancssori ablakban):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Nyissa meg az alkalmazás a VS Code-ban

### <a name="windows"></a>Windows
Kattintson a jobb gombbal a VS Code ikonját a Start menüben, és válassza a **Futtatás rendszergazdaként**. Társítsa a hibakeresőt a szolgáltatásokhoz, a VS Code Futtatás rendszergazdaként kell.

### <a name="linux"></a>Linux
A terminálon nyissa meg azt az elérési út /service-fabric-dotnet-core-getting-started/Services/CounterService azon címtárból, amellyel az alkalmazás helyileg klónozni.

Futtassa a következő parancsot a VS Code megnyitásához a legfelső szintű felhasználóként, hogy a hibakeresőt csatolhat a szolgáltatásokhoz.
```
sudo code . --user-data-dir='.'
```

Az alkalmazás most már meg kell jelennie a VS Code-munkaterület.

![Számláló Service alkalmazás-munkaterületen](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Az alkalmazás létrehozása
1. (Ctrl + Shift + p) megnyitásához nyomja le az **Parancskatalógus** a VS Code-ban.
2. Keresse meg és válassza a **Service Fabric: Alkalmazás létrehozása** parancsot. Az integrált terminálon kimenete kerül.

   ![A VS Code alkalmazást parancs létrehozása](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Az alkalmazás a helyi fürt üzembe helyezése
Az alkalmazás létrehozása után telepítheti a helyi fürthöz. 

1. Az a **Parancskatalógus**, jelölje be a **Service Fabric: Alkalmazás (Localhost) üzembe helyezése**. A kimenet a telepítési folyamatot, az integrált terminálon küld.

   ![Alkalmazás parancsot a VS Code telepítése](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Az üzembe helyezés befejeződése után elindít egy böngészőt, és nyissa meg a Service Fabric Explorer: http:\//localhost:19080 / Explorer. Megtekintheti, hogy az alkalmazás fut-e. Ez eltarthat némi ideig legyen a kis türelmet. 

   ![A számláló Service-alkalmazás a Service Fabric Explorerben](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Miután ellenőrizte az alkalmazás fut, elindít egy böngészőt, és nyissa meg a ezt oldal: http:\//localhost:31002. Ez az a webes előtér-alkalmazás. Frissítse az oldalt a számláló a jelenlegi érték megtekintéséhez, mert növeli.

   ![A számláló Service-alkalmazás a böngészőben](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése
Hibakeresés a VS Code alkalmazást, ha az alkalmazás egy helyi fürtön kell futnia. Töréspontok majd lehet hozzáadni a kódot.

Állítsa be egy töréspontot, és a hibakeresési, hajtsa végre az alábbi lépéseket:
1. Az Explorerben nyissa meg a */src/CounterServiceApplication/CounterService/CounterService.cs* fájlt, és állítson be egy töréspontot a sor 62 belül a `RunAsync` metódust.
3. A hibakeresés ikon a ban kattintson a **tevékenységsávon** a hibakereső nézet megnyitásához a VS Code-ban. Kattintson a fogaskerék ikonra a hibakereső nézet tetején, és válassza ki **.NET Core** a környezet legördülő listából. A launch.json fájl megnyílik. Zárja be ezt a fájlt. Most már megtekintheti a hibakeresési konfigurációja menüben a Futtatás gombra (zöld nyíl) mellett található konfigurációs beállításokkal.

   ![Hibakeresés ikon a VS Code-munkaterület](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Válassza ki **.NET Core csatolása** a hibakeresési konfigurációs menüből.

   ![Hibakeresés ikon a VS Code-munkaterület](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Egy böngészőben nyissa meg a Service Fabric Explorer: http:\//localhost:19080 / Explorer. Kattintson a **alkalmazások** és feltárásához az elsődleges csomópont, amelyen fut a CounterService határozza meg. Az elsődleges csomópont a CounterService az alábbi ábrán a csomópont 0.

   ![CounterService az elsődleges csomópont](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. A VS Code-ban kattintson a Futtatás ikonra (zöld nyíl) mellett a **.NET Core csatolása** hibakeresési konfiguráció. Folyamat kiválasztása párbeszédpanelen válassza ki a CounterService folyamat, amelyen fut az elsődleges csomópont, a 4. lépésben azonosított.

   ![Elsődleges folyamat](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. A töréspont az a *CounterService.cs* fájl nagyon gyorsan eléri. Áttekintheti a helyi változók értékeit, majd. A VS Code tetején a hibakeresési eszköztár segítségével végrehajtása, lépés keresztül sorok, a lépésben azokat a módszereket, vagy ki az aktuális módszer. 

   ![Értékek hibakeresése](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. A hibakeresési munkamenet befejezéséhez, a Debug eszköztáron felső részén a VS Code beépülő ikonra kattintva...
   
   ![A hibakereső leválasztása](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Ha befejezte, hibakeresés, használhatja a **Service Fabric: Alkalmazás eltávolítása** parancsot a CounterService alkalmazás eltávolítása a helyi fürtöt. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [fejlesztése és hibakeresése a VS Code használatával a Service Fabric Java-alkalmazások](./service-fabric-develop-java-applications-with-vs-code.md).



