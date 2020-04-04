---
title: Azure HPC-gyorsítótár csatlakoztatása
description: Ügyfelek csatlakoztatása Azure HPC cache szolgáltatáshoz
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657354"
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

Hozzon létre egy helyi könyvtárelérési utat minden ügyfélen a gyorsítótárhoz való csatlakozáshoz. Hozzon létre egy elérési utat minden csatlakoztatni kívánt tárolócélhoz.

Például: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>A csatlakoztatási útmutató segédprogram használata

Nyissa meg a **Csatlakoztatási utasítások** lapot a **konfigurálása** szakasza a gyorsítótár nézet az Azure Portalon.

![képernyőkép egy Azure HPC-gyorsítótár-példányról a portálon, > csatlakoztatási utasítások konfigurálása lap](media/mount-instructions.png)

A csatlakoztatási parancslap információkat tartalmaz az ügyfélcsatlakoztatási folyamatról és az előfeltételekről, valamint a másolható csatlakoztatási parancs létrehozásához használható mezőket.

Az oldal használatához kövesse az alábbi eljárást:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Tekintse át az ügyfél előfeltételeit, és telepítse `mount` az NFS parancs használatához szükséges segédprogramokat az Ügyfelek előkészítése című témakörben leírtak [szerint.](#prepare-clients)

1. Első lépés **a fájlrendszer felszerelése**<!-- label will change --> példaparancsot ad az ügyfél helyi elérési útjának létrehozására. Ez az az elérési út, amelyet az ügyfél az Azure HPC-gyorsítótárból származó tartalom eléréséhez fog használni.

   Jegyezze fel az elérési út nevét, hogy szükség esetén módosíthassa azt a parancsban.

1. A második lépésben válassza ki az egyik elérhető IP-címet. A gyorsítótár összes [ügyfélcsatlakoztatási pontja](#find-mount-command-components) itt található. Győződjön meg arról, hogy rendelkezik egy olyan rendszerrel, amely egyensúlyt teremt az összes IP-cím között.

1. A harmadik lépés mezője automatikusan feltölti a prototípus tartó parancsot. Kattintson a mező jobb oldalán található másolásszimbólumra, hogy automatikusan átmásolja a vágólapra.

   > [!NOTE]
   > Használat előtt ellenőrizze a másolás parancsot. Előfordulhat, hogy testre kell szabnia az ügyfélcsatlakoztatás útvonalát és a tárolócél virtuális névtér elérési útját, amely még nem választható ki ezen a kapcsolaton. A csatlakoztatási parancs beállításait is frissítenie kell, hogy azok tükrözzék az alábbi [ajánlott beállításokat.](#mount-command-options) Olvassa [el A csatlakoztatási parancs szintaxisának](#understand-mount-command-syntax) ismertetése a súgót.

1. Használja a másolt csatlakoztatási parancsot (szükség esetén szerkesztésekkel) az ügyfélgépen az Azure HPC-gyorsítótár tárolási céljához való csatlakoztatásához. A parancsot közvetlenül az ügyfél parancssorából is kiadhatja, vagy a csatlakoztatási parancsot beillesztheti egy ügyféltelepítő parancsfájlba vagy sablonba.

## <a name="understand-mount-command-syntax"></a>A csatlakoztatási parancs szintaxisának ismertetése

A csatlakoztatási parancs a következő formában jelenik meg:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*opciók*}

Példa:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
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

Ha a csatlakoztatási parancs **csatlakoztatása parancs** használata nélkül szeretne létrehozni a Csatlakoztatási utasítások lapot, megtalálhatja a csatlakoztatási címeket a gyorsítótár **áttekintése** lapon és a virtuális névtér elérési útjait a **Tárolási célok** lapon.

![képernyőkép az Azure HPC Cache-példány áttekintése lapjáról, a jobb alsó sarokban lévő csatlakoztatási címek listája körül egy kiemelési mezővel](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> A gyorsítótár-csatlakoztatási címek a gyorsítótár alhálózatán belüli hálózati adaptereknek felelnek meg. Egy erőforráscsoportban ezek a hálózati adapterek egy `-cluster-nic-` számés szám mal végződő nevekkel vannak felsorolva. Ne módosítsa vagy törölje ezeket az összeköttetéseket, mert a gyorsítótár elérhetetlenné válik.

A virtuális névtér elérési útjai a **Tárolási célok** lapon jelennek meg. Kattintson egy adott tároló célnevére a részletek megtekintéséhez, beleértve a hozzá társított összesített névtérelérési utakat is.

![képernyőkép a gyorsítótár Tároló célpaneljéről, a táblázat Görbe oszlopában lévő bejegyzés körül egy kiemelési mezővel](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>További lépések

* Ha adatokat szeretne áthelyezni a gyorsítótár tárolási céljaira, olvassa el [az új Azure Blob-tárterület feltöltése](hpc-cache-ingest.md)című részt.
