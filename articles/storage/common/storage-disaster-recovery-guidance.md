---
title: Mi a teendő az Azure Storage leállása esetén |} A Microsoft Docs
description: Mi a teendő az Azure Storage leállása esetén?
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
ms.date: 07/15/2018
ms.author: tamram
ms.openlocfilehash: 160fe756458e067125b9d696fd0cdb929774446e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072034"
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Mi a teendő az Azure Storage leállása esetén?
A Microsoft dolgozunk, hogy a szolgáltatásaink mindig elérhetők. Egyes esetekben kényszeríti a vezérlő hatás túli velünk a kapcsolatot, hogy a szolgáltatás nem tervezett leállások miatt egy vagy több régióban. Könnyebben kezelni ezeket ritkán fordul elő, az Azure Storage szolgáltatás a következő magas szintű útmutatást biztosítunk.

## <a name="how-to-prepare"></a>Előkészítése
Minden ügyfél számára kritikus fontosságú, hogy előkészítse a saját vészhelyreállítási tervét. Általában a társzolgáltatás kimaradása helyreállítás részéről az erőfeszítés annak érdekében, hogy az alkalmazások működőképes állapotban újraaktiválása műveleti személyzet és az automatikus folyamatokat foglalja magában. Tekintse meg a saját vész-helyreállítási terv készítése az alábbi Azure-dokumentáció:

