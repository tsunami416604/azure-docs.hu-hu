---
title: Helyreállítási szolgáltatások tárolóinak létrehozása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre helyreállítási szolgáltatások tárolók, amelyek a biztonsági mentések és helyreállítási pontok tárolására.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 38f23ee458845b92c04af47307a2411b26c8ac81
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529378"
---
# <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy entitás, amely tárolja a biztonsági mentések és helyreállítási pontok idővel létrehozott. A Recovery Services-tároló is tartalmazza a biztonsági mentési szabályzatok, amelyek a védett virtuális gépek társított.

Egy Recovery Services-tároló létrehozásához:

1. Jelentkezzen be az előfizetésbe az [Azure Portalon.](https://portal.azure.com/)

2. A bal oldali menüben válassza a **Minden szolgáltatás lehetőséget.**

    ![Az összes szolgáltatás kijelölése](./media/backup-create-rs-vault/click-all-services.png)

3. A **Minden szolgáltatás** párbeszédpanelen adja meg a **Helyreállítási szolgáltatások párbeszédpanelt.** Az erőforrások listája a bevitt adatok nak megfelelően szűr. Az erőforrások listájában válassza a **Helyreállítási szolgáltatások tárolói lehetőséget.**

    ![Adja meg és válassza a Helyreállítási szolgáltatások tárolóit](./media/backup-create-rs-vault/all-services.png)

    Megjelenik a Helyreállítási szolgáltatások tárolóinak listája az előfizetésben.

4. A **Recovery Services-tárolók** irányítópultján válassza a **Hozzáadás lehetőséget.**

    ![Helyreállítási szolgáltatások tárolójának hozzáadása](./media/backup-create-rs-vault/add-button-create-vault.png)

    Megnyílik **a Helyreállítási szolgáltatások tárolója** párbeszédpanel. Adja meg a **Név**, **Előfizetés**, **Erőforrás csoport**és **Hely értékét.**

    ![A Helyreállítási szolgáltatások tárolójának konfigurálása](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Név**: Adjon meg egy rövid nevet a tároló azonosításához. A névnek egyedinek kell lennie az Azure-előfizetésben. Adjon meg legalább két, de legfeljebb 50 karakterből álló nevet. A névnek betűvel kell kezdődnie, és csak betűkből, számokból és kötőjelekből állhat.
   - **Előfizetés**: Válassza ki a használni kívánt előfizetést. Ha csak egy előfizetés tagja, ezt a nevet fogja látni. Ha nem biztos abban, hogy melyik előfizetést használja, használja az alapértelmezett (javasolt) előfizetést. Több lehetőség csak akkor áll meg, ha a munkahelyi vagy iskolai fiókja egynél több Azure-előfizetéshez van társítva.
   - **Erőforráscsoport**: Használjon meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az előfizetésben elérhető erőforráscsoportok listájának megtekintéséhez válassza a **Meglévő használata**lehetőséget, majd válasszon ki egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához válassza **az Új létrehozása lehetőséget,** és írja be a nevet. Az erőforráscsoportokról az [Azure Resource Manager áttekintése című témakörben olvashat.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
   - **Hely**: Válassza ki a tároló földrajzi régióját. A virtuális gépek védelmére szolgáló tároló létrehozásához a **tárolónak** ugyanabban a régióban kell lennie, mint a virtuális gépeknek.

      > [!IMPORTANT]
      > Ha nem biztos a virtuális gép helyében, zárja be a párbeszédpanelt. Nyissa meg a portálon található virtuális gépek listáját. Ha több régióban rendelkezik virtuális gépekkel, hozzon létre egy Recovery Services-tárolót minden régióban. Hozza létre a tárolót az első helyen, mielőtt létrehozna egy másik helyhez a tárolót. Nincs szükség a biztonsági mentési adatok tárolásához tárfiókok megadására. A Recovery Services-tároló és az Azure Backup szolgáltatás leírója, amely automatikusan levan ezzel.
      >
      >

5. Ha készen áll a Helyreállítási szolgáltatások tárolójának létrehozására, válassza a **Létrehozás lehetőséget.**

    ![A Helyreállítási szolgáltatások tárolójának létrehozása](./media/backup-create-rs-vault/click-create-button.png)

    Eltarthat egy ideig a Recovery Services-tároló létrehozása. Az állapotértesítések figyelése a portál jobb felső sarkában lévő **Értesítések** területen. A tároló létrehozása után látható a Helyreállítási szolgáltatások tárolóinak listájában. Ha nem látja a trezort, válassza a **Frissítés**lehetőséget.

     ![A biztonsági másolat tárolóinak listájának frissítése](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Tárolóredundancia beállítása

Az Azure Backup automatikusan kezeli a tároló tárolására. Meg kell adnia, hogyan replikálja a tárolót.

1. A **Recovery Services-tárolók** panelen kattintson az új tárolóra. A **Beállítások csoportban** kattintson a **Tulajdonságok gombra.**
2. A **Tulajdonságok csoportBan**a **Biztonsági másolat beállítása csoportban**kattintson a **Frissítés gombra.**

3. Jelölje ki a tárolóreplikáció típusát, majd kattintson a **Mentés gombra.**

     ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Azt javasoljuk, hogy ha az Azure-t elsődleges biztonsági mentési tárolási végpontként használja, továbbra is használja az alapértelmezett **georedundáns** beállítást.
   - Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amely csökkenti az Azure Storage költségeit.
   - További információ a [földrajzi](../storage/common/storage-redundancy-grs.md) és [helyi](../storage/common/storage-redundancy-lrs.md) redundanciáról.

> [!NOTE]
> A **storage replikációtípusának** (helyileg redundáns/ georedundáns) módosítása a helyreállítási szolgáltatások tárolójában a biztonsági mentések konfigurálása előtt kell elvégezni. A biztonsági mentés konfigurálása után a módosítási lehetőség le van tiltva, és nem módosíthatja a **tárolóreplikáció típusát.**

## <a name="set-cross-region-restore"></a>Régióközi visszaállítás beállítása

A visszaállítási lehetőségek egyikeként a Régiók közötti visszaállítás (CRR) lehetővé teszi az Azure-beli virtuális gépek visszaállítását egy másodlagos régióban, amely egy [Azure-párosított régió.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) Ez az opció lehetővé teszi, hogy:

- gyakorlatokat végez, ha auditálási vagy megfelelőségi követelmény
- állítsa vissza a virtuális gép vagy a lemez, ha az elsődleges régióban katasztrófa történik.

A szolgáltatás kiválasztásához válassza a **Biztonsági másolat konfigurációja** panel en válassza a **Régiók közötti visszaállítás engedélyezése** lehetőséget.

Ehhez a folyamathoz a tárolási szinten szereplő díjszabási következményekkel jár.

>[!NOTE]
>Előkészületek:
>
>- Tekintse át a [támogatási mátrix](backup-support-matrix.md#cross-region-restore) ot a támogatott felügyelt típusok és régiók listájáért.
>- A Régiók közötti visszaállítás (CRR) szolgáltatás jelenleg csak a következő régiókban érhető el: 
>    - USA nyugati középső régiója
>    - USA nyugati régiója
>    - USA nyugati régiója, 2.
>    - USA déli középső régiója
>    - USA keleti régiója
>    - USA 2. keleti régiója
>    - USA északi középső régiója
>    - Közép-Kanada
>    - Kelet-Kanada
>    - Kelet-Ausztrália
>    - Délkelet-Ausztrália
>    - Közép-India
>    - Dél-India
>    - Nyugat-India
>    - Kelet-Japán
>    - Nyugat-Japán
>    - Délkelet-Ázsia
>    - Az Egyesült Királyság déli régiója
>    - Az Egyesült Királyság nyugati régiója
>    - Nyugat-Európa
>    - Észak-Európa
>    - Közép-Franciaország
>    - Dél-Korea középső régiója
>    - Dél-Korea déli régiója
>- A CRR egy tárolószintű opt-in funkció bármely GRS-tárolóhoz (alapértelmezés szerint ki van kapcsolva).
>- Kérjük, használja a következő parancsot az előfizetés ének beirányításához ehhez a funkcióhoz:<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- Ha nyilvános, korlátozott előzetes verzióban van betáblázva erre a funkcióra, az ellenőrzés-jóváhagyási e-mail tartalmazza az árképzési szabályzat részleteit.
>- A beiratkozás után akár 48 órát is igénybe vehet, amíg a biztonsági mentési elemek másodlagos régiókban elérhetők lesznek.
>- Jelenleg crr támogatott csak a Backup Management Type - ARM Azure VM (klasszikus Azure virtuális gép nem támogatott).  Ha további felügyeleti típusok támogatják a CRR-t, akkor **azok automatikusan** belesznek jelentkezve.

### <a name="configure-cross-region-restore"></a>Régióközi visszaállítás konfigurálása

A GRS-redundanciával létrehozott tároló tartalmazza a régiók közötti visszaállítás i. Minden GRS-trezornak lesz egy bannere, amely a dokumentációhoz kapcsolódik. A CRR konfigurálásához a tároló, nyissa meg a Biztonsági mentés konfigurációja panel, amely tartalmazza a lehetőséget, hogy ezt a funkciót.

 ![Biztonsági másolat konfigurációja szalagcím](./media/backup-azure-arm-restore-vms/banner.png)

1. A portálon nyissa meg a Recovery Services > Beállítások > tulajdonságok.
2. A funkció engedélyezéséhez kattintson a Régióközi visszaállítás engedélyezése elemre ebben a **tárolóban.**

   ![Mielőtt a tárolóban a Régiók közötti visszaállítás engedélyezése gombra kattintana](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![Miután a tárolóban a Régiók közötti visszaállítás engedélyezése gombra kattintott](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

További információ a [másodlagos régió biztonsági másolatainak megtekintéséről.](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region)

További információ [a másodlagos régió visszaállításáról.](backup-azure-arm-restore-vms.md#restore-in-secondary-region)

További információ a [másodlagos régió-visszaállítási feladatok figyelése.](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)

## <a name="modifying-default-settings"></a>Alapértelmezett beállítások módosítása

Javasoljuk, hogy a tárolóban tárolt biztonsági mentések konfigurálása előtt tekintse át a **tárolóreplikáció típusának** és **a biztonsági beállításokalapértelmezett** beállításait.

- **A storage replikációtípusa** alapértelmezés szerint **Georedundáns.** A biztonsági mentés konfigurálása után a módosítási lehetőség le van tiltva. A beállítások áttekintéséhez és módosításához kövesse az alábbi [lépéseket.](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)

- **A helyreállítható törlés** alapértelmezés szerint **engedélyezve** van az újonnan létrehozott tárolókon, hogy megvédje a biztonsági mentési adatokat a véletlen vagy rosszindulatú törlésekkel szemben. A beállítások áttekintéséhez és módosításához kövesse az alábbi [lépéseket.](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete)

## <a name="next-steps"></a>További lépések

[További információ a](backup-azure-recovery-services-vault-overview.md) Helyreállítási szolgáltatások tárolók.
[További információ a](backup-azure-delete-vault.md) A Helyreállítási szolgáltatások tárolóinak törlése.
