---
title: Oktatóanyag – SAP HANA DB biztonsági mentés az Azure-ban a CLI használatával
description: Ebben az oktatóanyagban megtudhatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokról egy Azure Backup Recovery Services-tárolóba az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: cdc8a8fb09a086a2b9212c21d071f267991fa275
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78206622"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: Az SAP HANA-adatbázisok biztonsági mentése egy Azure virtuális gépen az Azure CLI használatával

Az Azure CLI segítségével azure-erőforrásokat hozhat létre és kezelhet a parancssorból vagy parancsfájlokon keresztül. Ez a dokumentáció részletezi, hogyan készíthet biztonsági másolatot egy SAP HANA-adatbázisról, és hogyan indíthat el igény szerinti biztonsági mentéseket – mindezt az Azure CLI használatával. Ezeket a lépéseket az [Azure Portal](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)használatával is végrehajthatja.

Ez a dokumentum feltételezi, hogy már rendelkezik egy SAP HANA-adatbázis telepítve egy Azure virtuális gép. (Virtuális [gép is létrehozható az Azure CLI használatával.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli) Az oktatóanyag végére a következőkre lesz képes:

> [!div class="checklist"]
>
> * Recovery Services-tároló létrehozása
> * Sap HANA-példány regisztrálása és adatbázis(ok) felderítése rajta
> * Biztonsági mentés engedélyezése SAP HANA-adatbázisban
> * Igény szerinti biztonsági mentés aktiválása

Tekintse meg a [forgatókönyveket, amelyekjelenleg támogatja](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support) az SAP HANA.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A CLI helyi telepítéséhez és használatához futtatnia kell az Azure CLI xx.xxx.x vagy újabb verzióját. A parancssori felület verziójának megkereséséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy logikai tároló, amely tárolja a biztonsági mentési adatokat az egyes védett erőforrások, például az Azure virtuális gépek vagy az Azure virtuális gépeken futó számítási feladatok – például az SQL vagy hana adatbázisok. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

Recovery Services-tárolót az [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create) paranccsal hozhat létre. Adja meg a védeni kívánt virtuális gépével megegyező erőforráscsoportot és helyet. Ebből a virtuális gépből megtudhatja, hogyan hozhat létre virtuális gépeket az Azure CLI használatával ezzel a [virtuális gép rövid útmutatójával.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)

Ehhez az oktatóanyaghoz a következőket fogjuk használni:

* *saphanaResourceGroup* nevű erőforráscsoport
* *saphanaVM* nevű virtuális gép
* források a *westus2* helyen.

Létrehozunk egy *saphanaVault*nevű trezort.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

A Recovery Services-tároló alapértelmezés szerint georedundáns tárolásra van beállítva. A georedundáns tárolás biztosítja, hogy a rendszer egy olyan másodlagos Azure-régióba replikálja a biztonsági mentési adatokat, amely a forrásadatok elsődleges helyétől több száz kilométerre található. Ha a tárolási redundancia beállítást módosítani kell, használja az [az biztonsági másolat-tároló biztonsági mentési készlet](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) parancsmagját.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Annak megtekintéséhez, hogy a tároló sikeresen létrejött-e, használja az [az biztonsági mentési tároló](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) parancsmagját. A következő válasz jelenik meg:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Regisztrálja és védje az SAP HANA-példányt

Az SAP HANA-példány (a virtuális gép, amelyen telepítve van az SAP HANA) az [Azure-szolgáltatások](https://aka.ms/scriptforpermsonhana) által felderített, egy előzetes regisztrációs parancsfájlt kell futtatni az SAP HANA-gépen. Győződjön meg arról, hogy a parancsfájl futtatása előtt minden [előfeltétel](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#prerequisites) teljesül. Ha többet szeretne megtudni arról, hogy mit csinál a parancsfájl, olvassa el [a Mi az előzetes regisztrációs parancsfájl nem](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) szakaszban.

A parancsfájl futtatása után az SAP HANA-példány regisztrálható a helyreállítási szolgáltatások tárolókorában létrehozott. A példány regisztrálásához használja az [az biztonsági mentési tároló regiszter](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) parancsmagját. *A VMResourceId* az SAP HANA telepítéséhez létrehozott virtuális gép erőforrás-azonosítója.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Ha a virtuális gép nem ugyanabban az erőforráscsoportban, mint a tároló, majd *saphanaResourceGroup* hivatkozik az erőforráscsoport, ahol a tároló jött létre.

Az SAP HANA-példány regisztrálása automatikusan felderíti az összes aktuális adatbázisokat. Azonban a jövőben hozzáadható új adatbázisok felderítéséhez tekintse meg a [regisztrált SAP HANA-példány szakaszhoz hozzáadott új adatbázisok felderítése.](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance)

Annak ellenőrzéséhez, hogy az SAP HANA-példány sikeresen regisztrálva van-e a tárolóban, használja az [az biztonsági mentési tárolók listájának](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsmagját. A következő válasz jelenik meg:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> A fenti kimenetben a "name" oszlop a tároló nevére utal. Ez a tárolónév lesz használva a következő szakaszokban a biztonsági mentések engedélyezéséhez és aktiválásához. Amely ebben az esetben a *VMAppContainer; Számítás;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Biztonsági mentés engedélyezése az SAP HANA-adatbázisban

Az [az biztonsági mentés védett elemlista](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) parancsmag felsorolja az összes felderített adatbázisok az SAP HANA-példány, amely az előző lépésben regisztrált.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Ebben a listában meg kell találnia a biztonsági másolatot tartalmazó adatbázist, amely a következőképpen fog kinézni:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Mint látható a fenti kimenet, az SAP HANA rendszer SID-je HXE. Ebben az oktatóanyagban konfiguráljuk a *saphanadatabase;hxe;hxe* adatbázis biztonsági mentését, amely a *hxehost* kiszolgálón található.

Az adatbázis biztonsági mentésének védelme és konfigurálása érdekében az [az biztonsági mentés védelmi enable-for-azurewl](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) parancsmagját használjuk. Adja meg a használni kívánt házirend nevét. A CLI használatával házirend létrehozásához használja az [az biztonsági mentési házirend létrehozása](https://docs.microsoft.com//cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) parancsmag. Ebben az oktatóanyagban a *sapahanaPolicy szabályzatot* fogjuk használni.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Ellenőrizheti, hogy a fenti biztonsági mentési konfiguráció befejeződött-e az [az biztonsági mentési feladatlista](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) parancsmag használatával. A kimenet a következőképpen jelenik meg:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Az [az biztonsági mentési feladatlista](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) parancsmagja felsorolja az összes olyan biztonsági mentési feladatot (ütemezett vagy igény szerint), amely a védett adatbázisban futott vagy jelenleg fut, az egyéb műveleteken kívül, például a regisztráción, a biztonsági mentés konfigurálása, a biztonsági mentési adatok törlése stb.

## <a name="trigger-an-on-demand-backup"></a>Igény szerinti biztonsági mentés aktiválása

Míg a fenti szakasz részletezi, hogyan kell beállítani az ütemezett biztonsági mentést, ez a szakasz az igény szerinti biztonsági mentés elindításáról szól. Ehhez az [az biztonsági mentés védelmi biztonsági mentési parancsmamot](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now) használjuk.

>[!NOTE]
> Az igény szerinti biztonsági mentés adatmegőrzési szabályát az adatbázis alapul szolgáló adatmegőrzési házirendje határozza meg.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

A kimenet a következőképpen jelenik meg:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

A válasz megadja a feladat nevét. Ez a feladatnév a feladat állapotának nyomon követésére használható az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag használatával.

>[!NOTE]
>A teljes vagy különbözeti biztonsági mentés ütemezése mellett jelenleg manuálisan is aktiválhatók. A naplóbiztonsági mentések automatikusan aktiválódnak, és az SAP HANA belsőleg kezeli.
>
> Növekményes biztonsági mentések jelenleg nem támogatja az Azure Backup.

## <a name="next-steps"></a>További lépések

* Ha meg szeretné tudni, hogyan állíthatja vissza az SAP HANA-adatbázist az Azure virtuális gépében a CLI használatával, folytassa az oktatóanyaggal – Állítsa vissza az [SAP HANA-adatbázist az Azure virtuális gépben a CLI használatával](tutorial-sap-hana-restore-cli.md)

* Ha meg szeretné tudni, hogyan készíthet biztonsági másolatot az Azure Virtuális gépben az Azure Portalon futó SAP HANA-adatbázisról, tekintse meg az [SAP HANA-adatbázisok biztonsági mentését az Azure virtuális gépeken](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
