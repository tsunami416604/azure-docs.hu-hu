---
title: Recovery Services-tárolók létrehozása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Recovery Services-tárolókat, amelyek a biztonsági mentéseket és a helyreállítási pontokat tárolják.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 144d8cdb870e12474dfc47784749b5f0e466f8bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273397"
---
# <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy olyan entitás, amely az idő múlásával létrehozott biztonsági mentéseket és helyreállítási pontokat tárolja. A Recovery Services-tároló a védett virtuális gépekhez társított biztonsági mentési házirendeket is tartalmazza.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az előfizetésbe a [Azure Portalban](https://portal.azure.com/).

2. A bal oldali menüben válassza a **minden szolgáltatás**lehetőséget.

    ![Minden szolgáltatás kiválasztása](./media/backup-create-rs-vault/click-all-services.png)

3. A **minden szolgáltatás** párbeszédpanelen írja be a **Recovery Services**értéket. Az erőforrás-szűrők listája a bemenet alapján. Az erőforrások listájában válassza a **Recovery Services**-tárolók lehetőséget.

    ![Adja meg és válassza ki Recovery Services tárolókat](./media/backup-create-rs-vault/all-services.png)

    Megjelenik az előfizetésben található Recovery Services-tárolók listája.

4. A **Recovery Services** -tárolók irányítópultján válassza a **Hozzáadás**lehetőséget.

    ![Recovery Services-tároló hozzáadása](./media/backup-create-rs-vault/add-button-create-vault.png)

    Megnyílik az **Recovery Services** -tároló párbeszédpanel. Adja meg a **név**, az **előfizetés**, az **erőforráscsoport**és a **hely**értékét.

    ![A Recovery Services-tároló konfigurálása](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Név**: adjon meg egy rövid nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetésben. Adjon meg legalább két, de legfeljebb 50 karakterből álló nevet. A névnek betűvel kell kezdődnie, és csak betűt, számot és kötőjelet tartalmazhat.
   - **Előfizetés**: válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, akkor ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (javasolt) előfizetést. Több választási lehetőség is van, ha a munkahelyi vagy iskolai fiók egynél több Azure-előfizetéshez van társítva.
   - **Erőforráscsoport**: használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza az **új létrehozása** lehetőséget, és adja meg a nevet. Az erőforráscsoportok részletes ismertetését itt tekintheti meg: [Azure Resource Manager Overview (áttekintés](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)).
   - **Hely**: válassza ki a tároló földrajzi régióját. A virtuális gépeket védő tároló létrehozásához a tárolónak ugyanabban a régióban **kell lennie** , mint a virtuális gépeknek.

      > [!IMPORTANT]
      > Ha nem biztos benne, hogy a virtuális gép hol található, akkor zárjuk be a párbeszédpanelt. A portálon nyissa meg a virtuális gépek listáját. Ha több régióban rendelkezik virtuális gépekkel, minden régióban hozzon létre egy Recovery Services-tárolót. Hozza létre a tárolót az első helyen, mielőtt létrehozza a tárolót egy másik helyhez. Nem kell megadnia a tárolási fiókokat a biztonsági másolatok tárolásához. A Recovery Services-tároló és a Azure Backup szolgáltatás automatikus kezelése.
      >
      >

5. Ha készen áll az Recovery Services-tároló létrehozására, válassza a **Létrehozás**lehetőséget.

    ![A Recovery Services-tároló létrehozása](./media/backup-create-rs-vault/click-create-button.png)

    A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az **értesítéseket** a portál jobb felső sarkában található értesítések területről. A tároló létrehozása után a Recovery Services-tárolók listájában látható. Ha nem látja a tárolót, válassza a **frissítés**lehetőséget.

     ![A Backup-tárolók listájának frissítése](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Tárolási redundancia beállítása

A Azure Backup automatikusan kezeli a tároló tárterületét. Meg kell adnia a tárterület replikálásának módját.

1. A **Recovery Services-tárolók** panelen kattintson az új tárolóra. A **Beállítások** szakaszban kattintson a **Tulajdonságok**elemre.
2. A **Tulajdonságok**alatt a **biztonsági mentés konfigurálása**területen kattintson a **frissítés**elemre.

3. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés**gombra.

     ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Javasoljuk, hogy ha az Azure-t elsődleges biztonsági mentési tárolási végpontként használja, folytassa az alapértelmezett **geo-redundáns** beállítás használatát.
   - Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit.
   - További információ a [földrajzi](../storage/common/storage-redundancy-grs.md) és [helyi](../storage/common/storage-redundancy-lrs.md) redundanciáról.

> [!NOTE]
> A **Storage replikálási típusának** (helyileg redundáns/geo-redundáns) módosítását a helyreállítási tár számára a biztonsági mentések a tárolóban való konfigurálása előtt kell elvégezni. A biztonsági mentés konfigurálása után a módosítás lehetőség le van tiltva, és nem módosíthatja a **tárolási replikálás típusát**.

## <a name="modifying-default-settings"></a>Alapértelmezett beállítások módosítása

Javasoljuk, hogy a biztonsági mentések konfigurálása előtt tekintse át a **tárolási replikálási típus** és a **biztonsági beállítások** alapértelmezett beállításait. 
* Alapértelmezés szerint a **tárolási replikálás típusa** **geo-redundáns**értékre van állítva. A biztonsági mentés konfigurálása után a módosítás lehetőség le lesz tiltva. A beállítások áttekintéséhez és módosításához kövesse az alábbi [lépéseket](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) . 
* Az újonnan létrehozott tárolók alapértelmezés szerint a **Soft delete** lehetőséggel védik a biztonsági mentési adatok véletlen vagy kártékony törlési **funkcióit** . A beállítások áttekintéséhez és módosításához kövesse az alábbi [lépéseket](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) .


## <a name="next-steps"></a>Következő lépések

[További](backup-azure-recovery-services-vault-overview.md) információ Recovery Services-tárolók.
[További](backup-azure-delete-vault.md) információ Recovery Services tárolók törlése.
