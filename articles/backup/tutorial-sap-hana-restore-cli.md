---
title: Oktatóanyag – SAP HANA DB-visszaállítás az Azure-ban a CLI használatával
description: Ebben az oktatóanyagban megtudhatja, hogyan állíthatja vissza az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat egy Azure Backup Recovery Services-tárolóból az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472071"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: SAP HANA-adatbázisok visszaállítása egy Azure virtuális gépben az Azure CLI használatával

Az Azure CLI segítségével azure-erőforrásokat hozhat létre és kezelhet a parancssorból vagy parancsfájlokon keresztül. Ez a dokumentáció részletezi, hogyan lehet visszaállítani egy biztonsági másolatot készíteni AZ SAP HANA-adatbázis egy Azure virtuális gép - az Azure CLI használatával. Ezeket a lépéseket az [Azure Portal](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)használatával is végrehajthatja.

Az [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) használatával cli-parancsokat futtat.

Végére ez a bemutató akkor képes lesz arra, hogy:

> [!div class="checklist"]
>
> * Biztonsági másolatok visszaállításának megtekintése
> * Adatbázis helyreállítása

Ez az oktatóanyag feltételezi, hogy egy SAP HANA-adatbázis fut az Azure virtuális gép, amely az Azure Backup biztonsági mentést. Ha az [SAP HANA-adatbázis biztonsági mentése az Azure-ban a CLI használatával](tutorial-sap-hana-backup-cli.md) az SAP HANA-adatbázis biztonsági mentése, majd használja a következő erőforrásokat:

* *saphanaResourceGroup* nevű erőforráscsoport
* *egy saphanaVault* nevű trezor
* VMAppContainer nevű védett *tároló; Számítás;saphanaResourceGroup;saphanaVM*
* biztonsági másolatot készíteni *adatbázis/saphanadatabase nevű elem;hxe;hxe*
* források a *westus2* régióban

## <a name="view-restore-points-for-a-backed-up-database"></a>Biztonsági másolatok visszaállításának megtekintése

Az adatbázis összes helyreállítási pontjának listájának megtekintéséhez használja az [az biztonsági mentés helyreállítási pontlistájának](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) parancsmagját az alábbiak szerint:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

A helyreállítási pontok listája a következőképpen fog kinézni:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Mint látható, a fenti lista három helyreállítási pontot tartalmaz: egyet-egyet a teljes, különbözeti és napló biztonsági mentéshez.

>[!NOTE]
>Megtekintheti az összes töretlen naplóbiztonsági lánc kezdő és végpontjait is az [az biztonsági mentési helyreállítási pont show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) parancsmag használatával.

## <a name="prerequisites-to-restore-a-database"></a>Adatbázis visszaállításának előfeltételei

Az adatbázis visszaállítása előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Az adatbázis t csak egy SAP HANA-példány, amely ugyanabban a régióban
* A célpéldányt ugyanazzal a tárolóval kell regisztrálni, mint a forrást.
* Az Azure Backup nem tud azonosítani két különböző SAP HANA-példányok ugyanazon a virtuális gépen. Ezért az adatok visszaállítása az egyik példányból a másikba ugyanazon a virtuális gépen nem lehetséges.

## <a name="restore-a-database"></a>Adatbázis helyreállítása

Az Azure Backup az azure-beli virtuális gépeken futó SAP HANA-adatbázisokat az alábbiak szerint állíthatja vissza:

* Visszaállítás egy adott dátumra vagy időpontra (a másodikra) naplóbiztonsági mentések használatával. Az Azure Backup automatikusan meghatározza a megfelelő teljes, különbözeti biztonsági mentések és a napló biztonsági mentések láncolatát, amelyek a kiválasztott idő alapján visszaállíthatók.
* Visszaállítás egy adott teljes vagy különbözeti biztonsági mentésre egy adott helyreállítási pontra való visszaállításhoz.

Adatbázis visszaállításához használja az [az restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) parancsmag, amely megköveteli a helyreállítási konfigurációs objektum, mint az egyik bemenet. Ez az objektum az [az biztonsági mentés helyreállítási konfigurációs show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) parancsmaghasználatával generálható. A helyreállítási konfigurációs objektum tartalmazza a visszaállítás hozandó összes részletet. Az egyik a visszaállítási mód – **OriginalWorkloadRestore** vagy **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** - Állítsa vissza az adatokat az eredeti forrással azonos SAP HANA-példányra. Ez a beállítás felülírja az eredeti adatbázist. <br>
> **AlternateWorkloadRestore** - Állítsa vissza az adatbázist egy másik helyre, és tartsa meg az eredeti forrásadatbázist.

