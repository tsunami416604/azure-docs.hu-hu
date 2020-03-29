---
title: A Service Fabric-projekt létrehozása a következő lépésekkel
description: Ismerje meg a Visual Studióban most létrehozott alkalmazásprojektet.  Ismerje meg, hogyan hozhat létre szolgáltatásokat oktatóanyagok használatával, és tudjon meg többet a Service Fabric szolgáltatásainak fejlesztéséről.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349412"
---
# <a name="your-service-fabric-application-and-next-steps"></a>A Service Fabric-alkalmazás és a következő lépések
Az Azure Service Fabric-alkalmazás létrejött. Ez a cikk néhány kipróbálandó oktatóanyagot, a projekt sminkjét, néhány további információt és a lehetséges következő lépéseket ismerteti.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Ismerkedés az oktatóanyagokkal, a bemutatókkal és a mintákkal
Készen áll a kezdésre?  

A .NET alkalmazás oktatóanyagának átnézése. Megtudhatja, hogyan [hozhat létre alkalmazást](service-fabric-tutorial-create-dotnet-app.md) egy ASP.NET Core előtérrel és egy állapotalapú háttérrendszerrel, hogyan [telepítheti az alkalmazást](service-fabric-tutorial-deploy-app-to-party-cluster.md) egy fürtre, konfigurálhatja a [CI/CD-t,](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)és [hogyan állíthatja be a figyelést és a diagnosztikát.](service-fabric-tutorial-monitoring-aspnet.md)

Vagy próbálja ki az alábbi séták egyikét, és hozza létre az első...
- [C# Megbízható szolgáltatások szolgáltatás a Windows rendszeren](service-fabric-reliable-services-quick-start.md) 
- [C# Reliable Actors szolgáltatás Windows rendszeren](service-fabric-reliable-actors-get-started.md) 
- [Vendég végrehajtható szolgáltatás a Windows rendszeren](quickstart-guest-app.md) 
- [Windows-alapú tárolóalkalmazás](service-fabric-get-started-containers.md) 

Ön is érdekelt kipróbálni [a minta alkalmazások](https://aka.ms/servicefabricsamples).

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Kérdése van, vagy visszajelzést küldene?  Probléma bejelentése szükséges?
Olvassa el [a gyakori kérdéseket,](service-fabric-common-questions.md) és válaszokat talál, hogy mit tehet a Service Fabric, és hogyan kell használni.

[A támogatási lehetőségek](service-fabric-support.md) a StackOverflow és az MSDN fórumait sorolják fel a kérdések feltevéséhez, valamint a problémák jelentésének, a támogatás megszerzésének és a termékvisszajelzések elküldésének lehetőségeihez.

## <a name="the-application-project"></a>Az alkalmazásprojekt
Minden új alkalmazás tartalmaz egy alkalmazásprojektet. A választott szolgáltatás típusától függően egy vagy két további projekt is elvégezhető.

Az alkalmazásprojekt a következőkből áll:

* Az alkalmazást kiálló szolgáltatásokra mutató hivatkozások készlete.
* Három közzétételi profil (1 csomópontos helyi, 5 csomós helyi és felhőalapú), amelyek segítségével fenntarthatja a különböző környezetekkel való munka beállításait – például a fürtvégponthoz kapcsolódó beállításokat, és alapértelmezés szerint végrehajthatja-e a frissítési központi telepítéseket.
* Három alkalmazásparaméter-fájl (ugyanaz, mint fent), amelyek segítségével környezetspecifikus alkalmazáskonfigurációk, például a partíciók száma egy szolgáltatás hoz létre. Ismerje meg, hogyan [konfigurálhatja az alkalmazást több környezetben.](service-fabric-manage-multiple-environment-app-configuration.md)
* Központi telepítési parancsfájl, amely segítségével telepítheti az alkalmazást a parancssorból vagy egy automatikus folyamatos integrációs és üzembe helyezési folyamat részeként. További információ az [alkalmazások PowerShell használatával történő központi telepítéséről.](service-fabric-deploy-remove-applications.md)
* Az alkalmazás jegyzékfájlja, amely leírja az alkalmazást. A jegyzékfájl az ApplicationPackageRoot mappában található. További információ [az alkalmazás- és szolgáltatásjegyzékekről.](service-fabric-application-model.md)



## <a name="learn-more-about-the-programming-models"></a>További információ a programozási modellekről
A Service Fabric többféle módon írhatja és kezelheti a szolgáltatásokat.  Az alábbiakban áttekintést és fogalmi információkat talál az [állapotnélküli és állapotmentes megbízható szolgáltatásokról](service-fabric-reliable-services-introduction.md), [a megbízható szereplőkről](service-fabric-reliable-actors-introduction.md), [a tárolókról](service-fabric-containers-overview.md), [a vendég végrehajtható fájlokról](service-fabric-guest-executables-introduction.md), [valamint az állapotnélküli és állapotmentes ASP.NET Core szolgáltatásokról](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>További információ a szolgáltatáskommunikációról
A Service Fabric-alkalmazás különböző szolgáltatásokból áll, ahol minden szolgáltatás egy speciális feladatot hajt végre. Ezek a szolgáltatások kommunikálhatnak egymással, és előfordulhat, hogy a fürtön kívüli ügyfélalkalmazások csatlakoznak a szolgáltatásokhoz, és kommunikálnak velük. Ismerje meg, hogyan [állíthatja be a kommunikációt a szolgáltatásokkal és a szolgáltatások között a](service-fabric-connect-and-communicate-with-services.md) Service Fabric ben. 

## <a name="learn-about-configuring-application-security"></a>További információ az alkalmazásbiztonság konfigurálásáról
A fürtben különböző felhasználói fiókok alatt futó alkalmazások biztonságossá tehető. A Service Fabric is segít az alkalmazások által a felhasználói fiókok – például fájlok, könyvtárak és tanúsítványok – központi telepítésekor használt erőforrások biztonságossá tétele. Ez még egy megosztott üzemeltetett környezetben is biztonságosabbá teszi az alkalmazások futtatását egymástól.  További információ az [alkalmazás biztonsági házirendjeinek konfigurálásáról.](service-fabric-application-runas-security.md)

Az alkalmazás tartalmazhat bizalmas információkat, például tárolási kapcsolat ihúrok, jelszavak vagy más értékeket, amelyeket nem szabad egyszerű szövegként kezelni. Ismerje meg, hogyan [kezelheti a titkokat az alkalmazásban.](service-fabric-application-secret-management.md)

## <a name="learn-about-the-application-lifecycle"></a>További információ az alkalmazás életciklusáról
Más platformokhoz is a Service Fabric-alkalmazások általában a következő fázisokon megy keresztül: tervezés, fejlesztés, tesztelés, üzembe helyezés, frissítés, karbantartás és eltávolítás. [Ez](service-fabric-application-lifecycle.md) a cikk áttekintést nyújt az API-kat, és hogyan használják a különböző szerepkörök a Service Fabric-alkalmazás életciklusának fázisaiban.

## <a name="next-steps"></a>További lépések
- [Hozzon létre egy Windows-fürtöt az Azure-ban.](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)segítségével jelenítse meg a fürtöt, beleértve az üzembe helyezett alkalmazásokat és a fizikai elrendezést is.
- [A szolgáltatások verziója és frissítése](service-fabric-application-upgrade-tutorial.md)


