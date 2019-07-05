---
title: Az Azure Recovery Services-tároló törlése
description: Ismerteti, hogyan lehet egy Recovery Services-tároló törlése.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: e195d9a4b9d2bbe21848e083dbccf864188e0790
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508414"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ez a cikk bemutatja, hogyan törölhet egy [Azure Backup](backup-overview.md) Recovery Services-tároló. Függőségek eltávolítása-tároló törlése, és egy tároló által kényszerített törlése vonatkozó utasításokat tartalmazza.


## <a name="before-you-start"></a>Előkészületek

A Kezdés előtt fontos megérteni, hogy rendelkezik a kiszolgálók Recovery Services-tároló nem törölhető regisztrálva, vagy, amely tárolja a biztonsági mentési adatokat.

- Szabályosan töröl egy tárolót, tartalmaz kiszolgálók regisztrációját, tároló adatok eltávolítása, és ezután törölje a tárat.
- Próbál meg törölni a tárolót, amely továbbra is függőségekkel rendelkezik, egy hibaüzenet jelenik meg és kell manuálisan távolítsa el a tároló függőségeket, többek között:
    - Biztonsági másolatba mentett elemek
    - Védett kiszolgálók
    - Felügyeleti kiszolgáló (az Azure Backup Server, a DPM) biztonsági mentési ![válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- Ha nem szeretné megőrizni a helyreállítási tárban lévő adatokat, és törli a tárolót, törölheti a tárolót kényszeríti.
- Próbálja meg törölni egy tároló, de nem tudja, ha a tároló továbbra is fogadására van konfigurálva, biztonsági mentési adatokat.


## <a name="delete-a-vault-from-the-azure-portal"></a>Tároló törlése az Azure Portalról

1. Nyissa meg a tároló irányítópultján.  
2. Az irányítópulton kattintson **törlése**. Győződjön meg arról, hogy törölni kívánja.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

Ha hibaüzenetet kap, távolítsa el [biztonsági mentési elemek](#remove-backup-items), [infrastruktúra-kiszolgálók](#remove-azure-backup-management-servers), és [helyreállítási pontok](#remove-azure-backup-agent-recovery-points), és ezután törölje a tárat.

![tároló-hiba törlése](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Az Azure Resource Manager-ügyfél használatával Recovery Services-tároló törlése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. A chocolatey telepítése [Itt](https://chocolatey.org/) és telepítéséhez ARMClient futtassa az alábbi parancsot:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Jelentkezzen be az Azure-fiókjával, és futtassa a következő parancsot:

    `ARMClient.exe login [environment name]`

3. Az Azure Portalon az előfizetés Azonosítóját és az erőforrás csoport nevét a törölni kívánt tároló összegyűjtése.

ARMClient parancsról további információkért tekintse meg ezt [dokumentum](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Recovery Services-tároló törlése az Azure Resource Manager-ügyfél használatával

1. Futtassa a következő parancsot, az előfizetés-azonosító, erőforráscsoport-nevet és a tároló neve. A parancs futtatásakor törli a tárolót, ha nincsenek függőségek.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Ha a tároló nem üres, a hibaüzenetet kapja "Tároló nem törölhető, mert nincsenek a tár meglévő erőforrásokat". Védett elemek eltávolítása / tárolón belül egy tároló tartalmazza, tegye a következőket:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Az Azure Portalon győződjön meg arról, hogy a tároló törlődik.


## <a name="remove-vault-items-and-delete-the-vault"></a>Tároló elemek eltávolítása és a tároló törlése

Távolítsa el a függőségeket, a Recovery Services-tároló törlése előtt.

### <a name="remove-backup-items"></a>Biztonsági mentési elemek eltávolítása

Ez az eljárás egy példa, amely bemutatja, hogyan távolíthatja el az Azure Files biztonsági mentési adatokat ismerteti.

1. Kattintson a **biztonsági mentési elemek** > **Azure Storage (Azure Files)**

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. Kattintson a jobb gombbal az egyes Azure Files elem eltávolítása, és kattintson a **biztonsági mentés leállítása**.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/stop-backup-item.png)


3. A **biztonsági mentés leállítása** > **válasszon egy lehetőséget**válassza **biztonsági másolatok adatainak törlése**.
4. Adja meg az elem nevét, és kattintson a **biztonsági mentés leállítása**.
   - Ez ellenőrzi, hogy szeretné-e az elem törlése.
   - A **biztonsági mentés leállítása** ellenőrzése után a gomb aktiválódik.
   - Ha megőrzi, és ne törölje az adatokat, nem tudja törölni a tárolót.

     ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Igény szerint miért érdemes az adatokat törölni adhatja meg, és adja hozzá a megjegyzéseit.
6. Győződjön meg arról, hogy a törlési feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png).
7. A feladat befejezése után a szolgáltatás egy üzenetet küld: **a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törlése**.
8. Az a lista egy elemének törlése után a **biztonsági másolati elemek** menüben kattintson a **frissítése** a tárolóban lévő elemek megtekintéséhez.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="deleting-backup-items-from-management-console"></a>A felügyeleti konzol biztonsági másolati elemek törlése

Biztonsági mentési infrastruktúra a biztonsági másolati elemeinek törléséhez keresse meg a helyszíni kiszolgáló felügyeleti konzol (MARS, az Azure Backup Server vagy SC DPM attól függően, ahol a háttérrendszer elemek védettek).

### <a name="for-mars-agent"></a>A MARS-ügynök

- Indítsa el a MARS-felügyeleti konzolt, nyissa meg a **műveletek** ablaktáblán, majd **biztonsági mentés ütemezése**.
- A **módosítása vagy egy ütemezett biztonsági mentés leállítása** varázslóban válassza ki a lehetőséget **leállítása, a biztonsági mentési ütemezés és a tárolt biztonsági másolatok törlése** kattintson **tovább**.

    ![Módosítása vagy ütemezett biztonsági mentés leállítása](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- A **egy ütemezett biztonsági mentés leállítása** varázslóban kattintson a **Befejezés**.

    ![Ütemezett biztonsági mentés leállítása](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Biztonsági PIN-kód megadását kéri. A PIN-kódot generál, hajtsa végre az alábbi lépéseket:
  - Jelentkezzen be az Azure portálra.
  - Keresse meg a **Recovery Services-tároló** > **beállítások** > **tulajdonságok**.
  - A **biztonsági PIN-kódot**, kattintson a **Generate**. Másolja a PIN-kódot. (A PIN-kódot a érvényes csak öt perc alatt)
- A felügyeleti konzol (ügyfélalkalmazás) illessze be a PIN-kódot, és kattintson a **Ok**.

  ![Biztonsági PIN-kódot](./media/backup-azure-delete-vault/security-pin.png)

- Az a **módosítsa biztonsági mentési folyamat** látni fogja varázsló *törölt biztonsági másolatok adatait 14 napig lesznek megőrizve. Ezt követően biztonsági mentési adatok véglegesen törölve lesz.*  

    ![Törölje a biztonsági mentési infrastruktúrára](./media/backup-azure-delete-vault/deleted-backup-data.png)

Most, hogy a helyszíni törölt biztonsági másolati elemek, hajtsa végre az alábbi lépéseket a portálról:
- A MARS kövesse a [távolítsa el az Azure Backup ügynök helyreállítási pontok](#remove-azure-backup-agent-recovery-points)

### <a name="for-mabs-agent"></a>MABS-ügynök

Többféle módszerrel leállítása vagy törlése az online védelem, közül bármelyik hajtsa végre az alábbi módszerek:

**1. módszer**

Indítsa el a **MABS felügyeleti** konzolon. Az a **adatvédelmi módszer kiválasztása** szakaszban nem válassza **online védelmet szeretnék**.

  ![Adatvédelmi módszer kiválasztása](./media/backup-azure-delete-vault/data-protection-method.png)

**2. módszer**

Védelmi csoport törléséhez először le kell állítania a csoport védelmét. A következő eljárás használatával állítsa le a védelmet, és a egy védelmi csoport törlésének engedélyezéséhez.

1.  A DPM felügyeleti konzolon kattintson a **védelmi** a navigációs sávon.
2.  A kijelzőpanelen válassza ki, hogy el kívánja távolítani a védelmi csoport tagja. Kattintson a jobb gombbal, válassza ki a **csoporttagok védelem leállítása** lehetőséget.
3.  Az a **védelem kikapcsolása** párbeszédpanelen jelölje ki **védett adatok törlése** > **tárhelyet törlése** jelölőnégyzetet és kattintson a **leállítása Védelem**.

    ![Tároló online törlése](./media/backup-azure-delete-vault/delete-storage-online.png)

Védett tag állapota mostantól a **elérhető inaktív replika**.

5. Kattintson a jobb gombbal az inaktív védelmi csoportot, és válassza ki **inaktív védelem eltávolítása**.

    ![Inaktív védelem eltávolítása](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Az a **inaktív védelem törlése** ablakban válassza **törlése az online tárhelyen** kattintson **Ok**.

    ![A lemezen, és online replika eltávolítása](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

Most, hogy a helyszíni törölt biztonsági másolati elemek, hajtsa végre az alábbi lépéseket a portálról:
- MABS és a DPM lépésekkel [távolítsa el az Azure Backup felügyeleti kiszolgálók](#remove-azure-backup-management-servers).


### <a name="remove-azure-backup-management-servers"></a>Távolítsa el az Azure Backup felügyeleti kiszolgálók

Azure biztonságimásolat-felügyeleti kiszolgáló eltávolítása előtt ügyeljen arra, hogy a felsorolt lépésekkel [biztonsági másolati elemek törlése a felügyeleti konzolról](#deleting-backup-items-from-management-console).

1. A tároló irányítópultos menüjében kattintson **biztonsági mentési infrastruktúra**.
2. Kattintson a **biztonságimásolat-felügyeleti kiszolgálók** kiszolgálók megjelenítése.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Kattintson jobb gombbal az elemre > **törlése**.
4. Az a **törlése** menüben írja be annak a kiszolgálónak a nevét, és kattintson a **törlése**.

     ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  Igény szerint miért érdemes az adatokat törölni adhatja meg, és adja hozzá a megjegyzéseit.

> [!NOTE]
> Ha az alábbi hiba, akkor először hajtsa végre a felsorolt lépéseket [biztonsági másolati elemek törlése a felügyeleti konzolról](#deleting-backup-items-from-management-console).
>
>![nem sikerült törölni](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Ha nem sikerül, a felügyeleti konzolról a biztonsági másolatok törléséről a lépések végrehajtásához, például a felügyeleti konzollal, a kiszolgáló elérhetetlensége miatt forduljon a Microsoft ügyfélszolgálatához.

6. Győződjön meg arról, hogy a törlési feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png).
7. A feladat befejezése után a szolgáltatás egy üzenetet küld: **a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törlése**.
8. Az a lista egy elemének törlése után a **biztonsági mentési infrastruktúra** menüben kattintson a **frissítése** a tárolóban lévő elemek megtekintéséhez.


### <a name="remove-azure-backup-agent-recovery-points"></a>Az Azure Backup ügynök helyreállítási pontok eltávolítása

Mielőtt megpróbálja eltávolítani az Azure biztonsági mentési helyreállítási pont, ügyeljen arra, hogy a felsorolt lépésekkel [biztonsági másolati elemek törlése a felügyeleti konzolról](#deleting-backup-items-from-management-console).

1. A tároló irányítópultos menüjében kattintson **biztonsági mentési infrastruktúra**.
2. Kattintson a **védett kiszolgálók** megtekintéséhez az infrastruktúra-kiszolgálókat.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Az a **védett kiszolgálók** listában, kattintson az Azure Backup szolgáltatás ügynökét.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Kattintson arra a kiszolgálóra, az Azure Backup ügynök használatával védett kiszolgálók listája.

    ![Válassza ki az adott védett kiszolgálóra](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. A kijelölt kiszolgáló irányítópultján kattintson **törlése**.

    ![a kijelölt kiszolgáló törlése](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Az a **törlése** menüben írja be annak a kiszolgálónak a nevét, és kattintson a **törlése**.

     ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Igény szerint miért érdemes az adatokat törölni adhatja meg, és adja hozzá a megjegyzéseit.

> [!NOTE]
> Ha az alábbi hiba, akkor először hajtsa végre a felsorolt lépéseket [biztonsági másolati elemek törlése a felügyeleti konzolról](#deleting-backup-items-from-management-console).
>
>![nem sikerült törölni](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Ha nem sikerül, a felügyeleti konzolról a biztonsági másolatok törléséről a lépések végrehajtásához, például a felügyeleti konzollal, a kiszolgáló elérhetetlensége miatt forduljon a Microsoft ügyfélszolgálatához. 

8. Győződjön meg arról, hogy a törlési feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png).
9. Az a lista egy elemének törlése után a **biztonsági mentési infrastruktúra** menüben kattintson a **frissítése** a tárolóban lévő elemek megtekintéséhez.


### <a name="delete-the-vault-after-removing-dependencies"></a>Függőségek eltávolítását követően a tároló törlése

1. Ha az összes függősége el lettek távolítva, görgessen a a **Essentials** ablaktábláján a tároló menüjében.
2. Győződjön meg arról, hogy nincs minden **biztonsági mentési elemek**, **biztonsági mentés a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel. Ha az elemek továbbra is megjelennek a tárolóban, távolítsa el őket.

3. Ha nincs több elem a tárolóban, a tároló irányítópultján kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Győződjön meg arról, hogy szeretné-e törölni a tárolót, kattintson a **Igen**. A tároló törlődik, és a portál visszatér a **új** szolgáltatás menüre.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Mi történik, ha a biztonsági mentés leállítása, de megőrizni az adatokat?

Ha a biztonsági mentés leállítása, de véletlenül megőrizni az adatokat, törölnie kell a biztonsági mentési adatokat a korábbi szakaszokban ismertetett módon.

## <a name="next-steps"></a>További lépések

[Ismerje meg](backup-azure-recovery-services-vault-overview.md) Recovery Services-tárolók.
