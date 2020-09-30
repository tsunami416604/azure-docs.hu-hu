---
title: Biztonsági mentés és visszaállítás a rugalmas skálázású Azure Database for PostgreSQL-kiszolgálócsoportokhoz
description: Biztonsági mentés és visszaállítás a rugalmas skálázású Azure Database for PostgreSQL-kiszolgálócsoportokhoz
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dde4db7f3eb476b7645e910504e48fea8bb6df0c
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569721"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoportok biztonsági mentése és visszaállítása

Megteheti az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport teljes biztonsági mentését és visszaállítását. Ha így tesz, az Azure arc-alapú PostgreSQL nagy kapacitású-kiszolgálócsoport összes csomópontján lévő teljes adatbázis biztonsági mentése és/vagy visszaállítása történik.
Ha biztonsági mentést szeretne készíteni, és vissza szeretné állítani, győződjön meg róla, hogy a kiszolgálócsoport számára konfigurálva van egy biztonságimásolat-tárolási osztály. Egyelőre meg kell adnia egy biztonsági mentési tárolási osztályt a kiszolgálócsoport létrehozásakor. A kiszolgálócsoport a létrehozása után nem állítható be a biztonságimásolat-tárolási osztály használatára.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> Az előnézet nem támogatja a postgres motor 11-es verziójának biztonsági mentését és visszaállítását. A szolgáltatás csak a 12-es verziójú postgres biztonsági mentését és visszaállítását támogatja.

## <a name="verify-configuration"></a>Konfiguráció ellenőrzése

Először ellenőrizze, hogy a meglévő kiszolgálócsoport konfigurálva van-e a biztonsági mentési tár osztály használatára.

Futtassa a következő parancsot a kiszolgálócsoport nevének beállítása után:
```console
 azdata arc postgres server show -n postgres01
```
Tekintse meg a kimenet tárolási szakaszát:
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
Ha megjelenik a parancs kimenetének "biztonsági mentések" szakaszában szereplő tárolási osztály neve, az azt jelenti, hogy a kiszolgálói csoport egy biztonsági mentési tárolási osztály használatára lett konfigurálva, és készen áll a biztonsági másolatok készítésére és a visszaállításokra. Ha nem látja a "biztonsági mentések" szakaszt, törölnie kell, majd újra létre kell hoznia a kiszolgálói csoportot a biztonsági mentési tár osztályának konfigurálásához. Ezen a ponton még nem lehet biztonsági mentési tárolási osztályt konfigurálni a kiszolgálócsoport létrehozása után.

>[!IMPORTANT]
>Ha a kiszolgálócsoport már konfigurálva van a biztonsági mentési tárolási osztály használatára, ugorja át a következő lépést, és lépjen közvetlenül a "manuális teljes biztonsági mentés" lépésre.

## <a name="create-a-server-group"></a>Kiszolgálócsoport létrehozása 

Ezután hozzon létre egy, a biztonsági mentéshez és a visszaállításhoz konfigurált kiszolgálócsoport-csoportot.

A biztonsági másolatok készítéséhez és visszaállításához létre kell hoznia egy tárolási osztállyal konfigurált kiszolgálót.

A Kubernetes-fürtön elérhető tárolási osztályok listájának lekéréséhez futtassa a következő parancsot:

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

Például, ha létrehozott egy egyszerű környezetet a kubeadm alapján:
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>Manuális teljes biztonsági mentés készítése

Ezután készítsen manuális teljes biztonsági mentést.

A következő parancs futtatásával teljes biztonsági mentést készíthet a kiszolgálócsoport összes adat-és naplófájl-mappájáról:

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Ebben a példában:
- a __név__ a biztonsági másolat nevét jelzi.
- a __kiszolgáló neve__ egy kiszolgálócsoport-csoportot jelöl
- a __No-WAIT__ érték azt jelzi, hogy a parancssor nem várja meg a biztonsági mentés befejeződését, hogy továbbra is használni tudja ezt a parancssori ablakot.