## <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

Ha egy adatbázist vissza szeretne állítani egy másik helyre, használja az **AlternateWorkloadRestore-t** visszaállítási módként. Ezután ki kell választania a visszaállítási pontot, amely lehet egy korábbi időpont, vagy az előző visszaállítási pontok bármelyike.

Ebben az oktatóanyagban egy korábbi visszaállítási pontra állíthatja vissza. Tekintse meg az adatbázis [visszaállítási pontjainak listáját,](#view-restore-points-for-a-backed-up-database) és válassza ki azt a pontot, amelybe vissza szeretné állítani. Ez az oktatóanyag a *7660777527047692711*nevű visszaállítási pontot fogja használni.

A fenti visszaállítási pont nevét és a visszaállítási módot, hozzuk létre a helyreállítási konfigurációs objektum ot az [az biztonsági mentés recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) parancsmag használatával. Nézzük meg, mit jelentenek a parancsmagban fennmaradó paraméterek:

* **--cél-elem-név** Ez az a név, amelyet a visszaállított adatbázis használni fog. Ebben az esetben a *restored_database*nevet használtuk.
* **--cél-kiszolgáló-név** Ez egy SAP HANA-kiszolgáló neve, amely sikeresen regisztrálva van egy helyreállítási szolgáltatások tárolójában, és ugyanabban a régióban található, mint a visszaállítandó adatbázis. Ebben az oktatóanyagban visszaállítjuk az adatbázist ugyanarra az SAP HANA-kiszolgálóra, amelyet védtünk, *hxehost*néven.
* **--cél-szerver-típusú** Az SAP HANA-adatbázisok visszaállításához **saphanadatabase** kell használni.

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

A fenti lekérdezésre adott válasz egy helyreállítási konfigurációs objektum lesz, amely valahogy így néz ki:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Most az adatbázis visszaállításához futtassa az [az restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) parancsmag. Ahhoz, hogy ezt a parancsot, akkor adja meg a fenti json kimenet, amely menti a fájl nevű *recoveryconfig.json*.

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

A válasz megadja a feladat nevét. Ez a feladatnév a feladat állapotának nyomon követésére használható az [biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag használatával.

## <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

Az eredeti helyre való visszaállításhoz az **OrignialWorkloadRestore-t** használjuk visszaállítási módként. Ezután ki kell választania a visszaállítási pontot, amely lehet egy korábbi időpont, vagy az előző visszaállítási pontok bármelyike.

Ebben az oktatóanyagban az előző "28-11-2019-09:53:00" pontot választjuk a visszaállításhoz. Ezt a visszaállítási pontot a következő formátumokban adja meg: dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss. Ha érvényes időpontban történő visszaállításhoz szükséges időpontot szeretne választani, használja az [az biztonsági mentéshelyreállítási pontot show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) parancsmag, amely felsorolja a töretlen naplólánc biztonsági mentések időközeit.

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

Most az adatbázis visszaállításához futtassa az [az restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) parancsmag. Ahhoz, hogy ezt a parancsot, akkor adja meg a fenti json kimenet, amely menti a fájl nevű *recoveryconfig.json*.

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

A válasz megadja a feladat nevét. Ez a feladatnév a feladat állapotának nyomon követésére használható az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag használatával.

## <a name="next-steps"></a>További lépések

* Ha meg szeretné tudni, hogyan kezelheti az Azure CLI használatával biztonsági másolatot tartalmazó SAP HANA-adatbázisokat, folytassa az [oktatóanyaggal SAP HANA-adatbázis kezelése az Azure virtuális gépben a CLI használatával](tutorial-sap-hana-backup-cli.md)

* Ha meg szeretné tudni, hogyan állíthatja vissza az Azure virtuális gépben futó SAP HANA-adatbázist az Azure Portalon, olvassa [el az SAP HANA-adatbázisok visszaállítása az Azure virtuális gépeken](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) című részt.
