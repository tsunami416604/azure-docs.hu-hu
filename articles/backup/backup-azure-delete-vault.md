---
title: 'Az Azure Recovery Services-tároló törlése:'
description: Ez a cikk bemutatja, hogyan törölhet egy Recovery Services-tárolót. A cikk a hibaelhárítási lépések, próbálja meg törölni egy tároló, de nem tartalmaz.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 7/6/2018
ms.author: markgal
ms.openlocfilehash: e5bc68b55fdefc07b9764b8393ea6f4062468252
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816463"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése

Ez a cikk ismerteti egy Recovery Services-tároló összes elemének eltávolításához, és törölje azt. Recovery Services-tároló nem törölhető, ha egy kiszolgáló regisztrálva van, és tárolja a biztonsági mentési adatokat. Próbálja meg törölni egy tároló, de nem tudja, ha a tároló továbbra is fogadására van konfigurálva, biztonsági mentési adatokat.

Tároló törlése, tekintse meg a hogyan [tároló törlése az Azure Portalról](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal). Ha nem szeretné megőrizni a helyreállítási tárban lévő adatokat, és törli a tárolót, tekintse meg a [kényszeríti a tároló törlése](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force). Ha nem tudja, mi az a tárolóban, és győződjön meg arról, hogy törölheti a tárolót, hogy szüksége, tekintse meg a [Remove tároló függőségeket és a tároló törlése](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault).

## <a name="delete-a-vault-from-azure-portal"></a>Tároló törlése az Azure Portal használatával

Ha már rendelkezik nyissa meg a Recovery Services-tároló, hagyja ki a második lépésre.

1. Nyissa meg az Azure Portalon, és az irányítópulton nyissa meg a törölni kívánt tárolót.

   Ha nem rendelkezik a Recovery Services-tároló rögzítve az irányítópulton, a központi menüben kattintson a **minden szolgáltatás** és az erőforrások listájában írja be a **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Tárolók listája az előfizetés megtekintéséhez kattintson **Recovery Services-tárolók**.

   ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   A Recovery Services-tárolók listája megjelenik. 

   ![tároló kiválasztása a listából](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. A listában jelölje ki a törölni kívánt tárolót. Amikor kiválasztja a tárolót, megnyílik a tároló irányítópultján.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. Kattintson a tároló irányítópultjának, tároló törlése **törlése**. Győződjön meg arról, hogy szeretné-e a tároló törlése a rendszer.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    Ha a **törlési hiba tároló** jelenik meg, vagy eltávolíthatja a függőségeket a tárból, vagy a PowerShell használatával törölje a tárolót kényszeríti. Az alábbi szakaszok ismertetik, hogyan végezheti el ezeket a feladatokat.

    ![Tártörlési hiba](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>Kényszeríti a Recovery Services-tároló törlése

Kényszeríti a Recovery Services-tároló törlése a PowerShell használatával is. Kényszerített módon a Recovery Services-tárolót, és minden társított biztonsági mentési adatokat, az véglegesen törlődni fog. 

> [!Warning]
> Ha a Recovery Services-tároló törlése a PowerShell segítségével, gondolja át alaposan, hogy szeretné-e a tárolóban lévő összes biztonsági mentési adatok véglegesen törli.
>

Recovery Services-tároló törlése:

1. Jelentkezzen be Azure-fiókjába.

   Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
    Connect-AzureRmAccount
   ```
   Amikor első alkalommal használja az Azure Backup szolgáltatást, regisztrálnia kell az Azure Recovery Services-szolgáltatót az előfizetésében a [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) paranccsal.

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. Nyisson meg egy PowerShell-ablakot rendszergazdai jogosultságokkal.

1. Használat `Set-ExecutionPolicy Unrestricted` korlátozások eltávolítása.

1. Futtassa a következő parancsot az Azure Resource Manager ügyfélcsomag chocolately.org töltheti le.

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. A következő paranccsal telepítse az Azure Resource Manager API-ügyfélből.

   `choco.exe install armclient`

1. Az Azure Portalon gyűjtse össze az előfizetés-azonosító és a kapcsolódó erőforráscsoport nevét a Recovery Services-tároló törli.

1. A PowerShellben futtassa a következő parancsot, az előfizetés-azonosító, erőforráscsoport-nevet és a Recovery Services-tároló neve. A parancs futtatásakor törli, a tárolóhoz és annak összes függőségét.

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
   A tároló üresnek kell lennie, mielőtt törölheti azt. Ellenkező esetben hibaüzenet "Tároló nem törölhető, mert nincsenek a tár meglévő erőforrásokat" bejegyzése. A következő parancsot egy tárolón belül egy tároló eltávolítása mutatja be:

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```
   
1. Jelentkezzen be az előfizetés az Azure Portalon, és ellenőrizze, hogy a tároló törlődik.


## <a name="remove-vault-dependencies-and-delete-vault"></a>Távolítsa el a tároló függőségeket, és a tároló törlése

Manuálisan távolítsa el a tároló függőségeket, törölje a konfiguráció minden egyes cikk vagy a kiszolgáló és a Recovery Services-tároló között. Az alábbi eljárás lépésein végighaladva használja a **biztonsági másolati elemek** menü (lásd a kép) esetében:

* Az Azure Storage (Azure Files) biztonsági mentések
* Az SQL Server Azure virtuális gép biztonsági mentéseinek
* Azure-beli virtuális gépek biztonsági mentések
* A Microsoft Azure Recovery Services agent biztonsági mentések

Használja a **biztonsági mentési infrastruktúra** menü (lásd a kép) esetében:

* Az Azure Backup Server biztonsági mentés
* A System Center DPM biztonsági mentések

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. A tároló irányítópultos menüjében görgessen le a védett elemek szakaszban, és kattintson a **biztonsági másolati elemek**. Ebből a menüből is leállítása és törlése az Azure File Servers, SQL Server-kiszolgálók az Azure virtuális gép és az Azure-beli virtuális gépek. Ebben a példában eltávolítjuk biztonsági mentési adatokat egy Azure-fájlkiszolgálóról.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/selected-backup-items.png)

1. Válasszon ki egy biztonsági mentés az adott típusú összes elem megtekintéséhez.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. Összes elemet a listában, kattintson jobb gombbal az elemre, és a helyi menüből válassza ki a **biztonsági mentés leállítása**.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/stop-backup-item.png) 

    Megnyílik a biztonsági mentés leállítása menü.

