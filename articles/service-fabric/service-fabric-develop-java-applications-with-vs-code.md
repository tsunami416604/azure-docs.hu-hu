---
title: A Visual Studio Code Java Azure Service Fabric-alkalmazások fejlesztéséhez |} Microsoft Docs
description: Ez a cikk bemutatja, hogyan létrehozásához, telepítéséhez, és használja a Visual Studio Code Java Service Fabric-alkalmazások hibakeresését.
services: service-fabric
documentationcenter: .net
author: JimacoMS
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 54c94c50f6292694e947d97a10fd6976c14e19df
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116050"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>A Visual Studio Code Java Service Fabric-alkalmazások fejlesztéséhez

A [Service Fabric Reliable Services bővítmény a Visual STUDIO Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) megkönnyíti a Java Service Fabric-alkalmazások a Windows, Linux és macOS operációs rendszereken.

Ez a cikk bemutatja, hogyan létrehozásához, telepítéséhez és hibakeresése a Visual Studio Code használó Java Service Fabric-alkalmazás.

> [!IMPORTANT]
> Service Fabric Java-alkalmazások fejleszthetők Windows gépeken, de csak a fürtök Azure Linux-kiszolgálóra telepíthető. Java-alkalmazások hibakeresés nem támogatott Windows rendszeren.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepített Visual STUDIO Code, a Service Fabric Reliable Services bővítmény a Visual STUDIO Code és a fejlesztési környezet számára szükséges összes függőséget. További tudnivalókért lásd: [bevezetés](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>A minta letöltése
Ez a cikk a szavazási alkalmazást használ a [Service Fabric Java alkalmazások gyors üzembe helyezési minta GitHub-tárházban](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Klónozza a tárházat a fejlesztési számítógépen, a következő parancsot a egy terminálablakot (a Windows parancssori ablakban):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Nyissa meg az alkalmazást a Visual STUDIO Code

Megnyitás Visual STUDIO Code.  Kattintson Explorer ikonra a **tevékenység sáv** kattintson **mappa megnyitása**, vagy kattintson a **fájl -> mappa megnyitása**. Keresse meg a *./service-fabric-java-quickstart/Voting* a mappában, amelyben klónozott a tárház majd directory kattintson **OK**. A munkaterület tartalmaznia kell az alábbi képernyőfelvételen látható módon ugyanazokat a fájlokat.

![Java Szavazóalkalmazásról munkaterület](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Az alkalmazás létrehozása

1. (Ctrl + Shift + p) megnyitásához nyomja le az **parancs paletta** VS-kódban.
2. Keresse meg és jelölje ki a **Service Fabric: Build alkalmazás** parancsot. A felépítési művelet kimenetében nem jut hozzá a integrált terminál.

   ![Visual STUDIO Code alkalmazás parancs létrehozása](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>A helyi fürthöz az alkalmazás központi telepítése
Az alkalmazás létrehozása után telepítheti a helyi fürthöz. 

> [!IMPORTANT]
> A helyi fürt Java-alkalmazások telepítése Windows gépeken nem támogatott.

1. Az a **parancs paletta**, jelölje be a **Service Fabric: alkalmazás központi telepítése (Localhost) parancs**. A kimenet a telepítési folyamat nem jut hozzá a integrált terminál.

   ![Alkalmazás parancs Visual STUDIO Code telepítése](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Ha a telepítés befejeződött, elindít egy böngészőt, és nyissa meg a Service Fabric Explorer: http://localhost:19080/Explorer. Megtekintheti, hogy fut-e az alkalmazás. Ez eltarthat egy ideig, így türelemmel. 

   ![A Service Fabric Explorerben Szavazóalkalmazást](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Miután ellenőrizte, hogy az alkalmazás futásának, elindít egy böngészőt, és nyissa meg az ezen a lapon: http://localhost:8080. Ez az alkalmazás előtér-webkiszolgáló. Elemek hozzáadása, és kattintson a ahhoz, hogy a szavazati.

   ![A böngészőben Szavazóalkalmazást](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Az alkalmazás eltávolítása a fürtből, válassza ki a **Service Fabric: alkalmazás eltávolítása** parancsot a **parancs paletta**. Az eltávolítási folyamat kimenetét a integrált terminál küld. Service Fabric Explorer segítségével győződjön meg arról, hogy a kérelem el lett távolítva a helyi fürthöz.

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése
Visual STUDIO Code alkalmazások hibakeresés, amikor az alkalmazás a helyi fürtön lévő kell futnia. Töréspontokat felveheti a kód.

> [!IMPORTANT]
> Java-alkalmazások hibakeresés nem támogatott a Windows-gépek.

A VotingDataService és a szavazási alkalmazást a hibakereséshez előkészítéséhez kövesse az alábbi lépéseket:

1. Frissítés a *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* fájlt.
A parancs az (használja a "#") sor 6 megjegyzéssé, és adja hozzá a következő parancsot a fájl alján:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Frissítés a *Voting/VotingApplication/ApplicationManifest.xml* fájlt. Állítsa be a **MinReplicaSetSize** és a **TargetReplicaSetSize** az "1" attribútumot a **StatefulService** elem:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Kattintson a hibakeresési ikonra a **tevékenység sáv** a hibakereső nézet megnyitásához a Visual STUDIO Code. Kattintson a fogaskerék ikonra a bal felső a hibakereső nézet, és válassza **Java** a környezet legördülő menüből. Launch.json nyílik meg. 

   ![Hibakeresése a Visual STUDIO Code munkaterületen ikon](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. A launch.json fájlban, a konfiguráció a port értékének beállítására **(Attach) Debug** való **8001**. Mentse a fájlt.

   ![A launch.json a konfigurációs hibakeresése](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Az alkalmazás a helyi fürtre telepítését a **Service Fabric: alkalmazás központi telepítése (Localhost)** parancsot. Ellenőrizze, hogy az alkalmazás fut-e a Service Fabric Explorerben. Az alkalmazás mostantól készen áll a indítja el.

Állítson be egy töréspontot, végezze el a következő lépéseket:

1. Az Intézőben nyissa meg a */Voting/VotingDataService/src/statefulservice/VotingDataService.java* fájlt. Állítson be egy töréspontot első kódsort a `try` letiltása a `addItem` metódus (sor: 80).
   
   ![Állítsa be töréspont a szavazás adatszolgáltatás](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Győződjön meg arról, hogy állítson be töréspontokat a végrehajtható kódsorokat a. Például töréspontok metódus deklarációjában beállítása `try` utasítások, vagy `catch` utasítás által a hibakereső rendszer nem talált.
2. Hibakeresés elindításához kattintson a hibakeresési ikonra a **tevékenység sáv**, jelölje be a **hibakeresési (Attach)** konfigurációs hibakeresési menüben, majd kattintson a Futtatás gombra (zöld nyíl).

   ![Debug (csatolása) konfigurálása](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Egy webböngészőben nyissa meg a http://localhost:8080. Írja be egy új elemet a szövegmezőbe, és kattintson a **+ Hozzáadás**. A töréspont megjelenését kell találati. Használhatja a hibakeresési eszköztár Visual STUDIO Code tetején a végrehajtási, a sorok, módszerek, lépés Átlépés folytatáshoz, vagy ki az aktuális metódust. 
   
   ![Kattintson a töréspont](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. A hibakeresési munkamenet befejezéséhez kattintson a Visual STUDIO Code tetején hibakeresési eszköztár plug ikonjára.
   
   ![A hibakereső leválasztása](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Ha befejezte a hibakeresést, használhatja a **Service Fabric: alkalmazás eltávolítása** parancs beírásával távolítsa el a szavazási alkalmazás a helyi fürtről. 

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [fejlesztéséhez és a Visual STUDIO Code C# Service Fabric-alkalmazások hibakeresését](./service-fabric-develop-csharp-applications-with-vs-code.md).
