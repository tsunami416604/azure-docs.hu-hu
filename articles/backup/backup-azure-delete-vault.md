---
title: Az Azure Recovery Services-tároló törlése
description: Ismerteti, hogyan lehet egy Recovery Services-tároló törlése.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: raynew
ms.openlocfilehash: 1cc86470b9e45469d633d47121869b3c2dc1b052
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439005"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ez a cikk bemutatja, hogyan törölhet egy [Azure Backup](backup-overview.md) Recovery Services-tároló. Függőségek eltávolítása-tároló törlése, és egy tároló által kényszerített törlése vonatkozó utasításokat tartalmazza.




## <a name="before-you-start"></a>Előkészületek

A Kezdés előtt fontos megérteni, hogy rendelkezik a kiszolgálók Recovery Services-tároló nem törölhető regisztrálva, vagy, amely tárolja a biztonsági mentési adatokat.


- Tároló szabályosan törlése, a kiszolgálók regisztrációját, és tároló adatok eltávolítása.
- Ha nem szeretné megőrizni a helyreállítási tárban lévő adatokat, és törli a tárolót, törölheti a tárolót kényszeríti.
- Próbálja meg törölni egy tároló, de nem tudja, ha a tároló továbbra is fogadására van konfigurálva, biztonsági mentési adatokat.

