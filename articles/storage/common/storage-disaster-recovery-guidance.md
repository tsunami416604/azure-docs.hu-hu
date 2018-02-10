---
title: "Mi a teendő az Azure Storage kimaradás esetén |} Microsoft Docs"
description: "Mi a teendő az Azure Storage kimaradás esetén"
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: tamram
ms.openlocfilehash: 327816874fb2f2d43192e02410bd31c168e574f7
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Mi a teendő az Azure Storage leállása esetén?
A Microsoft dolgozunk merevlemez annak biztosítását, hogy a szolgáltatások mindig elérhető. Egyes esetekben kényszeríti a vezérlő hatás túl velünk, melyek következtében a nem tervezett szolgáltatáskimaradások egy vagy több régióban. Segítséget az ilyen ritka események kezelésére, az Azure Storage szolgáltatás a következő magas szintű útmutatást nyújtunk.

## <a name="how-to-prepare"></a>Előkészítése
Minden ügyfél számára kritikus fontosságú, hogy előkészítse a saját vészhelyreállítási tervét. Általában egy tároló leállás helyreállítás részéről az erőfeszítés ahhoz, hogy az alkalmazások működőképes állapotban újraaktiválása műveleti személyzet és automatizált eljárások egyaránt magában foglalja. Tekintse meg az alábbi saját vész-helyreállítási terv létrehozása az Azure dokumentációja:

