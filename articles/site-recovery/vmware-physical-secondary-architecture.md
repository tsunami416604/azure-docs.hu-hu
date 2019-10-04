---
title: A VMware/fizikai kiszolgáló vészhelyzeti helyreállításának architektúrája egy másodlagos helyre a Azure Site Recovery használatával | Microsoft Docs
description: Ez a cikk áttekintést nyújt a helyszíni VMware virtuális gépek vagy fizikai Windows-/Linux-kiszolgálók vész-helyreállítási folyamata során használt összetevőkről és architektúráról egy másodlagos VMware-helyre Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: 0c00e26e6c12835db96c192400c3fe8652534dd4
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972112"
---
# <a name="architecture-for-vmwarephysical-server-replication-to-a-secondary-on-premises-site"></a>Architektúra a VMware/fizikai kiszolgálók replikálásához egy másodlagos helyszíni helyre

Ez a cikk a vész-helyreállítási replikáció, a feladatátvétel és a helyszíni VMware virtuális gépek (VM-EK) vagy fizikai Windows-/Linux-kiszolgálók másodlagos VMware-helyre történő helyreállításának beállításakor használt architektúrát és folyamatokat ismerteti [Azure site Recovery ](site-recovery-overview.md).


## <a name="architectural-components"></a>Az architektúra összetevői

**Terület** | **Összetevő** | **Részletek**
--- | --- | ---
**Azure** | A forgatókönyv üzembe helyezéséhez használja az InMage Scout segédprogramot. | Az InMage Scout beszerzéséhez Azure-előfizetésre van szüksége.<br/><br/> Miután létrehozott egy Site Recovery-tárolót, töltse le az InMage Scoutot, és telepítse a legújabb frissítéseket az üzembe helyezés előkészítéséhez.
**Folyamatkiszolgáló** | Az elsődleges helyen található | Helyezze üzembe a folyamatkiszolgálót, amely kezeli az adatok gyorsítótárazását, tömörítését és optimalizálását.<br/><br/> Ezenfelül ez az összetevő kezeli a Unified Agent ügynöknek a védeni kívánt gépekre történő leküldéses telepítését.
**Konfigurációs kiszolgáló** | A másodlagos helyen található | A konfigurációs kiszolgáló végzi az üzemelő példány felügyeleti webhelyen vagy a vContinuum-konzolban végzett felügyeletét, konfigurálását és megfigyelését.
**vContinuum-kiszolgáló** | Nem kötelező. Ugyanoda kell telepíteni, mint a konfigurációs kiszolgálót. | Ez az összetevő elérhetővé tesz egy konzolt, amelyről felügyelheti és figyelheti a védett környezetet.
**Fő célkiszolgáló** | A másodlagos helyen található | A fő célkiszolgáló tárolja a replikált adatokat. Ez fogadja a folyamatkiszolgáló által küldött adatokat, létrehozza a replika gépet a másodlagos helyen, és tárolja az adatmegőrzési pontokat.<br/><br/> Az, hogy hány fő célkiszolgálóra van szükség, attól függ, hogy hány gépnek kíván védelmet biztosítani.<br/><br/> Ha vissza szeretné adnia a feladatokat az elsődleges helynek, azon is szüksége lesz egy fő célkiszolgálóra. A Unified Agent ügynök nincs telepítve ezen a kiszolgálón.
**VMware ESX/ESXi- és vCenter-kiszolgáló** |  A virtuális gépek ESX-/ESXi-gazdagépeken futnak. A gazdagépeket egy vCenter-kiszolgáló felügyeli | A VMware virtuális gépek replikálásához VMware-infrastruktúrára van szükség.
**Virtuális gépek/fizikai kiszolgálók** |  A replikálni kívánt VMware virtuális gépeken és fizikai kiszolgálókon telepített Unified Agent. | Ez az ügynök valósítja meg az összetevők közötti kommunikációt.

## <a name="replication-process"></a>Replikációs folyamat

1. Állítsa be mindkét oldalon az összetevőkiszolgálókat (konfigurációs, folyamat- és fő célkiszolgáló), majd telepítse a replikálni kívánt gépekre a Unified Agent ügynököt.
2. A kezdeti replikációt követően a gépek ügynökei továbbítják a változásreplikálás módosításait a folyamatkiszolgálónak.
3. A folyamatkiszolgáló optimalizálja az adatokat, majd átviszi őket a másodlagos hely fő célkiszolgálójára. A replikációs folyamatot a konfigurációs kiszolgáló kezeli.

**6. ábra: VMware – VMware replikáció**

![VMware és VMware közötti replikáció](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="next-steps"></a>További lépések

A VMware virtuális gépek és a fizikai kiszolgálók vész-helyreállításának [beállítása](vmware-physical-secondary-disaster-recovery.md) másodlagos helyre.
