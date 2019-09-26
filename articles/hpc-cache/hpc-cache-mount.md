---
title: Azure HPC-gyorsítótár csatlakoztatása (előzetes verzió)
description: Ügyfelek összekötése az Azure HPC cache szolgáltatással
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: v-erkell
ms.openlocfilehash: ea23331ebc75b5ede22c9f7357a9e0de12d819e2
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299966"
---
# <a name="mount-the-azure-hpc-cache-preview"></a>Az Azure HPC cache csatlakoztatása (előzetes verzió)

A gyorsítótár létrehozása után az NFS-ügyfelek egy egyszerű csatlakoztatási paranccsal férhetnek hozzá.

A csatlakoztatási parancs két elemből áll:

* A gyorsítótár csatlakoztatási címeinek egyike (a gyorsítótár áttekintése oldalon látható)
* A tárolási cél létrehozásakor beállított virtuális névtér elérési útja

![képernyőkép az Azure HPC cache-példány áttekintő oldaláról, amely a jobb alsó sarokban lévő csatlakoztatási címek listája körüli kiemelési lista](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> A gyorsítótár-csatlakoztatási címek a gyorsítótár alhálózatán belüli hálózati adaptereknek felelnek meg. Egy erőforráscsoport esetében ezek a hálózati adapterek a (z) `-cluster-nic-` és egy szám végződésű névvel vannak felsorolva. Ne módosítsa vagy törölje ezeket a csatolókat, vagy a gyorsítótár elérhetetlenné válik.

A virtuális névtér elérési útjai a **tárolási célok** lapon jelennek meg. Kattintson egy egyedi tárolási cél nevére a részletek megjelenítéséhez, beleértve a hozzájuk társított összesített névtér-elérési utakat is.

![képernyőfelvétel a gyorsítótár tárolási céljáról panelről, a tábla Path oszlopában lévő egyik mező körül](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Csatlakoztatási parancs szintaxisa

A következőhöz hasonló csatlakoztatási parancsot használjon:

> sudo Mount *cache_mount_address*:/*namespace_path* *local_path* {*Options*}

Példa:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

A parancs sikeres végrehajtása után a tároló- ``hpccache`` exportálás tartalmának meg kell jelennie az ügyfél címtárában.

> [!NOTE] 
> Az ügyfeleknek el kell tudniuk érni a gyorsítótárat üzemeltető virtuális hálózatot és alhálózatot. Hozzon létre például egy virtuális hálózaton belül az ügyfél virtuális gépeket, vagy használjon végpontot, átjárót vagy más megoldást a virtuális hálózaton kívülről való hozzáféréshez. Ne feledje, hogy a gyorsítótár alhálózatán belül semmi más nem üzemeltethető.

### <a name="mount-command-options"></a>Csatlakoztatási parancs beállításai

Robusztus ügyfél csatlakoztatása esetén adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Javasolt csatlakoztatási parancs beállításai | |
--- | --- 
``hard`` | Az Azure HPC cache-hez való Soft mounts az alkalmazás hibáival és az esetleges adatvesztéssel jár. 
``proto=netid`` | Ez a beállítás támogatja az NFS-hálózati hibák megfelelő kezelését.
``mountproto=netid`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=n`` | Állítsa ``retry=30`` be az átmeneti csatlakoztatási hibák elkerülését. (Az előtér-csatlakoztatásokban egy másik érték használata javasolt.)

## <a name="next-steps"></a>További lépések

* Ha adatokat szeretne áthelyezni a gyorsítótár tárolási céljaira, olvassa el az [új Azure Blob Storage feltöltése](hpc-cache-ingest.md)című cikkét.
