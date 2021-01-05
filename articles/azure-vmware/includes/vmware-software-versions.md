---
title: VMware-szoftverek verziói
description: Támogatott VMware-verziók az Azure VMware-megoldáshoz.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825097"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Az Azure VMware-megoldás saját felhőalapú fürtjében használt VMware-szoftver aktuális szoftververzió a következők:

| Szoftverek              |    Verzió   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| vSAN                  |    6,7 U3    |
| NSX – T                 |      2.5     |


>[!NOTE]
>A NSX-T a NSX egyetlen támogatott verziója.

A privát felhőben lévő összes új fürt esetében a szoftver verziója megegyezik a jelenleg futó verzióval. Egy előfizetéshez tartozó új privát felhő esetén a szoftveres verem legújabb verziója lesz telepítve. További információ: a [VMware szoftver verziójának követelményei](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

A Private Cloud szoftvercsomag frissítései megőrzik a szoftvert a VMware-ből származó legújabb szoftvercsomag-kiadás egy verzióján belül. A Private Cloud Software-verziók eltérhetnek az egyes szoftver-összetevők legújabb verzióitól (ESXi, NSX-T, vCenter, vSAN). Az általános frissítési szabályzatokat és folyamatokat a [saját Felhőbeli frissítések és verziófrissítések](../concepts-upgrades.md)című cikkben ismertetett Azure VMware Solution platform szoftverhez tekintheti meg.

