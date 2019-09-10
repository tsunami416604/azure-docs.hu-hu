---
title: Microsoft Azure Backup Recovery Services-tároló törlése
description: Ez a cikk egy Microsoft Azure Backup Recovery Services-tároló törlését ismerteti.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: dacurwin
ms.openlocfilehash: a49449f799696ce6962afea6bdc212f658c660bd
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860373"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure Backup Recovery Services-tároló törlése

Ez a cikk a Microsoft [Azure Backup](backup-overview.md) Recovery Services (MARS) tárolójának törlését ismerteti. A függőségek eltávolítására, majd a tároló törlésére vonatkozó utasításokat tartalmaz.


## <a name="before-you-start"></a>Előkészületek

Nem törölhet olyan Recovery Services-tárolót, amelynek függőségei, például védett kiszolgálók vagy biztonságimásolat-felügyeleti kiszolgálók vannak társítva.

- A biztonsági mentési adattárakat tartalmazó tárolókat nem lehet törölni (még akkor is, ha leállította a védelmet, de megtartotta a biztonsági mentési adatforrásokat).

- Ha olyan tárolót töröl, amely függőségeket tartalmaz, a következő üzenet jelenik meg:

  ![Törölje a tár hibáját.](./media/backup-azure-delete-vault/error.png)

- Ha töröl egy helyszíni védett elemet egy olyan portálról, amely függőségeket tartalmaz, a rendszer figyelmeztető üzenetet jelenít meg:

  ![A védett kiszolgáló hibájának törlése.](./media/backup-azure-delete-vault/error-message.jpg)

  
A tár törléséhez válassza ki a beállításnak megfelelő forgatókönyvet, és kövesse az ajánlott lépéseket:

