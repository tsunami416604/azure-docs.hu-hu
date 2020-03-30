---
title: Java-alkalmazások fejlesztése visual studio kóddal
description: Ez a cikk bemutatja, hogyan hozhat létre, telepíthet és debugolhatot készíthet java Service Fabric-alkalmazásokat a Visual Studio-kód használatával.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 999dbb8c36c4e0413f287b2a73cf39ab4acd15f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610046"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Java Service Fabric-alkalmazások fejlesztése visual studio-kóddal

A [Service Fabric Reliable Services bővítmény vs kód](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) megkönnyíti a Java Service Fabric alkalmazások létrehozása Windows, Linux és macOS operációs rendszereken.

Ez a cikk bemutatja, hogyan hozhat létre, üzembe helyezhet és debug egy Java Service Fabric-alkalmazást a Visual Studio-kód használatával.

> [!IMPORTANT]
> A Service Fabric Java-alkalmazások fejleszthetők Windows-gépeken, de csak Azure Linux-fürtökre telepíthetők. A Java-alkalmazások hibakeresését a Windows nem támogatja.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már telepítette a VS-kódot, a Service Fabric reliable services bővítményt a VS-kódhoz, és a fejlesztői környezethez szükséges függőségeket. További információ: [Első lépések](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>A minta letöltése
Ez a cikk a szavazási alkalmazást használja a [Service Fabric Java-alkalmazás rövid útmutatójában a GitHub-tárházból.](https://github.com/Azure-Samples/service-fabric-java-quickstart) 

A tárház fejlesztői gépre történő klónozásához futtassa a következő parancsot egy terminálablakból (parancsablak a Windows rendszeren):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Az alkalmazás megnyitása a VS-kódban

Nyissa meg a VS-kódot.  Kattintson a **Tevékenységsáv** Intéző ikonjára, majd a **Mappa megnyitása**parancsra, vagy a **Fájl -> Mappa megnyitása parancsra.** Keresse meg a *./service-fabric-java-quickstart/Voting* könyvtárat abban a mappában, ahol klónozta a tárházat, majd kattintson az **OK**gombra. A munkaterületnek ugyanazokat a fájlokat kell tartalmaznia, amelyek az alábbi képernyőképen láthatók.

![Java szavazóalkalmazás a munkaterületen](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Az alkalmazás létrehozása

1. Nyomja meg a (Ctrl + Shift + p) billentyűkombinációt a **Parancspaletta** VS-kódban való megnyitásához.
2. Keresse meg és válassza ki a **Service Fabric: Build Application** parancsot. A buildkimenet az integrált terminálra kerül.

   ![Alkalmazás készítési parancsa a VS-kódban](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Az alkalmazás telepítése a helyi fürtre
Miután elkészítette az alkalmazást, üzembe helyezheti azt a helyi fürtre. 

> [!IMPORTANT]
> Windows-gépek en nem támogatott a Java-alkalmazások telepítése a helyi fürtre.

1. A **parancspalettán**válassza ki a **Service Fabric: Deploy Application (Localhost) parancsot.** A telepítési folyamat kimenete az integrált terminálra kerül.

   ![Alkalmazásparancs telepítése a VS-kódban](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Amikor a központi telepítés befejeződött, indítson el `http://localhost:19080/Explorer`egy böngészőt, és nyissa meg a Service Fabric Explorert: . Látnia kell, hogy az alkalmazás fut. Ez eltarthat egy ideig, ezért légy türelmes. 

   ![Szavazási alkalmazás a Service Fabric Intézőben](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Miután meggyőződött arról, hogy az alkalmazás fut, indítson `http://localhost:8080`el egy böngészőt, és nyissa meg ezt a lapot: . Ez az alkalmazás webes előtér- kiszolgálója. Felveheti elemeket, és kattintson rájuk, hogy szavazzanak.

   ![Szavazási alkalmazás böngészőben](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Ha el szeretné távolítani az alkalmazást a fürtből, válassza a **Service Fabric: Application remove command** parancsot a **parancspalettáról**. Az eltávolítási folyamat kimenete az integrált terminálra kerül. A Service Fabric Explorer segítségével ellenőrizheti, hogy az alkalmazást eltávolították-e a helyi fürtből.

## <a name="debug-the-application"></a>Az alkalmazás hibakeresése
Alkalmazások vs kódban történő hibakeresésekénél az alkalmazásnak helyi fürtön kell futnia. Ezután töréspontokadhatók hozzá a kódhoz.

> [!IMPORTANT]
> Windows-gépeken a Java-alkalmazások hibakeresése nem támogatott.

A VotingDataService és a Szavazás alkalmazás hibakeresésre való előkészítéséhez hajtsa végre az alábbi lépéseket:

1. Frissítse a *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* fájlt.
Fűzzön hozzá megjegyzést a 6.

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Frissítse a *Voting/VotingApplication/ApplicationManifest.xml* fájlt. Állítsa a **MinReplicaSetSize** és a **TargetReplicaSetSize** attribútumokat "1"-re a **StatefulService** elemben:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. A **Hibakeresés** ikonra kattintva nyissa meg a hibakereső nézetet a VS-kódban. Kattintson a hibakereső nézet tetején lévő fogaskerék ikonra, és válassza a **Java** lehetőséget a legördülő környezet menüből. Megnyílik a launch.json fájl. 

   ![Hibakeresési ikon a VS code workspace-ben](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. A launch.json fájlban állítsa a **Debug (Csatolás)** nevű konfiguráció portértékét **8001-re.** Mentse a fájlt.

   ![Hibakeresési konfiguráció a launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Telepítse az alkalmazást a helyi fürtre a **Service Fabric: Deploy Application (Localhost)** paranccsal. Ellenőrizze, hogy az alkalmazás fut-e a Service Fabric Intézőben. Az alkalmazás készen áll a hibakeresésre.

Töréspont beállításához hajtsa végre az alábbi lépéseket:

1. Az Explorerben nyissa meg a */Voting/VotingDataService/src/statefulservice/VotingDataService.java* fájlt. Állítson be egy töréspontot a `try` metódus `addItem` (80. sor) blokkjában lévő kód első sorára.
   
   ![Töréspont beállítása a voting data service szolgáltatásban](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Győződjön meg róla, hogy töréspontokat állított be a végrehajtható kódsorokon. A hibakereső például a `try` metódusdeklarációkra, utasításokra vagy `catch` utasításokra beállított töréspontokat nem veszi észre.
2. A hibakeresés megkezdéséhez kattintson a Hibakeresés ikonra a **Tevékenységsávon**, válassza a **Hibakeresési (Csatolás)** konfigurációt a hibakeresési menüből, majd kattintson a futtatás gombra (zöld nyíl).

   ![Hibakeresési (csatolási) konfiguráció](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Egy böngészőben nyissa meg a következőt: `http://localhost:8080`. Írjon be egy új elemet a szövegmezőbe, és kattintson **a + Hozzáadás gombra.** A töréspontodnak el kell találnia. A VS-kód tetején található Debug eszköztár segítségével folytathatja a végrehajtást, átléphet a sorokon, módszerekbe léphet, vagy kiléphet az aktuális módszerből. 
   
   ![Találati töréspont](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. A hibakeresési munkamenet befejezéséhez kattintson a VS-kód tetején lévő Debug eszköztár dugóikonjára.
   
   ![Kapcsolat bontása a hibakeresővel](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Miután befejezte a hibakeresést, használhatja a **Service Fabric: Application eltávolítása** parancsot a szavazási alkalmazás eltávolításához a helyi fürtből. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [fejleszthet és debugolhat C# Service Fabric-alkalmazásokat a VS Code segítségével.](./service-fabric-develop-csharp-applications-with-vs-code.md)
