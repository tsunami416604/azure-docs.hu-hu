---
title: A Visual Studio Code-dal Java Azure Service Fabric-alkalmazások fejlesztéséhez |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre, üzembe helyezése és hibakeresése a Visual Studio Code Java Service Fabric-alkalmazásokat.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 34fa8303176d228ea9025cbf6dfbac8997764b35
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497771"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>A Visual Studio Code-dal Java Service Fabric-alkalmazások fejlesztése

A [Service Fabric Reliable Services bővítmény a VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) megkönnyíti a Java Service Fabric-alkalmazások Windows, Linux és macOS operációs rendszereken.

Ez a cikk bemutatja, hogyan hozhat létre, üzembe helyezése és hibakeresése a Visual Studio Code egy Java Service Fabric-alkalmazás.

> [!IMPORTANT]
> Service Fabric Java-alkalmazások fejleszthetők Windows-gépeken, de csak az Azure-beli Linuxos fürtöket is lehet telepíteni. Hibakeresés Java-alkalmazások a Windows nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már telepítette a VS Code, VS Code a Service Fabric Reliable Services-bővítmény és a fejlesztési környezet számára szükséges függőségek. További tudnivalókért lásd: [bevezetés](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>A minta letöltése
Ez a cikk a szavazóalkalmazásnak használja a [Service Fabric Java application rövid minta GitHub-adattárban](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Klónozza az adattárat a fejlesztői gépén, futtassa a következő parancsot egy terminálablakból (a Windows parancssori ablakban):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Nyissa meg az alkalmazás a VS Code-ban

Nyissa meg a VS Code.  Kattintson az ikonjára a a **tevékenységsávon** kattintson **mappa megnyitása**, vagy kattintson a **File -> mappa megnyitása**. Keresse meg a *./service-fabric-java-quickstart/Voting* könyvtár a mappában, amelybe klónozta a tárházat majd kattintson a **OK**. A munkaterület tartalmaznia kell az alábbi képernyőképen látható ugyanazokat a fájlokat.

![Java-Szavazóalkalmazáshoz munkaterületen](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Az alkalmazás létrehozása

1. (Ctrl + Shift + p) megnyitásához nyomja le az **Parancskatalógus** a VS Code-ban.
2. Keresse meg és válassza a **Service Fabric: Alkalmazás létrehozása** parancsot. Az integrált terminálon kimenete kerül.

   ![A VS Code alkalmazást parancs létrehozása](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Az alkalmazás a helyi fürt üzembe helyezése
Az alkalmazás létrehozása után telepítheti a helyi fürthöz. 

> [!IMPORTANT]
> A helyi fürt Java-alkalmazások telepítése Windows gépeken nem támogatott.

1. Az a **Parancskatalógus**, jelölje be a **Service Fabric: Alkalmazás (Localhost) üzembe helyezése**. A kimenet a telepítési folyamatot, az integrált terminálon küld.

   ![Alkalmazás parancsot a VS Code telepítése](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Az üzembe helyezés befejeződése után elindít egy böngészőt, és nyissa meg a Service Fabric Explorer: `http://localhost:19080/Explorer`. Megtekintheti, hogy az alkalmazás fut-e. Ez eltarthat némi ideig legyen a kis türelmet. 

   ![A Service Fabric Explorerben Szavazóalkalmazás](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Miután meggyőződött arról, hogy az alkalmazás fut, elindít egy böngészőt, és nyissa meg ezt oldal: `http://localhost:8080`. Ez az a webes előtér-alkalmazás. Elemek hozzáadása, és kattintson a szavazáshoz őket.

   ![Szavazóalkalmazás böngészőben](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Az alkalmazás eltávolítása a fürtből, válassza ki a **Service Fabric: Alkalmazás eltávolítása** parancsot a **Parancskatalógus**. Az integrált terminálon az eltávolítási folyamat kimenete kerül. A Service Fabric Explorer segítségével győződjön meg arról, hogy a kérelem el lett távolítva a helyi fürtöt.

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése
Hibakeresés a VS Code alkalmazást, ha az alkalmazás egy helyi fürtön kell futnia. Töréspontok majd lehet hozzáadni a kódot.

> [!IMPORTANT]
> Hibakeresés Java-alkalmazások Windows gépeken nem támogatott.

Készítse elő a VotingDataService és a hibakereséshez a szavazóalkalmazást, hajtsa végre az alábbi lépéseket:

1. Frissítés a *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* fájlt.
Tegye megjegyzésbe a parancs az (használja a "#") a 6. sorban, és adja hozzá a következő parancsot a fájl végére:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Frissítés a *Voting/VotingApplication/ApplicationManifest.xml* fájlt. Állítsa be a **MinReplicaSetSize** és a **TargetReplicaSetSize** az "1" attribútumok a **statefulservice-ből** elem:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. A hibakeresés ikon a ban kattintson a **tevékenységsávon** a hibakereső nézet megnyitásához a VS Code-ban. Kattintson a fogaskerék ikonra a hibakereső nézet tetején, és válassza ki **Java** a környezet legördülő listából. A launch.json fájl megnyílik. 

   ![Hibakeresés ikon a VS Code-munkaterület](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. A launch.json fájl, állítsa be a port értékének a konfigurációban nevű **hibakeresés (csatolás)** való **8001**. Mentse a fájlt.

   ![A launch.json a konfigurációs hibakeresése](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Az alkalmazás a helyi fürt üzembe helyezése a használatával a **Service Fabric: (Localhost) üzembe helyezése** parancsot. Ellenőrizze, hogy fut-e az alkalmazás a Service Fabric Explorerben. Az alkalmazás már készen áll a hibakereséséhez.

Állítson be egy töréspontot, hajtsa végre az alábbi lépéseket:

1. Az Explorerben nyissa meg a */Voting/VotingDataService/src/statefulservice/VotingDataService.java* fájlt. A kód első sorában állítson be egy töréspontot a `try` letiltása a `addItem` metódus (80-as. sor).
   
   ![Töréspont elhelyezése szavazási Data Service-ben](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Ellenőrizze, hogy állítson be töréspontokat a végrehajtható sornyi kóddal. Módszer nyilatkozatok, állítsa be például a töréspontok `try` utasítások, vagy `catch` utasítások szerint a hibakereső rendszer nem talált.
2. Hibakeresés a kezdéshez kattintson a hibakeresés ikon a a **tevékenységsávon**, jelölje be a **hibakeresés (csatolás)** konfigurációs a hibakeresés menüből, majd kattintson a Futtatás gombra (zöld nyíl).

   ![Hibakeresés (csatolás) konfigurációja](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Egy webböngészőben nyissa meg `http://localhost:8080`. A szövegmezőbe írja be egy új elemet, és kattintson a **+ Hozzáadás**. A töréspont találati kell lennie. A hibakeresési eszköztár felső részén a VS Code végrehajtása, lépés keresztül sorok, a lépésben azokat a módszereket, vagy ki az aktuális módszer használata. 
   
   ![Töréspont](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. A hibakeresési munkamenet leállításához kattintson a beépülő ikonra a felső részén a VS Code hibakeresési eszköztáron.
   
   ![A hibakereső leválasztása](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Ha befejezte, hibakeresés, használhatja a **Service Fabric: Alkalmazás eltávolítása** parancsot a szavazóalkalmazás eltávolítása a helyi fürtöt. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [fejlesztésekor és hibakeresésekor C# Service Fabric-alkalmazásokat a VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
