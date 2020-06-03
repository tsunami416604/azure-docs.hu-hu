---
title: Recovery Services-tárolók létrehozása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Recovery Services-tárolókat, amelyek a biztonsági mentéseket és a helyreállítási pontokat tárolják.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: f1f1a66bd6e0fbd28be59b0211a1ac17f030022e
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298618"
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

## <a name="set-cross-region-restore"></a>Régiók közötti visszaállítás beállítása

A visszaállítási lehetőségek egyike, a régiók közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy Azure-beli [párosított régió](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Ez a beállítás a következőket teszi lehetővé:

- a naplózási vagy megfelelőségi követelmények betartásának elvégzése
- Állítsa vissza a virtuális gépet vagy a lemezét, ha az elsődleges régióban katasztrófa következik be.

A szolgáltatás kiválasztásához válassza a **tartományok közötti visszaállítás engedélyezése** lehetőséget a **biztonsági mentési konfiguráció** panelen.

Ehhez a folyamathoz díjszabási szempontok vonatkoznak, mivel azok tárolási szinten vannak.

>[!NOTE]
>Előkészületek:
>
>- A támogatott felügyelt típusok és régiók listáját a [támogatási mátrixban](backup-support-matrix.md#cross-region-restore) tekintheti meg.
>- A régión belüli visszaállítás (CRR) szolgáltatás most már az összes Azure-beli nyilvános régióban meg van jelenítve.
>- A CRR bármely GRS-tárolóhoz engedélyezhető a tár szintjén (alapértelmezés szerint kikapcsolva).
>- A választás után akár 48 órát is igénybe vehet, hogy a biztonsági mentési elemek elérhetők legyenek a másodlagos régiókban.
>- Jelenleg a CRR csak a Backup Management Type-ARM Azure-beli virtuális gépen támogatott (a klasszikus Azure-beli virtuális gép nem lesz támogatott).  Ha a további felügyeleti típusok támogatják a CRR-t, a **rendszer automatikusan** regisztrálja őket.

### <a name="configure-cross-region-restore"></a>Régiók közötti visszaállítás konfigurálása

A GRS-redundanciával létrehozott tárolók tartalmazzák a régiók közötti visszaállítási szolgáltatás konfigurálásának lehetőségét. Minden GRS-tárolóban megjelenik egy szalagcím, amely a dokumentációra hivatkozik. A tár CRR konfigurálásához nyissa meg a biztonsági mentési konfiguráció panelt, amely a funkció engedélyezésének lehetőségét tartalmazza.

 ![Biztonsági mentési konfiguráció szalagcíme](./media/backup-azure-arm-restore-vms/banner.png)

1. A portálon lépjen a Recovery Services tároló > beállítások > Tulajdonságok elemre.
2. A funkció engedélyezéséhez kattintson a **régión belüli visszaállítás engedélyezése** ebben a tárolóban elemre.

   ![Mielőtt a tárolóban a régiók közötti visszaállítás engedélyezése lehetőségre kattint](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Miután rákattintott a régiók közötti visszaállítás engedélyezése ebben a tárolóban](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Megtudhatja, hogyan [tekintheti meg a másodlagos régióban található biztonsági másolati elemeket](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Ismerje meg, hogyan lehet [visszaállítani a másodlagos régióban](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Megtudhatja, hogyan [figyelheti a másodlagos régió visszaállítási feladatait](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## <a name="modifying-default-settings"></a>Alapértelmezett beállítások módosítása

Javasoljuk, hogy a biztonsági mentések konfigurálása előtt tekintse át a **tárolási replikálási típus** és a **biztonsági beállítások** alapértelmezett beállításait.

- Alapértelmezés szerint a **tárolási replikálás típusa** **geo-redundáns**értékre van állítva. A biztonsági mentés konfigurálása után a módosítás lehetőség le lesz tiltva. A beállítások áttekintéséhez és módosításához kövesse az alábbi [lépéseket](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) .

- Az újonnan létrehozott tárolók alapértelmezés szerint a **Soft delete** lehetőséggel védik a biztonsági mentési adatok véletlen vagy kártékony törlési **funkcióit** . A beállítások áttekintéséhez és módosításához kövesse az alábbi [lépéseket](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#enabling-and-disabling-soft-delete) .

## <a name="next-steps"></a>További lépések

[További](backup-azure-recovery-services-vault-overview.md) információ Recovery Services-tárolók.
[További](backup-azure-delete-vault.md) információ Recovery Services tárolók törlése.
