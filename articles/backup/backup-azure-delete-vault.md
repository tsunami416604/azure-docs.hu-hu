---
title: Microsoft Azure Recovery Services-tároló törlése
description: Ebből a cikkből megtudhatja, hogyan távolíthatja el a függőségeket, majd törölheti az Azure Backup Recovery Services-tárolót.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 69fed6c53914ed7aa16b04b5311ec69966734f25
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025135"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure biztonsági mentéshelyreállítási szolgáltatások tárolójának törlése

Ez a cikk ismerteti, hogyan törölheti az [Azure Backup](backup-overview.md) Recovery Services-tároló. Utasításokat tartalmaz a függőségek eltávolítására és a tároló törlésére.

## <a name="before-you-start"></a>Előkészületek

A recovery services-tárolók nem törölhetők, amelyek hez függőségek , például védett kiszolgálók vagy biztonsági mentéskezelő kiszolgálók tartoznak.

- A biztonsági mentési adatokat tartalmazó tárolók nem törölhetők (még akkor sem, ha leállította a védelmet, de megtartotta a biztonsági mentési adatokat).

- Ha függőségeket tartalmazó tárolót töröl, a következő üzenet jelenik meg:

  ![Törölje a tároló hibát.](./media/backup-azure-delete-vault/error.png)

- Ha töröl egy helyszíni védett elemet egy függőségeket tartalmazó portálról, egy figyelmeztető üzenet jelenik meg:

  ![Törölje a védett kiszolgálóhibát.](./media/backup-azure-delete-vault/error-message.jpg)

