---
title: Azure HPC-gyorsítótár csatlakoztatása
description: Ügyfelek csatlakoztatása Azure HPC cache szolgáltatáshoz
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458369"
---
# <a name="mount-the-azure-hpc-cache"></a>Az Azure HPC-gyorsítótár csatlakoztatása

A gyorsítótár létrehozása után az NFS-ügyfelek `mount` egy egyszerű paranccsal érhetik el azt. A parancs egy adott tárolási célelérési utat az Azure HPC-gyorsítótáregy helyi könyvtárhoz az ügyfélgépen.

A csatlakoztatási parancs a következő elemekből áll:

* A gyorsítótár egyik csatlakoztatási címe (a gyorsítótár áttekintő oldalán található)
* A tárolócél létrehozásakor beállított virtuális névtérelérési út
* Az ügyfélen használandó helyi elérési út
* Parancsparaméterek, amelyek optimalizálják az ilyen típusú NFS-csatlakoztatás sikerét

A gyorsítótár **csatlakoztatási utasítások** lapja összegyűjti az információkat és az ajánlott beállításokat, és létrehoz egy prototípus csatlakoztatási parancsot, amelyet másolhat. Olvassa [el A szerelési útmutató segédprogram használata](#use-the-mount-instructions-utility) a részletekért.

## <a name="prepare-clients"></a>Ügyfelek előkészítése

Győződjön meg arról, hogy az ügyfelek képesek csatlakoztatni az Azure HPC-gyorsítótár at az ebben a szakaszban szereplő irányelvek betartásával.

### <a name="provide-network-access"></a>Hálózati hozzáférés biztosítása

Az ügyfélgépeknek hálózati hozzáféréssel kell rendelkezniük a gyorsítótár virtuális hálózatához és magánalhálózatához.

Hozzon létre például ügyfélvirtuális gépeket ugyanazon a virtuális hálózaton belül, vagy használjon végpontot, átjárót vagy más megoldást a virtuális hálózatban a külső hozzáféréshez. (Ne feledje, hogy magának a gyorsítótárnak csak a gyorsítótárnak kell a gyorsítótár alhálózatán belül lennie.)

### <a name="install-utilities"></a>Segédprogramok telepítése

Telepítse a megfelelő Linux segédprogramot, hogy támogassa az NFS csatlakoztatási parancsot:

* Red Hat Enterprise Linux vagy SuSE esetén:`sudo yum install -y nfs-utils`
* Ubuntu vagy Debian esetén:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Helyi elérési út létrehozása

Hozzon létre egy helyi könyvtárelérési utat minden ügyfélen a gyorsítótárhoz való csatlakozáshoz. Hozzon létre egy elérési utat a csatlakoztatni kívánt névtérelérési utakhoz.

Például: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Az Azure [Portalon](#use-the-mount-instructions-utility) található Mount utasítások lap tartalmaz egy prototípus parancsot, amelyet másolhat.

Amikor csatlakoztatja az ügyfélgépet a gyorsítótárhoz, ezt az elérési utat egy virtuális névtérelérési úthoz társítja, amely egy tárolási cél exportálását jelöli. Könyvtárak létrehozása az ügyfél által használt virtuális névtérelérési utakhoz.

## <a name="use-the-mount-instructions-utility"></a>A csatlakoztatási útmutató segédprogram használata

Az Azure **Portalon** a Mount utasítások lapon egy másolható csatlakoztatási parancs létrehozásához. Nyissa meg a lapot a portál gyorsítótárnézetének **Konfigurálás** szakaszában.

Mielőtt egy ügyfélen használná a parancsot, győződjön meg arról, hogy az `mount` ügyfél megfelel az előfeltételeknek, és rendelkezik az NFS parancs használatához szükséges szoftverrel, ahogy azt az Ügyfelek előkészítése című részben [leírtak szerint leírtuk.](#prepare-clients)

![képernyőkép egy Azure HPC-gyorsítótár-példányról a portálon, > csatlakoztatási utasítások konfigurálása lap](media/mount-instructions.png)

A csatlakoztatási parancs létrehozásához kövesse az alábbi eljárást.

1. Az **Ügyfél elérési útja** mező testreszabása. Ez a mező egy példaparancsot ad, amelynek segítségével helyi elérési utat hozhat létre az ügyfélen. Az ügyfél az Azure HPC-gyorsítótárból éri el a tartalmat ebben a címtárban.

   Kattintson a mezőre, és a kívánt könyvtárnevet tartalmazó parancs szerkesztése. A név a karakterlánc végén jelenik meg, miután`sudo mkdir -p`

   ![képernyőkép az ügyfél elérési út mezőjéről, a végén elhelyezett kurzorral](media/mount-edit-client.png)

   A mező szerkesztésének befejezése után a lap alján található csatlakoztatási parancs az új ügyfélelérési úttal frissül.

1. Válassza ki a **gyorsítótár-csatlakoztatás címét** a listából. Ez a menü a gyorsítótár összes [ügyfélcsatlakozási pontját](#find-mount-command-components)sorolja fel.

   A gyorsítótár jobb teljesítménye érdekében egyensúlyba hozza az ügyfélterhelést az összes rendelkezésre álló csatlakoztatási cím között.

   ![képernyőkép a gyorsítótár-csatlakoztatási címmezőről, a választóval három IP-cím közül választhat](media/mount-select-ip.png)

1. Válassza ki az ügyfél számára használni kívánt **virtuális névtér elérési útját.** Ezek az elérési utak a háttértároló rendszer exportálásához kapcsolódnak.

   ![képernyőkép a névtér elérési útjaimezőről, megnyitott választóval](media/mount-select-target.png)

   A virtuális névtér elérési útjait a Storage targets portal lapon tekintheti meg és módosíthatja. Olvassa [el a Tárolási célok hozzáadása című témakört.](hpc-cache-add-storage.md)

   Ha többet szeretne megtudni az Azure HPC Cache összesített névtér funkciójáról, olvassa el [az összesített névtér megtervezése](hpc-cache-namespace.md)című olvasni.

1. A harmadik lépés **Mount parancsmezője** automatikusan feltölti a testreszabott csatlakoztatási parancsot, amely az előző mezőkben beállított csatlakoztatási címet, virtuális névtér-útvonalat és ügyfélútvonalat használja.

   Kattintson a mező jobb oldalán található másolásszimbólumra, hogy automatikusan átmásolja a vágólapra.

   ![képernyőkép a névtér elérési útjaimezőről, megnyitott választóval](media/mount-command-copy.png)

1. Használja a másolt csatlakoztatási parancsot az ügyfélgépen az Azure HPC-gyorsítótárhoz való csatlakoztatásához. A parancsot közvetlenül az ügyfél parancssorából is kiadhatja, vagy a csatlakoztatási parancsot beillesztheti egy ügyféltelepítő parancsfájlba vagy sablonba.

## <a name="understand-mount-command-syntax"></a>A csatlakoztatási parancs szintaxisának ismertetése

A csatlakoztatási parancs a következő formában jelenik meg:

> sudo mount {*options*} *cache_mount_address*:/*namespace_path* *local_path*

Példa:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Miután ez a parancs sikeres, a tároló exportálásának tartalma látható lesz az ``hpccache`` ügyfél könyvtárában.

### <a name="mount-command-options"></a>Csatlakoztatási parancs beállításai

Robusztus ügyfélcsatlakoztatás esetén adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban:

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Ajánlott csatlakoztatási parancs beállításai | |
--- | ---
``hard`` | Az Azure HPC-gyorsítótárhoz való ideiglenes csatlakoztatások alkalmazáshibákkal és esetleges adatvesztéssel vannak társítva.
``proto=tcp`` | Ez a beállítás támogatja az NFS hálózati hibák megfelelő kezelését.
``mountproto=tcp`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=<value>`` | Állítsa ``retry=30`` be, hogy elkerülje az átmeneti csatlakoztatási hibákat. (Az előtér-tartókban más érték ajánlott.)

### <a name="find-mount-command-components"></a>Csatlakoztatási parancsösszetevőinek keresése

Ha a csatlakoztatási parancs csatlakoztatása parancs használata nélkül szeretne létrehozni a **Csatlakoztatási utasítások** lapot, megtalálhatja a csatlakoztatási címeket a gyorsítótár **áttekintése** lapon és a virtuális névtér elérési útjait a **Storage céllapon.**

![képernyőkép az Azure HPC Cache-példány áttekintése lapjáról, a jobb alsó sarokban lévő csatlakoztatási címek listája körül egy kiemelési mezővel](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> A gyorsítótár-csatlakoztatási címek a gyorsítótár alhálózatán belüli hálózati adaptereknek felelnek meg. Egy erőforráscsoportban ezek a hálózati adapterek egy `-cluster-nic-` számés szám mal végződő nevekkel vannak felsorolva. Ne módosítsa vagy törölje ezeket az összeköttetéseket, mert a gyorsítótár elérhetetlenné válik.

A virtuális névtér elérési útjai az egyes tárolók részletei lapon jelennek meg. Kattintson egy adott tároló célnevére a részletek megtekintéséhez, beleértve a hozzá társított összesített névtérelérési utakat is.

![a tárolócél részletes oldalának képernyőképe ("Tárhelycél frissítése" fejléc). A tábla Virtuális névtér elérési útjának egyik bejegyzése körül van egy kiemelési mező.](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>További lépések

* Ha adatokat szeretne áthelyezni a gyorsítótár tárolási céljaira, olvassa el [az új Azure Blob-tárterület feltöltése](hpc-cache-ingest.md)című részt.
