---
title: Az Azure Container Instances és tárolóvezénylés
description: Ismerje meg, hogyan az Azure container instances használatához tárolóvezénylőt az.
services: container-instances
author: seanmck
ms.service: container-instances
ms.topic: article
ms.date: 10/05/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c17bdb5a81640a7162ae735a4633a31cdfffbb1d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803511"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Az Azure Container Instances és tárolóvezénylőt

Kis méret és alkalmazás tájolást tárolók kifejezetten a mikroszolgáltatás-alapú architektúrák és Agilis kézbesítési környezetben. A feladat automatizált és felügyelt tárolókat és azok működését nagy számú, más néven *vezénylési*. Népszerű tárolóvezénylőt közé tartozik a Kubernetes, DC/OS és Docker Swarm.

Az Azure Container Instances néhány vezénylési platformokra alapszintű ütemezési funkciókat biztosít. És nem terjed ki a magasabb értékű szolgáltatások platformokhoz biztosító, amíg az Azure Container Instances kiegészíti azokat. Ez a cikk ismerteti az Azure Container Instances kezeli, és teljes tárolóvezénylőt előfordulhat, hogy mobiljelentésre annak hatókörén.

## <a name="traditional-orchestration"></a>Hagyományos vezénylési

Vezénylési standard definícióját a következő feladatokból áll:

- **Ütemezés**: egy tárolórendszerképet, és a egy erőforrás-kérelmet kap, keresse meg a megfelelő gép, amelyen a tároló futtatásához.
- **Kapcsolat/Anti-affinity**: Adja meg, hogy a tárolók kell a Futtatás közeli hasznos helyek egymással (a teljesítmény) vagy elég távolságra (a rendelkezésre állás érdekében).
- **Szolgáltatásállapot-figyelést**: nézze meg a tároló hibák, és automatikusan le őket.
- **Feladatátvétel**: nyomon követheti az összes olyan számítógépen futó, és ütemezze újra a sikertelen gépekről tárolók a megfelelő állapotú csomópontokat.
- **Skálázás**: hozzáadása vagy eltávolítása a megfelelő igény szerint, manuálisan vagy automatikusan a container Instances szolgáltatásban.
- **Hálózatkezelés**: Adjon meg egy fölérétegzett hálózatot a gazdagép több gép közötti kommunikációhoz tárolók összehangolása.
- **A szolgáltatásészlelés**: tárolók keresse meg egymással automatikusan, még akkor is, mivel azok között gazdagépekre, és módosítsa az IP-címek engedélyezése.
- **Alkalmazásfrissítések koordinált**: alkalmazás üzemszünet elkerülése érdekében a tároló frissítések kezelése, és a visszaállítás engedélyezése, ha valami probléma merül fel.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Az Azure Container Instances vezénylési: rétegelt megközelítést

Az Azure Container Instances lehetővé teszi a réteges megközelítésének szervezését, minden egyetlen tároló, miközben lehetővé teszi az orchestrator platform többtárolós feladatait megkönnyítése végrehajtásához szükséges ütemezés és a felügyeleti képességet biztosít.

Az Azure container Instances az alapul szolgáló infrastruktúra kezeli, mivel az orchestrator platform nem kell magát a keresés, egy megfelelő gazdagép egyetlen tároló futtatására vonatkoznak. A felhő rugalmasságát biztosítja, hogy egy mindig elérhető. Ehelyett az orchestrator összpontosíthat a feladatok, amelyek megkönnyítik a több tároló-architektúrák, beleértve a méretezés és összehangolt frissítése.

## <a name="potential-scenarios"></a>A lehetséges forgatókönyvek

Bár az Azure Container Instances használatával az orchestrator integrációs továbbra is születőfélben, várhatóan tovább fogjuk, hogy néhány különböző környezetekben előfordulhat, hogy kész:

### <a name="orchestration-of-container-instances-exclusively"></a>Vezénylési tároló kizárólag példányok

Mivel gyorsan elkezdheti a munkát, és másodpercalapú számlázás, kizárólag az Azure Container Instances alapján környezet kínál a leggyorsabban használatba, és nagymértékben változó számítási feladatok kezelésére.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Container Instances szolgáltatásban és a tárolókat a virtuális gépek együttes használata

A hosszan futó, stabil számítási feladatokhoz dedikált virtuális gépek egy fürtben lévő tárolók replikálásával segít a vállalatnak rendszerint olcsóbb futó ugyanazokat a tárolókat az Azure Container Instances használatával. Ugyanakkor a tárolópéldányok biztosít egy remek megoldást gyorsan és csökkenthetjük a teljes kapacitás használati váratlan vagy rövid ideig tartó kiugrások kezelésére.

Ahelyett, hogy a horizontális felskálázás a fürtben lévő virtuális gépek számát, akkor ezeken a gépeken alakzatot további tárolók üzembe helyezése, az orchestrator is egyszerűen ütemezheti a további tárolókat az Azure Container Instances szolgáltatásban, és törölje őket, ha azok már nem szükséges.

## <a name="sample-implementation-virtual-kubelet-for-kubernetes"></a>Példa: a Kubernetes Virtual Kubelet

A [Virtual Kubelet] [ aci-connector-k8s] projektet mutatja be, hogyan integrálható a vezénylési tárolóplatformokhoz az Azure Container Instances használatával.

Virtual Kubelet utánozza a Kubernetes [kubelet] [ kubelet-doc] csomópontként regisztrálás korlátlan kapacitást és létrehozásának zahájeno [podok] [ pod-doc] , tárolócsoportok az Azure Container Instances szolgáltatásban.

Összekötők más vezénylők sikerült felépíteni, amelyek hasonló módon integrálhatók a platform primitívek úgy, hogy az orchestrator API hatékonyságát a gyorsasága és egyszerűsége kezelése az Azure Container Instances a tárolók.

## <a name="next-steps"></a>További lépések

Az első tároló létrehozása az Azure Container Instances használatával a [a rövid útmutató](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/