* [Rendelkezésre állási ellenőrzőlista](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Rugalmas alkalmazások tervezése az Azure számára](/azure/architecture/resiliency)
* [Az Azure Site Recovery szolgáltatásban](https://azure.microsoft.com/services/site-recovery/)
* [Azure Storage replication (Azure Storage replikáció)](storage-redundancy)
* [Az Azure biztonsági mentési szolgáltatás](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Hogyan észlelése
Az ajánlott módszer az Azure-szolgáltatások állapotának meghatározása, hogy fizessen elő a [Azure az állapotjelző irányítópulthoz](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Mi a teendő, ha egy tárolási kimaradás során
Ha egy vagy több tároló szolgáltatás átmenetileg nem érhető el egy vagy több régióban, két módon figyelembe kell venni. Ha az adatok közvetlen hozzáférést kívánnak, fontolja meg 2. lehetőség.

### <a name="option-1-wait-for-recovery"></a>1. lehetőség: Várjon, amíg a helyreállítás
Ebben az esetben a letöltés intézkedés nem szükséges. Jelenleg dolgozunk gondossággal visszaállítása az Azure szolgáltatás rendelkezésre állása. A szolgáltatás állapotát a figyelheti a [Azure az állapotjelző irányítópulthoz](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>2. lehetőség: Adatok másolása a másodlagos kiszolgálóról
Ha úgy döntött, hogy [írásvédett georedundáns tárolás (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (ajánlott) a storage-fiókok, akkor olvasási hozzáféréssel az adatokat másodlagos régióban. Használhatja például a [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), és a [Azure adatok adatátviteli könyvtár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) használatával adatait átmásolhatja a másodlagos régióban lévő másik tárolási fiókot egy unimpacted régió, és jelzi az alkalmazások tárolási fiók mindkét és olvasási rendelkezésre állását.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Mi történik, ha egy Storage feladatátvételt hajt végre
Ha úgy döntött, hogy [georedundáns tárolás (GRS)](storage-redundancy.md#geo-redundant-storage) vagy [írásvédett georedundáns tárolás (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (ajánlott), Azure Storage akkor is megtartja az adatok tartósságát két régió (elsődleges és másodlagos). Azure Storage mindkét régió folyamatosan több replika adatait is kezeli.

Ha egy regionális katasztrófa érinti az elsődleges régióban, először megpróbáljuk a szolgáltatást az adott régióban. A vészhelyreállítás és annak hatások, az egyes ritka esetekben jellegétől függ jelenleg nem lehet visszaállítani az elsődleges régióban. A földrajzi feladatátvétel ezen a ponton végezzük el. A kereszt-régió adatreplikáció egy aszinkron folyamattal, amely magába foglaló késleltetés, ezért lehetséges, hogy még nem replikálódott a másodlagos régióba módosítások elveszhetnek. Lekérheti a ["Utolsó szinkronizálásának időpontja". a tárfiók](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) a replikációs állapot kapcsolatban.

Pontokra vonatkozó tárolási földrajzi feladatátvételt élménye néhány:

* Tároló földrajzi feladatátvételt csak akkor is kiváltódik, az Azure Storage csapat – nincs nincs szükség felhasználói beavatkozásra.
* A meglévő tárolási Szolgáltatásvégpontok blobokat, táblák, üzenetsorok és fájlok változatlan marad a feladatátvétel; a Microsoft által biztosított DNS-bejegyzést kell frissíteni, hogy az elsődleges régióban átvált a másodlagos régióba.  A Microsoft a frissítés automatikusan a földrajzi-feladatátvételi folyamat részeként fogja elvégezni.
* Előtt, és a földrajzi feladatátvétel során nem rendelkezik írási engedéllyel a tárfiók, mert az a katasztrófa, de továbbra is elolvashatja a másodlagos adatbázisból, ha a tárfiók RA-GRS van konfigurálva.
* Ha a földrajzi feladatátvétel befejeződött, és a DNS-módosítások propagálása, olvasási és írási hozzáférést a tárfiókhoz fog folytatható; Ez a másodlagos végpontot bizonyult használttól mutat. 
* Vegye figyelembe, úgy kell írási hozzáférést Ha Georedundáns vagy RA-GRS a tárfiók konfigurálva. 
* Lekérheti ["Utolsó földrajzi feladatátvételi idő" a tárfiókja](https://msdn.microsoft.com/library/azure/ee460802.aspx) további információkhoz juthat.
* A feladatátvétel után a tárfiókhoz teljes körűen működik, de "csökkentett teljesítményű" állapota, ahogy ténylegesen tárolódik nem georeplikáció lehet önálló régióban. A kockázat csökkentése érdekében, rendszer-visszaállítás az eredeti elsődleges régióban és hajtsa végre a földrajzi-feladat-visszavétel visszaállítása az eredeti állapotra. Ha az eredeti elsődleges régió nem állítható helyre, hogy foglal le egy másik másodlagos régióba.
  Az infrastruktúra az Azure Storage georeplikáció további részletekért tekintse meg a cikk a Storage csapat blogja a kapcsolatos [redundancia beállítások és az RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Ajánlott eljárások az adatok védelme
Nincsenek adatok biztonsági mentésének a tárolási rendszeresen néhány ajánlott megközelítés.

* Virtuális gépek lemezei – használja a [Azure Backup szolgáltatás](https://azure.microsoft.com/services/backup/) biztonsági mentése a virtuális gép lemezeivel az Azure virtuális gépek által használt.
* Blobok blokk – hozzon létre egy [pillanatkép](https://msdn.microsoft.com/library/azure/hh488361.aspx) minden egyes blob letiltása, vagy a BLOB másolása másik tárolási fiókot be egy másik régióban [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), vagy a [Azure adatok adatátviteli könyvtár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Táblázatok – használjon [AzCopy](storage-use-azcopy.md) exportálni a adatait egy másik régióban másik tárolási fiókot.
* Fájlok – az [AzCopy](storage-use-azcopy.md) vagy [Azure PowerShell](storage-powershell-guide-full.md) a fájlok másolása egy másik régióban másik tárolási fiókot.

Az alkalmazásokat, amelyek az RA-GRS szolgáltatás teljes körű kihasználása létrehozásával kapcsolatban vegye ki [tervezése magas rendelkezésre álló alkalmazások RA-GRS-tárolót](../storage-designing-ha-apps-with-ragrs.md)