* [Rendelkezésre állási ellenőrzőlista](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [Rugalmas alkalmazások tervezése az Azure számára](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Az Azure Site Recovery szolgáltatás](https://azure.microsoft.com/services/site-recovery/)
* [Azure Storage replication (Azure Storage replikáció)](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Az Azure Backup szolgáltatás](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Észlelése
Az Azure-szolgáltatás állapotának meghatározása ajánlott módja a Feliratkozás a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Mi a teendő, ha a Társzolgáltatás kimaradása történik
Ha egy vagy több tárolószolgáltatások átmenetileg nem érhető el egy vagy több régióban, két lehetőség van, érdemes figyelembe venni. Ha az adatok közvetlen hozzáférést kívánnak, fontolja meg a 2. lehetőség.

### <a name="option-1-wait-for-recovery"></a>1. lehetőség: Várjon a helyreállítás
Ebben az esetben az Ön részéről semmit nem kell. Jelenleg is dolgozunk térségekre való kiterjesztésén az Azure-szolgáltatás rendelkezésre állásának visszaállítása érdekében. A szolgáltatás állapotát a figyelemmel kísérheti a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>2. lehetőség: Adatok másolása a másodlagos
Ha úgy döntött [írásvédett georedundáns tárolás (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (ajánlott) a storage-fiókok, olvasási hozzáférést kap az adatokhoz a másodlagos régióból. Eszközöket használhatja például [AzCopy](storage-use-azcopy.md), [Azure PowerShell-lel](storage-powershell-guide-full.md), és a [Azure Adatáthelyezés könyvtár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) adatokat másol a másodlagos régióban lévő egy másik tárfiókba történő egy unimpacted régióban, és ezután mutasson az alkalmazásokat, hogy a tárolási fiók is írási és olvasási.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Mire számítson, ha a tárolási feladatátvétel történik
Ha úgy döntött [georedundáns tárolás (GRS)](storage-redundancy-grs.md) vagy [írásvédett georedundáns tárolás (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (ajánlott), az Azure Storage fogja megőrizni az adatok tartós két régióban (elsődleges és másodlagos). Mindkét régióban az Azure Storage folyamatosan megőrzi az adatok több replika.

Ha regionális katasztrófa hatással van az elsődleges régióban, azt először megpróbálja állítsa vissza a szolgáltatás az adott régióban. A vészhelyreállítási és annak hatással, egyes ritka esetekben jellegétől függ azt nem lehet visszaállítani az elsődleges régióba. Ezen a ponton fogunk földrajzi feladatátvételt végrehajtani. A régiók közötti adatreplikáció egy aszinkron folyamat, amely késéssel is magában foglalhat, így lehetséges, hogy módosításokat, amelyek még nem replikálódott a másodlagos régióba elveszhetnek. Lekérdezheti a ["Legutóbbi szinkronizálás időpontja" a tárfiók](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) úgy szerezheti be a replikációs állapot részletei.

A tároló földrajzi feladatátvételt tapasztalatokra vonatkozó pontok néhány:

* Az Azure Storage csapata akkor csak tárolási földrajzi feladatátvételt aktiválódik – nem tartoznak felhasználói műveletek szükséges.
* A meglévő tárolási Szolgáltatásvégpontok blobok, táblák, üzenetsorok és fájlok esetében ugyanaz marad a feladatátvétel; után a Microsoft által biztosított DNS-bejegyzést kell frissíteni, hogy az elsődleges régióból történő váltson a másodlagos régióba.  Ez a frissítés automatikusan a földrajzi feladatátvételt folyamat részeként a Microsoft végrehajtja.
* Előtt, és a földrajzi feladatátvétel során a tárfiók a hatását a katasztrófa miatt nem kell írási hozzáférést, de továbbra is olvashat a másodlagos helyről, ha a tárfiók van konfigurálva, RA-GRS.
* Ha a földrajzi feladatátvétel befejezése és a DNS-módosítások propagálása, olvasási és írási hozzáférést a tárfiókhoz fog folytatható; Mi a alkalmazni kell a másodlagos végpontra mutat ez. 
* Vegye figyelembe, hogy írási hozzáférést kap, ha a GRS vagy RA-GRS a tárfiók számára beállított. 
* Lekérdezheti ["Földrajzi feladatátvétel utoljára" a tárfiók](https://msdn.microsoft.com/library/azure/ee460802.aspx) további információért.
* A feladatátvétel után a tárfiók teljes körűen működik, de "csökkentett teljesítményű" állapotban, mert van ténylegesen régióban üzemeltet egy önálló nincs georeplikáció útján lehet. A kockázat csökkentése érdekében, hogy visszaállítja az eredeti elsődleges régió és tegye a geo-feladat-visszavétel az eredeti állapot. Ha az eredeti elsődleges régióban nem állítható helyre, hogy foglal le egy másik másodlagos régióba.
  Az Azure Storage georeplikációs infrastruktúrára további részletekért tekintse meg a Storage csapat blogja foglalkozó cikkel kapcsolatos [Redundanciabeállításokkal és RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Ajánlott eljárások az adatok védelmére
Van néhány ajánlott megközelítéseket, az adatok biztonsági másolatát a storage rendszeres időközönként.

* Virtuálisgép-lemezek – használja az [Azure Backup szolgáltatás](https://azure.microsoft.com/services/backup/) biztonsági mentése az Azure-beli virtuális gépek által használt virtuális gép lemezei.
* Kapcsolja be – a blokkblobok használatát támogatják [helyreállítható törlési](../blobs/storage-blob-soft-delete.md) objektumszintű törlések ellen, és felülírja, vagy a blobokat másolja be egy másik régió egy másik tárfiókba [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), vagy a [Azure Adatáthelyezés könyvtár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Táblák – használata [AzCopy](storage-use-azcopy.md) táblák adatainak exportálása egy másik régióban egy másik tárfiókba.
* Fájlok – az [AzCopy](storage-use-azcopy.md) vagy [Azure PowerShell-lel](storage-powershell-guide-full.md) a fájlok másolása egy másik régióban egy másik tárfiókba.

Az alkalmazásokat, amelyek teljes mértékben kihasználhatja az RA-GRS funkció létrehozásával kapcsolatos információkért tekintse meg [tervezése magas rendelkezésre álló alkalmazások RA-GRS tároló használatával](../storage-designing-ha-apps-with-ragrs.md)
