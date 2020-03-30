---
title: Azure HPC-gyorsítótár csatlakoztatása
description: Ügyfelek csatlakoztatása Azure HPC cache szolgáltatáshoz
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582217"
---
# <a name="mount-the-azure-hpc-cache"></a>Az Azure HPC-gyorsítótár csatlakoztatása

A gyorsítótár létrehozása után az NFS-ügyfelek egy egyszerű csatlakoztatási paranccsal érhetik el azt.

A csatlakoztatási parancs két elemből áll:

* A gyorsítótár egyik csatlakoztatási címe (a gyorsítótár áttekintő oldalán található)
* A tárolócél létrehozásakor beállított virtuális névtérelérési út

![képernyőkép az Azure HPC Cache-példány áttekintése lapjáról, a jobb alsó sarokban lévő csatlakoztatási címek listája körül egy kiemelési mezővel](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> A gyorsítótár-csatlakoztatási címek a gyorsítótár alhálózatán belüli hálózati adaptereknek felelnek meg. Egy erőforráscsoportban ezek a hálózati adapterek egy `-cluster-nic-` számés szám mal végződő nevekkel vannak felsorolva. Ne módosítsa vagy törölje ezeket az összeköttetéseket, mert a gyorsítótár elérhetetlenné válik.

A virtuális névtér elérési útjai a **Tárolási célok** lapon jelennek meg. Kattintson egy adott tároló célnevére a részletek megtekintéséhez, beleértve a hozzá társított összesített névtérelérési utakat is.

![képernyőkép a gyorsítótár Tároló célpaneljéről, a táblázat Görbe oszlopában lévő bejegyzés körül egy kiemelési mezővel](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Csatlakoztatás parancs szintaxisa

Használjon a következőhez hasonló csatlakoztatási parancsot:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*opciók*}

Példa:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Miután ez a parancs sikeres, a tároló exportálásának tartalmának láthatónak kell lennie az ``hpccache`` ügyfél könyvtárában.

> [!NOTE] 
> Az ügyfeleknek hozzá kell tudniuk férni a gyorsítótárat tartalmazó virtuális hálózathoz és alhálózathoz. Hozzon létre például ügyfélvirtuális gépeket ugyanazon a virtuális hálózaton belül, vagy használjon végpontot, átjárót vagy más megoldást a virtuális hálózatban a külső hozzáféréshez. Ne feledje, hogy semmi más nem lehet a gyorsítótár alhálózatán belül kiosztani.

### <a name="mount-command-options"></a>Csatlakoztatási parancs beállításai

Robusztus ügyfélcsatlakoztatás esetén adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Ajánlott csatlakoztatási parancs beállításai | |
--- | --- 
``hard`` | Az Azure HPC-gyorsítótárhoz való ideiglenes csatlakoztatások alkalmazáshibákkal és esetleges adatvesztéssel vannak társítva. 
``proto=netid`` | Ez a beállítás támogatja az NFS hálózati hibák megfelelő kezelését.
``mountproto=netid`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=n`` | Állítsa ``retry=30`` be, hogy elkerülje az átmeneti csatlakoztatási hibákat. (Az előtér-tartókban más érték ajánlott.)

## <a name="next-steps"></a>További lépések

* Ha adatokat szeretne áthelyezni a gyorsítótár tárolási céljaira, olvassa el [az új Azure Blob-tárterület feltöltése](hpc-cache-ingest.md)című részt.
