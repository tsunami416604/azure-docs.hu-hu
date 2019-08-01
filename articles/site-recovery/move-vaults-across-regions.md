---
title: Azure Site Recovery konfiguráció áthelyezése másik Azure-régióba | Microsoft Docs
description: Útmutató Site Recovery konfiguráció áthelyezéséhez egy másik Azure-régióba
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708297"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>Recovery Services-tároló áthelyezése és Azure Site Recovery konfigurálása egy másik Azure-régióba

Különböző helyzetekben érdemes áthelyezni a meglévő Azure-erőforrásokat az egyik régióról a másikra, az irányításra vagy a vállalati fúziók/beszerzések miatt. Az Azure-beli virtuális gépek áthelyezésekor az egyik kapcsolódó erőforrás a vész-helyreállítási konfiguráció. Az egyik régióból a másikba nem helyezhető át egy meglévő vész-helyreállítási konfiguráció. Ez lényegében azért van, mert a célként megadott régiót a forrás virtuálisgép-régiója alapján konfigurálta volna, és ha úgy dönt, hogy módosítja ezt a beállítást, a célként megadott régió korábbi konfigurációit nem lehet újra felhasználni, és alaphelyzetbe kell állítani. Ez a cikk a vész-helyreállítási beállítások újrakonfigurálásának lépésenkénti folyamatát határozza meg, és egy másik régióba helyezi át.

Ebben a dokumentumban a következőket fogja megtekinteni:

> [!div class="checklist"]
> * Az áthelyezés előfeltételeinek ellenőrzése
> * Az Azure Site Recovery által használt erőforrások azonosítása 
> * A replikálás letiltása
> * Az erőforrások törlése 
> * Állítsa be újra a site Recoveryt a virtuális gépek új forrás régiója alapján.

> [!IMPORTANT]
> Jelenleg a Recovery Services-tároló és a DR-konfiguráció nem helyezhető át egy másik régióba, ez a dokumentum végigvezeti az ügyfelet a replikáció letiltásának és az új régióban való visszaállításának folyamatán.

## <a name="prerequisites"></a>Előfeltételek

- Mielőtt az Azure-beli virtuális gépeket más régióba helyezi, távolítsa el és törölje a vész-helyreállítási konfigurációt. 

> [!NOTE]
> Ha az Azure-beli virtuális gép új célcsoportja megegyezik a vész-helyreállítási célként megadott régióval, használhatja a meglévő replikációs konfigurációját, és az [itt](azure-to-azure-tutorial-migrate.md) említett lépések alapján áthelyezheti azokat. 

- Győződjön meg arról, hogy tájékozott döntést hoz, és tájékoztatja az érintetteket arról, hogy a virtuális gép nem lesz védve a katasztrófák ellen, amíg a virtuális gép áthelyezése be nem fejeződik. 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Az Azure Site Recovery által használt erőforrások azonosítása
Azt javasoljuk, hogy ezt a lépést a következő lépés végrehajtása előtt végezze el, mivel könnyebben azonosíthatók lesznek a kapcsolódó erőforrások, miközben a virtuális gépek replikálása még folyamatban van

Minden replikált Azure-beli virtuális gép esetében navigáljon a **védett elemek** > **replikált elemek**>**tulajdonságaihoz** , és azonosítsa a következő erőforrásokat

- Célzott erőforráscsoport
- Gyorsítótárfiók
- Cél Storage-fiók (nem felügyelt lemezes Azure-beli virtuális gép esetén) 
- Célhálózat


## <a name="disable-existing-disaster-recovery-configuration"></a>Meglévő vész-helyreállítási konfiguráció letiltása

1. Navigáljon a **Recovery Services** -tárolóhoz 
2.  A **védett elemek** > **replikált elemek**területen kattintson a jobb gombbal a gépre > a **replikáció letiltása**lehetőségre.
3. Ismételje meg ezt az összes áthelyezni kívánt virtuális gép esetében.
> [!NOTE]
> a mobilitási szolgáltatás nem lesz eltávolítva a védett kiszolgálókról, manuálisan kell eltávolítania. Ha azt tervezi, hogy ismét meg szeretné tenni a kiszolgálót, akkor kihagyhatja a mobilitási szolgáltatás eltávolítását.

## <a name="delete-the-resources"></a>Az erőforrások törlése

1. Lépjen a **Recovery Services** -tárolóhoz
2. Kattintson a **Törlés** gombra
3. Folytassa az[ előző lépésben](#identify-the-resources-that-were-used-by-azure-site-recovery) azonosított összes többi erőforrás törlésével
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Azure-beli virtuális gépek áthelyezése az új célként megadott régióba

Kövesse az alábbi lépéseket az Azure-beli virtuális gépek a célcsoportba való áthelyezésének követelménye alapján.

- [Azure-beli virtuális gépek áthelyezése egy másik régióba](azure-to-azure-tutorial-migrate.md)
- [Azure-beli virtuális gépek áthelyezése rendelkezésre állási zónákba](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Site Recovery újrabeállítása a virtuális gépek új forrásoldali régiója alapján

Az új régióba áthelyezett Azure-beli virtuális gépek vész-helyreállításának konfigurálása az [itt](azure-to-azure-tutorial-enable-replication.md) említett lépések használatával
