---
title: Oktatóanyag – SAP HANA adatbázis-visszaállítás az Azure-ban a parancssori felület használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja vissza az Azure-beli virtuális gépen futó SAP HANA-adatbázisokat egy Azure Backup Recovery Services-tárolóból az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472071"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: SAP HANA-adatbázisok visszaállítása Azure-beli virtuális gépen az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére szolgál. Ez a dokumentáció részletesen ismerteti, hogyan lehet visszaállítani egy biztonsági másolattal rendelkező SAP HANA adatbázist egy Azure-beli virtuális gépen – az Azure CLI használatával. Ezeket a lépéseket a [Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)használatával is végrehajthatja.

A CLI-parancsok futtatásához használja a [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) .

Az oktatóanyag végén a következőket teheti:

> [!div class="checklist"]
>
> * Biztonsági másolattal rendelkező adatbázis visszaállítási pontjainak megtekintése
> * Adatbázis helyreállítása

Ez az oktatóanyag feltételezi, hogy rendelkezik egy olyan Azure-beli virtuális gépen futó SAP HANA-adatbázissal, amely biztonsági mentést végez a Azure Backup használatával. Ha az [Azure-ban SAP HANA-adatbázis biztonsági mentését](tutorial-sap-hana-backup-cli.md) HASZNÁLTA a parancssori felület használatával a SAP HANA-adatbázis biztonsági mentéséhez, akkor a következő erőforrásokat fogja használni:

* egy *saphanaResourceGroup* nevű erőforráscsoport
* egy *saphanaVault* nevű tároló
* VMAppContainer nevű védett tároló *; Számítás; saphanaResourceGroup; saphanaVM*
* a (z) *saphanadatabase; hxe; hxe* nevű biztonsági másolati adatbázis/tétel
* a *westus2* régió erőforrásai

## <a name="view-restore-points-for-a-backed-up-database"></a>Biztonsági másolattal rendelkező adatbázis visszaállítási pontjainak megtekintése

Az adatbázishoz tartozó összes helyreállítási pont listájának megtekintéséhez használja az az [Backup recoverypoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) parancsmagot az alábbiak szerint:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

A helyreállítási pontok listája a következőképpen fog megjelenni:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Amint láthatja, a fenti lista három helyreállítási pontot tartalmaz: az egyiket a teljes, a különbözeti és a napló biztonsági mentéshez.

>[!NOTE]
>Megtekintheti az összes nem törött napló biztonsági mentési láncának kezdő-és végpontját az az [Backup recoverypoint show-log-Chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) parancsmag használatával is.

## <a name="prerequisites-to-restore-a-database"></a>Adatbázis visszaállításának előfeltételei

Az adatbázis visszaállítása előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Az adatbázist csak olyan SAP HANA-példányra állíthatja vissza, amely ugyanabban a régióban található
* A cél példányt regisztrálni kell ugyanazzal a tárolóval, mint a forrás
* Azure Backup nem képes két különböző SAP HANA példány azonosítására ugyanazon a virtuális gépen. Ezért nem lehetséges az adatok egyik példányról egy másikra való visszaállítása ugyanazon a virtuális gépen.

## <a name="restore-a-database"></a>Adatbázis helyreállítása

A Azure Backup az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat a következőképpen állíthatja helyre:

* Visszaállítás adott dátumra vagy időpontra (a másodikra) a naplók biztonsági másolatainak használatával. A Azure Backup automatikusan meghatározza a megfelelő teljes, különbözeti biztonsági másolatokat, valamint a visszaállításhoz szükséges napló biztonsági mentésének láncát a kiválasztott idő alapján.
* Egy adott helyreállítási pontra való visszaállításhoz állítsa vissza egy adott teljes vagy különbözeti biztonsági mentést.

Egy adatbázis visszaállításához használja az az Restore [Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) parancsmagot, amelyhez egy helyreállítási konfigurációs objektum szükséges a bemenetek egyikéhez. Ez az objektum az az [Backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) parancsmag használatával hozható létre. A helyreállítási konfigurációs objektum a visszaállítás végrehajtásához szükséges összes adatot tartalmazza. Egyikük a visszaállítási mód – **OriginalWorkloadRestore** vagy **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** – az eredeti forrással megegyező SAP HANA példányra állítja vissza az adathalmazt. Ez a beállítás felülírja az eredeti adatbázist. <br>
> **AlternateWorkloadRestore** – az adatbázist egy másik helyre állítja vissza, és megtartja az eredeti forrás-adatbázist.

