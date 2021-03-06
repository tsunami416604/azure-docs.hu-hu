---
title: Az Azure HPC gyorsítótárának előfeltételei
description: Az Azure HPC cache használatának előfeltételei
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/05/2020
ms.author: v-erkel
ms.openlocfilehash: a31aee3f4548d3137fa1241aaa3a0f6171cf6895
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412510"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Az Azure HPC cache használatának előfeltételei

Mielőtt a Azure Portal használatával új Azure HPC-gyorsítótárat hozzon létre, győződjön meg arról, hogy a környezet megfelel a követelményeknek.

## <a name="video-overviews"></a>Videók áttekintése

Tekintse meg ezeket a videókat, és tekintse át a rendszer összetevőinek gyors áttekintését, és hogy mire van szükségük a közös munkához.

(Kattintson a videó képre vagy a megtekinteni kívánt hivatkozásra.)

* [Hogyan működik](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) – elmagyarázza, hogyan kommunikál az Azure HPC cache a Storage és az ügyfelekkel

  [![videó miniatűr képe: Azure HPC cache: Hogyan működik (kattintson ide a videó megtekintéséhez)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Előfeltételek](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) – a NAS Storage, az Azure Blob Storage, a hálózati hozzáférés és az ügyfelek hozzáférésének követelményeit ismerteti

  [![videó miniatűr képe: Azure HPC cache: előfeltételek (kattintson ide a videó megtekintéséhez)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

A cikk további részében találhat konkrét javaslatokat.

## <a name="azure-subscription"></a>Azure-előfizetés

Díjköteles előfizetés ajánlott.

## <a name="network-infrastructure"></a>Hálózati infrastruktúra

A gyorsítótár használata előtt két hálózattal kapcsolatos előfeltételt kell beállítani:

* Dedikált alhálózat az Azure HPC cache-példányhoz
* DNS-támogatás, hogy a gyorsítótár hozzáférhessen a tárolóhoz és egyéb erőforrásokhoz

### <a name="cache-subnet"></a>Alhálózat gyorsítótárazása

Az Azure HPC cache-nek dedikált alhálózatra van szüksége a következő tulajdonságokkal:

* Az alhálózatnak elérhetőnek kell lennie legalább 64 IP-címmel.
* Az alhálózat nem tud más virtuális gépeket üzemeltetni, még a kapcsolódó szolgáltatások, például az ügyfélszámítógépek esetében is.
* Ha több Azure HPC cache-példányt használ, mindegyiknek saját alhálózatra van szüksége.

Az ajánlott eljárás az, ha új alhálózatot hoz létre minden gyorsítótárhoz. Létrehozhat egy új virtuális hálózatot és alhálózatot a gyorsítótár létrehozásának részeként.

### <a name="dns-access"></a>DNS-hozzáférés

A gyorsítótárnak szüksége van a DNS-re a virtuális hálózatán kívüli erőforrásokhoz való hozzáféréshez. Attól függően, hogy melyik erőforrást használja, lehet, hogy be kell állítania egy testreszabott DNS-kiszolgálót, és konfigurálnia kell a továbbítást a kiszolgáló és a Azure DNS kiszolgálók között:

* Az Azure Blob Storage-végpontok és egyéb belső erőforrások eléréséhez az Azure-alapú DNS-kiszolgálóra van szükség.
* A helyszíni tároló eléréséhez konfigurálnia kell egy egyéni DNS-kiszolgálót, amely képes megoldani a tárolási gazdagépeket. Ezt a gyorsítótár létrehozása **előtt** kell elvégeznie.

Ha csak a blob Storage-hoz fér hozzá, az alapértelmezett Azure által biztosított DNS-kiszolgálót használhatja a gyorsítótárhoz. Ha azonban más erőforrásokhoz való hozzáférésre van szüksége, hozzon létre egy egyéni DNS-kiszolgálót, és konfigurálja úgy, hogy az Azure-specifikus feloldási kérelmeket továbbítsa a Azure DNS-kiszolgálónak.

Ha egyéni DNS-kiszolgálót szeretne használni, a gyorsítótár létrehozása előtt el kell végeznie ezeket a telepítési lépéseket:

* Hozza létre az Azure HPC cache-t futtató virtuális hálózatot.
* Hozza létre a DNS-kiszolgálót.
* Adja hozzá a DNS-kiszolgálót a gyorsítótár virtuális hálózatához.

  A következő lépésekkel adhatja hozzá a DNS-kiszolgálót a virtuális hálózathoz a Azure Portalban:

  1. Nyissa meg a virtuális hálózatot a Azure Portalban.
  1. Válassza a **DNS-kiszolgálók** lehetőséget az oldalsáv **Beállítások** menüjében.
  1. **Egyéni** kijelölése
  1. Adja meg a DNS-kiszolgáló IP-címét a mezőben.

Egy egyszerű DNS-kiszolgáló is használható az ügyfélkapcsolatok elosztására az összes elérhető gyorsítótár-csatlakoztatási pont között.

További információ az Azure Virtual Networks és a DNS-kiszolgáló konfigurációkról az Azure-beli [virtuális hálózatok erőforrásainak feloldásához](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="permissions"></a>Engedélyek

A gyorsítótár létrehozásának megkezdése előtt olvassa el ezeket az engedélyeket érintő előfeltételeket.

* A gyorsítótár-példánynak képesnek kell lennie virtuális hálózati adapterek (NIC-EK) létrehozására. A gyorsítótárat létrehozó felhasználónak elegendő jogosultsággal kell rendelkeznie az előfizetésben a hálózati adapterek létrehozásához.

* Ha blob Storage-t használ, az Azure HPC cache-nek engedélyre van szüksége a Storage-fiók eléréséhez. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja a gyorsítótár elérését a blob-tárolóhoz. Két szerepkörre van szükség: Storage-fiók közreműködői és Storage blob adatközreműködői.

  A szerepkörök hozzáadásához kövesse a [tárolási célok hozzáadása](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) című témakör utasításait.

## <a name="storage-infrastructure"></a>Tárolási infrastruktúra

A gyorsítótár támogatja az Azure Blob-tárolókat vagy az NFS hardveres tárolók exportálását. Adja hozzá a tárolási célokat a gyorsítótár létrehozása után.

Mindegyik tárolási típushoz konkrét előfeltételek vonatkoznak.

### <a name="blob-storage-requirements"></a>BLOB Storage-követelmények

Ha az Azure Blob Storage-t a gyorsítótárral szeretné használni, egy kompatibilis Storage-fiókra és egy üres blob-tárolóra vagy egy olyan tárolóra van szükség, amely az [adatok áthelyezése az Azure Blob Storage](hpc-cache-ingest.md)-ba című témakörben leírtak szerint feltölti az Azure HPC gyorsítótárral formázott adatokat.

A tárolási cél hozzáadása előtt hozza létre a fiókot. A cél hozzáadásakor új tárolót is létrehozhat.

Kompatibilis Storage-fiók létrehozásához használja az alábbi beállításokat:

* Teljesítmény: **standard**
* Fiók típusa: **StorageV2 (általános célú v2)**
* Replikáció: **helyileg redundáns tárolás (LRS)**
* Hozzáférési szint (alapértelmezett): **gyors**

Célszerű a Storage-fiókot a gyorsítótárral megegyező helyen használni.

Meg kell adnia a gyorsítótár-alkalmazásnak az Azure Storage-fiókhoz való hozzáférését is, ahogy az a fenti [engedélyekben](#permissions)is szerepel. Kövesse a [tárolási célok hozzáadása](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) lehetőséget a szükséges hozzáférési szerepkörök gyorsítótárazásához. Ha Ön nem a Storage-fiók tulajdonosa, akkor a tulajdonos ezt a lépést hajtja végre.

### <a name="nfs-storage-requirements"></a>NFS-tárolási követelmények
<!-- linked from configuration.md -->

Ha NFS-tárolót használ (például egy helyszíni hardveres NAS-rendszer), ellenőrizze, hogy az megfelel-e a követelményeknek. A beállítások ellenőrzéséhez előfordulhat, hogy a hálózati rendszergazdák vagy a tűzfal kezelőjével kell dolgoznia a tárolási rendszer (vagy az adatközpont) számára.

> [!NOTE]
> A tárolási cél létrehozása sikertelen lesz, ha a gyorsítótár nem rendelkezik megfelelő hozzáféréssel az NFS-tárolási rendszerhez.

További információt a [NAS-konfiguráció és az NFS-tárolási cél problémáinak elhárítása](troubleshoot-nas.md)című témakör tartalmaz.

* **Hálózati kapcsolat:** Az Azure HPC-gyorsítótár nagy sávszélességű hálózati hozzáférést igényel a gyorsítótár-alhálózat és az NFS-szolgáltatás adatközpontja között. [ExpressRoute](../expressroute/index.yml) vagy hasonló hozzáférés ajánlott. VPN használata esetén előfordulhat, hogy úgy kell beállítania, hogy a 1350-es számú TCP-MSS-t használja, hogy a nagyméretű csomagok ne legyenek letiltva. A VPN- [csomagok méretére vonatkozó korlátozások](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) beolvasása További segítség a VPN-beállítások hibaelhárításához.

* **Port hozzáférése:** A gyorsítótárnak hozzá kell férnie az adott TCP/UDP-portokhoz a tárolási rendszeren. A különböző típusú tárolók különböző portokra vonatkozó követelményekkel rendelkeznek.

  A tárolási rendszerek beállításainak vizsgálatához kövesse az alábbi eljárást.

  * Adjon ki egy `rpcinfo` parancsot a tárolási rendszernek a szükséges portok vizsgálatához. Az alábbi parancs felsorolja a portokat, és formázza a kapcsolódó eredményeket egy táblában. (A *<storage_IP>* kifejezés helyett használja a számítógép IP-címét.)

    Ezt a parancsot bármely olyan Linux-ügyfélről kiállíthatja, amelyen telepítve van az NFS-infrastruktúra. Ha a fürt alhálózatán belül használ ügyfelet, az az alhálózat és a tárolási rendszer közötti kapcsolat ellenőrzéséhez is segítséget nyújt.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Győződjön meg arról, hogy a lekérdezés által visszaadott összes port ``rpcinfo`` engedélyezi az Azure HPC cache alhálózatának korlátozás nélküli forgalmát.

  * Ha nem tudja használni a `rpcinfo` parancsot, győződjön meg arról, hogy ezek a gyakran használt portok engedélyezik a bejövő és kimenő forgalmat:

    | Protokoll | Port  | Szolgáltatás  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    Egyes rendszerek különböző portszámokat használnak ezekhez a szolgáltatásokhoz – a tárolási rendszer dokumentációjában tájékozódhat.

  * Ellenőrizze, hogy a tűzfalbeállítások engedélyezik-e a forgalmat az összes szükséges porton. Ügyeljen arra, hogy ellenőrizze az Azure-ban használt tűzfalakat és a helyszíni tűzfalakat az adatközpontban.

* **Címtár-hozzáférés:** Engedélyezze a `showmount` parancsot a tárolási rendszeren. Az Azure HPC cache ezt a parancsot használja annak ellenőrzéséhez, hogy a tárolási cél konfigurációja érvényes exportálásra mutat-e, valamint hogy több csatlakoztatás nem fér hozzá ugyanahhoz az alkönyvtárakhoz (a fájlok ütközésének kockázata).

  > [!NOTE]
  > Ha az NFS-tárolási rendszer a NetApp ONTAP 9,2 operációs rendszert használja, ne **engedélyezze `showmount`**. Segítségért [forduljon a Microsoft szolgáltatáshoz és a támogatási](hpc-cache-support-ticket.md) szolgálathoz.

  További információ a címtárbeli hozzáférésről az NFS-tárolási cél [hibaelhárítási cikkében](troubleshoot-nas.md#enable-export-listing).

* **Rendszergazdai hozzáférés** (olvasás/írás): a gyorsítótár a 0. felhasználói azonosítóként csatlakozik a háttérrendszer-kiszolgálóhoz. A következő beállítások megadásával a tárolási rendszeren:
  
  * Engedélyezés `no_root_squash` . Ezzel a beállítással biztosíthatja, hogy a távoli legfelső szintű felhasználó hozzáférhessen a root tulajdonában lévő fájlokhoz.

  * Ellenőrizze az exportálási szabályzatokat, és győződjön meg arról, hogy nem tartalmaznak korlátozásokat a gyorsítótár alhálózatához való rendszergazdai hozzáféréshez.

  * Ha a tároló olyan exportálással rendelkezik, amely egy másik exportálás alkönyvtára, akkor győződjön meg arról, hogy a gyorsítótárban rendszergazdai hozzáférés van az elérési út legalacsonyabb szegmenséhez. A részletekért olvassa el az NFS-tárolási cél hibaelhárítási cikkének [elérési útját a gyökérkönyvtár](troubleshoot-nas.md#allow-root-access-on-directory-paths) eléréséhez.

* Az NFS-háttérbeli tárterületnek kompatibilis hardver/szoftver platformnak kell lennie. Részletekért forduljon az Azure HPC cache csapatához.

## <a name="set-up-azure-cli-access-optional"></a>Azure CLI-hozzáférés beállítása (nem kötelező)

Ha Azure HPC-gyorsítótárat szeretne létrehozni vagy kezelni az Azure parancssori felületéről (Azure CLI), telepítenie kell a CLI-szoftvert és a HPC-cache kiterjesztést. Kövesse az Azure [parancssori felület beállítása az Azure HPC cache-hez](az-cli-prerequisites.md)című témakör utasításait.

## <a name="next-steps"></a>További lépések

* [Azure HPC cache-példány létrehozása](hpc-cache-create.md) a Azure Portal