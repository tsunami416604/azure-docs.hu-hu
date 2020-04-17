---
title: Az Azure HPC-gyorsítótár előfeltételei
description: Az Azure HPC-gyorsítótár használatának előfeltételei
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: ab342dba5c8be2ff3793c0eb36926969b3e364e5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537287"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Az Azure HPC-gyorsítótár előfeltételei

Mielőtt az Azure Portalon új Azure HPC-gyorsítótárat hozna létre, győződjön meg arról, hogy a környezet megfelel ezeknek a követelményeknek.

## <a name="azure-subscription"></a>Azure-előfizetés

Fizetős előfizetés ajánlott.

## <a name="network-infrastructure"></a>Hálózati infrastruktúra

A gyorsítótár használata előtt két, hálózattal kapcsolatos előfeltételt kell beállítani:

* Dedikált alhálózat az Azure HPC-gyorsítótár-példányhoz
* DNS-támogatás, hogy a gyorsítótár hozzáférhessen a tárolóhoz és más erőforrásokhoz

### <a name="cache-subnet"></a>Gyorsítótár alhálózata

Az Azure HPC-gyorsítótárnak egy dedikált alhálózatra van szüksége az alábbi tulajdonságokkal:

* Az alhálózatnak legalább 64 IP-címmel kell rendelkeznie.
* Az alhálózat nem tud más virtuális gépeket üzemeltetni, még a kapcsolódó szolgáltatások, például az ügyfélgépek esetében sem.
* Ha több Azure HPC-gyorsítótár-példányt használ, mindegyiknek saját alhálózatra van szüksége.

Az ajánlott eljárás az, hogy hozzon létre egy új alhálózat minden gyorsítótár. A gyorsítótár létrehozásának részeként új virtuális hálózatot és alhálózatot hozhat létre.

### <a name="dns-access"></a>DNS-hozzáférés

A gyorsítótárnak DNS-re van szüksége a virtuális hálózaton kívüli erőforrások eléréséhez. Attól függően, hogy milyen erőforrásokat használ, előfordulhat, hogy be kell állítania egy testreszabott DNS-kiszolgálót, és konfigurálnia kell a továbbítást a kiszolgáló és az Azure DNS-kiszolgálók között:

* Az Azure Blob storage-végpontok és más belső erőforrások eléréséhez szüksége van az Azure-alapú DNS-kiszolgáló.
* A helyszíni tároló eléréséhez egyéni DNS-kiszolgálót kell konfigurálnia, amely fel tudja oldani a tárolóállomásneveket.

Ha csak a Blob storage-hoz való hozzáférésre van szüksége, használhatja az alapértelmezett Azure által biztosított DNS-kiszolgálót a gyorsítótárhoz. Ha azonban más erőforrásokhoz kell hozzáférnie, hozzon létre egy egyéni DNS-kiszolgálót, és konfigurálja úgy, hogy továbbítsa az Azure-specifikus megoldási kérelmeket az Azure DNS-kiszolgálóra.

Egy egyszerű DNS-kiszolgáló is használható az összes rendelkezésre álló gyorsítótár-csatlakoztatási pont közötti ügyfélkapcsolatok terhelésére.

További információ az Azure virtuális hálózatokról és DNS-kiszolgálók konfigurációiról az [Azure virtuális hálózatokban lévő erőforrások névfeloldása](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)című részben.

## <a name="permissions"></a>Engedélyek

A gyorsítótár létrehozása előtt ellenőrizze ezeket az engedélyekkel kapcsolatos előfeltételeket.

* A gyorsítótár-példánynak képesnek kell lennie virtuális hálózati csatolók (NIC) létrehozására. A gyorsítótárat létrehozó felhasználónak megfelelő jogosultsággal kell rendelkeznie az előfizetésben a hálózati adapterek létrehozásához.

