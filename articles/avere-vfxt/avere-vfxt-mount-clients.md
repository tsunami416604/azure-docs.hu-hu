---
title: Az Avere vFXT csatlakoztatása - Azure
description: Ügyfelek csatlakoztatása az Avere vFXT for Azure-ral
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: b8486b5a33226b1faa5e3874144129dbe7a1a2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153411"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Az Avere vFXT-fürt csatlakoztatása

Az alábbi lépésekkel csatlakoztathatja az ügyfélgépeket a vFXT-fürthöz.

1. Döntse el, hogyan töltheti el az ügyfélforgalmat a fürtcsomópontok között. Olvassa [el egyenleg ügyfél terhelés](#balance-client-load), az alábbiakban, a részletekért.
1. Azonosítsa a csatlakoztatni a csatlakoztatni a csatlakoztatni a csatlakoztatni a csatlakoztatni.
1. Adja ki a [csatlakoztatási parancsot](#mount-command-arguments)a megfelelő argumentumokkal.

## <a name="balance-client-load"></a>Ügyfélterhelés elosztása

Az ügyfélkérelmek nek a fürt összes csomópontja közötti egyensúlyának érdekében az ügyfeleket az ügyféloldali IP-címek teljes tartományához kell csatlakoztatnia. A feladat automatizálásának számos egyszerű módja van.

> [!TIP]
> Más terheléselosztási módszerek is megfelelőek lehetnek nagy vagy bonyolult rendszerek hez; [nyisson meg egy támogatási jegyet](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) segítségért.)
>
> Ha dns-kiszolgálót szeretne használni a kiszolgálóoldali terheléselosztás automatikus használatához, be kell állítania és kezelnie kell a saját DNS-kiszolgálóját az Azure-on belül. Ebben az esetben a ciklikus multiplexeléses DNS-t a következő dokumentum szerint konfigurálhatja a vFXT-fürthöz: [Avere cluster DNS-konfiguráció](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Minta kiegyensúlyozott ügyfél csatlakoztatási parancsfájl

Ez a példakód az ügyfél IP-címeket használja véletlenszerű összetevőként az ügyfelek elosztásához a vFXT-fürt összes rendelkezésre álló IP-címére.

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
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

A fenti függvény része a Batch példa elérhető az [Avere vFXT példák](https://github.com/Azure/Avere#tutorials) oldalon.

## <a name="create-the-mount-command"></a>A csatlakoztatási parancs létrehozása

> [!NOTE]
> Ha nem hozott létre új Blob-tárolót az Avere vFXT-fürt létrehozásakor, adja hozzá a tárolórendszereket a [Tároló konfigurálása](avere-vfxt-add-storage.md) című részben leírtak szerint, mielőtt ügyfeleket próbálna csatlakoztatni.

Az ügyféltől ``mount`` a parancs leképezi a virtuális kiszolgálót (vserver) a vFXT-fürtön a helyi fájlrendszer egyik elérési útjára. A formátum``mount <vFXT path> <local path> {options}``

A csatlakoztatási parancs három elemből áll:

* vFXT elérési út - ip-cím és névtér-csomópont útvonalának kombinációja a 9. fürtön az alábbiakban leírt)
* helyi elérési út - az ügyfél elérési útja
* csatlakoztatási parancs beállításai - a [Mount parancs argumentumai](#mount-command-arguments) között

### <a name="junction-and-ip"></a>Elágazás és IP

A vserver elérési útja az *IP-cím* és a *névtércsomópont*elérési útja kombinációja. A névtércsomópont egy virtuális elérési út, amely a tárolórendszer hozzáadásakor lett definiálva.

Ha a fürt blobtármal lett létrehozva, a tároló névtérelérési útja`/msazure`

Például: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Ha a fürt létrehozása után hozzáadott tárolót, a névtér csomópont elérési útja a **Névtér elérési útban** a csomópont létrehozásakor megadott érték. Ha például a ``/avere/files`` névtér elérési útjaként használt, az ügyfelek *IP_address*:/avere/files-t csatlakoztatnák a helyi csatlakoztatási pontjukhoz.

!["Új csomópont hozzáadása" párbeszédpanel a /avere/files kapcsolóval a névtér elérési útja mezőben](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

Az IP-cím a vserverhez definiált ügyféloldali IP-címek egyike. Az ügyfélre néző IP-k körét két helyen találja az Avere vezérlőpulton:

* **VServers** tábla (Dashboard lap) -

  ![Az Avere vezérlőpult irányítópultja a grafikon alatti adattáblázatban kijelölt VServer lapmal, az IP-cím szakasz bekarikázva](media/avere-vfxt-ip-addresses-dashboard.png)

* **Ügyfél hálózati** beállítások lap -

  ![A VServer > ügyfél hálózati konfigurációs lapja > egy körrel a táblázat Címtartomány szakasza körül egy adott virtuális kiszolgálóhoz](media/avere-vfxt-ip-addresses-settings.png)

Az elérési utakon kívül adja meg az alábbiakban [ismertetett Mount parancs argumentumait](#mount-command-arguments) az egyes ügyfelek csatlakoztatásakor.

### <a name="mount-command-arguments"></a>Parancsargumentumok csatlakoztatása

A zökkenőmentes ügyfélcsatlakoztatás biztosításához adja át ezeket a beállításokat és argumentumokat a csatlakoztatási parancsban:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Szükséges beállítások | |
--- | ---
``hard`` | A vFXT-fürthöz való lágy csatlakoztatások alkalmazáshibákkal és esetleges adatvesztéssel vannak társítva.
``proto=netid`` | Ez a beállítás támogatja az NFS hálózati hibák megfelelő kezelését.
``mountproto=netid`` | Ez a beállítás támogatja a hálózati hibák megfelelő kezelését a csatlakoztatási műveletekhez.
``retry=n`` | Állítsa ``retry=30`` be, hogy elkerülje az átmeneti csatlakoztatási hibákat. (Az előtér-tartókban más érték ajánlott.)

## <a name="next-steps"></a>További lépések

Miután csatlakoztatta az ügyfeleket, adatok másolása a fürt új Blob storage-tárolóba. Ha nem kell új tárhelyet feltöltenem, olvassa el a további telepítési feladatokról szóló további hivatkozásokat:

* [Adatok áthelyezése fürtmagos fájlkezelőbe](avere-vfxt-data-ingest.md) – Több ügyfél és szál használata az adatok hatékony feltöltéséhez egy új központi fájlba
* [Fürthangolás testreszabása](avere-vfxt-tuning.md) – A fürtbeállítások testreszabása a munkaterheléshez
* [A fürt kezelése](avere-vfxt-manage-cluster.md) – A fürt indítása vagy leállítása és a csomópontok kezelése
