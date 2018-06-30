---
title: A Visual Studio Code .NET Core Azure Service Fabric-alkalmazások fejlesztéséhez |} Microsoft Docs
description: Ez a cikk bemutatja, hogyan létrehozásához, telepítéséhez, és használja a Visual Studio Code .NET Core Service Fabric-alkalmazások hibakeresését.
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
ms.openlocfilehash: 27c7c62125f3f559fb1764292729cbbfdc1c4e5f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116125"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>A Visual Studio Code C# Service Fabric-alkalmazások fejlesztéséhez

A [Service Fabric Reliable Services bővítmény a Visual STUDIO Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) megkönnyíti a Windows, Linux és macOS operációs rendszereken a .NET Core Service Fabric alkalmazásokat hozhatnak létre.

Ez a cikk bemutatja, hogyan létrehozásához, telepítéséhez és hibakeresése a Visual Studio Code használatával .NET Core Service Fabric-alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepített Visual STUDIO Code, a Service Fabric Reliable Services bővítmény a Visual STUDIO Code és a fejlesztési környezet számára szükséges összes függőséget. További tudnivalókért lásd: [bevezetés](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>A minta letöltése
Ez a cikk a CounterService alkalmazást használ a [Service Fabric .NET Core első lépések – minták GitHub-tárházban](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Klónozza a tárházat a fejlesztési számítógépen, a következő parancsot a egy terminálablakot (a Windows parancssori ablakban):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Nyissa meg az alkalmazást a Visual STUDIO Code

### <a name="windows"></a>Windows
Kattintson a jobb gombbal a Visual STUDIO Code ikonra a Start menüben, és válassza a **Futtatás rendszergazdaként**. A Hibakereső csatlakoztatása a szolgáltatások, Visual STUDIO Code rendszergazdaként futtatnia kell.

### <a name="linux"></a>Linux
A Terminálszolgáltatások nyissa meg azt az elérési út /service-fabric-dotnet-core-getting-started/Services/CounterService azon címtárból, amellyel az alkalmazás klónozása megtörtént a helyileg.

A következő parancsot nyissa meg a Visual STUDIO Code legfelső szintű felhasználóként, hogy a hibakereső csatolhat a szolgáltatásokhoz.
```
sudo code . --user-data-dir='.'
```

Az alkalmazás ekkor meg kell jelennie a Visual STUDIO Code munkaterületén.

![A számláló szolgáltatásalkalmazás munkaterület](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Az alkalmazás létrehozása
1. (Ctrl + Shift + p) megnyitásához nyomja le az **parancs paletta** VS-kódban.
2. Keresse meg és jelölje ki a **Service Fabric: Build alkalmazás** parancsot. A felépítési művelet kimenetében nem jut hozzá a integrált terminál.

   ![Visual STUDIO Code alkalmazás parancs létrehozása](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>A helyi fürthöz az alkalmazás központi telepítése
Az alkalmazás létrehozása után telepítheti a helyi fürthöz. 

1. Az a **parancs paletta**, jelölje be a **Service Fabric: alkalmazás központi telepítése (Localhost) parancs**. A kimenet a telepítési folyamat nem jut hozzá a integrált terminál.

   ![Alkalmazás parancs Visual STUDIO Code telepítése](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Ha a telepítés befejeződött, elindít egy böngészőt, és nyissa meg a Service Fabric Explorer: http://localhost:19080/Explorer. Megtekintheti, hogy fut-e az alkalmazás. Ez eltarthat egy ideig, így türelemmel. 

   ![A számláló szolgáltatásalkalmazás a Service Fabric Explorerben](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Miután ellenőrizte, hogy az alkalmazás fut, elindít egy böngészőt, és nyissa meg a lap: http://localhost:31002. Ez az alkalmazás előtér-webkiszolgáló. Frissítse az oldalt, hogy a számláló az aktuális értéke, nő.

   ![A számláló szolgáltatásalkalmazás böngészőben](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése
Visual STUDIO Code alkalmazások hibakeresés, amikor az alkalmazás a helyi fürtön lévő kell futnia. Töréspontokat felveheti a kód.

A töréspont és hibakeresési beállításához kövesse az alábbi lépéseket:
1. Az Intézőben nyissa meg a */src/CounterServiceApplication/CounterService/CounterService.cs* fájlt, és állítson be egy töréspontot sor 62 belül a `RunAsync` metódust.
3. Kattintson a hibakeresési ikonra a **tevékenység sáv** a hibakereső nézet megnyitásához a Visual STUDIO Code. Kattintson a fogaskerék ikonra a bal felső a hibakereső nézet, és válassza **.NET Core** a környezet legördülő menüből. Launch.json nyílik meg. Bezárhatja ezt a fájlt. Most látnia kell a hibakeresési konfigurációs menü mellett a Futtatás gombra (zöld nyíl) található a konfigurációs beállításokkal.

   ![Hibakeresése a Visual STUDIO Code munkaterületen ikon](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Válassza ki **.NET Core csatolása** a hibakeresési konfigurációs menüből.

   ![Hibakeresése a Visual STUDIO Code munkaterületen ikon](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Service Fabric Explorer megnyitása a böngészőben: http://localhost:19080/Explorer. Kattintson a **alkalmazások** elemezze dowwn a CounterService futtató elsődleges csomópontra meghatározni. A kép a CounterService az elsődleges csomópont alatt van a csomópont 0.

   ![CounterService az elsődleges csomópont](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. VS-kódban, kattintson a Futtatás ikonra (zöld nyíl) mellett a **.NET Core csatolása** hibakeresési konfigurációs. A folyamat kiválasztása párbeszédpanelen válassza a 4. lépésben azonosított végrehajtó elsődleges csomóponton futó CounterService folyamat.

   ![Elsődleges folyamat](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. A töréspont az a *CounterService.cs* nagyon gyorsan kattintson az fájlt. Megismerheti, majd a helyi változók értékeit. Visual STUDIO Code tetején a hibakeresési eszköztár segítségével továbbra is a végrehajtási, a lépés keresztül, lépés módszerek, vagy ki az aktuális metódust. 

   ![Értékek hibakeresése](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. A hibakeresési munkamenet befejezéséhez kattintson a Visual STUDIO Code tetején hibakeresési eszköztár plug ikonjára...
   
   ![A hibakereső leválasztása](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Ha befejezte a hibakeresést, használhatja a **Service Fabric: alkalmazás eltávolítása** parancs beírásával távolítsa el a CounterService alkalmazás a helyi fürtről. 

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [fejlesztéséhez és a Visual STUDIO Code Java Service Fabric-alkalmazások hibakeresését](./service-fabric-develop-java-applications-with-vs-code.md).