* Blob storage használata esetén az Azure HPC cache-nek engedélyre van szüksége a tárfiók eléréséhez. A szerepköralapú hozzáférés-vezérlés (RBAC) használatával hozzáférést biztosít a gyorsítótár a Blob storage.Use role-based access control (RBAC) to give the cache access to your Blob storage. Két szerepkör szükséges: storage-fiók közreműködője és a Storage Blob Data Contributor.

  A [szerepkörök](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) hozzáadásához kövesse a Tárolási célok hozzáadása című, tárolási célok hozzáadása című részben található utasításokat.

## <a name="storage-infrastructure"></a>Tárolási infrastruktúra

A gyorsítótár támogatja az Azure Blob-tárolókat vagy az NFS hardvertároló-exportálást. A gyorsítótár létrehozása után adja hozzá a tárolócélokat.

Minden tárolási típus nak meghatározott előfeltételei vannak.

### <a name="blob-storage-requirements"></a>Blob tárolási követelmények

Ha az Azure Blob storage-ot a gyorsítótárral szeretné használni, szüksége van egy kompatibilis tárfiókra, és vagy egy üres Blob-tárolóra vagy egy Azure HPC-gyorsítótárral formázott adatokkal feltöltött tárolóra az [Adatok áthelyezése az Azure Blob storage-ba](hpc-cache-ingest.md)című dokumentumban leírtak szerint.

Hozza létre a fiókot, mielőtt tárolócélt próbálna hozzáadni. A cél hozzáadásakor új tárolót hozhat létre.

Kompatibilis tárfiók létrehozásához használja az alábbi beállításokat:

* Teljesítmény: **Standard**
* Fiók fajta: **StorageV2 (általános célú v2)**
* Replikáció: **Helyileg redundáns tárolás (LRS)**
* Hozzáférési szint (alapértelmezett): **Gyakori elérésű**

Célszerű egy tárfiókot használni ugyanazon a helyen, mint a gyorsítótár.

A gyorsítótár-alkalmazás nak is hozzáférést kell biztosítania az Azure-tárfiókhoz a fenti Engedélyek ) című részben [említettek](#permissions)szerint. Kövesse a [tárolási célok hozzáadása](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) című, a szükséges hozzáférési szerepkörök gyorsítótárba adása című eljárását. Ha nem a tárfiók tulajdonosa, a tulajdonos ezt a lépést.

### <a name="nfs-storage-requirements"></a>NFS tárolási követelmények
<!-- linked from configuration.md -->

Ha NFS-tárolórendszert (például helyszíni hardveres NAS-rendszert) használ, győződjön meg arról, hogy az megfelel ezeknek a követelményeknek. Előfordulhat, hogy a beállítások ellenőrzéséhez együtt kell működnie a hálózati rendszergazdákkal vagy a tárolórendszer (vagy adatközpont) tűzfalkezelőivel.

> [!NOTE]
> A tárolási cél létrehozása sikertelen lesz, ha a gyorsítótár nem rendelkezik elegendő hozzáféréssel az NFS tárolórendszerhez.

További információt a [NAS-konfiguráció és az NFS-tárolási célproblémák elhárítása](troubleshoot-nas.md)tartalmaz.

* **Hálózati kapcsolat:** Az Azure HPC cache nagy sávszélességű hálózati hozzáférést igényel a gyorsítótár alhálózata és az NFS-rendszer adatközpontja között. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) vagy hasonló hozzáférés ajánlott. Ha VPN-t használ, előfordulhat, hogy úgy kell konfigurálnia, hogy a TCP MSS 1350-nél legyen lebilincselő, hogy a nagy csomagok ne legyenek blokkolva. Olvassa el [a VPN-csomagméretre vonatkozó korlátozásokat](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) a VPN-beállítások hibaelhárításához.

