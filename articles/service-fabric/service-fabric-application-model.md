---
title: Az Azure Service Fabric alkalmazásmodellje
description: Alkalmazások és szolgáltatások modellezése és leírása az Azure Service Fabric ben alkalmazás- és szolgáltatásjegyzékfájlok használatával.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7179686b7d4ef2df267cb95ece8f83d5fb7682b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551879"
---
# <a name="model-an-application-in-service-fabric"></a>Alkalmazás modellezése a Service Fabricben
Ez a cikk áttekintést nyújt az Azure Service Fabric alkalmazásmodellről, valamint arról, hogyan definiálhat egy alkalmazást és egy szolgáltatást a jegyzékfájlokon keresztül.

## <a name="understand-the-application-model"></a>Az alkalmazásmodell ismertetése
Az alkalmazás egy bizonyos funkciót vagy függvényeket ellátó rendszerelem-szolgáltatások gyűjteménye. A szolgáltatás teljes és önálló funkciót hajt végre, és más szolgáltatásoktól függetlenül indíthat és futtatható.  A szolgáltatás kódból, konfigurációból és adatokból áll. Minden szolgáltatás esetében a kód a végrehajtható bináris fájlokból áll, a konfiguráció futásidőben betölthető szolgáltatásbeállításokból áll, és az adatok tetszőleges statikus adatokból állnak, amelyeket a szolgáltatás használ fel. A hierarchikus alkalmazásmodell minden egyes összetevője egymástól függetlenül verziószámozható és frissíthető.

![A Service Fabric alkalmazásmodell][appmodel-diagram]

Az alkalmazástípus egy alkalmazás kategorizálása, és szolgáltatástípusok ból áll. A szolgáltatástípus egy szolgáltatás kategorizálása. A kategorizálás különböző beállításokkal és konfigurációkkal rendelkezhet, de az alapvető funkciók változatlanok maradnak. A szolgáltatás példányai az azonos szolgáltatástípus különböző szolgáltatáskonfigurációs változatai.  

Az alkalmazások és szolgáltatások osztályait (vagy "típusait") XML-fájlok (alkalmazásjegyzékek és szolgáltatásjegyzékek) ismertetik.  A jegyzékek alkalmazásokat és szolgáltatásokat írnak le, és azok a sablonok, amelyekhez az alkalmazások a fürt lemezképtárolójából példányosodhatnak.  A jegyzékek részletesen szerepelnek az [alkalmazás és a szolgáltatásjegyzékekben.](service-fabric-application-and-service-manifests.md) A ServiceManifest.xml és az ApplicationManifest.xml fájl sémadefiníciója a Service Fabric SDK-val, a *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricModel.xsd*eszközökkel van telepítve. Az XML-sémát a [ServiceFabricServiceModel.xsd séma dokumentációja dokumentálja.](service-fabric-service-model-schema.md)

A különböző alkalmazáspéldányok kódja külön folyamatokként fut, még akkor is, ha ugyanaz a Service Fabric-csomópont üzemelteti. Továbbá az egyes alkalmazáspéldányok életciklusa egymástól függetlenül kezelhető (például frissíthető). Az alábbi ábra bemutatja, hogy az alkalmazástípusok hogyan állnak szolgáltatástípusokból, amelyek viszont kódból, konfigurációból és adatcsomagokból állnak. A diagram egyszerűsítése érdekében csak a kód/konfigurációs/adatcsomagok `ServiceType4` jelennek meg, bár minden szolgáltatástípus tartalmazna néhány vagy az összes ilyen csomagtípust.

![A Service Fabric alkalmazástípusai és szolgáltatástípusai][cluster-imagestore-apptypes]

A fürtben egy vagy több szolgáltatástípus egy vagy több példánya is lehet aktív. Például az állapotalapú szolgáltatáspéldányok vagy replikák nagy megbízhatóságot érnek el a fürt különböző csomópontjain található replikák közötti állapot replikálásával. A replikáció lényegében redundanciát biztosít a szolgáltatás számára, még akkor is, ha egy fürt egyik csomópontja meghibásodik. A [particionált szolgáltatás](service-fabric-concepts-partitioning.md) tovább osztja az állapotát (és az adott állapothoz való hozzáférési mintákat) a fürt csomópontjai között.

Az alábbi ábra az alkalmazások és a szolgáltatáspéldányok, partíciók és replikák közötti kapcsolatot mutatja be.

![Partíciók és replikák egy szolgáltatáson belül][cluster-application-instances]

> [!TIP]
> A fürtben lévő alkalmazások elrendezését a Service Fabric Explorer&lt;eszközzel&gt;tekintheti meg, amely http:// a fürtcíme :19080/Explorer címen érhető el. További információt a [fürt megjelenítése a Service Fabric Intézővel](service-fabric-visualizing-your-cluster.md)című témakörben talál.
> 
> 


## <a name="next-steps"></a>További lépések
- További információ az [alkalmazások méretezhetőségéről.](service-fabric-concepts-scalability.md)
- További információ a szolgáltatás [állapotáról,](service-fabric-concepts-state.md) [a particionálásról](service-fabric-concepts-partitioning.md)és [a rendelkezésre állásról.](service-fabric-availability-services.md)
- További információ arról, hogyan vannak definiálva az alkalmazások és szolgáltatások az [Alkalmazás- és szolgáltatásjegyzékekben.](service-fabric-application-and-service-manifests.md)
- [Az alkalmazásüzemeltetési modellek](service-fabric-hosting-model.md) egy üzembe helyezett szolgáltatás és a szolgáltatásgazdafolyamat replikái (vagy példányai) közötti kapcsolatot írják le.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


