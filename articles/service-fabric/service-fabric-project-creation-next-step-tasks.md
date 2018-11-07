---
title: A Service Fabric-projekt létrehozásának további lépései |} A Microsoft Docs
description: Ismerje meg a Visual studióban újonnan létrehozott projektre.  Megtudhatja, hogyan oktatóanyagaival-szolgáltatások létrehozásához, és tudjon meg többet a Service Fabric-szolgáltatások fejlesztéséhez.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: rwike77
ms.openlocfilehash: 4d5e74b9ecffbf8f1161cf6c5ef948cd154d993f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233166"
---
# <a name="your-service-fabric-application-and-next-steps"></a>A Service Fabric-alkalmazás és a következő lépések
Az Azure Service Fabric-alkalmazás létrehozása. Ez a cikk ismerteti az egyes oktatóanyagok, és próbálja ki, a projekthez, talán érdekelheti a további információkat és esetleges további lépések a makeup.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Az oktatóanyagok, útmutatók és minták használatának első lépései
Készen áll a kezdésre?  

Munka a .NET-alkalmazás oktatóanyaga –. Ismerje meg, hogyan [alkalmazás készítése](service-fabric-tutorial-create-dotnet-app.md) az ASP.NET Core kezelőfelülete és egy állapotalapú háttérszolgáltatás, [az alkalmazás üzembe helyezése](service-fabric-tutorial-deploy-app-to-party-cluster.md) fürt [CI/CD beállítása](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), és [beállítása a monitoring and diagnostics](service-fabric-tutorial-monitoring-aspnet.md).

Vagy próbálja ki az alábbi útmutatók egyik és az első alkalmazás létrehozása...
- [C#A Reliable Services-szolgáltatás a Windows](service-fabric-reliable-services-quick-start.md) 
- [C#Reliable Actors-szolgáltatás a Windows](service-fabric-reliable-actors-get-started.md) 
- [Vendég végrehajtható szolgáltatást a Windows](quickstart-guest-app.md) 
- [Windows-alapú tárolóalkalmazás](service-fabric-get-started-containers.md) 

Akkor is hasznos lehet a próbálhatja ki a [mintaalkalmazások](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Kérdése vagy visszajelzése van?  Kell jelentse a problémát?
Olvassa el az [gyakori kérdésekre](service-fabric-common-questions.md) és válaszokat a Service Fabric mire képes, és hogyan kell használni.

[Támogatási lehetőségek](service-fabric-support.md) sorolja fel a StackOverflow-n és az MSDN fórumok kérdések, valamint a beállítások feltevéséhez jelentéskészítési problémái, támogatás igénybevétele és visszajelzés elküldése.

## <a name="the-application-project"></a>A projekt
Minden új alkalmazás tartalmaz egy alkalmazási projektet. Előfordulhat, hogy egy vagy két további projektek, a kiválasztott szolgáltatás típusától függően.

A projekt tartalmazza:

* A szolgáltatások, az alkalmazás alkotó hivatkozások halmaza.
* Három közzétételi profilok (1 csomópontos helyi 5 csomópontos helyi és Felhőbeli), amelyek segítségével a különböző környezetek – például az alapértelmezés szerint a fürt végpontja és -e a frissítési helyezésekkel kapcsolatos beállítások használata a beállítások kezelése.
* Alkalmazás három paraméter-fájlok (ugyanaz, mint fent), hogy használhatja a környezetspecifikus alkalmazás konfigurációk, például egy szolgáltatás számára létrehozandó partíciók száma karbantartása. Ismerje meg, hogyan [az alkalmazás konfigurálása több környezethez](service-fabric-manage-multiple-environment-app-configuration.md).
* A telepítési parancsfájlt, amely segítségével a parancssorból vagy egy automatizált folyamatos integrációs és üzembe helyezési folyamat részeként az alkalmazás üzembe helyezéséhez. Tudjon meg többet [PowerShell-lel végzett alkalmazástelepítéshez](service-fabric-deploy-remove-applications.md).
* Az alkalmazásjegyzékben, amely ismerteti az alkalmazást. A jegyzékfájlt a ApplicationPackageRoot mappában találja. Tudjon meg többet [alkalmazás és jegyzékek](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>További tudnivalók a programozási modellekről
A Service Fabric írni, és a szolgáltatások kezeléséhez több lehetőséget is kínál.  Itt van a áttekintése és általános tájékoztatást [állapot nélküli és állapotalapú Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [tárolók](service-fabric-containers-overview.md), [Vendég végrehajtható fájlok ](service-fabric-guest-executables-introduction.md), és [állapot nélküli és állapotalapú ASP.NET Core szolgáltatások](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Ismerje meg a szolgáltatások közötti kommunikáció
Service Fabric-alkalmazás különböző szolgáltatásokat, ahol minden szolgáltatás hajt végre egy specializált feladat tevődik össze. Ezek a szolgáltatások kommunikálhatnak egymással, és előfordulhat, hogy ügyfélalkalmazások a fürtön kívülről, amely csatlakozik, és a kommunikáció a szolgáltatásokkal. Ismerje meg, hogyan [és a szolgáltatások közötti kommunikáció beállítását](service-fabric-connect-and-communicate-with-services.md) a Service Fabricben. 

## <a name="learn-about-configuring-application-security"></a>További információ az alkalmazások biztonságának konfigurálása
A különböző felhasználói fiókok a fürtben futó alkalmazások biztonságát. A Service Fabric emellett segít az erőforrások által használt alkalmazások időpontjában üzemelő példányt a felhasználói fiókok – például, fájlok, könyvtárak és tanúsítványok védelmét. Ez lehetővé teszi futó alkalmazások még megosztott környezetben üzemeltetett, egy biztonságosabb.  Ismerje meg, hogyan [konfigurálhat biztonsági házirendeket az alkalmazás](service-fabric-application-runas-security.md).

Az alkalmazás tartalmazhatnak bizalmas adatokat, például a storage kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem szövegként kezelje. Ismerje meg, hogyan [az alkalmazás titkos kódok kezelése](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Ismerje meg az alkalmazás-életciklus
Egyéb platformok esetén a Service Fabric-alkalmazás általában halad végig a következő fázisok szerint: tervezési, fejlesztési, tesztelési, üzembe helyezés, frissítése, karbantartási és eltávolítását. [Ez a cikk](service-fabric-application-lifecycle.md) áttekintést ad az API-k és azok hogyan használhatók a eltérő szerepkörök fázisai a a Service Fabric-alkalmazás életciklusa során.

## <a name="next-steps"></a>További lépések
- [Egy Windows-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- A fürt, ideértve a központilag telepített alkalmazások és a fizikai elrendezését, az megjelenítése [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Verzió és a szolgáltatások frissítése](service-fabric-application-upgrade-tutorial.md)