>**Megjegyzés**: az a parancs, amely lehetővé teszi a visszaállításhoz elérhető biztonsági másolatok listázását, a biztonsági mentés dátumát és időpontját. Ezért javasoljuk, hogy adjon nevet a biztonsági mentésnek (a--name paraméterrel), amely tartalmazza a dátum-és időadatokat.

Ez a parancs egy elosztott teljes biztonsági mentést koordinál az összes olyan csomóponton, amely az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport részét képezi. Más szóval a koordinátor és a munkavégző csomópontok összes adatait biztonsági másolatot készít.

Például:
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

A biztonsági mentés befejezésekor a rendszer a biztonsági másolat AZONOSÍTÓját, nevét és állapotát adja vissza. Például:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> A következő lehetőségek még nem lehetségesek:
> - Automatikus biztonsági mentések ütemezése
> - A biztonsági mentés előrehaladásának megjelenítése a folyamat során

## <a name="list-backups"></a>Biztonsági másolatok listázása

A visszaállításhoz elérhető biztonsági másolatok listázása.

A visszaállítani kívánt biztonsági másolatok listázásához futtassa a következő parancsot:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Például:
```console
azdata arc postgres backup list --server-name postgres01
```

A következőhöz hasonló kimenetet ad vissza:
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

Az időbélyeg azt az időpontot jelzi, amikor a biztonsági mentés történt.

## <a name="restore-a-backup"></a>Visszaállítás biztonsági másolatból

A teljes kiszolgálócsoport biztonsági másolatának visszaállításához futtassa a következő parancsot:

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

Ebben a példában:
- a Backup __-ID__ a Backup (biztonsági mentés) parancsban látható biztonsági másolat azonosítója (lásd a 3. lépést).
Ez összehangolja az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport összes csomópontjának elosztott teljes visszaállítását. Más szóval a koordinátor és a munkavégző csomópontok összes adatait visszaállítja.

Például:
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

A visszaállítási művelet befejezésekor a rendszer a parancssorhoz hasonló kimenetet ad vissza:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> A következő lehetőségek még nem lehetségesek:
> - Biztonsági másolat visszaállítása a név megjelölésével
> - Kiszolgálócsoport visszaállítása másik név vagy másik kiszolgálócsoport alatt
> - Visszaállítási művelet előrehaladásának megjelenítése

## <a name="delete-backups"></a>Biztonsági másolatok törlése
A biztonsági mentés megőrzése nem állítható be előzetes verzióban. Azonban manuálisan is törölheti a nem szükséges biztonsági másolatokat.
A biztonsági másolatok törlésére szolgáló általános parancs a következő:
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
ahol:
- `--server-name` annak a csoportnak a neve, amelyből a felhasználó törölni kívánja a biztonsági mentést
- `--name` a törlendő biztonsági másolat neve
- `-id`a törlendő biztonsági másolat azonosítója

> [!NOTE]
> `--name` és `-id` kölcsönösen kizárják egymást.

A biztonsági másolatok nevének és AZONOSÍTÓjának lekéréséhez futtassa az előző bekezdésben ismertetett biztonsági mentési parancsot.

Tegyük fel például, hogy a következő biztonsági másolatok vannak felsorolva:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
és törölni kívánja az elsőt, a következő parancsot kell futtatnia:
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
Ha ezen a ponton a biztonsági másolatok listáját választotta, a következő kimenetet fogja kapni:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

A DELETE paranccsal kapcsolatos további információkért futtassa a következőt:
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Következő lépések
- További információ a kiszolgálócsoport [kibővítéséről (munkavégző csomópontok hozzáadásáról)](scale-out-postgresql-hyperscale-server-group.md)
- További információ a kiszolgálócsoport [fel-vagy leskálázásáról (a memória/virtuális mag növelése/csökkentése)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
