---
title: Linux-rendszerképek létrehozása az Azure-ban – áttekintés
description: A Linux rendszerű virtuális gépek rendszerképeinek beszerzése, illetve az Azure-ban használandó új rendszerképek létrehozása.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 40d028ade5429c89ce40b718c90c601dfcb0e470
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308150"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Linux-rendszerképek készítése és létrehozása az Azure-ban

Ez az Áttekintés a képalkotással kapcsolatos alapvető fogalmakat és a Linux-lemezképek Azure-beli sikeres felépítését és használatát ismerteti. Mielőtt egyéni rendszerképet hozna az Azure-ba, tisztában kell lennie az Ön számára elérhető típusokkal és lehetőségekkel.

Ez a cikk a rendszerképekkel kapcsolatos döntési pontokon és követelményeken keresztül tárgyal, ismerteti a főbb fogalmakat, így követheti ezt, és saját egyéni rendszerképeit is létrehozhatja a specifikációban.

## <a name="difference-between-managed-disks-and-images"></a>A felügyelt lemezek és a lemezképek közötti különbség


Az Azure lehetővé teszi, hogy egy virtuális merevlemezt a platformra, [felügyelt lemezként](https://docs.microsoft.com/azure/virtual-machines/windows/faq-for-disks#managed-disks)használja, vagy a lemezkép forrásaként használja. 

Az Azure Managed Disks egyetlen virtuális merevlemez. Létrehozhat egy meglévő VHD-t, és létrehozhat egy felügyelt lemezt, vagy létrehozhat egy üres, felügyelt lemezt. Létrehozhat virtuális gépeket a felügyelt lemezekről úgy, hogy csatlakoztatja a lemezt a virtuális géphez, de csak egy virtuális géppel rendelkező virtuális merevlemezt tud használni. Az operációs rendszer tulajdonságai nem módosíthatók, az Azure csak a virtuális gép bekapcsolását és a lemez használatának megkezdését kísérli meg. 

Az Azure-lemezképek több operációsrendszer-lemezből és adatlemezből is készíthetők. Ha felügyelt rendszerképet használ a virtuális gép létrehozásához, a platform másolatot készít a rendszerképről, és ezt használja a virtuális gép létrehozásához, így a felügyelt rendszerkép támogatja ugyanazt a rendszerképet több virtuális gép esetében. Az Azure speciális felügyeleti funkciókat is biztosít a rendszerképekhez, például a globális replikáláshoz és a [megosztott](shared-image-galleries.md)képkatalóguson keresztül történő verziószámozáshoz. 



## <a name="generalized-and-specialized"></a>Általánosított és specializált

Az Azure két fő képtípust kínál, általánosítva és specializálva. Az általánosított és specializált kifejezések eredetileg Windows-feltételek, amelyek az Azure-ba migrálva vannak. Ezek a típusok határozzák meg, hogy a platform hogyan fogja kezelni a virtuális gépet, amikor bekapcsolja. Mindkét típushoz előnyök és hátrányok tartoznak, és előfeltételei vannak. Az első lépések előtt tudnia kell, hogy milyen típusú rendszerképekre lesz szüksége. Az alábbi összefoglalja a forgatókönyveket és a szükséges típusokat:

| Forgatókönyv      | Lemezkép típusa  | Tárolási lehetőségek |
| ------------- |:-------------:| :-------------:| 
| Hozzon létre egy olyan rendszerképet, amely több virtuális gép általi használatra is konfigurálható, és beállítható az állomásnév, rendszergazdai felhasználó hozzáadása és egyéb feladatok végrehajtása az első rendszerindítás során. | Általánosított | Megosztott képgyűjtemény vagy önálló felügyelt lemezképek |
| Rendszerkép létrehozása VM-pillanatképből vagy biztonsági másolatból | Specializált |Megosztott képgyűjtemény vagy felügyelt lemez |
| Gyorsan létrehozhat egy olyan rendszerképet, amely nem igényel konfigurációt több virtuális gép létrehozásához |Specializált |Megosztott rendszerkép-katalógus |


### <a name="generalized-images"></a>Általánosított rendszerképek

Az általánosított rendszerkép olyan rendszerkép, amely az első rendszerindításkor a telepítés befejezését igényli. Az első rendszerindításkor például az állomásnév, a rendszergazda felhasználó és más virtuálisgép-specifikus konfigurációk állíthatók be. Ez akkor hasznos, ha azt szeretné, hogy a rendszer többször is felhasználja a képet, és ha a létrehozás során paramétereket szeretne megadni a paraméterekben. Ha az általánosított rendszerkép tartalmazza az Azure-ügynököt, akkor az ügynök feldolgozza a paramétereket, és visszaküldi azt a platformra, amelyen a kezdeti konfiguráció befejeződött. Ezt a folyamatot **üzembe**helyezésnek nevezzük. 

A kiépítés megköveteli, hogy a rendszerkép tartalmazza a kiépítés részét. Két kiépítés létezik:
- [Azure Linux-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
- [Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)

Ezek a rendszerképek létrehozásának [előfeltételei](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) .


### <a name="specialized-images"></a>Speciális rendszerképek
Ezek olyan rendszerképek, amelyek teljesen konfigurálva vannak, és nem igénylik a virtuális gépeket és a speciális paramétereket, a platform csak a virtuális gépet kapcsolja be, a virtuális gépen egyedivé kell tennie, mint például a hostname beállítása, hogy elkerülje a DNS-ütközéseket ugyanazon a VNET. 

Ezekhez a lemezképekhez nem szükségesek kiépítési ügynökök, azonban lehetséges, hogy a bővítmények kezelési képességei is rendelkezésre állnak. Telepítheti a Linux-ügynököt, de letilthatja a létesítési lehetőséget. Annak ellenére, hogy nincs szüksége kiépítési ügynökre, a rendszerképnek meg kell felelnie az Azure-lemezképek [előfeltételeinek](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) .


## <a name="image-storage-options"></a>Rendszerkép-tárolási beállítások
A Linux-rendszerkép létrehozásakor két lehetőség közül választhat:

- Felügyelt lemezképek egyszerű virtuális gépek létrehozásához fejlesztési és tesztelési környezetben.
- [Megosztott](shared-image-galleries.md) képkatalógus a rendszerképek méretének létrehozásához és megosztásához.


### <a name="managed-images"></a>Felügyelt lemezképek

A felügyelt lemezképek több virtuális gép létrehozásához is használhatók, de számos korlátozással rendelkeznek. A felügyelt lemezképek csak általánosított forrásból (VM vagy VHD) hozhatók létre. A virtuális gépeket csak ugyanabban a régióban lehet létrehozni, és az előfizetések és a bérlők között nem oszthatók meg.

A felügyelt lemezképek fejlesztési és tesztelési környezetekhez használhatók, ahol néhány egyszerű általánosított lemezképre van szükség az egyetlen régióban és előfizetésben való használathoz. 

### <a name="azure-shared-image-gallery-sig"></a>Azure Shared Képgaléria (SIG)

A [megosztott képtárakat](shared-image-galleries.md) a lemezképek nagy léptékű létrehozásához, kezeléséhez és megosztásához ajánljuk. A megosztott képtárak segítenek felépíteni a lemezképek körét a szerkezet és a szervezet számára.  

- Az általánosított és a speciális rendszerképek támogatása.
- Az 1. és a 2. generációs lemezképek támogatása.
- Lemezképek globális replikálása.
- A könnyebb felügyelet érdekében a rendszerképek verziószámozása és csoportosítása.
- Az Availability Zonest támogató régiókban található, a zóna redundáns tárolásával (ZRS) rendelkező, magasan elérhető rendszerképek. A ZRS nagyobb rugalmasságot biztosít a zónabeli hibákkal szemben.
- Megosztás az előfizetések között, és akár Active Directory (AD) bérlők között a RBAC használatával.
- Az üzembe helyezések skálázása minden egyes régióban képreplikákkal.

Magas szinten létre kell hoznia egy SIG-t, amely az alábbiakból áll:
- Képdefiníciók – ezek olyan tárolók, amelyek képcsoportokat tárolnak.
- Lemezkép-verziók – ezek a tényleges lemezképek



## <a name="hyper-v-generation"></a>Hyper-V generáció

Az Azure támogatja a Hyper-V 1. generációs (Gen1) és a 2. generációs (Gen2), a Gen2 a legújabb generációt, és további funkciókat nyújt a Gen1-hoz. Például: megnövekedett memória, Intel Software Guard Extensions (Intel SGX ENKLÁVÉHOZ) és virtualizált állandó memória (vPMEM). A 2. generációs virtuális gépek a helyszínen futnak, az Azure-ban még nem támogatott funkciók vannak. További információ: szolgáltatások és képességek szakasz. További információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2). Ha további funkciókra van szüksége, hozzon létre Gen2-lemezképeket.

Ha továbbra is létre kell hoznia egy saját rendszerképet, győződjön meg arról, hogy megfelel a [rendszerkép előfeltételeinek](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic), és feltölti az Azure-ba. Terjesztési specifikus követelmények:


- [CentOS-alapú disztribúciók](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES és openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan hozhat létre [megosztott képtárat](tutorial-custom-images.md).



