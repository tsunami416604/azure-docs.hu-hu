---
title: Az Azure Recovery Services-tároló törlése
description: Ismerteti, hogyan lehet egy Recovery Services-tároló törlése.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 01c20ce84f5c97b3a0ac437fe602861085b5052c
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827882"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ez a cikk bemutatja, hogyan törölhet egy [Azure Backup](backup-overview.md) Recovery Services-tároló. Függőségek eltávolítása, és ezután-tároló törlése vonatkozó utasításokat tartalmazza.


## <a name="before-you-start"></a>Előkészületek

Például a védett kiszolgálók vagy biztonságimásolat-felügyeleti kiszolgálókat a tárolóhoz rendelt függőségei Recovery Services-tároló nem törölhető.<br/>
(Azaz akkor is, ha rendelkezik védelem leállítása, de nem őrzi meg a biztonsági mentési adatok) nem lehet törölni a biztonsági mentési adatot tartalmazó tároló.

Ha a függőségeket tartalmazó tároló törlése, akkor a következő hiba jelenik meg:

![tároló-hiba törlése](./media/backup-azure-delete-vault/error.png)

Szabályosan törölni egy tárolót a sorrendben az alábbi lépéseket hajtsa végre:
- Védelem leállítása és biztonsági mentési adatok törlése
- Törölje a védett kiszolgálók vagy a biztonsági másolatokat kezelő kiszolgálók
- A tároló törlése


## <a name="delete-backup-data-and-backup-items"></a>Biztonsági másolatok és a biztonsági másolati elemek törlése

Mielőtt folytatja a további olvasási **[ez ](#before-you-start)** szakasz a függőségek megértéséhez, valamint a tároló törlése folyamatban.

### <a name="for-protected-items-in-cloud"></a>Védett elemek a felhőben

Állítsa le a védelmet, és a biztonsági mentési adatok törlése, hajtsa végre az alábbi:

1. A portál > helyreállítási tár > biztonsági mentési elemek válassza ki a védett elemek a felhőben.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. Kattintson a jobb gombbal, és válassza ki kell minden egyes elem esetében **biztonsági mentés leállítása**.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/stop-backup-item.png)

3. A **biztonsági mentés leállítása** > **válasszon egy lehetőséget**válassza **biztonsági másolatok adatainak törlése**.
4. Adja meg az elem nevét, és kattintson a **biztonsági mentés leállítása**.
   - Ez ellenőrzi, hogy szeretné-e az elem törlése.
   - A **biztonsági mentés leállítása** ellenőrzése után a gomb aktiválódik.
   - Ha megőrzi, és ne törölje az adatokat, nem tudja törölni a tárolót.

     ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Ellenőrizze a **értesítési** ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). A művelet befejezését követően a szolgáltatás az üzenetet jeleníti meg: **A biztonsági mentés leállítása és törlése az adatok biztonsági mentése "*biztonsági másolati elem*"** . **A művelet sikeresen befejeződött**.
6. Kattintson a **frissítése** a a **biztonsági másolati elemek** ellenőrizheti, ha a rendszer eltávolítja a biztonságimásolat-elem menü.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>A MARS-ügynök

Védelem leállítása és a biztonsági mentési adatok törlése, hajtsa végre az alábbi sorrendben:

- [1. lépés: Biztonsági másolati elemek törölni a MARS-kezelési konzolon](#step-1-delete-backup-items-from-mars-management-console)
- [2. lépés: Portálról az Azure Backup szolgáltatás ügynökének eltávolítása](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>1\. lépés: Biztonsági másolati elemek törölni a MARS-kezelési konzolon

Ha nem tudja végrehajtani ezt a lépést, a kiszolgáló elérhetetlensége miatt majd forduljon a Microsoft ügyfélszolgálatához.

- Indítsa el a MARS-felügyeleti konzolt, nyissa meg a **műveletek** ablaktáblán, majd **biztonsági mentés ütemezése**.
- A **módosítása vagy egy ütemezett biztonsági mentés leállítása** varázslóban válassza ki a lehetőséget **leállítása, a biztonsági mentési ütemezés és a tárolt biztonsági másolatok törlése** kattintson **tovább**.

    ![Módosítása vagy ütemezett biztonsági mentés leállítása](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- A **egy ütemezett biztonsági mentés leállítása** varázslóban kattintson a **Befejezés**.

    ![Ütemezett biztonsági mentés leállítása](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Biztonsági PIN-kód megadását kéri. A PIN-kódot generál, hajtsa végre az alábbi lépéseket:
  - Jelentkezzen be az Azure portálra.
  - Keresse meg a **Recovery Services-tároló** > **beállítások** > **tulajdonságok**.
  - A **biztonsági PIN-kódot**, kattintson a **Generate**. Másolja a PIN-kódot. (A PIN-kód, a érvényes csak öt perc alatt)
- A felügyeleti konzol (ügyfélalkalmazás) illessze be a PIN-kódot, és kattintson a **Ok**.

  ![Biztonsági PIN-kódot](./media/backup-azure-delete-vault/security-pin.png)

- Az a **módosítsa biztonsági mentési folyamat** látni fogja varázsló *törölt biztonsági másolatok adatait 14 napig lesznek megőrizve. Ezt követően biztonsági mentési adatok véglegesen törölve lesz.*  

    ![Törölje a biztonsági mentési infrastruktúrára](./media/backup-azure-delete-vault/deleted-backup-data.png)

Most, hogy a helyszíni törölt biztonsági másolati elemek, hajtsa végre a következő lépéseket a portálról.

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>2\. lépés: Portálról az Azure Backup szolgáltatás ügynökének eltávolítása

Győződjön meg, hogy [1. lépés](#step-1-delete-backup-items-from-mars-management-console) befejeződik, mielőtt továbblépne:

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

> [!NOTE]
> Ha az alábbi hiba, akkor először hajtsa végre a felsorolt lépéseket [biztonsági másolati elemek törlése a felügyeleti konzolról](#step-1-delete-backup-items-from-mars-management-console).
>
>![nem sikerült törölni](./media/backup-azure-delete-vault/deletion-failed.png)
>
>Ha nem sikerül, a felügyeleti konzolról a biztonsági másolatok törléséről a lépések végrehajtásához, például a felügyeleti konzollal, a kiszolgáló elérhetetlensége miatt forduljon a Microsoft ügyfélszolgálatához.

7. Ellenőrizze a **értesítési** ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). A művelet befejezését követően a szolgáltatás az üzenetet jeleníti meg: **A biztonsági mentés leállítása és törlése az adatok biztonsági mentése "*biztonsági másolati elem*"** . **A művelet sikeresen befejeződött**.
8. Kattintson a **frissítése** a a **biztonsági másolati elemek** ellenőrizheti, ha a rendszer eltávolítja a biztonságimásolat-elem menü.


### <a name="for-mabs-agent"></a>MABS-ügynök

Védelem leállítása és a biztonsági mentési adatok törlése, hajtsa végre az alábbi sorrendben:

- [1. lépés: Biztonsági másolati elemek törlése a MABS-kezelési konzolon](#step-1-delete-backup-items-from-mabs-management-console)
- [2. lépés: A portál Azure biztonságimásolat-felügyeleti kiszolgálók eltávolítása](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>1\. lépés: Biztonsági másolati elemek törlése a MABS-kezelési konzolon

Ha nem tudja végrehajtani ezt a lépést, a kiszolgáló elérhetetlensége miatt majd forduljon a Microsoft ügyfélszolgálatához.

**1. módszer** állítsa le a védelmet, és a biztonsági mentési adatok törlése, hajtsa végre az alábbi lépéseket:

1.  A DPM felügyeleti konzolon kattintson a **védelmi** a navigációs sávon.
2.  A kijelzőpanelen válassza ki, hogy el kívánja távolítani a védelmi csoport tagja. Kattintson a jobb gombbal, válassza ki a **csoporttagok védelem leállítása** lehetőséget.
3.  Az a **védelem kikapcsolása** párbeszédpanelen jelölje ki **védett adatok törlése** > **tárhelyet törlése** jelölőnégyzetet és kattintson a **leállítása Védelem**.

    ![Tároló online törlése](./media/backup-azure-delete-vault/delete-storage-online.png)

Védett tag állapota mostantól a **elérhető inaktív replika**.

5. Kattintson a jobb gombbal az inaktív védelmi csoportot, és válassza ki **inaktív védelem eltávolítása**.

    ![Inaktív védelem eltávolítása](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. Az a **inaktív védelem törlése** ablakban válassza **törlése az online tárhelyen** kattintson **Ok**.

    ![A lemezen, és online replika eltávolítása](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**2. módszer** indítsa el a **MABS felügyeleti** konzolon. Az a **adatvédelmi módszer kiválasztása** szakaszban nem válassza **online védelmet szeretnék**.

  ![Adatvédelmi módszer kiválasztása](./media/backup-azure-delete-vault/data-protection-method.png)

Most, hogy a helyszíni törölt biztonsági másolati elemek, hajtsa végre a következő lépéseket a portálról.

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>2\. lépés: A portál Azure biztonságimásolat-felügyeleti kiszolgálók eltávolítása

Győződjön meg, hogy [1. lépés](#step-1-delete-backup-items-from-mabs-management-console) befejeződik, mielőtt továbblépne:

1. A tároló irányítópultos menüjében kattintson **biztonsági mentési infrastruktúra**.
2. Kattintson a **biztonságimásolat-felügyeleti kiszolgálók** kiszolgálók megjelenítése.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Kattintson jobb gombbal az elemre > **törlése**.
4. Az a **törlése** menüben írja be annak a kiszolgálónak a nevét, és kattintson a **törlése**.

     ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> Ha az alábbi hiba, akkor először hajtsa végre a felsorolt lépéseket [biztonsági másolati elemek törlése a felügyeleti konzolról](#step-2-from-portal-remove-azure-backup-management-servers).
>
>![nem sikerült törölni](./media/backup-azure-delete-vault/deletion-failed.png)
>
> Ha nem sikerül, a felügyeleti konzolról a biztonsági másolatok törléséről a lépések végrehajtásához, például a felügyeleti konzollal, a kiszolgáló elérhetetlensége miatt forduljon a Microsoft ügyfélszolgálatához.

5. Ellenőrizze a **értesítési** ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). A művelet befejezését követően a szolgáltatás az üzenetet jeleníti meg: **A biztonsági mentés leállítása és törlése az adatok biztonsági mentése "*biztonsági másolati elem*"** . **A művelet sikeresen befejeződött**.
6. Kattintson a **frissítése** a a **biztonsági másolati elemek** ellenőrizheti, ha a rendszer eltávolítja a biztonságimásolat-elem menü.


## <a name="delete-the-recovery-services-vault"></a>A helyreállítási tár törlése

1. Ha az összes függősége el lettek távolítva, görgessen a a **Essentials** ablaktábláján a tároló menüjében.
2. Győződjön meg arról, hogy nincs minden **biztonsági mentési elemek**, **biztonsági mentés a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel. Ha az elemek továbbra is megjelennek a tárolóhoz, [távolítsa el őket](#delete-backup-data-and-backup-items).

3. Ha nincs több elem a tárolóban, a tároló irányítópultján kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Győződjön meg arról, hogy szeretné-e törölni a tárolót, kattintson a **Igen**. A tároló törlődik, és a portál visszatér a **új** szolgáltatás menüre.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Az Azure Resource Manager-ügyfél használatával Recovery Services-tároló törlése

Ezt a beállítást, a Recovery Services-tároló törlése esetén az összes függőséget el lesznek távolítva, és Ön továbbra is első csak ajánlott a *törlési hiba tároló*.



- Az a **Essentials** a tároló menüjében ablaktáblán győződjön meg arról, hogy nincs minden **biztonsági mentési elemek**, **biztonsági mentés a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel. Ha biztonsági mentési elem, majd hajtsa végre a lépéseket [törölni a biztonsági másolatok és a biztonsági másolati elemek](#delete-backup-data-and-backup-items).
- Ismételje meg [törli a tárolót a portálról](#delete-the-recovery-services-vault).
- Ha a függőségeket törlődnek, és továbbra is kihozhatják a *tároló-törlési hiba* ARMClient eszköz használatával; az alábbi lépésekkel

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
2. Ha a tároló nem üres, akkor hibaüzenetet fog kapni a "Tár nem törölhető, mert a tár meglévő erőforrásokat". Védett elemek eltávolítása / tárolón belül egy tároló tartalmazza, tegye a következőket:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Az Azure Portalon győződjön meg arról, hogy a tároló törlődik.


## <a name="next-steps"></a>További lépések

[Ismerje meg](backup-azure-recovery-services-vault-overview.md) Recovery Services-tárolók.<br/>
[Ismerje meg](backup-azure-manage-windows-server.md) Recovery Services-tárolók figyelése és kezelése.
