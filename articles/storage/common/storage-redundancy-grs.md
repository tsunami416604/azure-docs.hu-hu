---
title: Geo-redundáns tárolás (GRS) a régiók közötti tartósság érdekében
titleSuffix: Azure Storage
description: A Geo-redundáns tárolás (GRS) két, egymástól több száz kilométerre lévő régió között replikálja az adatait. A GRS az adatközpontban és a regionális katasztrófákban is védelmet nyújt.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: c44c13f268a561e3094ae76757504a86627e1f58
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895216"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Geo-redundáns tárolás (GRS): régiók közötti replikáció az Azure Storage-hoz

[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Írásvédett georedundáns tárolás (RA-GRS)

A Read-Access geo-redundáns tárolás (RA-GRS) maximalizálja a Storage-fiók rendelkezésre állását. Az RA-GRS csak olvasási hozzáférést biztosít a másodlagos helyen lévő adatokhoz, a két régió közötti földrajzi replikálás mellett.

Ha engedélyezi a csak olvasási hozzáférést az adataihoz a másodlagos régióban, az adatai egy másodlagos végponton, valamint a Storage-fiók elsődleges végpontján is elérhetők. A másodlagos végpont az elsődleges végponthoz hasonló, de az utótagot a fiók nevéhez `–secondary` hozzáfűzi. Ha például a Blob service elsődleges végpontja `myaccount.blob.core.windows.net`, akkor a másodlagos végpont `myaccount-secondary.blob.core.windows.net`. A Storage-fiókhoz tartozó hozzáférési kulcsok mind az elsődleges, mind a másodlagos végpont esetében azonosak.

Néhány megfontolandó szempont az RA-GRS használatakor:

- Az alkalmazásnak kezelnie kell, hogy melyik végpontot használja az RA-GRS használatakor.
- Mivel az aszinkron replikáció késéssel jár, a másodlagos régióba még nem replikált módosítások elvesznek, ha az adatok nem állíthatók helyre az elsődleges régióból.
- A Storage-fiók utolsó szinkronizálási idejét is megtekintheti. A legutóbbi szinkronizálás ideje egy GMT dátum/idő érték. A legutóbbi szinkronizálási idő előtti összes elsődleges írást a másodlagos helyre sikerült írni, ami azt jelenti, hogy elérhetők a másodlagos helyről való olvasáshoz. A legutóbbi szinkronizálási idő után az elsődleges írások még nem állnak rendelkezésre az olvasáshoz. Ezt az értéket lekérdezheti az Azure Storage ügyféloldali kódtárainak [Azure Portalával](https://portal.azure.com/), [Azure PowerShellával](storage-powershell-guide-full.md)vagy valamelyikével.
- Ha egy GRS vagy RA-GRS fiók feladatátvételét (előzetes verzió) kezdeményezi a másodlagos régióba, a feladatátvétel befejeződése után a rendszer az írási hozzáférést a fiókhoz is visszaállítja. További információ: vész- [helyreállítási és Storage-fiók feladatátvétele (előzetes verzió)](storage-disaster-recovery-guidance.md).
- Az RA-GRS magas rendelkezésre állású célokra szolgál. A méretezhetőséggel kapcsolatos útmutatásért tekintse át a [teljesítmény ellenőrzőlistáját](storage-performance-checklist.md).
- Az RA-GRS-vel való magas rendelkezésre állás kialakításával kapcsolatos javaslatokért lásd: [magas rendelkezésre állású alkalmazások tervezése ra-GRS Storage használatával](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Mi a RPO és a RTO a GRS?

**Helyreállítási pont célkitűzése (RPO):** A GRS és RA-GRS-ben a Storage szolgáltatás aszinkron módon geo-replikálja az adatait az elsődleges helyről a másodlagos helyre. Abban az esetben, ha az elsődleges régió elérhetetlenné válik, a fiók feladatátvételét (előzetes verzió) a másodlagos régióra is elvégezheti. Feladatátvétel kezdeményezése esetén a legutóbbi olyan módosítások elvesznek, amelyek még nem lettek földrajzilag replikálva. Az elveszett potenciális adatmennyiség percben megadott száma RPO néven ismert. A RPO azt az időpontot jelöli, ameddig az Adathelyreállítás visszaállítható. Az Azure Storage-nak jellemzően 15 percnél rövidebb RPO van, bár jelenleg nem áll rendelkezésre SLA a Geo-replikáció során.

**Helyreállítási időre vonatkozó célkitűzés (RTO):** A RTO azt méri, hogy mennyi ideig tart a feladatátvétel végrehajtása, és a Storage-fiók online állapotba kerül. A feladatátvétel elvégzéséhez szükséges idő a következő műveleteket tartalmazza:

- Az az idő, ameddig az ügyfél kezdeményezi a Storage-fiók elsődlegestől a másodlagos régióba való feladatátvételét.
- Az Azure által a feladatátvétel végrehajtásához szükséges idő, ha megváltoztatja az elsődleges DNS-bejegyzéseket, hogy a másodlagos helyre mutassanak.

## <a name="paired-regions"></a>Párosított régiók

A Storage-fiók létrehozásakor ki kell választania a fiók elsődleges régióját. A párosított másodlagos régió az elsődleges régió alapján van meghatározva, és nem módosítható. Az Azure által támogatott régiók naprakész információit az [üzletmenet folytonossága és a vész-helyreállítási (BCDR): Azure párosított régiók](../../best-practices-availability-paired-regions.md)című témakörben tekintheti meg.

## <a name="see-also"></a>Lásd még:

- [Azure Storage replication (Azure Storage replikáció)](storage-redundancy.md)
- [Helyileg redundáns tárolás (LRS): alacsony költséghatékonyságú adatredundancia az Azure Storage szolgáltatáshoz](storage-redundancy-lrs.md)
- [Zone-redundáns tárolás (ZRS): magasan elérhető Azure Storage-alkalmazások](storage-redundancy-zrs.md)
