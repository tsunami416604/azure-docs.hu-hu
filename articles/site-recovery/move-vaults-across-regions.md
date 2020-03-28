---
title: Azure-webhely-helyreállítási tároló áthelyezése másik régióba
description: A Recovery Services-tároló (Azure Site Recovery) áthelyezése egy másik Azure-régióba
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74090300"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Helyreállítási szolgáltatások tárolójának és az Azure Site Recovery konfigurációjának áthelyezése egy másik Azure-régióba

Vannak különböző forgatókönyvek, amelyekben érdemes lehet áthelyezni a meglévő Azure-erőforrások egyik régióból a másikba. Ilyenpéldául a kezelhetőség, az irányítási okok, vagy a vállalati egyesülések és felvásárlások. Az egyik kapcsolódó erőforrásokat érdemes áthelyezni, amikor az Azure-beli virtuális gépek áthelyezése a vész-helyreállítási konfiguráció. 

Nincs első osztályú módja egy meglévő vész-helyreállítási konfiguráció egyik régióból a másikba való áthelyezésére. Ennek az az oka, hogy a célrégiót a forrás virtuálisgép-régió alapján konfigurálta. Ha úgy dönt, hogy módosítja a forrásrégiót, a célrégió korábban meglévő konfigurációi nem használhatók fel újra, és alaphelyzetbe kell állítani őket. Ez a cikk határozza meg a lépésről-lépésre folyamat átkonfigurálása a vész-helyreállítási telepítő, és helyezze át egy másik régióba.

Ebben a dokumentumban a következőket fogja:

> [!div class="checklist"]
> * Ellenőrizze az áthelyezés előfeltételeit.
> * Azonosítsa az Azure Site Recovery által használt erőforrásokat.
> * A replikáció letiltása.
> * Törölje az erőforrásokat.
> * Állítsa be a hely-helyreállítást a virtuális gépek új forrásrégiója alapján.

> [!IMPORTANT]
> Jelenleg nincs első osztályú módja a Recovery Services-tároló és a vész-helyreállítási konfiguráció áthelyezése, mint egy másik régióban. Ez a cikk végigvezeti a replikáció letiltásának és beállításának folyamatán az új régióban.

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy távolítsa el és törölje a vész-helyreállítási konfigurációt, mielőtt megpróbálna áthelyezni az Azure-beli virtuális gépek egy másik régióba. 

  > [!NOTE]
  > Ha az Azure virtuális gép új célrégiója megegyezik a vész-helyreállítási célrégióval, használhatja a meglévő replikációs konfigurációt, és áthelyezheti azt. Kövesse az [Azure IaaS-virtuális gépek áthelyezése egy másik Azure-régióba](azure-to-azure-tutorial-migrate.md)című lépéseit.

- Győződjön meg arról, hogy megalapozott döntést hoz, és tájékoztatja az érdekelt feleket. A virtuális gép nem lesz védve a katasztrófák ellen, amíg a virtuális gép áthelyezése befejeződött.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Az Azure Site Recovery által használt erőforrások azonosítása
Javasoljuk, hogy ezt a lépést a következő lépés előtt tegye meg. A virtuális gépek replikálása közben könnyebb azonosítani a megfelelő erőforrásokat.

A replikált minden egyes Azure virtuális gép esetében nyissa meg **a védett elemek** > **replikált elemek** > **tulajdonságai nak,** és azonosítsa a következő erőforrásokat:

- Cél erőforráscsoport
- Gyorsítótártár-fiók
- Céltárfiók (nem felügyelt lemezalapú Azure-virtuális gép esetén) 
- Célhálózat


## <a name="disable-the-existing-disaster-recovery-configuration"></a>A meglévő vész-helyreállítási konfiguráció letiltása

1. Nyissa meg a Helyreállítási szolgáltatások tárolójában.
2. A **Védett elemek** > **replikált elemei csoportban**kattintson a jobb gombbal a számítógépre, és válassza **a Replikáció letiltása**parancsot.
3. Ismételje meg ezt a lépést az összes áthelyezni kívánt virtuális gép esetében.

> [!NOTE]
> A mobilitási szolgáltatás nem lesz eltávolítva a védett kiszolgálókról. Manuálisan kell eltávolítania. Ha ismét védeni szeretné a kiszolgálót, kihagyhatja a mobilitási szolgáltatás eltávolítását.

## <a name="delete-the-resources"></a>Az erőforrások törlése

1. Nyissa meg a Helyreállítási szolgáltatások tárolójában.
2. Válassza a **Törlés** elemet.
3. Törölje a korábban azonosított összes többi [erőforrást.](#identify-the-resources-that-were-used-by-azure-site-recovery)
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Az Azure-beli virtuális gépek áthelyezése az új célrégióba

Kövesse az alábbi cikkekben leírt lépéseket az Azure-beli virtuális gépek célrégióba való áthelyezésének követelménye alapján:

- [Azure-beli virtuális gépek áthelyezése egy másik régióba](azure-to-azure-tutorial-migrate.md)
- [Azure-beli virtuális gépek áthelyezése rendelkezésre állási zónákba](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Site Recovery beállítása a virtuális gépek új forrásrégiója alapján

Konfigurálja a vészhelyreállítást az új régióba áthelyezett Azure-beli virtuális gépekhez az [Azure-beli virtuális gépek vész-helyreállítási állapotának beállítása](azure-to-azure-tutorial-enable-replication.md)című lépéseit követve.