1. A a **biztonsági mentés leállítása** menüben, a **válasszon egy lehetőséget** menüjében válassza **biztonsági mentési adatok törlése**, írja be az elem nevét, majd kattintson **biztonsági mentés leállítása**.

    Adja meg az elem nevét, ellenőrzése, hogy törölni szeretné. A **biztonsági mentés leállítása** gomb aktiválódik, miután meggyőződött arról, hogy az elemet. Ha megőrzi az adatokat, nem tudja törölni a tárolót.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Ha azt szeretné, miért érdemes az adatokat törölni adhatja meg, és adjon hozzá megjegyzéseket. Ellenőrizze a feladat befejeződött, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). <br/>
    A feladat befejezése után a szolgáltatás egy üzenetet küld: *a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törlése*.

1. Az a lista egy elemének törlése után a **biztonsági másolati elemek** menüben kattintson a **frissítése** a tárolóban lévő elemek megtekintéséhez.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

      Ha nem találhatók elemek a listában, görgessen a **Essentials** a helyreállítási tárok menüjének ablaktábláján. Nem lehetnek bármely **biztonsági mentési elemek**, **biztonsági mentés a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel. Elemek továbbra is megjelennek a tárolóban, ha harmadik lépése lépjen vissza, és válasszon egy másik elem típusa listát.  

1. Ha nem találhatók további elemek a tároló eszköztáron, kattintson az **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Győződjön meg arról, hogy szeretné-e törölni a tárolót, kattintson a **Igen**.

    A tároló törlődik, és a portál visszatér a **új** szolgáltatás menüre.

## <a name="removing-azure-backup-server-or-dpm"></a>Az Azure Backup Server vagy a DPM eltávolítása

1. A tároló irányítópultos menüjében görgessen le a kezelés szakaszhoz, és kattintson a **biztonsági mentési infrastruktúra**. 

1. Az almenü kattintson **biztonságimásolat-felügyeleti kiszolgálók** megtekintése az Azure Backup-kiszolgálók és a System Center DPM-kiszolgáló. Állítsa le, és az Azure File Servers, SQL Server-kiszolgálók az Azure virtuális gép és az Azure-beli virtuális gépek törlése. 

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. Kattintson a jobb gombbal a kívánt konfigurációelemet, törölje, és válassza ki a almenüjében **törlése**.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    Megnyílik a biztonsági mentés leállítása menü.

1. A a **biztonsági mentés leállítása** menüben, a **válasszon egy lehetőséget** menüjében válassza **biztonsági mentési adatok törlése**, írja be az elem nevét, majd kattintson **biztonsági mentés leállítása**.

    Annak ellenőrzéséhez, hogy törölni kívánja, írja be annak nevét. A **biztonsági mentés leállítása** gomb aktiválódik, miután meggyőződött arról, hogy az elemet. Ha megőrzi az adatokat, a tároló nem törölhető.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Szükség esetén egy okot miért törli az adatokat, és adjon hozzá megjegyzéseket is adja meg. Győződjön meg arról, hogy a feladat befejeződött-e, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). <br/>
    Ha a feladat befejeződött, a szolgáltatás egy üzenetet küld: a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törölve lett.

