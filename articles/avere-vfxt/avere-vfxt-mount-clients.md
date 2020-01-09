---
title: A avere-vFXT csatlakoztatása – Azure
description: Ügyfelek csatlakoztatása az Azure-hoz készült avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 39c4d6a77121e0b52a1da827ebb9e1976f609b30
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415288"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Az Avere vFXT-fürt csatlakoztatása

Kövesse az alábbi lépéseket az ügyfélszámítógépek vFXT-fürthöz való összekapcsolásához.

1. Döntse el, hogyan egyenlítheti ki az ügyfelek forgalmát a fürtcsomópontok között. A részletekért olvassa el az [Egyenleg-ügyfél terhelését](#balance-client-load)alább.
1. Azonosítsa a csatlakoztatni kívánt IP-címet és elágazási útvonalat.
1. Adja ki a [csatlakoztatási parancsot](#mount-command-arguments)a megfelelő argumentumokkal.

## <a name="balance-client-load"></a>Ügyfél terhelésének elosztása

Az ügyfelek kéréseinek a fürt összes csomópontja közötti egyensúlyának biztosításához az ügyfeleket az ügyfél által elérhető IP-címek teljes tartományához kell csatlakoztatnia. A feladat automatizálásának számos egyszerű módja van.

> [!TIP]
> A többi terheléselosztási módszer a nagy vagy bonyolult rendszerek esetében is megfelelő lehet. [Nyisson meg egy támogatási jegyet](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) segítségért.)
>
> Ha inkább DNS-kiszolgálót szeretne használni az automatikus kiszolgálóoldali terheléselosztáshoz, be kell állítania és kezelnie kell a saját DNS-kiszolgálóját az Azure-on belül. Ebben az esetben a vFXT-fürt ciklikus multiplexelés DNS-t konfigurálhatja a következő dokumentum szerint: [avere-fürt DNS-konfigurációja](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Kiegyensúlyozott ügyfél-csatlakoztatási parancsfájl

Ez a példa az ügyfél IP-címeit használja véletlenszerű elemként, amellyel az ügyfeleket az összes vFXT-fürt elérhető IP-címeire terjesztheti.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

A fenti függvény a [avere vFXT-példák](https://github.com/Azure/Avere#tutorials) helyén elérhető batch-példa részét képezi.

## <a name="create-the-mount-command"></a>A csatlakoztatási parancs létrehozása

> [!NOTE]
> Ha nem hozott létre új BLOB-tárolót a avere vFXT-fürt létrehozásakor, kövesse a [tároló konfigurálása](avere-vfxt-add-storage.md) az ügyfelek csatlakoztatása előtt című szakasz lépéseit.

Az ügyféltől a ``mount`` parancs leképezi a virtuális kiszolgálót (VServer) a vFXT-fürtön a helyi fájlrendszer egyik elérési útjára. A formátum ``mount <vFXT path> <local path> {options}``

A csatlakoztatási parancsnak három eleme van:

* vFXT elérési út – (az alábbi, az IP-cím és a névtér csatlakozási útvonalának kombinációja)
* helyi elérési út – az ügyfél elérési útja
* csatlakoztatási parancs beállításai – (a [csatlakoztatási parancs argumentumai](#mount-command-arguments)között szerepel)

### <a name="junction-and-ip"></a>Elágazás és IP

A VServer elérési útja az *IP-címe* és a *névtér-elágazás*elérési útja kombinációja. A névtér-összekapcsolás egy virtuális elérési út, amely a tárolási rendszer hozzáadásakor lett meghatározva.

Ha a fürt blob Storage-sel lett létrehozva, a névtér elérési útja `/msazure`

Például: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Ha a fürt létrehozása után hozzáadta a tárolót, a névtér-elágazás elérési útja megfelel a csomópont létrehozásakor a **névtér elérési** útján megadott értéknek. Ha például a névtér elérési útjaként ``/avere/files`` használta, az ügyfelek *IP_address*:/avere/Files a helyi csatlakoztatási pontra.

!["Új elágazás hozzáadása" párbeszédpanel a/avere/Files a névtér elérési útja mezőben](media/avere-vfxt-create-junction-example.png)

Az IP-cím a VServer definiált ügyféloldali IP-címek egyike. Az ügyféloldali IP-címek tartományát két helyen találja a avere Vezérlőpultján:

* **VServers** -tábla (irányítópult lap) –

  ![A avere Vezérlőpultjának irányítópult lapján a diagram alatti adattáblában kiválasztott VServer lapon, az IP-cím szakaszban pedig a kör alakú](media/avere-vfxt-ip-addresses-dashboard.png)

* **Ügyféloldali hálózati** beállítások lap –

  ![Beállítások > VServer > ügyfél hálózati konfigurációjának lapja, amely egy adott VServer tartozó táblázat címtartomány szakaszának körét tartalmazza](media/avere-vfxt-ip-addresses-settings.png)

Az elérési utakon kívül az egyes ügyfelek csatlakoztatásakor az alább ismertetett [csatlakoztatási parancs argumentumait](#mount-command-arguments) is meg kell adni.

### <a name="mount-command-arguments"></a>Csatlakoztatási parancs argumentumai

A zökkenőmentes ügyfél-csatlakoztatás biztosításához adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Szükséges beállítások | |
--- | ---
``hard`` | A vFXT-fürthöz való Soft-csatlakoztatások az alkalmazás hibáival és az esetleges adatvesztéssel vannak társítva.
``proto=netid`` | Ez a beállítás támogatja az NFS-hálózati hibák megfelelő kezelését.
``mountproto=netid`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=n`` | Az átmeneti csatlakoztatási hibák elkerülése érdekében ``retry=30`` beállítása. (Az előtér-csatlakoztatásokban egy másik érték használata javasolt.)

| Előnyben részesített beállítások  | |
--- | ---
``nointr``            | A "nem intr" kapcsoló előnyben részesített olyan ügyfelek számára, akik örökölt kernelekkel rendelkeznek (a 2008. előtt), amelyek támogatják ezt a beállítást. Vegye figyelembe, hogy a "intr" beállítás az alapértelmezett.

## <a name="next-steps"></a>Következő lépések

Miután csatlakoztatta az ügyfeleket, felhasználhatja őket a háttérbeli adattárolás (Core Filer) feltöltéséhez. A további telepítési feladatokkal kapcsolatos további információkért tekintse meg ezeket a dokumentumokat:

* [Adatok áthelyezése a cluster Core filerbe](avere-vfxt-data-ingest.md) – hogyan lehet több ügyfelet és szálat használni az adatok hatékony feltöltéséhez
* [Fürt testreszabása](avere-vfxt-tuning.md) – a fürt beállításainak személyre szabása a munkaterhelésnek megfelelően
* [A fürt kezelése](avere-vfxt-manage-cluster.md) – a fürt elindítása és leállítása, valamint a csomópontok kezelése
