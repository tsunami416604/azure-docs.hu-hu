---
title: Az Azure Service Fabric első lépései a VS Code és |} A Microsoft Docs
description: Ez a cikk a Visual Studio Code használatával a Service Fabric-alkalmazások létrehozásának áttekintése.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: a0405e6c699192839f72b290d0466ab5062ac584
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670128"
---
# <a name="service-fabric-for-visual-studio-code"></a>A Service Fabric a Visual Studio Code

A [Service Fabric Reliable Services bővítmény a VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) létrehozhat, felépíthet és a Service Fabric-alkalmazások Windows, Linux és macOS operációs rendszereken szükséges eszközöket biztosít.

A cikk ismerteti a követelmények áttekintését és a bővítmény telepítése, valamint a különböző parancsokat a bővítmény által biztosított használatát. 

> [!IMPORTANT]
> Service Fabric Java-alkalmazások fejleszthetők Windows-gépeken, de csak az Azure-beli Linuxos fürtöket is lehet telepíteni. Hibakeresés Java-alkalmazások a Windows nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételeket kell telepíteni minden környezetben.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman-generátorok – az alkalmazás megfelelő generátorainak telepítése

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Java-fejlesztésekhez telepíteni kell a következő előfeltételek vonatkoznak:

* [A Java SDK](https://aka.ms/azure-jdks) (1.8-as verzió)
* [Gradle](https://gradle.org/install/)
* [Java VS Code-bővítmény a hibakeresőt](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Java szolgáltatás hibakeresése szükséges. Hibakeresés Java-szolgáltatások Linux rendszeren csak támogatott. Vagy a bővítmény ikonra kattintva telepítheti a **tevékenységsávon** a VS Code és a Keresés a bővítményhez, vagy a VS Code piacteréről.

A következő előfeltételeket kell telepíteni a .NET Core /C# fejlesztési:

* [.NET core](https://www.microsoft.com/net/learn/get-started) (2.0.0-s verzió vagy újabb)
* [C#a Visual Studio Code-ot (szolgáltató: omnisharp) a VS Code-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) hibakeresése szükséges C# szolgáltatások. Vagy a bővítmény ikonra kattintva telepítheti a **tevékenységsávon** a VS Code és a Keresés a bővítményhez, vagy a VS Code piacteréről.

## <a name="setup"></a>Beállítás

1. Nyissa meg a VS Code.
2. Kattintson a bővítmény ikonra a **tevékenységsávon** VS Code bal oldalán. Keresés a "Service Fabric". Kattintson a **telepítése** a Service Fabric Reliable Services-bővítmény.

## <a name="commands"></a>Parancsok
A Service Fabric Reliable Services bővítmény a VS Code számos parancs segítségével a fejlesztők létrehozása és üzembe helyezése a Service Fabric-projektekkel biztosít. A parancsok segítségével meghívhatja a **Parancskatalógus** lenyomásával `(Ctrl + Shift + p)`, írja be a parancs neve a bemeneti sávon, majd a kívánt parancsot a parancssorba listából. 

* A Service Fabric: Alkalmazás létrehozása 
* A Service Fabric: Alkalmazás közzététele 
* A Service Fabric: Alkalmazás üzembe helyezése 
* A Service Fabric: Alkalmazás eltávolítása  
* A Service Fabric: Alkalmazás létrehozása 
* A Service Fabric: Alkalmazás törlése 

### <a name="service-fabric-create-application"></a>A Service Fabric: Alkalmazás létrehozása

A **Service Fabric: Alkalmazás létrehozása** parancs létrehoz egy új Service Fabric-alkalmazás az aktuális munkaterületen. Attól függően, melyik yeoman-generátorok a fejlesztői gépen vannak telepítve, a számos különböző típusú Service Fabric-alkalmazást, beleértve a Java, létrehozhat C#, tároló, és a Vendég projektek. 

1.  Válassza ki a **Service Fabric: Szolgáltatás hozzáadása** parancs
2.  Jelölje ki az új Service Fabric-alkalmazás. 
3.  Adja meg a létrehozni kívánt alkalmazás nevét
3.  Válassza ki a szolgáltatás, amely a Service Fabric-alkalmazásokat hozzáadni kívánt típusát. 
4.  Kövesse az utasításokat, nevezze el a szolgáltatást. 
5.  Az új Service Fabric-alkalmazás az a munkaterületben megjelenik.
6.  Nyissa meg az új alkalmazás-mappába, úgy, hogy a munkaterület a gyökérmappában lesz. Továbbra is parancsok végrehajtása itt.

### <a name="service-fabric-add-service"></a>A Service Fabric: Szolgáltatás hozzáadása
A **Service Fabric: Szolgáltatás hozzáadása** parancs hozzáadja az új szolgáltatást meglévő Service Fabric-alkalmazás. Az alkalmazást, amelyet a szolgáltatás megjelenik a munkaterület gyökérkönyvtárában kell lennie. 

1.  Válassza ki a **Service Fabric: Szolgáltatás hozzáadása** parancsot.
2.  Válassza ki az aktuális Service Fabric-alkalmazásokat. 
3.  Válassza ki a szolgáltatás, amely a Service Fabric-alkalmazásokat hozzáadni kívánt típusát. 
4.  Kövesse az utasításokat, nevezze el a szolgáltatást. 
5.  Az új szolgáltatás a projektkönyvtárban jelenik meg. 

### <a name="service-fabric-publish-application"></a>A Service Fabric: Alkalmazás közzététele
A **Service Fabric: Alkalmazás közzététele** a parancs üzembe helyezi a Service Fabric-alkalmazás egy távoli fürtön. Lehet, hogy a cél fürtnek biztonságos vagy a nem biztonságos fürtökhöz. Ha paraméterek nincsenek beállítva a Cloud.json, a rendszer telepíti az alkalmazást a helyi fürthöz.

1.  Az alkalmazást a létrehozása, először a projekt könyvtárában Cloud.json fájl jön létre.
2.  Adjon meg az értékeket a fürt, amely a Cloud.json fájlban csatlakozni szeretne.
3.  Válassza ki a **Service Fabric: Alkalmazás közzététele** parancsot.
4.  Tekintse meg a Service Fabric Explorerrel ellenőrizze, hogy az alkalmazás telepítve van-e a cél fürtnek. 

### <a name="service-fabric-deploy-application-localhost"></a>A Service Fabric: (Localhost) üzembe helyezése
A **Service Fabric: Alkalmazás üzembe helyezése** a parancs üzembe helyezi a Service Fabric-alkalmazás a helyi fürthöz. Győződjön meg arról, hogy a helyi fürt fut-e a parancs használata előtt. 

1. Válassza ki a **Service Fabric: Alkalmazás üzembe helyezése** parancs
2. A helyi fürthöz, a Service Fabric Explorerrel megtekintése (http:\//localhost:19080 / Explorer), győződjön meg arról, hogy az alkalmazás telepítve van-e. Ez eltarthat némi ideig legyen a kis türelmet.
3. Is **Service Fabric: Alkalmazás közzététele** parancs paraméter nélküli állítsa be a Cloud.json fájl egy helyi fürtön történő üzembe helyezéséhez.

> [!NOTE]
> A helyi fürt Java-alkalmazások telepítése Windows gépeken nem támogatott.

### <a name="service-fabric-remove-application"></a>A Service Fabric: Alkalmazás eltávolítása
A **Service Fabric: Alkalmazás eltávolítása** parancs eltávolítja a fürtöt, hogy korábban telepítették a VS Code-bővítmény használata a Service Fabric-alkalmazás. 

1.  Válassza ki a **Service Fabric: Alkalmazás eltávolítása** parancsot.
2.  Tekintse meg a fürthöz a Service Fabric Explorerrel ellenőrizze, hogy a kérelem el lett távolítva. Ez eltarthat némi ideig legyen a kis türelmet.

### <a name="service-fabric-build-application"></a>A Service Fabric: Alkalmazás létrehozása
A **Service Fabric: Alkalmazás eltávolítása** parancsot hozhat létre vagy a Java vagy C# Service Fabric-alkalmazásokat. 

1.  Győződjön meg róla, hogy az alkalmazás gyökérmappájában lévő mappának a parancs végrehajtása előtt. A parancs azonosítja az alkalmazás (C# vagy Java), és ennek megfelelően létrehozza az alkalmazást.
2.  Válassza ki a **Service Fabric: Alkalmazás létrehozása** parancsot.
3.  A létrehozási folyamat kimenete az integrált terminálon íródik.

### <a name="service-fabric-clean-application"></a>A Service Fabric: Alkalmazás törlése
A **Service Fabric: Alkalmazás tiszta** parancs törli a jar-fájlok és a natív szalagtárak, a build által generált. Csak a Java-alkalmazások esetében érvényes. 

1.  Győződjön meg róla, hogy az alkalmazás gyökérmappájában lévő mappának a parancs végrehajtása előtt. 
2.  Válassza ki a **Service Fabric: Alkalmazás tiszta** parancsot.
3.  A kimenet a tiszta folyamatát írja be az integrált terminálon.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [fejlesztésekor és hibakeresésekor C# Service Fabric-alkalmazásokat a VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Ismerje meg, hogyan [fejlesztése és hibakeresése a VS Code használatával a Service Fabric Java-alkalmazások](./service-fabric-develop-java-applications-with-vs-code.md).