1. Miután törölt egy elemet a listában a a **biztonsági másolati elemek** menüben kattintson a **frissítése** a tárolóban lévő többi elemek megtekintéséhez.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

      Ha nem találhatók elemek a listában, görgessen a **Essentials** a helyreállítási tárok menüjének ablaktábláján. Nem lehetnek bármely **biztonsági mentési elemek**, **biztonsági mentés a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel. Elemek továbbra is megjelennek a tárolóban, ha harmadik lépése lépjen vissza, és válasszon egy másik elem típusa listát.  
1. Ha nincs több elem a tárolóban, a tároló irányítópultján kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Győződjön meg arról, hogy szeretné-e törölni a tárolót, kattintson a **Igen**.

    A tároló törlődik, és a portál visszatér a **új** szolgáltatás menüre.


## <a name="removing-azure-backup-agent-recovery-points"></a>Az Azure Backup ügynök helyreállítási pontok eltávolítása

1. A tároló irányítópultos menüjében görgessen le a kezelés szakaszhoz, és kattintson a **biztonsági mentési infrastruktúra**.

1. Az alárendelt menüjében kattintson **védett kiszolgálók** védett kiszolgáló típusok, beleértve az Azure Backup szolgáltatás ügynökének listájának megtekintéséhez.

    ![Válassza ki a tárolót az irányítópult megnyitásához](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. Az a **védett kiszolgálók** listában, kattintson az Azure Backup szolgáltatás ügynökét.

    ![Válassza ki a biztonsági mentés típusa](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    Megnyílik az Azure Backup Agent ügynök használatával védett kiszolgálók listája.

    ![Válassza ki az adott védett kiszolgálóra](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. A kiszolgálók listájában kattintson a menü megnyitásához.

    ![a kijelölt kiszolgáló irányítópultjának megtekintése](./media/backup-azure-delete-vault/selected-protected-server.png)

1. A kijelölt kiszolgáló irányítópultjának menüjében kattintson **törlése**.

    ![a kijelölt kiszolgáló törlése](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. Az a **törlése** menüben írja be az elem nevét, majd kattintson **törlése**.

    Adja meg az elem nevét, ellenőrzése, hogy törölni szeretné. A **törlése** gomb aktiválódik, miután meggyőződött arról, hogy az elemet.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    Szükség esetén egy okot miért törli az adatokat, és adjon hozzá megjegyzéseket is adja meg. Győződjön meg arról, hogy a feladat befejeződött-e, ellenőrizze az Azure-üzenetek ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/messages.png). <br/>
    Ha a feladat befejeződött, a szolgáltatás egy üzenetet küld: a biztonsági mentési folyamat le lett állítva, és a biztonsági mentési adatok törölve lett.

1. Miután törölt egy elemet a listában a a **biztonsági másolati elemek** menüben kattintson a **frissítése** a tárolóban lévő többi elemek megtekintéséhez.

      ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/empty-items-list.png)

      Ha nem találhatók elemek a listában, görgessen a **Essentials** a helyreállítási tárok menüjének ablaktábláján. Nem lehetnek bármely **biztonsági mentési elemek**, **biztonsági mentés a felügyeleti kiszolgálók**, vagy **replikált elemek** szerepel. Elemek továbbra is megjelennek a tárolóban, ha harmadik lépése lépjen vissza, és válasszon egy másik elem típusa listát.  
1. Ha nincs több elem a tárolóban, a tároló irányítópultján kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. Győződjön meg arról, hogy szeretné-e törölni a tárolót, kattintson a **Igen**.

    A tároló törlődik, és a portál visszatér a **új** szolgáltatás menüre.

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>Mi történik, ha a biztonsági mentés leállítása, de megőrizni az adatokat?

Ha viszont véletlenül állítsa le a biztonsági mentési folyamat *megőrzése* az adatokat, törölnie kell a biztonsági mentési adatokat a tároló törlése előtt. A biztonsági mentési adatok törlése:

1. Az a **biztonsági másolati elemek** menüben kattintson a jobb gombbal a cikket, és a helyi menüben kattintson a **biztonsági mentési adatok törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    A **biztonsági másolatok adatainak törlése** menü megnyitása.
1. Az a **biztonsági másolatok adatainak törlése** menüben írja be az elem nevét, majd kattintson **törlése**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Miután az adatok törlését, 4c lépésnél lépjen vissza, és folytassa a folyamatot.
