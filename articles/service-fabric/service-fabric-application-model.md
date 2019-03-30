---
title: Az Azure Service Fabric-alkalmazásmodell |} A Microsoft Docs
description: Hogyan modellezheti, és írja le, alkalmazásokat és szolgáltatásokat a Service Fabricben.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 750970233cbcb14d901dbb5fa94f649f6ff8ae6c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666273"
---
# <a name="model-an-application-in-service-fabric"></a>A Service Fabric-alkalmazás minta
Ez a cikk az Azure Service Fabric-alkalmazásmodell és a egy alkalmazás és szolgáltatás keresztül jegyzékfájlok definiálása áttekintést nyújt.

## <a name="understand-the-application-model"></a>Az alkalmazásmodell megismerése
Egy alkalmazás egy bizonyos funkciója vagy funkciói végző alkotó szolgáltatások gyűjteménye. Egy szolgáltatás egy teljes és a különálló funkciót látnak el, és elindíthatja és más szolgáltatások függetlenül futnak.  Egy szolgáltatás, konfigurációs, és adatai tevődik össze. Minden egyes szolgáltatás kód a végrehajtható bináris fájlok áll, konfigurálása a futási időben töltődnek Szolgáltatásbeállítások áll, és tetszőleges statikus adatokat a szolgáltatás által használt adatokat tartalmaz. A hierarchikus alkalmazásmodell az egyes összetevők lehetnek rendszerverzióval ellátott és a frissített egymástól függetlenül.

![A Service Fabric-alkalmazásmodell][appmodel-diagram]

Az alkalmazástípus egy kategorizálási kérelem, és egy kötegelt szolgáltatástípusok áll. A szolgáltatás típus egy kategorizálási szolgáltatás. A kategorizálási rendelkezhetnek más beállítások és konfigurációk, de a legfontosabb funkcióit változatlan marad. A szolgáltatás példányai a különböző konfigurációs változata létezik azonos típusú szolgáltatás.  

Osztályok (vagy "típusú") az alkalmazások és szolgáltatások leírása XML-fájlok (alkalmazás- és szolgáltatásjegyzékek) keresztül.  A jegyzékek írja le, alkalmazásokat és szolgáltatásokat, és a sablonokat, amelyek alkalmazásokat lehet létrehozni a fürt lemezképtárolójába való.  A részletesen ismertetett jegyzékek [alkalmazásra és szolgáltatásjegyzékre](service-fabric-application-and-service-manifests.md). A ServiceManifest.xml és ApplicationManifest.xml fájlt sémadefiníciója telepítve van a Service Fabric SDK és -eszközök *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Az XML-séma leírása itt található [ServiceFabricServiceModel.xsd séma dokumentáció](service-fabric-service-model-schema.md).

A különböző alkalmazáspéldányok a kódot futtató különálló folyamat akkor is, ha a ugyanazon a Service Fabric-csomópont. Ezenkívül mindegyik alkalmazáspéldány élettartama (például frissítése) kezelhetők egymástól függetlenül. Az alábbi ábrán látható, hogyan épülnek fel alkalmazástípusok szolgáltatás típusú, viszont álló kód, konfiguráció és adatok csomagok. A diagram leegyszerűsítése csak a kód/config/adatokat a csomagok `ServiceType4` látható, bár egyes szolgáltatástípusokról tartalmazhat néhány vagy minden csomag típusa.

![A Service Fabric alkalmazás és szolgáltatás típusainak][cluster-imagestore-apptypes]

Egy vagy több példányát egy típusú szolgáltatás lehet a fürt aktív. Például állapot-nyilvántartó szolgáltatási példányra, vagy a replikákat, érhet el nagy megbízhatósággal állapotban a fürt másik csomópontjain található replikák közötti replikálásával. Replikációs lényegében biztosít redundanciát, a szolgáltatás számára elérhetők, még akkor is, ha egy fürtben egy csomópont meghibásodik. A [szolgáltatás particionált](service-fabric-concepts-partitioning.md) további osztja fel a állam (és a hozzáférési minták az adott állapotot) a fürtben található csomópontok között.

Az alábbi ábrán látható az alkalmazások és a szolgáltatáspéldány, a partíciók és a replikák közötti kapcsolat.

![Partíciók és a egy szolgáltatáson belüli replikák][cluster-application-instances]

> [!TIP]
> Egy fürtben: http:// érhető el a Service Fabric Explorer eszköz segítségével megtekintheti a alkalmazások elrendezését&lt;yourclusteraddress&gt;: 19080/Explorer címen. További információkért lásd: [a Service Fabric Explorerrel a fürt megjelenítése](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>További lépések
- Ismerje meg [alkalmazás méretezhetőségi](service-fabric-concepts-scalability.md).
- További tudnivalók a service [állapot](service-fabric-concepts-state.md), [particionálás](service-fabric-concepts-partitioning.md), és [rendelkezésre állási](service-fabric-availability-services.md).
- További információ az alkalmazások és szolgáltatások meghatározott hogyan [alkalmazásra és szolgáltatásjegyzékre](service-fabric-application-and-service-manifests.md).
- [Az alkalmazás üzemeltetési modellek](service-fabric-hosting-model.md) ismertetik egy üzembe helyezett szolgáltatás és a szolgáltatás gazdafolyamat replikák (vagy példányok) közötti kapcsolat.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


