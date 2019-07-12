---
title: Az Azure Migrate replikációs készülék architektúrája |} A Microsoft Docs
description: A replikáció az Azure Migrate berendezés áttekintése
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811439"
---
# <a name="replication-appliance"></a>Replikációs készülék

Ez a cikk ismerteti az Azure Migrate által használt replikációs készülék: Server Assessment át a VMware virtuális gépek, a fizikai gépek és a privát és nyilvános felhőbeli virtuális géphez az Azure-bA egy ügynök-alapú áttelepítés használatával. 

Az eszköz megtalálható a [Azure Migrate](migrate-overview.md) hub. A hub natív szabási eszközzel elemzésekhez és migráláshoz, valamint az egyéb Azure-szolgáltatások, illetve külső független szoftvergyártók (ISV-k) az eszközök.


## <a name="appliance-overview"></a>Készülék – áttekintés

A replikációs berendezés helyezünk üzembe egy a helyszíni gépen, vagy egy VMware virtuális gép vagy fizikai kiszolgálóként. Fut:
- **Replikációs berendezés**: A replikációs berendezés kommunikáció koordinálását, valamint felügyeli az adatreplikációt, a helyszíni VMware virtuális gépek és fizikai kiszolgálók replikációja az Azure-bA.
- **Folyamatkiszolgáló**: A folyamatkiszolgáló, amely a replikáció berendezésen alapértelmezés szerint telepítve van, és a következő:
    - **Replikációs átjáró**: Azt a replikációs átjáróként üzemel. Engedélyezve van a replikáció gépekről kap replikációs adatokat. Optimalizálja a gyorsítótárazás, tömörítés és titkosítás a replikációs adatokat, és elküldi azt az Azure-bA.
    - **Ügynököt telepítő**: A mobilitási szolgáltatás leküldéses telepítést hajt végre. A szolgáltatásnak telepítve kell lennie, és az egyes futó helyszíni áttelepítés replikálni kívánt gépen.

## <a name="appliance-deployment"></a>Berendezés üzembe helyezés

**Üzembe helyezése** | **Használt** | **Részletek**
--- | --- |  ---
VMware virtuális gép | Általában akkor használatos, ha az Azure Migrate áttelepítési eszköz használata ügynök-alapú áttelepítés VMware virtuális gépek áttelepítése. | Töltse le az OVA sablont az Azure Migrate hubról, és importálása a vCenter-kiszolgáló a berendezés virtuális gép létrehozásához.
Fizikai gép | A helyszíni fizikai kiszolgálóknak áttelepítésekor, ha nem rendelkezik egy VMware-infrastruktúra, vagy ha nem sikerül egy OVA-sablon használatával a VMware virtuális gép létrehozásához használt. | Töltse le a szoftver az Azure Migrate hubról, és futtassa a készülék gép.

## <a name="appliance-deployment-requirements"></a>Készülék központi telepítésére vonatkozó követelmények

[Felülvizsgálat](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) a telepítési követelményeknek.



## <a name="appliance-license"></a>Készülék-licenc
A berendezés tartalmaz egy Windows Server 2016 próbalicencre, amely 180 napig érvényes. Ha a próbaidőszak lejártakor közelében van, ajánlott töltse le, és a egy új berendezések üzembe helyezése vagy aktiválni a készülék virtuális Gépet az operációsrendszer-licenccel.

## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi egy virtuális gép replikációját, megkezdődik a kezdeti replikálás az Azure storage, a megadott replikációs házirenddel. 
2. Forgalom replikálja az Azure storage nyilvános végpontokat az interneten keresztül. Forgalom a helyek közötti virtuális magánhálózati (VPN) keresztül egy helyszíni hely Azure-ba történő nem támogatott.
3. Kezdeti replikálás befejezése után kezdődik a változásreplikációhoz. A gépek nyomon követett módosításait a rendszer naplózza.
4. Kommunikáció a következőképpen történik:
    - Virtuális gépek a replikációs berendezéssel HTTPS a 443-as porton bejövő kommunikációt, a replikáció kezelését.
    - A replikációs berendezés koordinálja a replikálás az Azure-ral HTTPS 443-as kimenő porton keresztül.
    - Virtuális gépek a replikációs adatokat a folyamatkiszolgálónak (a replikáció berendezés fut) a HTTPS 9443-as porton bejövő küldése. Ez a port módosítható.
    - A folyamatkiszolgáló fogadja a replikált adatokat, optimalizálja a és titkosítja azokat, és elküldi azt az Azure storage 443-as porton keresztüli kimenő.
5. A replikációs adatok első föld bejelentkezik egy gyorsítótárfiókot, az Azure-ban. Ezek a naplófájlok feldolgozásának és az adatokat az Azure-ban tárolt felügyelt lemez.

![Architektúra](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Készülék-frissítések

A berendezés manuálisan frissíteni az Azure Migrate hubról. Azt javasoljuk, hogy mindig a legújabb verzióra.

1. Az Azure Migrate > kiszolgáló > az Azure Migrate: Server Assessment, infrastruktúra-kiszolgálók lapon kattintson a **konfigurációs kiszolgálók**.
2. A **konfigurációs kiszolgálók**, megjelenik egy hivatkozás **Ügynökverzió** amikor a replikáció berendezés új verziója érhető el. 
3. A replikációs készülék gépre töltse le a telepítőt, és telepítse a frissítést. A telepítő észleli, hogy az aktuális verzióját a készüléken.
 
## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](tutorial-assess-vmware.md#set-up-the-appliance-vm) a berendezés beállítása VMware-ről.
[Ismerje meg, hogyan](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) a berendezés beállítása a Hyper-V.

