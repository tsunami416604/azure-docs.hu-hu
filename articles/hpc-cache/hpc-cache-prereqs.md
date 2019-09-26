---
title: Az Azure HPC cache (előzetes verzió) előfeltételei
description: Az Azure HPC cache használatának előfeltételei
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: v-erkell
ms.openlocfilehash: fab85785ea183736b4012c349af143ef3a8c784a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299920"
---
# <a name="prerequisites-for-azure-hpc-cache-preview"></a>Az Azure HPC cache használatának előfeltételei (előzetes verzió)

Mielőtt a Azure Portal használatával új Azure HPC-gyorsítótárat hozzon létre, győződjön meg arról, hogy a környezet megfelel a követelményeknek.

## <a name="azure-subscription"></a>Azure-előfizetés

Díjköteles előfizetés ajánlott.

> [!NOTE]
> Az előzetes kiadás során az Azure HPC gyorsítótár-csapatának hozzá kell adnia az előfizetést a hozzáférési listához, mielőtt felhasználható lenne a gyorsítótár-példány létrehozásához. Ez az eljárás segít biztosítani, hogy minden ügyfél magas színvonalú reagálást biztosítson a tesztelési gyorsítótárból. A hozzáférés kéréséhez töltse ki [ezt az űrlapot](https://aka.ms/onboard-hpc-cache) .

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
* A helyszíni tároló eléréséhez konfigurálnia kell egy egyéni DNS-kiszolgálót, amely képes megoldani a tárolási gazdagépeket.

Ha csak a blob Storage-hoz fér hozzá, az alapértelmezett Azure által biztosított DNS-kiszolgálót használhatja a gyorsítótárhoz. Ha azonban más erőforrásokhoz való hozzáférésre van szüksége, hozzon létre egy egyéni DNS-kiszolgálót, és konfigurálja úgy, hogy az Azure-specifikus feloldási kérelmeket továbbítsa a Azure DNS-kiszolgálónak. (Egy egyszerű DNS-kiszolgáló is használható az ügyfélkapcsolatok elosztására az összes elérhető gyorsítótár-csatlakoztatási pont között.)

További információ az Azure Virtual Networks és a DNS-kiszolgáló konfigurációkról az Azure-beli [virtuális hálózatok erőforrásainak feloldásához](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Engedélyek

A gyorsítótár létrehozásának megkezdése előtt olvassa el ezeket az engedélyeket érintő előfeltételeket.

* A gyorsítótár-példánynak képesnek kell lennie virtuális hálózati adapterek (NIC-EK) létrehozására. A gyorsítótárat létrehozó felhasználónak elegendő jogosultsággal kell rendelkeznie az előfizetésben a hálózati adapterek létrehozásához.
<!-- There are several ways to authorize this access; read [Additional prerequisites](media/preview-prereqs.md) to learn more. -->

* Ha blob Storage-t használ, az Azure HPC cache-nek engedélyre van szüksége a Storage-fiók eléréséhez. Szerepköralapú hozzáférés-vezérlés (RBAC) használatával biztosíthatja a gyorsítótár elérését a blob-tárolóhoz. Két szerepkörre van szükség: Storage-fiók közreműködői és tárolási blob adatközreműködői. A szerepkörök hozzáadásához kövesse a [tárolási célok hozzáadása](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) című témakör utasításait.

## <a name="storage-infrastructure"></a>Tárolási infrastruktúra

A gyorsítótár támogatja az Azure Blob-tárolókat vagy az NFS hardveres tárolók exportálását. A tárolási célokat a gyorsítótár létrehozásakor is meghatározhatja, de később is hozzáadhat tárhelyet.

Mindegyik tárolási típushoz konkrét előfeltételek vonatkoznak. 

### <a name="nfs-storage-requirements"></a>NFS-tárolási követelmények

Helyszíni hardveres tároló használata esetén a gyorsítótárnak nagy sávszélességű hálózati hozzáféréssel kell rendelkeznie az adatközponthoz az alhálózatról. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) vagy hasonló hozzáférés ajánlott.

Az NFS-háttérbeli tárterületnek kompatibilis hardver/szoftver platformnak kell lennie. Részletekért forduljon az Azure HPC cache csapatához.

### <a name="blob-storage-requirements"></a>BLOB Storage-követelmények

Ha az Azure Blob Storage-t a gyorsítótárral szeretné használni, egy kompatibilis Storage-fiókra és egy üres blob-tárolóra vagy egy olyan tárolóra van szükség, amely az [adatok áthelyezése az Azure Blob Storage](hpc-cache-ingest.md)-ba című témakörben leírtak szerint feltölti az Azure HPC gyorsítótárral formázott adatokat.

Hozza létre a fiókot és a tárolót, mielőtt felveszi a tárolási célként.

Kompatibilis Storage-fiók létrehozásához használja az alábbi beállításokat:

* Teljesítmény **Standard**
* Fiók típusa: **StorageV2 (általános célú v2)**
* Replikációs **Helyileg redundáns tárolás (LRS)**
* Hozzáférési szintek (alapértelmezett): **gyakori elérésű**

Célszerű a Storage-fiókot a gyorsítótárral megegyező helyen használni.
<!-- need to clarify location - same region or same resource group or same virtual network? -->

Az Azure Storage-fiókhoz is meg kell adnia a gyorsítótár-alkalmazáshoz való hozzáférést. Kövesse a Storage-tárolók [hozzáadása](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) című témakör leírását, és adja meg a gyorsítótárhoz a hozzáférési szerepkörök Storage-fiók közreműködőjét és a Storage blob adatközreműködőjét. Ha Ön nem a Storage-fiók tulajdonosa, akkor a tulajdonos ezt a lépést hajtja végre.

## <a name="next-steps"></a>További lépések

* [Azure HPC cache-példány létrehozása](hpc-cache-create.md) a Azure Portal