- Ha a biztonsági másolat elemei helyreállíthatóállapotban vannak, megjelenik a figyelmeztető üzenet alatt, és meg kell várnia, amíg véglegesen törlődnek. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

   ![Törölje a tároló hibát.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- A regisztrált tárfiókkal rendelkező tárolók nem törölhetők. A fiók regisztrációjának megszüntetése című témakörben olvashat: [Unregister aan](manage-afs-backup.md#unregister-a-storage-account)storage account .
  
A tároló törléséhez válassza ki a beállításnak megfelelő forgatókönyvet, és kövesse az ajánlott lépéseket:

Forgatókönyv | A tároló törléséhez szükséges függőségek eltávolításának lépései |
-- | --
Helyszíni fájljaim és mappái az Azure Backup-ügynök használatával védettek, és biztonsági másolatot készítek az Azure-ról | A Biztonsági [másolat elemeinek törlése a MARS felügyeleti konzolról](#delete-backup-items-from-the-mars-management-console) című részben található lépések végrehajtása
Helyszíni gépeim vannak, amelyek et mabs (Microsoft Azure Backup Server) vagy DPM (System Center Data Protection Manager) használatával védenek az Azure-hoz | A Biztonsági [másolat elemeinek törlése a MABS felügyeleti konzolról](#delete-backup-items-from-the-mabs-management-console) című lépések végrehajtása
Védett elemekkel rendelkezem a felhőben (például egy laaS virtuális gép vagy egy Azure Files megosztás)  | A védett [elemek törlése a felhőben](#delete-protected-items-in-the-cloud) című részben leírt lépések végrehajtása
A helyszínen és a felhőben is védett elemeket | Hajtsa végre a következő szakaszok ban leírt lépéseket a következő sorrendben: <br> 1. [Védett elemek törlése a felhőben](#delete-protected-items-in-the-cloud)<br> 2. [Biztonsági másolat törlése a MARS felügyeleti konzolról](#delete-backup-items-from-the-mars-management-console) <br> 3. [Biztonsági mentési elemek törlése a MABS felügyeleti konzolról](#delete-backup-items-from-the-mabs-management-console)
Nincsenek védett elemek a helyszínen vagy a felhőben; bármennyire, Én csendes szerzés a Boltozat törlés hiba | A Helyreállítási [szolgáltatások tárolójának törlése az Azure Resource Manager használatával című](#delete-the-recovery-services-vault-by-using-azure-resource-manager) lépés lépéseit hajtsa végre <br><br> Győződjön meg arról, hogy nincsenek a tároló regisztrálva lévő tárfiókok. A fiók regisztrációjának megszüntetése című témakörben olvashat: [Unregister aan](manage-afs-backup.md#unregister-a-storage-account)storage account .

## <a name="delete-protected-items-in-the-cloud"></a>Védett elemek törlése a felhőben

Először olvassa el a **[Mielőtt elkezdené](#before-you-start)** a függőségek és a tároló törlési folyamat.

A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre az alábbi lépéseket:

1. A portálon nyissa meg a **Helyreállítási szolgáltatások tárolóját,** majd a **Biztonsági másolat elemek lehetőséget.** Ezután válassza ki a védett elemeket a felhőben (például az Azure virtuális gépek, az Azure Storage [az Azure Files szolgáltatás], vagy az SQL Server az Azure virtuális gépeken).

    ![Válassza ki a biztonsági másolat típusát.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Kattintson a jobb gombbal a biztonsági másolat elemének kijelöléséhez. Attól függően, hogy a biztonsági másolat eleme védett-e vagy sem, a menü a **Biztonsági másolat leállítása** ablaktáblát vagy a Biztonsági másolat **adatainak törlése** ablaktáblát jeleníti meg.

    - Ha megjelenik a **Biztonsági másolat leállítása** ablaktábla, válassza a legördülő menü Biztonsági másolat **adatainak törlése** parancsát. Írja be a biztonsági másolat elemének nevét (ez a mező nem tartalmazza a kis- és nagybetűket), majd válasszon egy okot a legördülő menüből. Írja be észrevételeit, ha van ilyen. Ezután válassza a **Biztonsági mentés leállítása**lehetőséget.

        ![A Biztonsági másolat leállítása ablaktábla.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Ha megjelenik a **Biztonsági másolat adatainak törlése** ablaktábla, írja be a biztonsági másolat elemének nevét (ez a mező a kis- és nagybetűk et is figyelembe teszi), majd válasszon egy okot a legördülő menüből. Írja be észrevételeit, ha van ilyen. Ezután válassza a **Törlés gombot.**

         ![A Biztonsági másolat adatainak törlése ablaktábla.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Jelölje **Notification** be az ![Értesítés ikont: Az Értesítés ikont.](./media/backup-azure-delete-vault/messages.png) A folyamat befejezése után a szolgáltatás a következő üzenetet jeleníti meg: *Biztonsági mentés leállítása és a biztonsági mentési adatok törlése a "* Biztonsági másolat*elem "* számára. *A művelet sikeresen befejeződött.*
4. A Biztonsági **másolat elemei menü** **Frissítés** parancsának kiválasztásához győződjön meg arról, hogy a biztonságimásolat-elem törölve lett.

      ![A Biztonsági másolat elemeinek törlése lap.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Védett elemek törlése a helyszínen

Először olvassa el a **[Mielőtt elkezdené](#before-you-start)** a függőségek és a tároló törlési folyamat.

1. A tároló irányítópultjának menüjében válassza a **Biztonsági infrastruktúra lehetőséget.**
2. A helyszíni forgatókönyvtől függően válasszon az alábbi lehetőségek közül:

      - A MARS esetében válassza **a Védett kiszolgálók,** majd az **Azure backup agent lehetőséget.** Ezután jelölje ki a törölni kívánt kiszolgálót.

        ![A MARS esetében válassza ki a trezort az irányítópult megnyitásához.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - MABS vagy DPM esetén válassza a **Biztonságimásolat-kezelő kiszolgálók lehetőséget.** Ezután jelölje ki a törölni kívánt kiszolgálót.

          ![A MABS esetében válassza ki a trezort az irányítópult megnyitásához.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. A **Törlés** ablaktábla figyelmeztető üzenettel jelenik meg.

     ![A törlés ablaktábla.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Tekintse át a figyelmeztető üzenetet és a hozzájárulási jelölőnégyzetet.
    > [!NOTE]
    >- Ha a védett kiszolgáló szinkronizálva van az Azure-szolgáltatásokkal, és a biztonsági mentési elemek léteznek, a hozzájárulási jelölőnégyzet ben megjelenik a függő biztonsági mentési elemek száma és a biztonsági mentési elemek megtekintéséhez szükséges hivatkozás.
    >- Ha a védett kiszolgáló nincs szinkronizálva az Azure-szolgáltatásokkal, és a biztonsági mentési elemek léteznek, a hozzájárulási jelölőnégyzet csak a biztonsági mentési elemek számát jeleníti meg.
    >- Ha nincsenek biztonsági másolatelemek, a hozzájárulás jelölőnégyzet törlést kér.

4. Jelölje be a hozzájárulás jelölőnégyzetet, majd a **Törlés**lehetőséget.

5. Jelölje **Notification** be ![az Értesítés](./media/backup-azure-delete-vault/messages.png)ikon a biztonsági mentési adatok törlését . A művelet befejezése után a szolgáltatás a következő üzenetet jeleníti meg: *A biztonsági mentés leállítása és a biztonsági mentési adatok törlése a "Biztonsági másolat elem" számára.* *A művelet sikeresen befejeződött.*
6. A Biztonsági **másolat elemei menü** **Frissítés** parancsának kiválasztásához győződjön meg arról, hogy a biztonsági másolat eleme törölve van.Select Refresh on the Backup Items menu, to sure the backup item is deleted.

A folyamat befejezése után törölheti a biztonsági mentési elemeket a felügyeleti konzolról:

- [Biztonsági másolat készítése a MARS felügyeleti konzoljáról](#delete-backup-items-from-the-mars-management-console)
- [Biztonsági másolat elemeinek törlése a MABS felügyeleti konzolról](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Biztonsági másolat készítése a MARS felügyeleti konzoljáról

1. Nyissa meg a MARS felügyeleti konzolt, nyissa meg a **Műveletek** ablaktáblát, és válassza **a Biztonsági másolat ütemezése**lehetőséget.
2. Az **Ütemezett biztonsági másolat módosítása vagy leállítása** lapon válassza a Biztonsági másolat **használatának leállítása lehetőséget, és törölje az összes tárolt biztonsági másolatot**. Ezután válassza a **Tovább gombot.**

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az **Ütemezett biztonsági másolat leállítása** lapon válassza a **Befejezés gombot.**

    ![Állítsa le az ütemezett biztonsági mentést.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer kéri, hogy adjon meg egy biztonsági PIN-kódot (személyes azonosítószámot), amelyet manuálisan kell létrehoznia. Ehhez először jelentkezzen be az Azure Portalon.
5. Nyissa meg a Helyreállítási szolgáltatások > **tárolóbeállításainak** > **tulajdonságait.** **Recovery Services vault**
6. A **Biztonsági PIN csoportban**válassza a **Létrehozás lehetőséget.** Másolja a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson az **OK gombra.**

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A **Biztonsági másolat módosítása folyamata** lapon a következő üzenet jelenik meg: *A törölt biztonsági mentési adatok 14 napig megmaradnak. Ezt követően a biztonsági mentési adatok véglegesen törlődnek.*  

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlése után kövesse a portál következő lépéseit.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Biztonsági másolat elemeinek törlése a MABS felügyeleti konzolról

A biztonsági másolat elemeinek törlésére két módszer használható a MABS felügyeleti konzolról.

#### <a name="method-1"></a>módszer

A védelem leállításához és a biztonsági mentési adatok törléséhez tegye a következő lépéseket:

1. Nyissa meg a DPM felügyeleti konzolt, és válassza a **védelem** lehetőséget a navigációs sávon.
2. A kijelzőablakban jelölje ki az eltávolítani kívánt védelmicsoport-tagot. Kattintson a jobb gombbal a **Csoporttagok védelmének leállításához beállításkiválasztásához.**
3. A **Védelem leállítása** párbeszédpanelen jelölje be a **Védett adatok törlése jelölőnégyzetet,** majd jelölje be a Tárhely online **törlése** jelölőnégyzetet. Ezután válassza a **Védelem leállítása**lehetőséget.

    ![Válassza a Védett adatok törlése lehetőséget a Védelem leállítása ablaktáblán.](./media/backup-azure-delete-vault/delete-storage-online.png)

    A védett tag állapota *Inaktív kópiára változik.*

4. Kattintson a jobb gombbal az inaktív védelmi csoportra, és válassza **az Inaktív védelem eltávolítása parancsot.**

    ![Távolítsa el az inaktív védelmet.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Az **Inaktív védelem törlése ablakban** jelölje be az **Online tárhely törlése** jelölőnégyzetet, majd kattintson az OK **gombra.**

    ![Online tárhely törlése.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>módszer

Nyissa meg a **MABS felügyeleti** konzolt. Az **Adatvédelmi módszer kiválasztása csoportban**törölje a jelet az **Online védelemhez** való értesítés jelölőnégyzetből.

  ![Válassza ki az adatvédelmi módszert.](./media/backup-azure-delete-vault/data-protection-method.png)

A helyszíni biztonsági mentési elemek törlése után kövesse a portál következő lépéseit.

## <a name="delete-the-recovery-services-vault"></a>A helyreállítási tár törlése

1. Ha az összes függőséget eltávolította, görgessen a tároló **menüEssentials** ablaktáblájához.
2. Ellenőrizze, hogy nincsenek-e biztonsági mentési elemek, biztonságimentési kiszolgálók vagy replikált elemek a listában. Ha az elemek továbbra is megjelennek a tárolóban, olvassa el a [Mielőtt elkezdi](#before-you-start) szakaszt.

3. Ha nincs több elem a tárolóban, válassza a **törlés** a tároló irányítópultján.

    ![Válassza a Törlés lehetőséget a tároló irányítópultján.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Válassza az **Igen** lehetőséget annak ellenőrzéséhez, hogy törölni szeretné-e a tárolót. A tároló törlődik. A portál visszatér az **Új** szolgáltatás menübe.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>A Recovery Services-tároló törlése a PowerShell használatával

Először olvassa el a **[Mielőtt elkezdené](#before-you-start)** a függőségek és a tároló törlési folyamat.

A védelem leállítása és a biztonsági mentési adatok törlése:

- Ha az SQL-t az Azure virtuális gépek biztonsági mentésében és az SQL-példányok automatikus védelmében használja, először tiltsa le az automatikus védelmet.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [További információ](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) az Azure biztonsági mentést védve lévő elemek védelmének letiltásáról.

- Állítsa le a védelmet, és törölje az adatokat az összes biztonsági mentés által védett elem felhőben (pl. laaS VM, Azure File Share stb.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [További információ](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) a biztonsági másolattal védett elemek védelmének letiltásáról.

- Az Azure biztonsági másolatügynök (MARS) Azure-ra történő biztonsági mentésével védett helyszíni fájlok és mappák esetében használja a következő PowerShell-parancsot az egyes MARS PowerShell-modulok biztonsági mentési adatainak törléséhez:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Tegye közzé, ahol a következő üzenet jelenik meg:

    *Microsoft Azure biztonsági mentés: Biztosan eltávolítja ezt a biztonsági mentési szabályzatot? A törölt biztonsági mentési adatok 14 napig maradnak meg. Ezt követően a biztonsági mentési adatok véglegesen törlődnek. <br/> [Y] Igen [A] Igen minden [N] Nem [L] Nem az összes [S] Felfüggesztés [?] Súgó (az alapértelmezett érték "Y"):*

- A MABS (Microsoft Azure Backup Server) vagy a DPM az Azure (System Center Data Protection Manager) használatával védett helyszíni gépek esetében a következő paranccsal törölheti a biztonsági mentést az Azure-ban.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Tegye közzé, ahol a következő üzenet jelenik meg:

   *Microsoft Azure biztonsági másolat* Biztosan eltávolítja ezt a biztonsági mentési házirendet? A törölt biztonsági mentési adatok 14 napig maradnak meg. Ezt követően a biztonsági mentési adatok véglegesen törlődnek. <br/>
   [Y] Igen [A] Igen minden [N] Nem [L] Nem az összes [S] Felfüggesztés [?] Súgó (alapértelmezett: "Y"):*

A biztonsági másolatok adatainak törlése után törölje a helyszíni tárolók és felügyeleti kiszolgálók regisztrációját.

- Az Azure Backup Agent (MARS) biztonsági másolattal védett helyszíni fájlok és mappák az Azure-ba való biztonsági mentéshez:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [További információ](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) a Windows Server vagy más tárolók regisztrálásának a tárolóból való regisztrációjának megszüntetése.

- A MABS (Microsoft Azure Backup Server) vagy a DPM az Azure (System Center Data Protection Manage) használatával védett helyszíni gépek esetén:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [További információ](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) a biztonsági mentési felügyeleti tároló kriptodes tárolóból való regisztrációjának megszüntetése.

Miután véglegesen törölte a biztonsági másolatot az adatokról, és törölte az összes tároló regisztrációjának törlését, folytassa a tároló törlésével.

A Recovery Services-tároló törlése:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[További információ](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) a helyreállítási szolgáltatások tárolójának törléséről.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>A Helyreállítási szolgáltatások tárolójának törlése a CLI használatával

Először olvassa el a **[Mielőtt elkezdené](#before-you-start)** a függőségek és a tároló törlési folyamat.

> [!NOTE]
> Jelenleg az Azure Backup CLI csak az Azure VM biztonsági mentések kezelését támogatja, így a következő parancs a tároló törlésére csak akkor működik, ha a tároló tartalmazza az Azure virtuális gép biztonsági mentéseit. Nem törölheti a tárolót az Azure Backup CLI használatával, ha a tároló az Azure virtuális gépeken kívüli más típusú biztonsági mentési elemet tartalmaz.

A meglévő Helyreállítási szolgáltatások tárolójának törléséhez hajtsa végre az alábbi műveleteket:

- A védelem leállítása és a biztonsági mentési adatok törlése

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    További információt ebben a [cikkben](/cli/azure/backup/protection#az-backup-protection-disable)talál.

- Meglévő helyreállítási szolgáltatások tárolójának törlése:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    További információt ebben a [cikkben](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) talál.

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>A Helyreállítási szolgáltatások tárolójának törlése az Azure Resource Manager használatával

Ez a lehetőség a Recovery Services-tároló törléséhez csak akkor ajánlott, ha az összes függőséget eltávolítja, és továbbra is megjelenik a *Vault törlési hibája.* Próbálkozzon az alábbi tippekkel:

- A tároló **menüEssentials** ablaktábláján ellenőrizze, hogy nincsenek-e biztonsági mentési elemek, biztonsági mentési felügyeleti kiszolgálók vagy replikált elemek. Ha vannak biztonsági másolat elemek, olvassa el a [Mielőtt](#before-you-start) szakaszt.
- Próbálja meg újra [kibiktatni a tárolót a portálról.](#delete-the-recovery-services-vault)
- Ha az összes függőséget eltávolítja, és továbbra is megjelenik a *Vault-törlési hiba,* használja az ARMClient eszközt a következő lépések végrehajtásához (a feljegyzés után).

1. Tovább a [chocolatey.org](https://chocolatey.org/) letölteni és telepíteni Chocolatey. Ezután telepítse az ARMClient programot a következő parancs futtatásával:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Jelentkezzen be Az Azure-fiókjába, majd futtassa a következő parancsot:

    `ARMClient.exe login [environment name]`

3. Az Azure Portalon gyűjtse össze a törölni kívánt tároló előfizetés-azonosítóját és erőforráscsoport-nevét.

Az ARMClient paranccsal kapcsolatos további információkért lásd: [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>A Recovery Services-tároló törlése az Azure Resource Manager-ügyféllel

1. Futtassa a következő parancsot az előfizetés-azonosító, az erőforráscsoport nevének és a tároló nevének használatával. Ha nincsenek függőségei, a tároló törlődik a következő parancs futtatásakor:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Ha a tároló nem üres, a következő hibaüzenet jelenik meg: *A Vault nem törölhető, mert a tárolóban vannak meglévő erőforrások.* Ha egy tárolón belül szeretne eltávolítani egy védett elemet vagy tárolót, futtassa a következő parancsot:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Az Azure Portalon győződjön meg arról, hogy a tároló törlődik.

## <a name="next-steps"></a>További lépések

[További információ a Helyreállítási szolgáltatások tárolóiról](backup-azure-recovery-services-vault-overview.md)<br/>
[További információ a Helyreállítási szolgáltatások tárolóinak figyeléséről és kezeléséről](backup-azure-manage-windows-server.md)
