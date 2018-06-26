---
title: Az Azure Recovery Services-tároló törlése "
description: Ez a cikk azt ismerteti, hogyan Recovery Services-tároló törlése. A cikk a hibaelhárítási lépések, próbálja meg törölni a tárolót, de nem tartalmaz.
services: service-name
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 6/21/2018
ms.author: markgal
ms.openlocfilehash: d8169eba6790e49a85d69434663faabe7430942e
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937600"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ez a cikk ismerteti az összes elem eltávolítása a Recovery Services-tároló, és törölje azt. Recovery Services-tároló nem törölhető, ha a kiszolgáló regisztrálva van, és tárolja a biztonsági mentési adatokat. Ha törli a tárolót próbálja, de nem, a tároló továbbra is fogadására van beállítva biztonsági mentési adatokat.

Megtudhatja, hogyan törölheti a tárolókat, tekintse meg a [tároló törlése az Azure-portálon](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Ha nem szeretné megőrizni a Recovery Services-tároló megadott adattal sem, és szeretné, hogy törli a tárolót, tekintse meg a [törli a tárolót által kényszerített](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Ha nem biztos a tárolóban, és meg kell győződnie arról, hogy a tároló törlése, tekintse meg a [eltávolítása tároló függőségeket és delete tároló](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Tároló törlése az Azure-portálon

Ha már rendelkezik nyissa meg a Recovery Services-tároló, ugorjon a második lépésre.

1. Nyissa meg az Azure-portálon, és az irányítópultról nyissa meg a törölni kívánt tárolót.

   Ha még nem rendelkezik a Recovery Services-tároló rögzítve az irányítópulton, a központ menüben kattintson a **minden szolgáltatás** írja be az erőforrások listájához, **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Az előfizetés tárolók listájának megtekintéséhez kattintson **Recovery Services-tárolók**.

   ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   A Recovery Services-tárolók listája jelenik meg. 

   ![tároló válasszon a listából](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

2. A listában jelölje ki a törölni kívánt tárolót. A tároló kiválasztásakor a tároló irányítópult megnyitása.

    ![Válassza ki a tároló az irányítópult megnyitása](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

3. Törli a tárolót, a tároló irányítópulton kattintson **törlése**. Győződjön meg arról, hogy szeretné-e a tárolót fog kérni.

    ![Válassza ki a tároló az irányítópult megnyitása](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Ha a **fióktörlési hiba tároló** megjelenik, távolítson el a függőségeket a tárolóból, vagy a PowerShell segítségével kényszeríti a tárolót. Az alábbi szakaszok azt ismertetik, hogyan hajthat végre ezeket a feladatokat.

    ![Tároló törlése hiba](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Kényszeríti a Recovery Services-tároló törlése

PowerShell segítségével által kényszerített Recovery Services-tároló törlése. Kényszerített módon a Recovery Services-tároló, és minden kapcsolódó biztonsági mentési adatokat, az véglegesen törlődni fog. 

> [!Warning]
> Ha a PowerShell használatával a Recovery Services-tároló törlése, róla, hogy véglegesen törli az összes biztonsági mentési adatok a tárolóban lévő állapottal szeretne.
>

Recovery Services-tároló törlése:

1. Jelentkezzen be az Azure-fiókjával.

   Jelentkezzen be Azure előfizetés a `Connect-AzureRmAccount` parancsot, és kövesse a képernyőn megjelenő utasításokat.

   ```powershell
    Connect-AzureRmAccount
   ```
   Amikor első alkalommal használja az Azure Backup szolgáltatást, regisztrálnia kell az Azure Recovery Services-szolgáltatót az előfizetésében a [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) paranccsal.

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

2. Nyisson meg egy PowerShell-ablakot rendszergazdai jogosultságokkal.

3. Használjon `Set-ExecutionPolicy Unrestricted` korlátozások eltávolítása.

4. A következő parancsot az Azure Resource Manager ügyfélcsomag chocolately.org töltheti le.

    `iex ((New-Object System.Net.WebClient) DownloadString('https://chocolatey.org/install.ps1))`

5. A következő paranccsal telepítse az Azure Resource Manager API-ügyfél.

   `choco.exe install armclient`

6. Az Azure portálon gyűjtse össze az előfizetés-azonosító és a Recovery Services-tároló törli a kapcsolódó az erőforráscsoport neve.

7. A PowerShell a következő parancsot az előfizetés-azonosító, a erőforráscsoport-név és a Recovery Services-tároló neve. A parancs futtatásakor törli a tárolóba és az összes függősége.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
8. Jelentkezzen be az előfizetés az Azure portálon, és ellenőrizze, hogy a tároló törlése.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Távolítsa el a tároló függőségeket és -tároló törlése

Manuálisan távolítsa el a tároló függőségeket, a konfigurációs közötti minden cikk vagy a kiszolgáló és a Recovery Services-tároló törlése Végrehajtása közben a következő eljárás, használja a **biztonsági mentés elemek** menü (lásd a kép) esetében:

* Azure Storage (Azure-fájlok) biztonsági mentések
* SQL Server Azure virtuális gép biztonsági mentései
* Az Azure virtuális gépek biztonsági mentések
* A Microsoft Azure Recovery Services agent biztonsági mentések

Használja a **biztonsági infrastruktúra** menü (lásd a kép) esetében:

* Az Azure Backup Server biztonsági mentése
* A System Center DPM biztonsági mentések

    ![Válassza ki a tároló az irányítópult megnyitása](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. A tároló irányítópult menü, görgessen le a védett elemek szakaszt, és kattintson a **biztonsági mentés elemek**. Az ebben a menüben állítsa le, és Azure fájlkiszolgálók, SQL Server Azure virtuális Gépen, és az Azure virtuális gépek törlése. Ebben a példában eltávolítjuk biztonsági mentési adatokat egy Azure fájlkiszolgálóról.

    ![Válassza ki a tároló az irányítópult megnyitása](./media/backup-azure-delete-vault/selected-backup-items.png)

2. Válassza ki a biztonsági mentés típusa az adott típusú összes elem megtekintéséhez.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. Az összes elemet a listában, kattintson a jobb gombbal az elemet, és a helyi menüből válassza ki a **Stop biztonsági mentés**.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/stop-backup-item.png) 

    A biztonsági mentés leállítása menü megnyitása.

4. A a **biztonsági mentés leállítása** menü, az a **válasszon egy lehetőséget** menüjében válassza **biztonsági mentési adatok törlése**, írja be az elem nevét, majd kattintson **Stop biztonsági mentés**.

    Írja be a következő elem ellenőrzése a törölni kívánt nevét. A **állítsa le a biztonsági mentés** gomb akkor aktiválódik, miután ellenőrizte, hogy a cikk. Ha megőrzi az adatokat, nem fogja tudni törli a tárolót.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Ha azt szeretné, miért törölni az adatok okot, és megjegyzéseket hozzáadni. Ellenőrizze a feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). <br/>
    Ha a feladat befejeződik, a szolgáltatás egy üzenetet küld: *a biztonsági mentési folyamat le lett állítva, a biztonsági mentési adatok törölve*.

5. A lista egy elemére a törlése után a **biztonsági mentés elemek** menüben kattintson **frissítése** az elemeket a tárolóban lévő állapottal.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

      Ha nincsenek elemek a listában, görgessen a **Essentials** a Recovery Services-tároló menü ablaktáblán. Nincs nem lehet bármely **elem biztonsági mentését**, **biztonsági mentése a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel a listában. Ha elemek még mindig a tárolóban, három lépés lépjen vissza, és válasszon egy másik elem típusa listát.  

6. Ha nincsenek további elemek tároló eszköztárán, kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. Győződjön meg arról, hogy szeretné-e a tárolót, kattintson a **Igen**.

    A tároló törlődik, és visszaadja a portál a **új** szolgáltatás menü.

## <a name="removing-azure-backup-server-or-dpm"></a>Azure biztonságimásolat-kiszolgáló vagy a DPM eltávolítása

1. A tároló irányítópult menü, görgessen le a kezelés szakaszt, és kattintson a **biztonsági infrastruktúra**. 

2. Kattintson az almenü **biztonságimásolat-felügyeleti kiszolgálók** megtekintéséhez az Azure biztonsági mentés kiszolgálók és a System Center DPM-kiszolgáló. Állítsa le, és törölje az Azure File Servers, SQL Server Azure virtuális Gépen, és az Azure virtuális gépek. 

    ![Válassza ki a tároló az irányítópult megnyitása](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Kattintson a jobb gombbal a törölni, és az almenü, válassza a kívánt elemet **törlése**.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    A biztonsági mentés leállítása menü megnyitása.

4. A a **biztonsági mentés leállítása** menü, az a **válasszon egy lehetőséget** menüjében válassza **biztonsági mentési adatok törlése**, írja be az elem nevét, majd kattintson **Stop biztonsági mentés**.

    Annak ellenőrzéséhez, hogy törölni kívánja, írja be annak nevét. A **állítsa le a biztonsági mentés** gomb akkor aktiválódik, miután ellenőrizte, hogy a cikk. Ha megőrzi az adatokat, a tároló nem törölhető.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Igény szerint is adja meg a okát, miért törölni az adatokat, majd adja meg megjegyzéseit. Győződjön meg arról, hogy a feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). <br/>
    Ha a feladat befejeződött, a szolgáltatás egy üzenetet küld: a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törölve lett.

5. A lista egy elemére a törlése után a **biztonsági mentés elemek** menüben kattintson **frissítése** a fennmaradó elemeket a tárolóban lévő állapottal.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

      Ha nincsenek elemek a listában, görgessen a **Essentials** a Recovery Services-tároló menü ablaktáblán. Nincs nem lehet bármely **elem biztonsági mentését**, **biztonsági mentése a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel a listában. Ha elemek még mindig a tárolóban, három lépés lépjen vissza, és válasszon egy másik elem típusa listát.  
6. Ha nincsenek elemeket nem a tárolóban, a tároló irányítópulton kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

7. Győződjön meg arról, hogy szeretné-e a tárolót, kattintson a **Igen**.

    A tároló törlődik, és visszaadja a portál a **új** szolgáltatás menü.


## <a name="removing-azure-backup-agent-recovery-points"></a>Azure Backup agent helyreállítási pontok eltávolítása

1. A tároló irányítópult menü, görgessen le a kezelés szakaszt, és kattintson a **biztonsági infrastruktúra**.

2. Kattintson az almenü **védett kiszolgálók** védett kiszolgálótípusok, beleértve az Azure Backup szolgáltatás ügynökének listájának megtekintése.

    ![Válassza ki a tároló az irányítópult megnyitása](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. Az a **védett kiszolgálók** listában, kattintson az Azure Backup szolgáltatás ügynöke.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Megnyitja az Azure Backup Agent ügynök használatával védett kiszolgálók listájához.

    ![Jelölje ki az adott védett kiszolgálót](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

4. A kiszolgálók listájának megtekintéséhez kattintson a menü megnyitásához.

    ![a kijelölt kiszolgáló irányítópult megtekintése](./media/backup-azure-delete-vault/selected-protected-server.png)

5. A kijelölt kiszolgáló irányítópult menüjének **törlése**.

    ![a kijelölt kiszolgáló törlése](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. Az a **törlése** menüben írja be az elem nevét, és kattintson a **törlése**.

    Írja be a következő elem ellenőrzése a törölni kívánt nevét. A **törlése** gomb akkor aktiválódik, miután ellenőrizte, hogy a cikk.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Igény szerint is adja meg a okát, miért törölni az adatokat, majd adja meg megjegyzéseit. Győződjön meg arról, hogy a feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). <br/>
    Ha a feladat befejeződött, a szolgáltatás egy üzenetet küld: a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törölve lett.

7. A lista egy elemére a törlése után a **biztonsági mentés elemek** menüben kattintson **frissítése** a fennmaradó elemeket a tárolóban lévő állapottal.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

      Ha nincsenek elemek a listában, görgessen a **Essentials** a Recovery Services-tároló menü ablaktáblán. Nincs nem lehet bármely **elem biztonsági mentését**, **biztonsági mentése a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel a listában. Ha elemek még mindig a tárolóban, három lépés lépjen vissza, és válasszon egy másik elem típusa listát.  
8. Ha nincsenek elemeket nem a tárolóban, a tároló irányítópulton kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

9. Győződjön meg arról, hogy szeretné-e a tárolót, kattintson a **Igen**.

    A tároló törlődik, és visszaadja a portál a **új** szolgáltatás menü.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Mi történik, ha a biztonsági mentési folyamat leállítása, de megőrizni az adatokat?

Ha azonban véletlenül állítsa le a biztonsági mentési folyamat *megőrzése* az adatokat, törölnie kell a biztonsági mentési adatok a tároló törlése előtt. A biztonsági mentési adatok törlése:

1. Az a **biztonsági másolati elemei** menüben kattintson a jobb gombbal az elemet, és a helyi menüben kattintson a **biztonsági mentési adatok törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    A **biztonságimásolat-adatok törlése** menü megnyitása.
2. Az a **biztonságimásolat-adatok törlése** menüben írja be az elem nevét, és kattintson a **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Ha az adatok törlését, 4c lépésnél lépjen vissza, és folytathatja a műveletet.