Tároló törlése, tekintse meg a hogyan [tároló törlése az Azure Portalról](#delete-a-vault-from-the-azure-portal). Ha a szakaszban [kényszeríti a tároló törlése](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Ha nem tudja, mi az a tárolóban, és győződjön meg arról, hogy törölheti a tárolót, hogy szüksége, tekintse meg a [Remove tároló függőségeket és a tároló törlése](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-the-azure-portal"></a>Tároló törlése az Azure Portalról

1. Nyissa meg a portálon a Recovery Services-tárolók listája.
2. A listában jelölje ki a törölni kívánt tárolót. A tároló irányítópultjának megnyitása.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. A tároló irányítópultján kattintson **törlése**. Győződjön meg arról, hogy törölni kívánja.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

2. Ha tároló függőségek, a **törlési hiba tároló** jelenik meg: 

    ![Tártörlési hiba](./media/backup-azure-delete-vault/vault-delete-error.png)

    - Kövesse ezeket az utasításokat a tároló törlése előtt távolítsa el a függőségeket, tekintse át
    - [Az alábbi lépéseket követve](#delete-the-recovery-services-vault-by-force) kényszeríti a tároló törlése a PowerShell használatával. 

## <a name="delete-the-recovery-services-vault-by-force"></a>Kényszeríti a Recovery Services-tároló törlése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kényszeríti a Recovery Services-tároló törlése a PowerShell használatával is. Ez azt jelenti, hogy a tárolót, és minden ahhoz kapcsolódó biztonsági mentési adat véglegesen törlődik. 

> [!Warning]
> Recovery Services-tároló törlése a PowerShell segítségével, győződjön meg arról, hogy szeretné-e a tárolóban lévő összes biztonsági mentési adatok véglegesen törli.
>

Recovery Services-tároló törlése:

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` parancsot, és kövesse a képernyőn megjelenő utasításokat.

   ```powershell
    Connect-AzAccount
   ```
2. Az első alkalommal használja az Azure Backup, az Azure Recovery Services-szolgáltatót regisztrálnia kell az előfizetésében lévő [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider).

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. Nyisson meg egy PowerShell-ablakot rendszergazdai jogosultságokkal.
4. Használat `Set-ExecutionPolicy Unrestricted` korlátozások eltávolítása.
5. Futtassa a következő parancsot az Azure Resource Manager ügyfélcsomag chocolately.org töltheti le.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. A következő paranccsal telepítse az Azure Resource Manager API-ügyfélből.

   `choco.exe install armclient`

7. Az Azure Portalon az előfizetés Azonosítóját és az erőforrás csoport nevét a törölni kívánt tároló összegyűjtése.
8. A PowerShellben futtassa a következő parancsot, az előfizetés-azonosító, erőforráscsoport-nevet és a tároló neve. A parancs futtatásakor törli, a tárolóhoz és annak összes függőségét.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. Ha a tároló nem üres, a hibaüzenetet kapja "Tároló nem törölhető, mert nincsenek a tár meglévő erőforrásokat". Egy tároló tárolójában eltávolításához tegye a következőket:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. Jelentkezzen be az előfizetés az Azure Portalon, és győződjön meg arról, hogy a tároló törlődik.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Távolítsa el a tároló függőségeket, és a tároló törlése

A tároló függőségeket, a következő manuális eltávolítása:

- Az a **biztonsági másolati elemek** menü, remove-függőségek:
    - Az Azure Storage (Azure Files) biztonsági mentések
    - Az SQL Server Azure virtuális gép biztonsági mentéseinek
    - Azure-beli virtuális gépek biztonsági mentések
- Az a **biztonsági mentési infrastruktúra** menü, remove-függőségek:
    - A Microsoft Azure Backup Server (MABS) biztonsági mentések
    - A System Center DPM biztonsági mentések

![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

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


### <a name="remove-backup-infrastructure-servers"></a>Távolítsa el a biztonsági mentési infrastruktúra-kiszolgálók

1. A tároló irányítópultos menüjében kattintson **biztonsági mentési infrastruktúra**.
2. Kattintson a **biztonságimásolat-felügyeleti kiszolgálók** kiszolgálók megjelenítése. 

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. Kattintson jobb gombbal az elemre > **törlése**.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . A **biztonsági mentés leállítása** > **válasszon egy lehetőséget**válassza **biztonsági másolatok adatainak törlése**.
4. Adja meg az elem nevét, és kattintson a **biztonsági mentés leállítása**. 
   - Ez ellenőrzi, hogy szeretné-e az elem törlése.
   - A **biztonsági mentés leállítása** ellenőrzése után a gomb aktiválódik.
   - Ha megőrzi, és ne törölje az adatokat, nem tudja törölni a tárolót.

     ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Igény szerint miért érdemes az adatokat törölni adhatja meg, és adja hozzá a megjegyzéseit.
6. Győződjön meg arról, hogy a törlési feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png).
7. A feladat befejezése után a szolgáltatás egy üzenetet küld: **a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törlése**.
8. Az a lista egy elemének törlése után a **biztonsági másolati elemek** menüben kattintson a **frissítése** a tárolóban lévő elemek megtekintéséhez.


### <a name="remove-azure-backup-agent-recovery-points"></a>Az Azure Backup ügynök helyreállítási pontok eltávolítása

1. A tároló irányítópultos menüjében kattintson **biztonsági mentési infrastruktúra**.
2. Kattintson a **biztonságimásolat-felügyeleti kiszolgálók** megtekintéséhez az infrastruktúra-kiszolgálókat.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Az a **védett kiszolgálók** listában, kattintson az Azure Backup szolgáltatás ügynökét.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. Kattintson arra a kiszolgálóra, az Azure Backup ügynök használatával védett kiszolgálók listája.

    ![Válassza ki az adott védett kiszolgálóra](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. A kijelölt kiszolgáló irányítópultján kattintson **törlése**.

    ![a kijelölt kiszolgáló törlése](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Az a **törlése** menüben írja be az elem nevét, majd kattintson **törlése**.
   - Ez ellenőrzi, hogy szeretné-e az elem törlése.
   - A **biztonsági mentés leállítása** ellenőrzése után a gomb aktiválódik.
   - Ha megőrzi, és ne törölje az adatokat, nem tudja törölni a tárolót.

     ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. Igény szerint miért érdemes az adatokat törölni adhatja meg, és adja hozzá a megjegyzéseit.
8. Győződjön meg arról, hogy a törlési feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png).
7. A feladat befejezése után a szolgáltatás egy üzenetet küld: **a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törlése**.
8. Az a lista egy elemének törlése után a **biztonsági másolati elemek** menüben kattintson a **frissítése** a tárolóban lévő elemek megtekintéséhez.



### <a name="delete-the-vault-after-removing-dependencies"></a>Függőségek eltávolítását követően a tároló törlése

1. Ha az összes függősége el lettek távolítva, görgessen a a **Essentials** ablaktábláján a tároló menüjében.

    - Nem lehetnek bármely **biztonsági mentési elemek**, **biztonsági mentés a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel.
    - Ha az elemek továbbra is megjelennek a tárolóban, távolítsa el őket.

2. Ha nincs több elem a tárolóban, a tároló irányítópultján kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Győződjön meg arról, hogy szeretné-e törölni a tárolót, kattintson a **Igen**. A tároló törlődik, és a portál visszatér a **új** szolgáltatás menüre.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Mi történik, ha a biztonsági mentés leállítása, de megőrizni az adatokat?

Ha a biztonsági mentés leállítása, de véletlenül megőrizni az adatokat, törölnie kell a biztonsági mentési adatokat a korábbi szakaszokban ismertetett módon.

## <a name="next-steps"></a>További lépések

[Ismerje meg](backup-azure-recovery-services-vault-overview.md) Recovery Services-tárolók.