* **Port-hozzáférés:** A gyorsítótárnak hozzá kell férnie a tárolórendszer adott TCP/UDP portjaihoz. A különböző típusú tárolókra eltérő portkövetelmények vonatkoznak.

  A tárolórendszer beállításainak ellenőrzéséhez kövesse az alábbi eljárást.

  * Adjon `rpcinfo` ki egy parancsot a tárolórendszernek a szükséges portok ellenőrzéséhez. Az alábbi parancs felsorolja a portokat, és formázza a táblázatban szereplő releváns eredményeket. (A *<storage_IP>storage_IP* kifejezés helyett használja a rendszer IP-címét.)

    Ezt a parancsot bármely Olyan Linux-ügyfélről kiadhatja, amelyen telepítve van az NFS-infrastruktúra. Ha a fürt alhálózatán belül használ egy ügyfelet, az segíthet az alhálózat és a tárolórendszer közötti kapcsolat ellenőrzésében is.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Győződjön meg arról, hogy ``rpcinfo`` a lekérdezés által visszaadott összes port engedélyezi az Azure HPC-gyorsítótár alhálózatának korlátlan forgalmát.

  * Ha nem tudja használni `rpcinfo` a parancsot, győződjön meg arról, hogy ezek a gyakran használt portok engedélyezik a bejövő és kimenő forgalmat:

    | Protocol (Protokoll) | Port  | Szolgáltatás  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr között |
    | TCP/UDP  | 4046  | csatlakoztatva   |
    | TCP/UDP  | 4047  | status   |

    Egyes rendszerek különböző portszámokat használnak ezekhez a szolgáltatásokhoz – a biztos ság érdekében olvassa el a tárolórendszer dokumentációját.

  * Ellenőrizze a tűzfal beállításait, hogy engedélyezi-e a forgalmat az összes szükséges porton. Győződjön meg arról, hogy ellenőrizze az Azure-ban használt tűzfalakat, valamint a helyszíni tűzfalakat az adatközpontban.

* **Címtár-hozzáférés:** Engedélyezze `showmount` a parancsot a tárolórendszeren. Az Azure HPC cache ezzel a paranccsal ellenőrzi, hogy a tárolási cél konfigurációs pontok egy érvényes exportálás, és győződjön meg arról, hogy több csatlakoztatások nem férnek hozzá ugyanazokat az alkönyvtárakat (a fájl ütközésének kockázatát).

  > [!NOTE]
  > Ha az NFS tárolórendszer a NetApp ONTAP 9.2 operációs rendszerét használja, **ne engedélyezze `showmount` **a . [Segítségért forduljon a Microsoft szervizelési és támogatási szolgálatához.](hpc-cache-support-ticket.md)

  További információ a címtárlistázási hozzáférésről az NFS-tároló [célhibaelhárítási cikkében.](troubleshoot-nas.md#enable-export-listing)

* **Root access** (olvasási/írási): A gyorsítótár 0-s felhasználói azonosítóként csatlakozik a háttérrendszerhez. Ellenőrizze ezeket a beállításokat a tárolórendszeren:
  
  * Engedélyezze a. `no_root_squash` Ez a beállítás biztosítja, hogy a távoli gyökérfelhasználó hozzáférhessen a gyökér tulajdonában lévő fájlokhoz.

  * Ellenőrizze az exportálási szabályzatokat, és győződjön meg arról, hogy azok nem tartalmaznak korlátozásokat a gyorsítótár alhálózatából származó gyökérhozzáférésre vonatkozóan.

  * Ha a tároló olyan exportálással rendelkezik, amely egy másik exportálás alkönyvtára, győződjön meg arról, hogy a gyorsítótár root hozzáféréssel rendelkezik az elérési út legalacsonyabb szegmenséhez. A részleteket az NFS-tároló célhibaelhárítási cikkében olvassa el a [könyvtárelérési utakgyökér-hozzáférését.](troubleshoot-nas.md#allow-root-access-on-directory-paths)

* Az NFS háttértárolónak kompatibilis hardver-/szoftverplatformnak kell lennie. A részletekért forduljon az Azure HPC cache csapatához.

## <a name="next-steps"></a>További lépések

* [Azure HPC-gyorsítótár-példány létrehozása](hpc-cache-create.md) az Azure Portalról
