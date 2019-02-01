---
title: Georedundáns tárolás (GRS) az Azure Storage-régiók közti tartóssági |} A Microsoft Docs
description: Georedundáns tárolás (GRS), amelyek több száz mérföld távolságra két régiója között replikálja az adatokat. GRS az adatközpontban, valamint a regionális katasztrófa elleni védelmet biztosít.
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/20/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: 2dc409743ce94ecb73e351b839a5a2fb09eadab2
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512105"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Georedundáns tárolás (GRS): Az Azure Storage-régiók közti replikáció
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Írásvédett georedundáns tárolás
Írásvédett georedundáns tárolás (RA-GRS) maximalizálja a tárfiók rendelkezésre állását. RA-GRS az adatokat a másodlagos hely, georeplikációt két régióban mellett csak olvasási hozzáférést biztosít.

Ha engedélyezi a csak olvasási hozzáférés az adatokhoz a másodlagos régióban, az adatok érhető el a másodlagos végpontra és a tárfiók elsődleges végpontjába. A másodlagos végpontot az elsődleges végpont hasonló, de az utótag `–secondary` fióknevet. Például, ha az elsődleges végpont a Blob szolgáltatás `myaccount.blob.core.windows.net`, akkor a másodlagos végpontra `myaccount-secondary.blob.core.windows.net`. A tárfiók hozzáférési kulcsait megegyeznek az elsődleges és másodlagos végpontokhoz.

Néhány szempontot, vegye figyelembe, amikor, RA-GRS használatával:

* Az alkalmazás melyik végponthoz, tesztverzióval az RA-GRS használata kezelheti azt.
* Aszinkron replikációs késleltetés jár, mivel a módosításokat, amelyek még nem lett replikálása még a másodlagos régióba elveszhetnek, ha az adatok nem állíthatók vissza az elsődleges régióból.
* Ellenőrizheti a tárfiók a legutóbbi szinkronizálás időpontja. Utolsó szinkronizálás időpontja egy olyan GMT dátum/idő érték. Az összes elsődleges írási művelet a legutóbbi szinkronizálás időpontja előtt sikeresen alkalmazáskonfigurációjának a másodlagos helyre, ami azt jelenti, hogy elérhetők legyenek az olvasható másodlagos helyről. Elsődleges írása után előfordulhat, hogy a legutóbbi szinkronizálás időpontja, vagy nem érhető el az olvasásokhoz még. Az érték használatával lekérdezheti a [az Azure portal](https://portal.azure.com/), [Azure PowerShell-lel](storage-powershell-guide-full.md), vagy az Azure Storage ügyfélkódtáraival közül.
* Ha egy a másodlagos régióba GRS vagy RA-GRS fiókok (előzetes verzió) fiók feladatátvételt kezdeményez, a feladatátvétel befejezése után írási hozzáféréssel ezekhez a fiókokhoz állítottak vissza. További információkért lásd: [katasztrófa utáni helyreállítás és a tárolási fiók feladatátvétel (előzetes verzió)](storage-disaster-recovery-guidance.md).
* RA-GRS a magas rendelkezésre állású célokra szolgál. Skálázhatósági útmutató, tekintse át a [teljesítmény ellenőrzőlista](storage-performance-checklist.md).
* Javaslatok a magas rendelkezésre állás az RA-GRS tervezéséhez, lásd: [tervezése magas rendelkezésre álló alkalmazásokat a RA-GRS tároló](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Mi az az RPO és RTO a grs Tárolással?

**Helyreállítási időkorlát (RPO):** A GRS és RA-GRS a storage szolgáltatás aszinkron módon geo-replikálja az adatokat az elsődleges kiszolgálóról a másodlagos helyen. Abban az esetben, ha az elsődleges régió elérhetetlenné válik, egy a másodlagos régióba (előzetes verzió) fiók feladatátvételt is végezhet. Kezdeményezzen feladatátvételt, ha még nem volt georeplikált bekövetkezett változásokat elveszhetnek. Az rpo-t percben, amely elvesztette a potenciális adatok néven ismert. Az RPO azt a pontot, amelyre az adatok helyreállíthatók időben. Az Azure Storage általában kevesebb, mint 15 perces egy Helyreállításipont-célkitűzéssel rendelkezik, noha jelenleg nem vonatkozik garantált szolgáltatási georeplikációs mennyi ideig tart.

**A helyreállítási időre vonatkozó célkitűzés (RTO):** Az RTO azt méri, mennyi ideig tart, hajtsa végre a feladatátvételt, és kérje le a tárfiókhoz online állapotba. Az idő a feladatátvétel végrehajtásához az alábbi műveleteket tartalmazza:

   * Az idő, amíg az ügyfél kezdeményezi a feladatátvétel, a storage-fiók az elsődleges kiszolgálóról a másodlagos régióba.
   * Az Azure-ban, hajtsa végre a feladatátvételt az elsődleges DNS-bejegyzéseket, hogy a másodlagos helyre mutasson módosításával szükséges időt.

## <a name="paired-regions"></a>Párosított régiók 
Amikor létrehoz egy tárfiókot, válassza ki az elsődleges régió a fiókhoz. A párosított másodlagos régió az elsődleges régió alapján határozza meg, és nem módosítható. Az Azure által támogatott régiók naprakész kapcsolatban lásd: [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Azure – párosított régiók](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Lásd még
- [Azure Storage replication (Azure Storage replikáció)](storage-redundancy.md)
- [Helyileg redundáns tárolás (LRS): Az Azure Storage alacsony költségű adatredundancia](storage-redundancy-lrs.md)
- [Zónaredundáns tárolás (ZRS): Azure Storage magas rendelkezésre állású alkalmazások](storage-redundancy-zrs.md)
