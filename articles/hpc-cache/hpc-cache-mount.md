---
title: Azure HPC-gyorsítótár csatlakoztatása (előzetes verzió)
description: Ügyfelek összekötése az Azure HPC cache szolgáltatással
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: v-erkell
ms.openlocfilehash: 3b7a59afa0dea300e200b953d045d38218e99b22
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180925"
---
# <a name="mount-the-azure-hpc-cache-preview"></a>Az Azure HPC cache csatlakoztatása (előzetes verzió)

A gyorsítótár létrehozása után az NFS-ügyfelek egy egyszerű csatlakoztatási paranccsal férhetnek hozzá.

Használja a gyorsítótár áttekintése oldalon felsorolt csatlakoztatási címeket, valamint a tárolási cél létrehozásakor beállított virtuális névtér elérési útját. 

![képernyőkép az Azure HPC cache-példány áttekintő oldaláról, amely a jobb alsó sarokban lévő csatlakoztatási címek listája körüli kiemelési lista](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> A gyorsítótár-csatlakoztatási címek a gyorsítótár alhálózatán belüli hálózati adaptereknek felelnek meg. Ezek a hálózati adapterek az erőforráscsoport `-cluster-nic-` és egy szám végződésű névvel jelennek meg. Ne módosítsa vagy törölje ezeket a csatolókat, vagy a gyorsítótár elérhetetlenné válik.

A virtuális névtér elérési útjai a **tárolási célok** lapon jelennek meg. Kattintson a cél nevére a részletek megjelenítéséhez, beleértve a hozzá társított összesített névtér elérési útját (vagy elérési útvonalát). 
![képernyőfelvétel a gyorsítótár tárolási céljáról panelről, a tábla Path oszlopában lévő egyik mező körül](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Csatlakoztatási parancs szintaxisa

A következőhöz hasonló csatlakoztatási parancsot használjon:

> sudo Mount *cache_mount_address*:/*namespace_path* *local_path* {*Options*}

Példa: 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

A parancs sikeres végrehajtása után a tároló- ``hpccache`` exportálás tartalmának meg kell jelennie az ügyfél címtárában. 

> [!NOTE] 
> Az ügyfeleknek el kell tudniuk érni a gyorsítótárat üzemeltető virtuális hálózatot és alhálózatot. Hozzon létre például egy virtuális hálózaton belül az ügyfél virtuális gépeket, vagy használjon végpontot, átjárót vagy más megoldást a virtuális hálózaton kívülről való hozzáféréshez. Ne feledje, hogy a gyorsítótár alhálózatán belül semmi más nem üzemeltethető.

### <a name="mount-command-options"></a>Csatlakoztatási parancs beállításai

Robusztus ügyfél csatlakoztatása esetén adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Javasolt csatlakoztatási parancs beállításai | |
--- | --- 
``hard`` | Az Azure HPC cache-hez való Soft mounts az alkalmazás hibáival és az esetleges adatvesztéssel jár. 
``proto=netid`` | Ez a beállítás támogatja az NFS-hálózati hibák megfelelő kezelését.
``mountproto=netid`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=n`` | Állítsa ``retry=30`` be az átmeneti csatlakoztatási hibák elkerülését. (Az előtér-csatlakoztatásokban egy másik érték használata javasolt.)

## <a name="next-steps"></a>További lépések

* Ha adatokat szeretne áthelyezni a gyorsítótár tárolási céljaira, olvassa el az [új Azure Blob Storage feltöltése](hpc-cache-ingest.md)című cikkét.
