---
title: 'Oktatóanyag: Az SAP HANA DB biztonsági másolatot adott kezelése a CLI használatával'
description: Ebben az oktatóanyagban megtudhatja, hogyan kezelheti az Azure-beli virtuális gépeken futó, azure-beli VIRTUÁLIS gépeken futó, biztonsági másolatot tartalmazó SAP HANA-adatbázisokat az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238740"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: SAP HANA-adatbázisok kezelése egy Azure virtuális gépben az Azure CLI használatával

Az Azure CLI segítségével azure-erőforrásokat hozhat létre és kezelhet a parancssorból vagy parancsfájlokon keresztül. Ez a dokumentáció részletezi, hogyan kezelheti a biztonsági másolatot készíteni az SAP HANA-adatbázis az Azure VM - minden azure CLI használatával. Ezeket a lépéseket [az Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)használatával is végrehajthatja.

Az [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) használatával cli-parancsokat futtat.

Az oktatóanyag végére a következőkre lesz képes:

> [!div class="checklist"]
>
> * Biztonsági mentési és visszaállítási feladatok figyelése
> * SAP HANA-példányhoz hozzáadott új adatbázisok védelme
> * A házirend módosítása
> * A védelem kikapcsolása
> * Folytatás védelem

Ha az [SAP HANA-adatbázis biztonsági mentése az Azure-ban a CLI használatával](tutorial-sap-hana-backup-cli.md) az SAP HANA-adatbázis biztonsági mentése, majd használja a következő erőforrásokat:

* *saphanaResourceGroup* nevű erőforráscsoport
* *egy saphanaVault* nevű trezor
* VMAppContainer nevű védett *tároló; Számítás;saphanaResourceGroup;saphanaVM*
* biztonsági másolatot készíteni *adatbázis/saphanadatabase nevű elem;hxe;hxe*
* források a *westus2* régióban

Az Azure CLI megkönnyíti az Azure-alapú virtuális gépen futó SAP HANA-adatbázis kezelését, amelyről az Azure Backup használatával készül biztonsági másolat. Ez az oktatóanyag részletezi az egyes felügyeleti műveletek.

## <a name="monitor-backup-and-restore-jobs"></a>Biztonsági mentési és visszaállítási feladatok figyelése

Befejezett vagy jelenleg futó feladatok (biztonsági mentés vagy visszaállítás) figyeléséhez használja az [az biztonsági mentési feladat listájának](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) parancsmagja. A CLI azt is lehetővé teszi, hogy [felfüggessze az éppen futó feladatot,](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) vagy [megvárja, amíg a feladat befejeződik.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

A kimenet a következőhöz hasonlóan fog kinézni:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Házirend módosítása

Az SAP HANA biztonsági mentési konfigurációja alapjául szolgáló házirend módosításához használja az [az biztonsági mentési házirend-készlet](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) parancsmagját. A parancsmagban lévő névparaméter arra a biztonsági másolat elemre utal, amelynek házirendje módosítani kíván. Ebben az oktatóanyagban az SAP HANA adatbázis *saphanadatabase;hxe;hxe,hxe* házirendjének szabályzatát egy új policy *newsaphanaPolicy -re*cseréljük. Új házirendek hozhatók létre az [az biztonsági mentési házirend létrehozása](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) parancsmag használatával.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Az alábbihoz hasonló eredményt kell kapnia:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>SAP HANA-példányhoz hozzáadott új adatbázisok védelme

[Egy SAP HANA-példány regisztrálása egy helyreállítási szolgáltatások tárolóautomatikusan](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) felderíti az összes adatbázisebben a példányban.

Azonban azokban az esetekben, amikor új adatbázisok at adnak hozzá az SAP HANA-példány később, használja az [az biztonsági mentés védett elem inicializálása](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) parancsmag. Ez a parancsmag felderíti a hozzáadott új adatbázisokat.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Ezután az [az biztonsági mentés védett-elemlista](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) parancsmag használatával az SAP HANA-példányban felderített adatbázisok listájának listáihoz. Ez a lista azonban nem tartalmazza azokat az adatbázisokat, amelyeken a biztonsági mentés már konfigurálva van. Miután a rendszer felfedezte a biztonsági mentésre váró adatbázist, olvassa el [a Biztonsági mentés engedélyezése az SAP HANA-adatbázisban](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)című területet.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

A biztonsági másolatot létrehozni kívánt új adatbázis megjelenik a listában, amely a következőképpen fog kinézni:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA-adatbázis védelmének leállítása

Az SAP HANA-adatbázisok védelme több féleképpen leállíthatja:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törölje az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy elhagyja a helyreállítási pontokat, tartsa szem előtt az alábbi részleteket:

* Minden helyreállítási pont örökre érintetlen marad, minden metszés nek meg kell állnia a stop védelem ben az adatok megőrzésével.
* A védett példányért és a felhasznált tárterületért díjat számítunk fel.
* Ha a biztonsági mentések leállítása nélkül töröl egy adatforrást, az új biztonsági mentések sikertelenek lesznek.

Nézzük meg részletesebben a védelem leállításának módjait.

### <a name="stop-protection-with-retain-data"></a>Védelem leállítása az adatok megőrzésével

Az adatok megőrzésével való védelem leállításához használja az [az biztonsági mentés védelmi letiltási](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) parancsmag.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Az alábbihoz hasonló eredményt kell kapnia:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

A művelet állapotának ellenőrzéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

### <a name="stop-protection-without-retain-data"></a>A védelem leállítása az adatok megőrzése nélkül

Az adatok megőrzése nélküli védelem leállításához használja az [az biztonsági mentés elleni védelem letiltási](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) parancsmag.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Az alábbihoz hasonló eredményt kell kapnia:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

A művelet állapotának ellenőrzéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

## <a name="resume-protection"></a>Folytatás védelem

Amikor leállítja az SAP HANA-adatbázis védelmét az adatok megőrzésével, később folytathatja a védelmet. Ha nem őrzi meg a biztonsági másolatot, nem tudja folytatni a védelmet.

A védelem folytatásához használja az [az biztonsági mentés védelmi folytatási](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) parancsmag.

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Az alábbihoz hasonló eredményt kell kapnia:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

A művelet állapotának ellenőrzéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

## <a name="next-steps"></a>További lépések

* Ha meg szeretné tudni, hogyan készíthet biztonsági másolatot az Azure-portálon futó SAP HANA-adatbázisról az Azure-portálon futó [SAP](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) HANA-adatbázisokról.

* Ha meg szeretné tudni, hogyan kezelheti az Azure Virtuális gépen futó, az Azure-portálon futó, biztonsági másolatot, olvassa el az [SAP HANA-adatbázisok kezelése az Azure VM-ben](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) című biztonsági másolatot.
