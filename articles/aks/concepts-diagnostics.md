---
title: Az Azure Kubernetes-szolgáltatás (AKS) diagnosztikai áttekintése
description: Ismerje meg a fürtök öndiagnosztizálása az Azure Kubernetes szolgáltatásban.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126602"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Az Azure Kubernetes szolgáltatásdiagnosztika (előzetes verzió) – áttekintés

Az Azure Kubernetes-szolgáltatás (AKS) fürtproblémáinak elhárítása fontos része a fürt karbantartásának, különösen akkor, ha a fürt kritikus fontosságú számítási feladatokat futtat. Az AKS Diagnostics egy intelligens, öndiagnosztikai élmény, amely segít azonosítani és megoldani a fürt problémáit. Az AKS Diagnostics natív felhőalapú, és további konfiguráció vagy számlázási költség nélkül is használhatja.

Ez a funkció mostantól nyilvános előzetes verzióban érhető el.

## <a name="open-aks-diagnostics"></a>AKS-diagnosztika megnyitása

Az AKS diagnosztika elérése:

- Keresse meg a Kubernetes-fürtöt az [Azure Portalon.](https://portal.azure.com)
- Kattintson **a diagnosztizálása és a problémák megoldása** a bal oldali navigációs, amely megnyitja AKS Diagnosztika.
- Válasszon olyan kategóriát, amely a legjobban leírja a fürt kérdését a kezdőlap csempéjének kulcsszavaival, vagy írjon be egy olyan kulcsszót, amely a legjobban leírja a problémát a keresősávban, például _fürtcsomópont-problémák_.

![Kezdőlap](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Diagnosztikai jelentés megtekintése

Miután rákattintott egy kategóriára, megtekintheti a fürtre jellemző diagnosztikai jelentést. A diagnosztikai jelentés intelligens módon jelzi, ha a fürtben bármilyen probléma merül fel az állapotikonok segítségével. Az egyes témaköröket a **További információk** elemre kattintva megtekintheti a probléma részletes leírását, az ajánlott műveleteket, a hasznos dokumentumokra mutató hivatkozásokat, a kapcsolódó mutatókat és a naplózási adatokat. A diagnosztikai jelentések intelligens módon jönnek létre a fürt jelenlegi állapota alapján, miután különböző ellenőrzéseket futtattak. A diagnosztikai jelentések hasznos eszközt jelentenek a fürt problémájának azonosításához és a probléma megoldásához szükséges következő lépések megkereséséhez.

![Diagnosztikai jelentés](./media/concepts-diagnostics/diagnostic-report.png)

![Bővített diagnosztikai jelentés](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Fürtelemzési adatok

A következő diagnosztikai ellenőrzések érhetők el a **Cluster Insights**.

### <a name="cluster-node-issues"></a>Fürtcsomópont-problémák

A fürtcsomóproblémák olyan csomópontokkal kapcsolatos problémákat keresnek, amelyek miatt a fürt váratlanul viselkedhet.

- Csomópontkészenléti problémák
- Csomóponthibák
- Nincs elegendő forrás
- Hiányzik a csomópont IP-konfigurációja
- Csomópontcni-hibái
- A csomópont nem található
- Csomópont kikapcsolása
- Csomópont-hitelesítési hiba
- Csomópont kube-proxy elavult

### <a name="create-read-update--delete-operations"></a>Műveletek létrehozása, olvasása, frissítése & törlése

A CRUD Operations ellenőrzi azokat a CRUD-műveleteket, amelyek problémákat okozhatnak a fürtben.

- Használatban lévő alhálózati törlési művelet hiba
- Hálózati biztonsági csoport törlési művelete – hiba
- Használatban lévő útvonaltábla törlési művelete hiba
- Hivatkozott erőforrás-kiépítési hiba
- Nyilvános IP-cím törlési művelete hiba
- Telepítési hiba a telepítési kvóta miatt
- Működési hiba a szervezeti házirend miatt
- Hiányzik az előfizetés regisztrációja
- Virtuálisgép-bővítmény kiépítési hibája
- Alhálózati kapacitás
- A kvóta túllépte a hibát

### <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

Az Identitás- és biztonságkezelés olyan hitelesítési és engedélyezési hibákat észlel, amelyek megakadályozhatják a fürttel való kommunikációt.

- Csomópontengedélyezési hibák
- 401-es hibák
- 403-as hibák

## <a name="next-steps"></a>További lépések

Az [AKS Periscope](https://aka.ms/aksperiscope)használatával naplókat gyűjthet a fürtproblémák további elhárításához.

Tegye fel kérdéseit vagy visszajelzését a [UserVoice-on](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) a "[Diag]" hozzáadásával a címben.