Forgatókönyv | A tár törléséhez szükséges függőségek eltávolításának lépései |
-- | --
A Azure Backup-ügynökkel védett helyszíni fájlokat és mappákat használok, az Azure-ba történő biztonsági mentéssel | Hajtsa végre a [biztonsági másolati elemek törlése a Mars felügyeleti konzolról](#delete-backup-items-from-the-mars-management-console) című témakör lépéseit.
MABS (Microsoft Azure Backup Server) vagy DPM (System Center Data Protection Manager) használatával védett helyszíni gépeket használok az Azure-ba | Hajtsa végre a [biztonsági másolati elemek törlése a MABS felügyeleti konzolról](#delete-backup-items-from-the-mabs-management-console) című témakör lépéseit.
Védett elemek vannak a felhőben (például laaS virtuális gép vagy Azure Files-megosztás)  | Kövesse a [védett elemek törlése a felhőben](#delete-protected-items-in-the-cloud) című témakör lépéseit.
A helyszínen és a felhőben egyaránt védett elemek vannak | Hajtsa végre a következő részekben ismertetett lépéseket a következő sorrendben: <br> 1. [Védett elemek törlése a felhőben](#delete-protected-items-in-the-cloud)<br> 2. [Biztonsági másolati elemek törlése a MARS felügyeleti konzolról](#delete-backup-items-from-the-mars-management-console) <br> 3. [Biztonsági másolati elemek törlése a MABS felügyeleti konzolról](#delete-backup-items-from-the-mabs-management-console)
Nem rendelkezem helyszíni vagy Felhőbeli védett elemekkel; a tár törlési hibája azonban továbbra is fennáll. | Hajtsa végre az [Recovery Services-tároló törlése Azure Resource Manager használatával](#delete-the-recovery-services-vault-by-using-azure-resource-manager) című témakör lépéseit.


## <a name="delete-protected-items-in-the-cloud"></a>Védett elemek törlése a felhőben

Először olvassa el az első **[lépések](#before-you-start)** szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

A védelem leállításához és a biztonsági mentési adatok törléséhez hajtsa végre a következő lépéseket:

1. A portálon nyissa meg **Recovery Services**-tárolót, majd lépjen a **biztonsági másolati elemek elemre**. Ezután válassza ki a védett elemeket a felhőben (például Azure Virtual Machines, Azure Storage [a Azure Files szolgáltatás] vagy SQL Server az Azure Virtual Machines).

    ![Válassza ki a biztonsági mentés típusát.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Kattintson a jobb gombbal a biztonsági mentési elem kiválasztásához. Attól függően, hogy a biztonsági másolati elem védett-e, vagy sem, a menü a **biztonsági mentés leállítása** vagy a **biztonsági másolat törlése** ablaktáblát jeleníti meg.

    - Ha megjelenik a **biztonsági mentés leállítása** ablaktábla, válassza a **biztonsági másolat adatainak törlése** lehetőséget a legördülő menüből. Adja meg a biztonsági másolati elem nevét (ez a kis-és nagybetűk megkülönböztetése), majd válasszon ki egy okot a legördülő menüből. Adja meg a megjegyzéseit, ha van ilyen. Ezután válassza a **biztonsági mentés leállítása**lehetőséget.

        ![A biztonsági mentés leállítása panel.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Ha megjelenik a **biztonsági mentési adatok törlése** ablaktábla, adja meg a biztonsági másolati elem nevét (ez a mező megkülönbözteti a kis-és nagybetűket), majd válasszon ki egy okot a legördülő menüből. Adja meg a megjegyzéseit, ha van ilyen. Ezután válassza a **Törlés**lehetőséget. 

         ![A biztonsági mentési adattábla törlése panel.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Keresse meg az **értesítés** ikonját: ![Az értesítés ikonja.](./media/backup-azure-delete-vault/messages.png) A folyamat befejeződése után a szolgáltatás a következő üzenetet jeleníti meg: *A biztonsági mentés leállítása és a biztonsági másolatok törlése a következőre:* Biztonsági másolati elem: *"* . *A művelet sikeresen befejeződött*.
6. Válassza a **frissítés** lehetőséget a **biztonsági másolati elemek** menüben, hogy meggyőződjön arról, hogy a biztonsági mentési elem törölve lett.

      ![A biztonsági másolati elemek törlése oldal.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Védett elemek törlése a helyszínen

Először olvassa el az első **[lépések](#before-you-start)** szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

1. A tároló irányítópultjának menüjében válassza a **biztonsági mentési infrastruktúra**elemet.
2. A helyszíni forgatókönyvtől függően válasszon a következő lehetőségek közül:

      - A MARS területen válassza a **védett kiszolgálók** elemet, majd **Azure Backup az ügynököt**. Ezután válassza ki a törölni kívánt kiszolgálót. 

        ![A MARS lapon válassza ki a tárolót az irányítópult megnyitásához.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - A MABS vagy a DPM lapon válassza a **biztonsági mentési felügyeleti kiszolgálók**lehetőséget. Ezután válassza ki a törölni kívánt kiszolgálót. 


          ![A MABS válassza ki a tárolót az irányítópult megnyitásához.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. A **Törlés** panel figyelmeztető üzenettel jelenik meg.

     ![A törlés panel.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Tekintse át a figyelmeztető üzenetet és az utasításokat a belefoglalt szövegben.
    > [!NOTE]
    >- Ha a védett kiszolgáló szinkronizálva van az Azure-szolgáltatásokkal és a biztonsági mentési elemekkel, akkor a beleegyezik jelölőnégyzet megjeleníti a függő biztonsági mentési elemek számát és a biztonsági másolati elemek megtekintésére szolgáló hivatkozást.
    >- Ha a védett kiszolgáló nincs szinkronizálva az Azure-szolgáltatásokkal, és a biztonsági másolati elemek léteznek, a beleegyezett jelölőnégyzet csak a biztonsági másolati elemek számát jeleníti meg.
    >- Ha nincsenek biztonsági másolati elemek, a beleegyezett jelölőnégyzettel a törlést kéri a rendszer.

4. Jelölje be a beleegyezett jelölőnégyzetet, majd válassza a **Törlés**lehetőséget.


5. Győződjön meg **arról** , ![hogy az értesítési ikon törli a biztonsági mentési fájlokat.](./media/backup-azure-delete-vault/messages.png) A művelet befejeződése után a szolgáltatás a következő üzenetet jeleníti meg: *A biztonsági mentés leállítása és a biztonsági másolati elemek törlése.* *A művelet sikeresen befejeződött*.
6. Válassza a **frissítés** lehetőséget a **biztonsági mentési elemek** menüben, hogy biztosan törölje a biztonsági mentési elemet.

A folyamat befejezése után törölheti a biztonsági mentési elemeket a felügyeleti konzolról:
    
   - [Biztonsági másolati elemek törlése a MARS felügyeleti konzolról](#delete-backup-items-from-the-mars-management-console)
    - [Biztonsági másolati elemek törlése a MABS felügyeleti konzolról](#delete-backup-items-from-the-mabs-management-console)


### <a name="delete-backup-items-from-the-mars-management-console"></a>Biztonsági másolati elemek törlése a MARS felügyeleti konzolról

1. Nyissa meg a MARS felügyeleti konzolját, lépjen a **műveletek** ablaktáblára, és válassza a **biztonsági mentés időzítése**elemet.
2. Az **ütemezett biztonsági mentés módosítása vagy leállítása** lapon válassza a **Leállítás a biztonsági mentési ütemezés használatával lehetőséget, és törölje az összes tárolt biztonsági**mentést. Ezután válassza a **Tovább** lehetőséget.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az **ütemezett biztonsági mentés leállítása** lapon válassza a **Befejezés**lehetőséget.

    ![Állítsa le az ütemezett biztonsági mentést.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer felszólítja, hogy adjon meg egy biztonsági PIN-kódot (személyes azonosító számot), amelyet manuálisan kell előkészítenie. Ehhez először jelentkezzen be a Azure Portalba.
5. Nyissa meg **Recovery Services** > tároló**beállításai** > **tulajdonságokat**.
6. A **biztonsági PIN-kód**területen válassza a **készítés**elemet. Másolja ezt a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson **az OK gombra**.

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A **biztonsági mentési folyamat módosítása** lapon a következő üzenet jelenik meg: *A törölt biztonsági mentési adat 14 napig őrzi meg a rendszer. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés.*  

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a portál következő lépéseit.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Biztonsági másolati elemek törlése a MABS felügyeleti konzolról

A MABS felügyeleti konzolon két módszer használható a biztonsági mentési elemek törlésére.

#### <a name="method-1"></a>1\. módszer
A védelem leállításához és a biztonsági másolatok állapotának törléséhez hajtsa végre a következő lépéseket:

1.  Nyissa meg a DPM Felügyeleti konzol, majd a navigációs sávon kattintson a **védelem** elemre.
2.  A Megjelenítés ablaktáblán válassza ki a védelmi csoport eltávolítani kívánt tagját. Kattintson a jobb gombbal a **csoporttagok védelmének leállítása** lehetőség kiválasztásához.
3.  A **védelem leállítása** párbeszédpanelen válassza a **védett adatok törlése**lehetőséget, majd jelölje be a **tárterület online törlése** jelölőnégyzetet. Ezután válassza a **védelem leállítása**lehetőséget.

    ![Válassza a védett adatok törlése lehetőséget a védelem leállítása ablaktáblán.](./media/backup-azure-delete-vault/delete-storage-online.png)

    A védett tag állapota a *rendelkezésre álló inaktív replikára*módosul.

4. Kattintson a jobb gombbal az inaktív védelmi csoportra, és válassza az **inaktív védelem eltávolítása**lehetőséget.

    ![Inaktív védelem eltávolítása.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Az **inaktív védelem törlése** ablakban jelölje be az **online tárterület törlése** jelölőnégyzetet, majd kattintson az **OK gombra**.

    ![Online tárterület törlése.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>2\. módszer
Nyissa meg a **MABS felügyeleti** konzolt. Az **adatvédelmi módszer kiválasztása**területen törölje a jelet a szeretnék **online védelem** jelölőnégyzetből.

  ![Válassza ki az adatvédelmi módszert.](./media/backup-azure-delete-vault/data-protection-method.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a portál következő lépéseit.


## <a name="delete-the-recovery-services-vault"></a>A helyreállítási tár törlése

1. Ha az összes függőség el lett távolítva, görgessen a tároló menü **Essentials (alapvető** erőforrások) paneljére.
2. Ellenőrizze, hogy nincsenek-e biztonsági másolati elemek, biztonságimásolat-kezelési kiszolgálók vagy replikált elemek felsorolva. Ha az elemek továbbra is megjelennek a tárolóban, [olvassa el az](#before-you-start) előkészületek című szakaszt.

3. Ha nincs több elem a tárolóban, válassza a **Törlés** lehetőséget a tároló irányítópultján.

    ![Válassza a Törlés lehetőséget a tároló irányítópultján.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Válassza az **Igen** lehetőséget annak ellenőrzéséhez, hogy törölni kívánja-e a tárolót. A tár törölve. A portál visszatér az **új** szolgáltatás menüjébe.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>A Recovery Services-tároló törlése a PowerShell használatával

Először olvassa el az első **[lépések](#before-you-start)** szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

A védelem leállításához és a biztonsági másolatok törléséhez:

- Ha az SQL-t használja az Azure virtuális gépek biztonsági mentése és az automatikus védelem engedélyezése SQL-példányok esetén, először tiltsa le az automatikus védelmet.

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

  [További információ](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) a Azure Backup-védelemmel ellátott elemek védelmének letiltásáról 

- Állítsa le a védelmet, és töröljön adatokat a felhőben található összes biztonsági mentéssel védett elemhez (pl. laaS VM, Azure fájlmegosztás stb.):

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
    [További információ a](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0)biztonságimásolattalvédettelemekvédelménekletiltásáról . 

- A Azure Backup ügynök (MARS) használatával védett helyszíni fájlok és mappák Azure-ba történő biztonsági mentését a következő PowerShell-paranccsal törölheti az egyes MARS PowerShell-modulokból származó biztonsági másolatok adatainak törléséhez:

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    A következő üzenet jelenik meg:
     
    *Microsoft Azure Backup biztosan el szeretné távolítani ezt a biztonsági mentési szabályzatot? A törölt biztonsági mentési adat 14 napig őrzi meg a rendszer. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés. <br/> [Y] igen [A] igen az összes [N] nem [L] nem az összes [S] felfüggesztése [?] Súgó (az alapértelmezett érték az "Y"):*


- A MABS (Microsoft Azure Backup-kiszolgáló) vagy az Azure-hoz (System Center Data Protection Manager) védett helyszíni gépek esetén a következő paranccsal törölheti az Azure-ban tárolt biztonsági másolati adataikat.

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin> 
    ```

    A következő üzenet jelenik meg: 
         
   *Microsoft Azure Backup biztosan el szeretné távolítani ezt a biztonsági mentési szabályzatot? A törölt biztonsági mentési adat 14 napig őrzi meg a rendszer. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés. <br/> [Y] igen [A] igen az összes [N] nem [L] nem az összes [S] felfüggesztése [?] Súgó (az alapértelmezett érték az "Y"):*

A biztonsági másolatok törlését követően törölje a helyszíni tárolók és a felügyeleti kiszolgálók regisztrációját. 

- A Azure Backup Agent (MARS) használatával védett helyszíni fájlok és mappák az Azure-ba történő biztonsági mentéssel:

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
    [További](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) információ a Windows Server vagy más tárolók a tárból való regisztrációjának törléséről. 

- A MABS (Microsoft Azure Backup-kiszolgáló) vagy az Azure-ba (System Center adatvédelem kezelése) védett helyszíni gépek esetén:

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

    [További](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) információ a biztonságimásolat-kezelési tárolók a tárból való regisztrációjának törléséről.

Miután véglegesen törölte a biztonsági mentést, és nem regisztrálja az összes tárolót, folytassa a tár törlésével. 

Recovery Services-tároló törlése: 

   ```PowerShell
       Remove-AzRecoveryServicesVault 
      -Vault <ARSVault> 
      [-DefaultProfile <IAzureContextContainer>] 
      [-WhatIf] 
      [-Confirm] 
      [<CommonParameters>]        
   ```

[További](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) információ a Recovery Services-tároló törléséről. 

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>A Recovery Services-tároló törlése a CLI használatával

Először olvassa el az első **[lépések](#before-you-start)** szakaszt a függőségek és a tár törlési folyamatának megismeréséhez.

> [!NOTE]
> Jelenleg Azure Backup CLI csak az Azure-beli virtuális gépek biztonsági másolatainak kezelését támogatja, így a tároló csak akkor törölhető, ha a tár Azure-beli virtuális gépek biztonsági másolatait tartalmazza. Azure Backup CLI használatával nem törölhet tárolót, ha a tár az Azure-beli virtuális gépektől eltérő típusú biztonságimásolat-elemeket tartalmaz. 

A meglévő Recovery Services-tároló törléséhez hajtsa végre az alábbi műveleteket: 

- A védelem leállítása és a biztonsági másolatok törlése 

    ```CLI
    az backup protection disable --container-name 
                             --item-name 
                             [--delete-backup-data {false, true}] 
                             [--ids] 
                             [--resource-group] 
                             [--subscription] 
                             [--vault-name] 
                             [--yes] 
    ```

    További információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable.). 

- Meglévő Recovery Services-tároló törlése: 

    ```CLI
    az backup vault delete [--force] 
                       [--ids] 
                       [--name] 
                       [--resource-group] 
                       [--subscription] 
                       [--yes] 
    ```

    További információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) 

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Az Recovery Services-tároló törlése Azure Resource Manager használatával

Ez a Recovery Services-tároló törlésének lehetősége csak akkor javasolt, ha az összes függőség el lett távolítva, és a tár *törlési hibája*továbbra is bekerül. Próbálja ki a következő tippek bármelyikét vagy mindegyikét:

- A tár menü **Essentials** paneljén ellenőrizze, hogy nincsenek-e biztonsági másolati elemek, biztonságimásolat-kezelési kiszolgálók vagy replikált elemek felsorolva. Ha vannak biztonsági másolati elemek, tekintse át az [első lépések](#before-you-start) szakaszt.
- Próbálja meg újból [törölni a tárat a portálról](#delete-the-recovery-services-vault) .
- Ha az összes függőség el lett távolítva, és még mindig beolvassa a tár *törlési hibáját*, a ARMClient eszközzel hajtsa végre a következő lépéseket (a Megjegyzés után).

1. A chocolatey letöltéséhez és telepítéséhez nyissa meg a [chocolatey.org](https://chocolatey.org/) . Ezután telepítse a ARMClient a következő parancs futtatásával:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Jelentkezzen be az Azure-fiókjába, majd futtassa a következő parancsot:

    `ARMClient.exe login [environment name]`

3. A Azure Portal Gyűjtse össze a törölni kívánt tároló előfizetés-AZONOSÍTÓját és erőforráscsoport-nevét.

A ARMClient paranccsal kapcsolatos további információkért tekintse meg a [ARMCLIENT readme](https://github.com/projectkudu/ARMClient/blob/master/README.md)című témakört.

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Recovery Services-tároló törlése a Azure Resource Manager ügyfél használatával

1. Futtassa az alábbi parancsot az előfizetés-azonosító, az erőforráscsoport neve és a tár neve használatával. Ha nem rendelkezik függőségekkel, a tár törlődik a következő parancs futtatásakor:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Ha a tár nem üres, a következő hibaüzenet jelenik meg: *A tároló nem törölhető, mert a tárolóban meglévő erőforrások vannak.* Egy védett elem vagy tároló egy tárolón belüli eltávolításához futtassa a következő parancsot:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Győződjön meg arról, hogy a Azure Portal törli a tárolót.


## <a name="next-steps"></a>További lépések

[További tudnivalók a Recovery Services-tárolók](backup-azure-recovery-services-vault-overview.md)<br/>
[Tudnivalók a Recovery Services-tárolók monitorozásáról és kezeléséről](backup-azure-manage-windows-server.md)