## <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

Egy adatbázis másik helyre történő visszaállításához használja a **AlternateWorkloadRestore** visszaállítási módot. Ezután ki kell választania a visszaállítási pontot, amely lehet korábbi időponthoz vagy az előző visszaállítási pontokhoz.

Ebben az oktatóanyagban visszaállítja egy korábbi visszaállítási pontra. [Tekintse meg az adatbázishoz tartozó visszaállítási pontok listáját](#view-restore-points-for-a-backed-up-database) , és válassza ki azt a pontot, amelyet vissza szeretne állítani. Ez az oktatóanyag a *7660777527047692711*nevű visszaállítási pontot fogja használni.

A fenti visszaállítási pont nevét és a visszaállítási módot használva hozzon létre egy helyreállítási konfigurációs objektumot az az [Backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) parancsmag használatával. Nézzük meg, hogy a parancsmag összes többi paramétere mit jelent:

* **--cél-elemnév-név** Ez az a név, amelyet a visszaállított adatbázis fog használni. Ebben az esetben a *restored_database*nevet használtuk.
* **--cél-kiszolgáló neve** Ez egy olyan SAP HANA-kiszolgáló neve, amely sikeresen regisztrálva van egy Recovery Services-tárolóban, és ugyanabban a régióban található, mint a visszaállítani kívánt adatbázis. Ebben az oktatóanyagban visszaállítjuk az adatbázist ugyanarra a SAP HANA kiszolgálóra, amelyet védett, *hxehost*nevezett.
* **--célkiszolgáló-típus** SAP HANA adatbázisok visszaállításához a **SapHanaDatabase** -t kell használni.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

A fenti lekérdezésre adott válasz egy olyan helyreállítási konfigurációs objektum lesz, amely a következőképpen néz ki:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Az adatbázis visszaállításához futtassa az az Restore [Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) parancsmagot. A parancs használatához meg kell adni a fenti JSON-kimenetet, amelyet egy *recoveryconfig. JSON*nevű fájlba mentünk.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A kimenet így fog kinézni:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A válasz megadja a feladatnév nevét. Ez a feladatnév használható a feladatok állapotának nyomon követésére az [az Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag használatával.

## <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

Az eredeti helyre történő visszaállításhoz a **OrignialWorkloadRestore** -t a visszaállítási módként fogjuk használni. Ezután ki kell választania a visszaállítási pontot, amely lehet korábbi időponthoz vagy az előző visszaállítási pontokhoz.

Ebben az oktatóanyagban az előző "28-11-2019-09:53:00" időpontot választjuk vissza a következőre:. Ezt a visszaállítási pontot a következő formátumokban adhatja meg: nn-hh-éééé, nn-hh-éééé-hh: PP: mm. Ha egy érvényes időpontot szeretne visszaállítani a szolgáltatásra, használja az az [Backup recoverypoint show-log-Chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) parancsmagot, amely felsorolja a nem törött naplózási lánc biztonsági másolatainak intervallumait.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

A fenti lekérdezésre adott válasz egy helyreállítási konfigurációs objektum lesz, amely a következőképpen néz ki:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Az adatbázis visszaállításához futtassa az az Restore [Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) parancsmagot. A parancs használatához meg kell adni a fenti JSON-kimenetet, amelyet egy *recoveryconfig. JSON*nevű fájlba mentünk.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A kimenet így fog kinézni:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A válasz megadja a feladatnév nevét. Ez a feladatnév használható a feladatok állapotának nyomon követésére az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag használatával.

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretné tudni, hogyan kezelheti SAP HANA az Azure CLI használatával biztonsági mentést tartalmazó adatbázisokat, folytassa a [SAP HANA-adatbázis kezelése az Azure virtuális gépen a parancssori felület használatával](tutorial-sap-hana-backup-cli.md) című oktatóanyagot.

* Az Azure-beli virtuális gépen futó SAP HANA-adatbázisok a Azure Portal használatával történő visszaállításáról a [SAP HANA adatbázisok visszaállítása Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) -beli virtuális gépeken című témakörben talál további információt.
