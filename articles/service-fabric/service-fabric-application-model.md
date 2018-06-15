---
title: Az Azure Service Fabric-alkalmazás modell |} Microsoft Docs
description: A modell, és ismerteti az alkalmazások és szolgáltatások a Service Fabric módjáról.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: dd453f393620923041eb8fa07b551f4945f48235
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204634"
---
# <a name="model-an-application-in-service-fabric"></a>A Service Fabric alkalmazás minta
Ez a cikk áttekintést nyújt az Azure Service Fabric alkalmazásmodell és hogyan adhat meg a kérelem és a service manifest-fájlok segítségével.

## <a name="understand-the-application-model"></a>Az alkalmazásmodell ismertetése
Egy alkalmazás egy bizonyos funkciója vagy funkciói végző alkotó szolgáltatások gyűjteménye. Egy szolgáltatás teljes és a különálló funkciót hajt végre és elindíthatja és egyéb szolgáltatások függetlenül futtatni.  A szolgáltatás kódot, a konfiguráció és az adatok tevődik össze. Minden egyes szolgáltatás kód a végrehajtható bináris áll, konfigurálása a futási időben betölthető Szolgáltatásbeállítások áll és a szolgáltatás által felhasznált tetszőleges statikus adatokat tartalmaz. A hierarchikus alkalmazás modellben minden egyes összetevő csak rendszerverzióval ellátott és frissített egymástól függetlenül.

![A Service Fabric alkalmazásmodellt.][appmodel-diagram]

Alkalmazástípust egy kategorizálási kérelem és a csomag egyik gyermekszoftver található szolgáltatástípusok áll. A szolgáltatás típus egy kategorizálási szolgáltatás. A kategorizálási különböző beállítások és konfigurációk rendelkezhet, de alapfunkciókat változatlan marad. A szolgáltatás példányainak a különböző nagyon sokféle konfiguráció azonos típusú szolgáltatás.  

Osztályok (vagy "típusú") az alkalmazások és szolgáltatások leírása XML-fájlok (alkalmazás és szolgáltatás jegyzékfájlokban) keresztül.  A jegyzékfájlokban alkalmazások és szolgáltatások leírása és a sablonokat, amely alkalmazások lehet létrehozni a fürt kép store-ból.  Jegyzékfájlokban szereplő részletesen ismertetett [alkalmazás és szolgáltatás jegyzékfájlokban](service-fabric-application-and-service-manifests.md). A séma meghatározása a ServiceManifest.xml és ApplicationManifest.xml fájl telepítve van a Service Fabric SDK-val, és az eszközök *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Az XML-séma részletes ismertetését lásd: [ServiceFabricServiceModel.xsd séma dokumentáció](service-fabric-service-model-schema.md).

A különböző alkalmazás-példányok kódot futtató különálló folyamat akkor is, ha a azonos a Service Fabric-csomópont. Ezenkívül az életciklus az összes alkalmazás-példány (például frissítése) kezelhetők egymástól függetlenül. Az alábbi ábrán látható, hogyan alkalmazástípusok szolgáltatástípusok, viszont álló kódot, a konfiguráció és adatok csomagok állnak. Egyszerűbbé teheti a diagramot, csak a kód/config/adatokat a csomagok `ServiceType4` láthatók, bár egyes szolgáltatástípusokról tartalmazhat néhány vagy minden csomagtípus.

![A Service Fabric alkalmazás és szolgáltatás típusainak][cluster-imagestore-apptypes]

Egy vagy több példányát egy típusú szolgáltatás lehet aktív a fürtben. Például állapot-nyilvántartó szolgáltatáspéldány, vagy a replikák elérése magas megbízhatóság állapot replikálja a replikák található a fürt csomópontjai között. Replikációs alapvetően a szolgáltatás számára elérhetők, még akkor is, ha a fürt egyik csomópontjáról sikertelen redundanciát biztosít. A [szolgáltatás particionálva](service-fabric-concepts-partitioning.md) további osztja a állapota (és a hozzáférési minták arra az állapotra) a fürt csomópontjai között.

Az alábbi ábrán látható, az alkalmazások és a szolgáltatáspéldány, a partíciók és a replikák közötti kapcsolat.

![Partíciók és replikáit a szolgáltatáson belül][cluster-application-instances]

> [!TIP]
> Azzal, hogy az alkalmazások megtekintheti egy http:// érhető el a Service Fabric Explorer eszközt használó fürtben&lt;yourclusteraddress&gt;: 19080/Explorer. További információkért lásd: [a fürt megjelenítése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>További lépések
- További tudnivalók [alkalmazás méretezhetőség](service-fabric-concepts-scalability.md).
- További tudnivalók a szolgáltatás [állapot](service-fabric-concepts-state.md), [particionálás](service-fabric-concepts-partitioning.md), és [rendelkezésre állási](service-fabric-availability-services.md).
- További információ az alkalmazások és szolgáltatások definiált hogyan [alkalmazás és szolgáltatás jegyzékfájlokban](service-fabric-application-and-service-manifests.md).
- [Modellek futtató alkalmazás](service-fabric-hosting-model.md) írják le a replikák (vagy példányok) telepített szolgáltatások és folyamata közötti kapcsolat.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


