---
title: Azure Service Fabric és a kód első lépések |} Microsoft Docs
description: Ez a cikk a Visual Studio Code használatával a Service Fabric-alkalmazások létrehozásának nyújt áttekintést.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 367829c269bd1d96e6aa5fab1be008483a4ab5ab
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37116066"
---
# <a name="service-fabric-for-visual-studio-code"></a>A Service Fabric Visual Studio Code

A [Service Fabric Reliable Services bővítmény a Visual STUDIO Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) létrehozásához, elkészítéséhez és a Windows, Linux és macOS operációs rendszereken a Service Fabric-alkalmazások hibakeresését szükséges eszközöket biztosít.

Ez a cikk ismerteti a követelmények áttekintését és a bővítmény telepítését, valamint a különböző parancsok a bővítmény által biztosított használatát. 

> [!IMPORTANT]
> Service Fabric Java-alkalmazások fejleszthetők Windows gépeken, de csak a fürtök Azure Linux-kiszolgálóra telepíthető. Java-alkalmazások hibakeresés nem támogatott Windows rendszeren.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételek telepíteni kell minden környezetben.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [A Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman generátorokat – a megfelelő generátorokat az alkalmazás telepítése

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Java fejlesztői telepítenie kell a következő előfeltételek teljesülését:

* [Java SDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (1.8-as verzióját)
* [Gradle-lel](https://gradle.org/install/)
* [Java Visual STUDIO Code-kiterjesztés hibakereső](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) hibakeresési Java-szolgáltatások szükségesek. Hibakeresés Java szolgáltatások nem támogatott Linux csak. Vagy a bővítmények ikonra kattintva telepítheti a **tevékenység sáv** a Visual STUDIO Code- és a bővítmény, vagy a Visual STUDIO Code piactérről.

A következő előfeltételek telepítenie kell a .NET Core / C# fejlesztési:

* [A .NET core](https://www.microsoft.com/net/learn/get-started) (2.0.0 verzió vagy újabb)
* [C# (OmniSharp technológiával) Visual Studio Code Visual STUDIO Code-kiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) szükséges C# szolgáltatások hibakereséséhez. Vagy a bővítmények ikonra kattintva telepítheti a **tevékenység sáv** a Visual STUDIO Code- és a bővítmény, vagy a Visual STUDIO Code piactérről.

## <a name="setup"></a>Beállítás

1. Megnyitás Visual STUDIO Code.
2. Kattintson a bővítmények a **tevékenység sáv** VS-kód bal oldalán. "A Service Fabric" keresése. Kattintson a **telepítése** a Service Fabric Reliable Services bővítmény.

## <a name="commands"></a>Parancsok
A Service Fabric Reliable Services bővítmény a Visual STUDIO Code számos parancs segítségével a fejlesztők létrehozása és telepítése a Service Fabric-projektek biztosít. A parancsok hívása a **parancs paletta** billentyűkombináció lenyomásával `(Ctrl + Shift + p)`, írja be a parancsnév a sávjának, majd válassza ki a kívánt parancs Rákérdezés listájáról. 

* A Service Fabric: Az alkalmazás létrehozása 
* A Service Fabric: Az alkalmazás közzététele 
* A Service Fabric: Az alkalmazás központi telepítése 
* A Service Fabric: Az alkalmazás eltávolítása  
* A Service Fabric: Az alkalmazás létrehozása 
* A Service Fabric: Tiszta alkalmazás 

### <a name="service-fabric-create-application"></a>A Service Fabric: Az alkalmazás létrehozása

A **Service Fabric: alkalmazás létrehozása** parancs létrehoz egy új Service Fabric-alkalmazás az aktuális munkaterületen. Attól függően, hogy mely yeoman generátorokat a fejlesztési számítógépen telepítve vannak számos különböző típusú Service Fabric-alkalmazás, beleértve a Java, a C#, tároló és a Vendég is létrehozhat. 

1.  Válassza ki a **Service Fabric: hozzáadása szolgáltatás** parancs
2.  Válassza ki az új Service Fabric-alkalmazás. 
3.  Adja meg a létrehozandó alkalmazás neve
3.  Válassza ki a szolgáltatás a Service Fabric-alkalmazás hozzáadni kívánt típusát. 
4.  Kövesse az utasításokat a szolgáltatás neve. 
5.  Az új Service Fabric-alkalmazás az a munkaterületben megjelenik.
6.  Nyissa meg az új alkalmazás mappát, hogy a munkaterület gyökérmappáját. Parancsok végrehajtása innen folytathatja.

### <a name="service-fabric-add-service"></a>A Service Fabric: Szolgáltatás hozzáadása
A **Service Fabric: hozzáadása szolgáltatás** parancs egy új szolgáltatás ad egy meglévő Service Fabric-alkalmazás. Az alkalmazást, amely a szolgáltatás nem kerülnek be a munkaterület gyökérkönyvtárában kell lennie. 

1.  Válassza ki a **Service Fabric: hozzáadása szolgáltatás** parancsot.
2.  Válassza ki az aktuális Service Fabric-alkalmazás típusú. 
3.  Válassza ki a szolgáltatás a Service Fabric-alkalmazás hozzáadni kívánt típusát. 
4.  Kövesse az utasításokat a szolgáltatás neve. 
5.  Az új szolgáltatás a projekt könyvtárában található meg. 

### <a name="service-fabric-publish-application"></a>A Service Fabric: Az alkalmazás közzététele
A **Service Fabric: alkalmazás közzététele** parancs telepíti a Service Fabric-alkalmazás egy távoli fürtön. Lehet, hogy a cél fürtnek a biztonságos vagy egy nem biztonságos fürthöz. Ha a paraméterek nincsenek beállítva a Cloud.json, a rendszer telepíti az alkalmazást, a helyi fürthöz.

1.  Az alkalmazás épül, először a projektkönyvtárban Cloud.json fájl jön létre.
2.  Adjon meg az értékeket a fürt, amely a Cloud.json fájlban csatlakozni szeretne.
3.  Válassza ki a **Service Fabric: alkalmazás közzététele** parancsot.
4.  Tekintse meg a Service Fabric Explorerrel annak ellenőrzéséhez, hogy az alkalmazás telepítve van-e a cél fürtnek. 

### <a name="service-fabric-deploy-application-localhost"></a>A Service Fabric: (Localhost) alkalmazás központi telepítése
A **Service Fabric: az alkalmazás központi telepítése** parancs telepíti a Service Fabric-alkalmazás a helyi fürthöz. Győződjön meg arról, hogy a helyi fürtön fut a parancs használata előtt. 

1.  Válassza ki a **Service Fabric: az alkalmazás központi telepítése** parancs
2.  A helyi fürthöz, a Service Fabric Explorerrel megtekintése (http://localhost:19080/Explorer) annak ellenőrzéséhez, hogy az alkalmazás telepítve van-e. Ez eltarthat egy ideig, így türelemmel.
3.  Is **Service Fabric: alkalmazás közzététele** parancs a helyi fürt központi telepítése a Cloud.json fájlban beállított paraméter nélkül.

> [!NOTE]
> A helyi fürt Java-alkalmazások telepítése Windows gépeken nem támogatott.

### <a name="service-fabric-remove-application"></a>A Service Fabric: Az alkalmazás eltávolítása
A **Service Fabric: alkalmazás eltávolítása** parancs eltávolítja a Service Fabric-alkalmazás a fürt, hogy korábban már telepítették a Visual STUDIO Code bővítményének használatával. 

1.  Válassza ki a **Service Fabric: alkalmazás eltávolítása** parancsot.
2.  Tekintse meg a fürt a Service Fabric Explorerrel annak ellenőrzéséhez, hogy a kérelem el lett távolítva. Ez eltarthat egy ideig, így türelemmel.

### <a name="service-fabric-build-application"></a>A Service Fabric: Az alkalmazás létrehozása
A **Service Fabric: alkalmazás eltávolítása** parancs Java vagy a Service Fabric C#-alkalmazást hozhat létre. 

1.  Ellenőrizze, hogy ez a parancs végrehajtása előtt a gyökérmappájában találhatók. A parancs (C# vagy Java) alkalmazás azonosítja, és ennek megfelelően épít fel az alkalmazást.
2.  Válassza ki a **Service Fabric: Build alkalmazás** parancsot.
3.  A létrehozási folyamat kimenetét a integrált terminál írása.

### <a name="service-fabric-clean-application"></a>A Service Fabric: Tiszta alkalmazás
A **Service Fabric: tiszta alkalmazás** parancs törli az összes jar-fájlok és a build által előállított natív szalagtárak. Csak a Java-alkalmazások esetében érvényes. 

1.  Ellenőrizze, hogy ez a parancs végrehajtása előtt a gyökérmappájában találhatók. 
2.  Válassza ki a **Service Fabric: tiszta alkalmazás** parancsot.
3.  A tiszta folyamat kimenetét a integrált terminál írása.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [fejlesztéséhez és a Visual STUDIO Code C# Service Fabric-alkalmazások hibakeresését](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Megtudhatja, hogyan [fejlesztéséhez és a Visual STUDIO Code Java Service Fabric-alkalmazások hibakeresését](./service-fabric-develop-java-applications-with-vs-code.md).
