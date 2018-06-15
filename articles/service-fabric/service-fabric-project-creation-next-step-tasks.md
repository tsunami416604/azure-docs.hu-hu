---
title: A Service Fabric-projekt létrehozása további lépések |} Microsoft Docs
description: Ismerje meg az imént létrehozott Visual Studio projekt.  Ismerje meg, hogyan hozhat létre oktatóanyagok szolgáltatásokat, és további tudnivalók a Service Fabric-szolgáltatások fejlesztéséhez.
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
ms.openlocfilehash: a87dd6f4afa152aebafdde24defcabe841ae2e9c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206465"
---
# <a name="your-service-fabric-application-and-next-steps"></a>A Service Fabric-alkalmazás és a következő lépések
Az Azure Service Fabric-alkalmazás létrehozása. Ez a cikk ismerteti a próbálhatja ki az oktatóprogramok találhatók, a projekt, néhány további információt a érdekelheti és esetleges további lépések makeup.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Ismerkedés a minták, oktatóanyagok és útmutatók
Próbálja ki.  

A .NET-alkalmazás oktatóanyag működjön. Megtudhatja, hogyan [az alkalmazás elkészítésére](service-fabric-tutorial-create-dotnet-app.md) az ASP.NET Core előtér-és állapot-nyilvántartó back-end, [az alkalmazás telepítéséhez](service-fabric-tutorial-deploy-app-to-party-cluster.md) egy fürtbe, [CI/CD konfigurálása](service-fabric-tutorial-deploy-app-with-cicd-vsts.md), és [beállítása megfigyelési és diagnosztikai](service-fabric-tutorial-monitoring-aspnet.md).

Vagy, próbálja ki a következő útmutatók egyikét, és az első létrehozása...
- [C# megbízható szolgáltatások Windows rendszeren](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors szolgáltatás Windows rendszeren](service-fabric-reliable-actors-get-started.md) 
- [Vendég végrehajtható szolgáltatás Windows rendszeren](quickstart-guest-app.md) 
- [Windows-alapú tárolóalkalmazás](service-fabric-get-started-containers.md) 

Bizonyos is érdeklődik próbálhatja ki a [mintaalkalmazást](http://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Kérdése van, vagy visszajelzést?  Probléma bejelentése kell?
Olvassa végig [gyakori kérdéseket](service-fabric-common-questions.md) és a válaszok a Service Fabric Teendők, és hogyan kell használni.

[Támogatási lehetőségek](service-fabric-support.md) StackOverflow és az MSDN fórumain felsorolja azokhoz a kérdéseit, valamint a beállítások jelentések problémákról, keresztüli támogatás és visszajelzés küldése.

## <a name="the-application-project"></a>A projekt
Minden egyes új alkalmazás egy alkalmazás projektet tartalmaz. Előfordulhat, hogy egy vagy két további projektek, attól függően, hogy a kiválasztott szolgáltatás típusának.

Az alkalmazási projektet tartalmaz:

* A szolgáltatások, az alkalmazás alkotó mutató hivatkozások gyűjteménye.
* Három közzététele profilok (1-csomópont helyi 5-csomópont helyi és felhőalapú), melyekkel a különböző környezetek – például az alapértelmezés szerint a fürt végpontja, és hogy kell-e a frissítés központi telepítések kapcsolatos beállítások használata beállítások megőrzése érdekében.
* Három alkalmazás paraméter fájlok (ugyanaz, mint a fent), hogy segítségével konfigurációinak környezetfüggő alkalmazás, például a szolgáltatás létrehozásához a partíciók száma. Megtudhatja, hogyan [állítsa be az alkalmazását, több környezetek](service-fabric-manage-multiple-environment-app-configuration.md).
* A telepítési parancsfájlt, amely segítségével telepítheti az alkalmazást, a parancssorban vagy egy automatizált folyamatos integrációt és telepítést folyamat részeként. További információ [PowerShell használatával alkalmazások központi telepítése](service-fabric-deploy-remove-applications.md).
* Az alkalmazás jegyzékében, amely ismerteti az alkalmazást. A jegyzék a ApplicationPackageRoot mappában található. További információ [alkalmazás és szolgáltatás jegyzékfájlokban](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>További tudnivalók a programozási modellekről
A Service Fabric és a szolgáltatások kezeléséhez több lehetőséget is kínál.  Áttekintés és kapcsolatos információkat itt van [állapotmentes és állapotalapú Reliable Services](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [tárolók](service-fabric-containers-overview.md), [Vendég végrehajtható fájlok ](service-fabric-guest-executables-introduction.md), és [állapotmentes és állapotalapú ASP.NET Core services](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>További információk a szolgáltatások közötti kommunikáció
A Service Fabric-alkalmazás különböző szolgáltatások, ahol minden szolgáltatás hajt végre egy speciális feladat jön létre. Ezek a szolgáltatások is kommunikálhatnak egymással, és előfordulhat, hogy a fürtön kívüli használó ügyfélalkalmazások csatlakozhat, és szolgáltatásokkal kommunikálni. Megtudhatja, hogyan [állítsa be a és a szolgáltatások közötti kommunikációs](service-fabric-connect-and-communicate-with-services.md) a Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>További tudnivalók az alkalmazás biztonsági beállításainak megadása
Biztosíthatja, hogy a különböző felhasználói fiókok a fürtben futó alkalmazás számára. A Service Fabric is lehetővé teszi az alkalmazások által használt felhasználói fiók – központi telepítés alkalmával például erőforrásokat, a fájlokat, a könyvtárak és a tanúsítványok biztonságos. Így futó alkalmazást, még akkor is, a megosztott üzemeltetési környezetben, nagyobb biztonságot nyújt, egymástól.  Megtudhatja, hogyan [konfigurálhat biztonsági házirendeket az alkalmazás](service-fabric-application-runas-security.md).

Az alkalmazás tartalmazhatnak bizalmas adatokat, például a tárolási kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem egyszerű szöveges kezelje. Megtudhatja, hogyan [az alkalmazás titkos kulcsok kezelése](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Az alkalmazás-életciklus megismerése
És egyéb platformok, a Service Fabric-alkalmazás általában végig kell vinnie a következő fázisok: tervezési, fejlesztési, tesztelési, telepítési, frissítésére, karbantartási és eltávolítása. [Ez a cikk](service-fabric-application-lifecycle.md) az API-k és azok használata során a Service Fabric-alkalmazás életciklusa fázisai a különböző szerepkörök áttekintése.

## <a name="next-steps"></a>További lépések
- [Windows-fürt létrehozása az Azure-ban](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- A fürt megjelenítése, beleértve a telepített alkalmazások és a fizikai elrendezését [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Verziója és a szolgáltatások frissítésére](service-fabric-application-upgrade-tutorial.md)


