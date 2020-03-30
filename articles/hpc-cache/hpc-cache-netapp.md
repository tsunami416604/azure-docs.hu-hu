---
title: Azure HPC-gyorsítótár és Azure NetApp-fájlok használata
description: Az Azure HPC-gyorsítótár használata az Azure NetApp-fájlokkal tárolt adatokhoz való hozzáférés javítása érdekében
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 38f9d0338ce4c47024d670e6d3ee89a97faecc91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238679"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Azure HPC-gyorsítótár használata Az Azure NetApp-fájlokkal

Az [Azure NetApp-fájlokat](https://azure.microsoft.com/services/netapp/) az Azure HPC-gyorsítótár tárolási célként használhatja. Ez a cikk bemutatja, hogyan működhet együtt a két szolgáltatás, és tippeket ad a beállításukhoz.

Az Azure NetApp Files egyesíti ONTAP operációs rendszerét a Microsoft Azure méretezhetőségével és sebességével. Ez a kombináció lehetővé teszi a felhasználók számára, hogy a létrehozott munkafolyamatokat átváltsák a felhőbe kód átírása nélkül.

Az Azure HPC cache-összetevő hozzáadása javíthatja a fájlhozzáférést azáltal, hogy több Azure NetApp-fájlkötetet mutat be egy összesített névtérben. Egy másik szolgáltatási régióban található kötetek élhálózati gyorsítótárazását biztosíthatja. Emellett javíthatja az alacsonyabb szintű szolgáltatási szinteken létrehozott kötetek igény szerinti teljesítményét a költségek csökkentése érdekében.

## <a name="overview"></a>Áttekintés

Ha az Azure NetApp Files rendszert háttértárolóként szeretné használni az Azure HPC-gyorsítótárral, kövesse ezt a folyamatot.

1. Hozza létre az Azure NetApp Files rendszert és köteteket a rendszer megtervezése című útmutatónak [megfelelően az alábbi .](#plan-your-azure-netapp-files-system)
1. Hozza létre az Azure HPC-gyorsítótárat abban a régióban, ahol fájlhozzáférésre van szüksége. (Kövesse az [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md)című útmutatót.)
1. Definiálja az Azure NetApp-fájlok köteteire mutató [tárolócélokat](#create-storage-targets-in-the-cache) a gyorsítótárban. Hozzon létre egy gyorsítótár-tárolási célt minden egyes egyedi IP-címhez, amely a kötetek eléréséhez használatos.
1. Az ügyfelek [csatlakoztassa az Azure HPC cache](#mount-storage-targets) helyett az Azure NetApp Files kötetek közvetlenül.

## <a name="plan-your-azure-netapp-files-system"></a>Az Azure NetApp Files rendszer megtervezése

Az Azure NetApp Files rendszer tervezésekor figyeljen az ebben a szakaszban található elemekre, hogy zökkenőmentesen integrálhassa azt az Azure HPC cache-szel.

Az Azure HPC-gyorsítótárral való használatra szánt kötetek létrehozása előtt olvassa el az [Azure NetApp Files dokumentációját](../azure-netapp-files/index.yml) is.

### <a name="nfs-client-access-only"></a>Csak Az NFS-ügyfél hozzáférése

Az Azure HPC-gyorsítótár jelenleg csak az NFS-hozzáférést támogatja. Nem használható SMB ACL vagy POSIX módú bitkötetekkel.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Exkluzív alhálózat az Azure NetApp-fájlokhoz

Az Azure NetApp Files egyetlen delegált alhálózatot használ a köteteihez. Más erőforrás nem használható az alhálózatban. Emellett a virtuális hálózatban csak egy alhálózat használható az Azure NetApp-fájlokhoz. További információ az [Azure NetApp Files hálózattervezés irányelvei](../azure-netapp-files/azure-netapp-files-network-topologies.md)című részben található.

### <a name="delegated-subnet-size"></a>Delegált alhálózat mérete

Használja a delegált alhálózat minimális méretét, amikor létrehoz egy Azure NetApp Files rendszert az Azure HPC-gyorsítótárral való használatra.

A netmask /28 paraméterrel megadott minimális méret 16 IP-címet ad meg. A gyakorlatban az Azure NetApp Files csak három ilyen rendelkezésre álló IP-címeket használ a kötet-hozzáféréshez. Ez azt jelenti, hogy csak három tárolási célt kell létrehoznia az Azure HPC-gyorsítótárban az összes kötet lefedéséhez.

Ha a delegált alhálózat túl nagy, lehetséges, hogy az Azure NetApp Files kötetek több IP-címet használjon, mint egy Azure HPC cache-példány képes kezelni. Egyetlen gyorsítótár legbénult lehet legbőlegelhető tíz tárolási cél.

Az Azure NetApp Files dokumentációjában a rövid útmutató 10.7.0.0/16-ot használ a delegált alhálózathoz, amely túl nagy alhálózatot biztosít.

### <a name="capacity-pool-service-level"></a>Kapacitáskészlet szolgáltatási szintje

A kapacitáskészlet szolgáltatási szintjének kiválasztásakor vegye figyelembe a munkafolyamatot. Ha gyakran ír adatokat az Azure NetApp Files kötetre, a gyorsítótár teljesítménye korlátozható, ha a visszaírási idő lassú. Válasszon magas szolgáltatási szintet a gyakran írással rendelkező kötetekhez.

Az alacsony szolgáltatási szinttel rendelkező kötetek is mutathatnak némi késést a feladat kezdetén, miközben a gyorsítótár előre kitölti a tartalmat. Miután a gyorsítótár működik, és fut egy jó működő fájlkészlettel, a késleltetésnek észrevehetetlenné kell válnia.

Fontos, hogy előre tervezze meg a kapacitáskészlet szolgáltatási szintjét, mert nem módosítható a létrehozás után. Egy másik kapacitáskészletben új kötetet kell létrehozni, és az adatokat át kell másolni.

Vegye figyelembe, hogy a kötet tárolási kvótáját és a kapacitáskészlet méretét a hozzáférés megszakítása nélkül módosíthatja.

## <a name="create-storage-targets-in-the-cache"></a>Tárolási célok létrehozása a gyorsítótárban

Miután az Azure NetApp Files rendszer be van állítva, és az Azure HPC-gyorsítótár létrehozása, határozza meg a tároló célokat a gyorsítótárban, amelyek a fájlrendszer kötetek.

Hozzon létre egy tárolási célt az Azure NetApp-fájlok kötetei által használt minden EGYES IP-címhez. Az IP-cím a kötet csatlakoztatási útmutatólapján jelenik meg.

Ha több kötet ugyanazt az IP-címet használja, mindegyikhez használhat egy tárolási célt.  

Kövesse [az Azure NetApp Files dokumentációjában található csatlakoztatási utasításokat](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) a használandó IP-címek megkereséséhez.

Az Azure CLI-vel is megtalálhatja az IP-címeket:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Az Azure NetApp Files rendszer neveinek exportálása egyetlen elérésiút-összetevővel rendelkezik. Ne próbáljon meg tárolási célt létrehozni ``/`` a gyökérexportáláshoz az Azure NetApp-fájlokban, mert ez az exportálás nem biztosít fájlhozzáférést.

Ezekhez a tárolócélokhoz nincsenek speciális korlátozások a virtuális névtér elérési útjaira vonatkozóan.

## <a name="mount-storage-targets"></a>Tárolási célok csatlakoztatása

Az ügyfélgépeknek az Azure NetApp-fájlok köteteinek közvetlen csatlakoztatása helyett csatlakoztatniuk kell a gyorsítótárat. Kövesse az [Azure HPC-gyorsítótár csatlakoztatása című útmutató utasításait.](hpc-cache-mount.md)

## <a name="next-steps"></a>További lépések

* További információ az [Azure NetApp-fájlok](../azure-netapp-files/index.yml) beállításáról és használatáról
* Ha segítségre van szüksége az Azure HPC cache-rendszer ének tervezéséhez és beállításához az Azure NetApp-fájlok használatához, [forduljon az ügyfélszolgálathoz.](hpc-cache-support-ticket.md)
