---
title: Csatlakoztassa a Avere vFXT – Azure
description: Az Azure-ügyfelek Avere vFXT csatlakoztatásáról
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 41065b4ac6bc486e204c2bfd72b78ba8722270c4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809163"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Az Avere vFXT-fürt csatlakoztatása  

Kövesse az alábbi lépéseket az ügyfélgépek a vFXT fürthöz való kapcsolódáshoz.

1. Annak eldöntése, hogyan kell a fürt csomópontok közötti terheléselosztás ügyfél forgalmát. Olvasási [egyenleg ügyfélterhelés](#balance-client-load), az alábbi részleteket. 
1. Azonosítsa az IP-cím és a szinkronizációs pont csatlakoztatásához elérési.
1. A probléma a [mount parancs](#mount-command-arguments), megfelelő argumentumokkal.

## <a name="balance-client-load"></a>Egyenleg ügyfél betöltése

Annak érdekében, eloszthatja a kérelmeket ügyfél a fürt összes csomópontja között, az ügyfelek az ügyfél által használt IP-címek teljes tartományához kell csatlakoztatja. Többféleképpen is egyszerű feladat automatizálását.

> [!TIP] 
> Más terhelés terheléselosztási módszert érdemes lehet nagy vagy összetett rendszer; [hozzon létre egy támogatási jegyet](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) segítségét.)
> 
> Ha inkább a DNS-kiszolgáló használata kiszolgálóoldali automatikus terheléselosztást, állítsa be, majd a saját DNS-kiszolgáló Azure-ban kezelheti. Ebben az esetben a vFXT fürt szerint ez a dokumentum ciklikus időszeletelési DNS konfigurálhatja: [Fürt DNS-konfiguráció Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Példa elosztott terhelésű ügyfél parancsfájl csatlakoztatása

Ez a Kódpélda randomizing elemként ügyfél IP-címek használatával terjeszteni az összes elérhető IP-címek a vFXT fürt ügyfelek.

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

A fenti funkció érhető el a Batch példa része a [Avere vFXT példák](https://github.com/Azure/Avere#tutorials) hely.

## <a name="create-the-mount-command"></a>A mount parancs létrehozása 

> [!NOTE]
> Ha nem hozott létre egy új Blob-tárolóba, a Avere vFXT fürt létrehozásakor, kövesse a [konfigurálta a tárterületet](avere-vfxt-add-storage.md) előtti ügyfelek csatlakoztatása során.

Az ügyfélről a ``mount`` parancsot a virtuális kiszolgáló (vserver) leképezi a helyi fájlrendszer a vFXT fürtön elérési útra. A formátum ``mount <vFXT path> <local path> {options}``

Nincsenek a mount parancs három elemek: 

* vFXT elérési út – (IP-címére és névtér szinkronizációs pont elérési alább leírt kombinációjával)
* helyi elérési út – a elérési utat az ügyfélen 
* csatlakoztatási beállítások parancs - (felsorolt [csatlakoztassa a parancs argumentumainak](#mount-command-arguments))

### <a name="junction-and-ip"></a>Szinkronizációs pont és az IP-cím

A vserver elérési út kombinációja a *IP-cím* plusz elérési útját egy *névtér szinkronizációs pont*. A névtér csatlakozási a virtuális elérési utat a tárolórendszer hozzáadásakor megadott.

Ha a fürt a Blob storage használatával lett létrehozva, a névtér elérési út `/msazure`

Például: ``mount 10.0.0.12:/msazure /mnt/vfxt``

A fürt létrehozását követően hozzáadta a tárolót, ha a névtér-szinkronizációs pont elérési útja felel meg a beállított érték **Namespace elérési út** a szinkronizációs pont létrehozásakor. Például, ha a használt ``/avere/files`` a névtér elérési útjaként az ügyfelek csatlakoztatni szeretné *IP_cím*: / avere vagy fájlokat a helyi csatlakoztatási ponton.

![A névtér elérési út mezőbe fájlokkal/avere / "Új szinkronizációs pont hozzáadása" párbeszédpanelen](media/avere-vfxt-create-junction-example.png)


Az IP-cím a megadott a vserver az ügyfél által használt IP-címek egyike. A számos ügyfél által használt IP-címek a Avere Vezérlőpult két helyen található:

* **VServers** táblára (fülre irányítópult) – 

  ![Irányítópult lap Avere Vezérlőpult a kiválasztott a graph és az IP-cím szakasz alatti adatok táblázatban VServer lap bekarikázott](media/avere-vfxt-ip-addresses-dashboard.png)

* **Ügyfél felől elérhető hálózati** beállítások lap – 

  ![Beállítások > VServer > a táblázat egy adott vserver címtartomány szakaszában körüli kör az ügyfél felől elérhető hálózati konfigurációs lapja](media/avere-vfxt-ip-addresses-settings.png)

Mellett az elérési utak közé tartozik a [csatlakoztassa a parancs argumentumainak](#mount-command-arguments) alább leírt, amikor az egyes ügyfelek.

### <a name="mount-command-arguments"></a>Mount parancs argumentumai

Ahhoz, hogy egy zökkenőmentes ügyfél csatlakoztatási, adja át ezeket a beállításokat és argumentumok a mount parancs: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Kötelező beállítások | |
--- | --- 
``hard`` | Helyreállítható csatlakoztatása a vFXT fürthöz társítva alkalmazáshibák és adatvesztés lehetséges. 
``proto=netid`` | Ez a lehetőség támogatja a megfelelő NFS hálózati hibák kezelésére.
``mountproto=netid`` | Ezt a beállítást támogatja a csatlakoztatási műveletek megfelelő hálózati hibák kezelésére.
``retry=n`` | Állítsa be ``retry=30`` átmeneti csatlakoztatási hibák elkerülése érdekében. (Az előtérben történő csatlakoztatása egy másik értéket javasolt.)

| Előnyben részesített beállításai  | |
--- | --- 
``nointr``            | A "nointr" beállítás részesíti előnyben az ügyfelek az örökölt kernelekkel (előtt április 2008), amely támogatja ezt a beállítást. Vegye figyelembe, hogy a "megszakítás" beállítást az alapértelmezett.


## <a name="next-steps"></a>További lépések

Miután csatlakoztatta a ügyfelek számára, feltölti a háttérrendszer adattárolás (core filer) használhatja őket. Tekintse meg ezeket a dokumentumokat, további információt a további telepítési feladatok:

* [Adatok áthelyezése a fürt alapvető filer](avere-vfxt-data-ingest.md) -ügyfelek és a szálak használatával hatékonyan töltse fel az adatokat
* [Testre szabhatja a fürt hangolása](avere-vfxt-tuning.md) -testre szabni a számítási feladat igényeinek megfelelően a fürtbeállítások
* [A fürt kezeléséhez](avere-vfxt-manage-cluster.md) -elindítása vagy leállítása a fürt és csomópontok kezelése
