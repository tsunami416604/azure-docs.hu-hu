---
title: Fogalmak – saját Felhőbeli frissítések
description: Ismerje meg az Azure VMware-megoldás legfontosabb frissítési folyamatait és funkcióit
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: f541aa4e4963cf40fad71201180ea118a1513fca
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752201"
---
# <a name="azure-vmware-solution-upgrade-concepts"></a>Az Azure VMware megoldás frissítési koncepciói

Az Azure VMware-megoldás privát felhők egyik legfőbb előnye, hogy a platform karbantartás alatt áll. A platform karbantartása magában foglalja a VMware ellenőrzött szoftvercsomag automatikus frissítését. A frissítések rendszeresen megtörténik, így biztosítva, hogy mindig a szoftver legújabb érvényesített verzióit használják.

## <a name="azure-vmware-solution-private-cloud-software-upgrades"></a>Azure VMware-megoldás saját Felhőbeli szoftverfrissítés

Az Azure VMware megoldás saját felhőalapú platformja VMware vSphere, ESXi, vSAN és NSX-T szoftverek adott verzióit tartalmazza. A Private Cloud szoftvercsomag az új, saját Felhőbeli telepítésekben való használatra, valamint a meglévő privát felhők frissítéseire van érvényesítve.

Az életciklus-kezelés frissítési folyamata nem igényel állásidőt a privát felhőknél. A frissítési folyamat biztosítja, hogy automatikusan az ellenőrzött Azure VMware-megoldás saját felhőalapú szoftverének legújabb verzióját használja. A frissítések a normál lépésszám szerint lesznek alkalmazva, így a privát felhők soha nem több, mint egy, a hitelesített szoftvercsomag legújabb kiadása mögötti változatot használnak. A rendszer értesítést küld a saját felhőbe tervezett frissítésekről. A frissítés elhalasztható, ha a privát felhő a legújabb kiadás egyik verzióján belül van.

A rendszer a kritikus javításokat és frissítéseket alkalmazza a hitelesítéskor. A szükséges kritikus frissítések előtt értesítést küldünk. Ez a szabályzat biztosítja, hogy a privát felhője azonnal alkalmazza a kritikus javításokat és frissítéseket.

A VMware-szoftververzió a [privát felhők és fürtök fogalma című cikkben](concepts-private-clouds-clusters.md) és a [Gyakori kérdések](faq.md)között található.

## <a name="next-steps"></a>További lépések

A következő lépés [egy privát felhő létrehozása](tutorial-create-private-cloud.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
