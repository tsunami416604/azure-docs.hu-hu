---
title: Service Fabric a projekt létrehozásának következő lépései
description: Ismerje meg a Visual Studióban létrehozott alkalmazás-projektet.  Ismerje meg, hogyan hozhat létre szolgáltatásokat oktatóanyagokkal, és hogyan fejlesztheti Service Fabric szolgáltatásainak fejlesztését.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75349412"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Az Service Fabric alkalmazás és a következő lépések
Az Azure Service Fabric-alkalmazás létrejött. Ez a cikk néhány olyan oktatóanyagot ismertet, amely kipróbálható, a projekt sminkje, néhány további információ, amely érdekli, és lehetséges a következő lépések.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Ismerkedés az oktatóanyagokkal, a bemutatókkal és a mintákkal
Készen áll?  

A .NET-alkalmazás oktatóanyagának használata. Megtudhatja, hogyan [hozhat létre egy alkalmazást](service-fabric-tutorial-create-dotnet-app.md) ASP.net Core előtér-és állapot-visszaállítási szolgáltatással, hogyan [helyezheti üzembe az alkalmazást](service-fabric-tutorial-deploy-app-to-party-cluster.md) egy fürtön, hogyan [konfigurálhatja a CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)-t, és [beállíthatja a figyelést és a diagnosztikát](service-fabric-tutorial-monitoring-aspnet.md).

Vagy próbálja ki az alábbi lépések egyikét, és hozza létre az elsőt...
- [C# Reliable Services szolgáltatás Windows rendszeren](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors szolgáltatás Windows rendszeren](service-fabric-reliable-actors-get-started.md) 
- [Vendég végrehajtható szolgáltatás Windows rendszeren](quickstart-guest-app.md) 
- [Windows-alapú tárolóalkalmazás](service-fabric-get-started-containers.md) 

Érdemes lehet a [minta alkalmazásaink](https://aka.ms/servicefabricsamples)kipróbálására is.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Kérdése van, vagy visszajelzést küldene?  Problémát kell jelentenie?
Olvassa el a [gyakori kérdéseket](service-fabric-common-questions.md) , és válaszoljon arra, hogy mit tehet a Service Fabric és hogyan használható.

A [támogatási lehetőségek](service-fabric-support.md) felsorolják a StackOverflow és az MSDN fórumait a kérdések feltevésére, valamint a problémák jelentésére, a támogatás beszerzésére és a termékkel kapcsolatos visszajelzések elküldésére vonatkozó lehetőségeket.

## <a name="the-application-project"></a>Az alkalmazás projektje
Minden új alkalmazás tartalmaz egy alkalmazás-projektet. A választott szolgáltatás típusától függően egy vagy két további projekt is lehet.

Az alkalmazás-projekt a következőkből áll:

* Az alkalmazást alkotó szolgáltatásokra mutató hivatkozások összessége.
* Három közzétételi profil (1 csomópontos helyi, 5 csomópontos helyi és felhő), amelyekkel megtarthatja a különböző környezetekkel való munkavégzéshez szükséges beállításokat – például a fürt végpontján kapcsolódó beállításokat, valamint azt, hogy alapértelmezés szerint kell-e végrehajtani a frissítési központi telepítéseket.
* Három alkalmazás-paraméter fájl (megegyezik a fentivel), amelyekkel karbantarthatja a környezettel kapcsolatos alkalmazás-konfigurációkat, például a szolgáltatáshoz létrehozandó partíciók számát. Ismerje meg, hogyan [konfigurálhatja alkalmazását több környezethez](service-fabric-manage-multiple-environment-app-configuration.md).
* Központi telepítési parancsfájl, amellyel az alkalmazást a parancssorból vagy egy automatizált, folyamatos integrációs és üzembe helyezési folyamat részeként helyezheti üzembe. További információ az [alkalmazások PowerShell használatával történő üzembe helyezéséről](service-fabric-deploy-remove-applications.md).
* Az alkalmazás jegyzékfájlja, amely leírja az alkalmazást. A jegyzékfájlt a ApplicationPackageRoot mappában találja. További információ az [alkalmazás-és szolgáltatás-jegyzékekről](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>További információ a programozási modellekről
A Service Fabric többféle módszert kínál a szolgáltatások írására és kezelésére.  Az alábbiakban áttekintheti az [állapot nélküli és állapot](service-fabric-reliable-services-introduction.md)-nyilvántartó Reliable Services, a [Reliable Actors](service-fabric-reliable-actors-introduction.md), a [tárolók](service-fabric-containers-overview.md), a [vendég végrehajtható fájlok](service-fabric-guest-executables-introduction.md), valamint az [állapot nélküli és állapot-nyilvántartó ASP.net Core szolgáltatásokat](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>A szolgáltatással kapcsolatos kommunikáció ismertetése
A Service Fabric alkalmazások különböző szolgáltatásokból állnak, ahol minden szolgáltatás egy speciális feladatot hajt végre. Ezek a szolgáltatások kommunikálhatnak egymással, és előfordulhat, hogy a fürtön kívüli ügyfélalkalmazások is csatlakozhatnak a szolgáltatásokhoz, és kommunikálhatnak velük. Ismerje meg, hogyan [állíthatja be a szolgáltatásait a és a között](service-fabric-connect-and-communicate-with-services.md) a Service Fabricban. 

## <a name="learn-about-configuring-application-security"></a>Tudnivalók az alkalmazások biztonságának konfigurálásáról
A fürtben futó alkalmazások különböző felhasználói fiókokban is biztonságossá tehetők. A Service Fabric az alkalmazások által használt erőforrásokat is biztonságossá teszi a felhasználói fiókok – például a fájlok, a címtárak és a tanúsítványok – telepítésének időpontjában. Így még a megosztottan üzemeltetett környezetekben is elérhetővé válik az alkalmazások egymástól való biztonságosabbá tétele.  Megtudhatja, hogyan [konfigurálhat biztonsági házirendeket az alkalmazáshoz](service-fabric-application-runas-security.md).

Az alkalmazás olyan bizalmas adatokat is tartalmazhat, mint például a tárolási kapcsolatok karakterláncai, a jelszavak vagy más olyan értékek, amelyeket nem szabad egyszerű szövegben kezelni. Ismerje meg, hogyan [kezelheti a titkokat az alkalmazásban](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Az alkalmazások életciklusának megismerése
Más platformokhoz hasonlóan a Service Fabric alkalmazások általában a következő fázisokon haladnak át: tervezés, fejlesztés, tesztelés, üzembe helyezés, frissítés, karbantartás és eltávolítás. [Ez a cikk](service-fabric-application-lifecycle.md) áttekintést nyújt az API-król, valamint arról, hogyan használják a különböző szerepkörök a Service Fabric alkalmazás életciklusának fázisaiban.

## <a name="next-steps"></a>További lépések
- [Hozzon létre egy Windows-fürtöt az Azure-ban](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Jelenítse meg a fürtöt, beleértve az üzembe helyezett alkalmazásokat és a fizikai elrendezést [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)segítségével.
- [A szolgáltatások verziója és frissítése](service-fabric-application-upgrade-tutorial.md)


