---
title: Fogalmak – saját Felhőbeli frissítések
description: Ismerje meg az Azure VMware-megoldás (AVS) legfontosabb frissítési folyamatait és funkcióit
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: c5c79625ef661f220a0ba88d6d24ab59e7228195
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82740368"
---
# <a name="azure-vmware-solution-avs-upgrade-concepts"></a>Az Azure VMware Solution (AVS) frissítésével kapcsolatos fogalmak

Az Azure VMware-megoldás (AVS) privát felhők egyik fő előnye, hogy a platform karbantartás alatt áll. A platform karbantartása magában foglalja a VMware ellenőrzött szoftvercsomag automatikus frissítését. A frissítések rendszeresen megtörténik, így biztosítva, hogy mindig a szoftver legújabb érvényesített verzióit használják.

## <a name="avs-private-cloud-software-upgrades"></a>AVS Private Cloud-szoftverfrissítés

Az AVS Private Cloud platform a VMware vSphere, ESXi, vSAN és NSX-T szoftverek adott verzióit tartalmazza. A Private Cloud szoftvercsomag az új, saját Felhőbeli telepítésekben való használatra, valamint a meglévő privát felhők frissítéseire van érvényesítve.

Az életciklus-kezelés frissítési folyamata nem igényel állásidőt a privát felhőknél. A frissítési folyamat biztosítja, hogy automatikusan az érvényesített AVS Private Cloud szoftver legújabb verzióját használja. A frissítések a normál lépésszám szerint lesznek alkalmazva, így a privát felhők soha nem több, mint egy, a hitelesített szoftvercsomag legújabb kiadása mögötti változatot használnak. A rendszer értesítést küld a saját felhőbe tervezett frissítésekről. A frissítés elhalasztható, ha a privát felhő a legújabb kiadás egyik verzióján belül van.

A rendszer a kritikus javításokat és frissítéseket alkalmazza a hitelesítéskor. A szükséges kritikus frissítések előtt értesítést küldünk. Ez a szabályzat biztosítja, hogy a privát felhője azonnal alkalmazza a kritikus javításokat és frissítéseket.

A VMware-szoftververzió a [privát felhők és fürtök fogalma című cikkben](concepts-private-clouds-clusters.md) és a [Gyakori kérdések](faq.md)között található.

## <a name="next-steps"></a>További lépések

A következő lépés [egy privát felhő létrehozása](tutorial-create-private-cloud.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
