---
title: Katasztrófa utáni helyreállítás és a tárolási fiók feladatátvétel (előzetes verzió) – Azure Storage
description: Azure Storage (előzetes verzió) fiók feladatátvételi georedundáns storage-fiókok támogatja. Fiók feladatátvétellel is kezdeményezhető a feladatátvételi folyamat a tárfiók, ha az elsődleges végpont elérhetetlenné válik.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/01/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: bc79379d1b893beffc085e79b7643fcb6e1dc26f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657314"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Katasztrófa utáni helyreállítás és a tárolási fiók feladatátvételi (előzetes verzió) az Azure Storage-ban

A Microsoft nagy hangsúlyt fektet a győződjön meg arról, hogy Azure-szolgáltatások mindig elérhetők. Azonban nem tervezett szolgáltatáskimaradások fordulhat elő. Ha az alkalmazáshoz szükséges rugalmasságot, Microsoft javasolja a georedundáns tárolás használata úgy, hogy az egy második régióba replikálja az adatokat. Ügyfelek emellett rendelkeznie kell egy vész-helyreállítási terv regionális szolgáltatáskimaradás kezelésére. Fontos része annak a vész-helyreállítási terv előkészíti a feladatátvételt a másodlagos végpontra, abban az esetben, ha az elsődleges végpont elérhetetlenné válik. 

Azure Storage (előzetes verzió) fiók feladatátvételi georedundáns storage-fiókok támogatja. Fiók feladatátvétellel is kezdeményezhető a feladatátvételi folyamat a tárfiók, ha az elsődleges végpont elérhetetlenné válik. A feladatátvétel a másodlagos végpontot, hogy a tárfiók elsődleges végpontjába válnak frissíti. A feladatátvétel befejezése után az ügyfelek az új elsődleges végpontra tartalomkészítés elkezdéséhez.

Ez a cikk ismerteti a fogalmakat és a folyamat vesz egy fiók feladatátvétellel, és ismerteti az ügyfél hatása a lehető legkevesebb helyreállítását készíti elő a tárfiók. Egy fiók feladatátvétel inicializálása az Azure Portalon vagy a PowerShell kezelésével kapcsolatos információkért lásd: [egy fiók feladatátvétel (előzetes verzió)](storage-initiate-account-failover.md).

## <a name="choose-the-right-redundancy-option"></a>Válassza ki a megfelelő adatredundáns tárolási mód

Mindegyik tárfiókban lesznek replikálva a redundancia biztosítása érdekében. A redundancia lehetőséget választja, a fiók az ideális rugalmasságot fokú függ. A regionális üzemkimaradások utáni helyreállításon elleni védelem válassza ki a georedundáns tárolással vagy anélkül olvasási hozzáférés a másodlagos régióból lehetőséget:  

**Georedundáns tárolás (GRS)** replikálja az adatokat aszinkron módon történik a két olyan földrajzi régióban, amely legalább több száz mérföld távolságra van. Ha az elsődleges régió romlik a szolgáltatáskimaradás, a másodlagos régió az az adatok redundáns forrást funkcionál. Alakítsa át a másodlagos végpontot az elsődleges végpont feladatátvételt kezdeményezhet.

**Írásvédett georedundáns tárolás (RA-GRS)** georedundáns tárolást biztosít további előnye, hogy olvasási hozzáférés a másodlagos végpontra. Ha az elsődleges végpont leállás esetén az RA-GRS konfigurált, és magas rendelkezésre állású tervezett alkalmazások továbbra is a másodlagos végpontról való olvasáshoz. A Microsoft javasolja a RA-GRS maximális rugalmasságot alkalmazásai számára.

Más Azure Storage redundanciabeállításai közé tartozik a zónaredundáns tárolás (ZRS), amely replikálja az adatokat egy adott régióban rendelkezésre állási zónák között, és a helyileg redundáns tárolás (LRS), amely replikálja az adatokat egy régió egyetlen adatközpontban. Ha a tárfiók úgy van konfigurálva, a zrs-t vagy LRS, GRS vagy RA-GRS használja, ezt a fiókot alakíthatja. A fiók georedundáns tárolás konfigurálása a további költség tekintetében. További információkért lásd: [Azure Storage replikáció](storage-redundancy.md).

> [!WARNING]
> Georedundáns tárolás hordozza az adatvesztés kockázatát. Adatok replikálódnak a másodlagos régióba aszinkron módon történik, ami azt jelenti a késleltetés között, ha az elsődleges régióba írt adatok íródik a másodlagos régióba. Leállás esetén az írási műveletek az elsődleges végpontot, amely még nem replikálódott a másodlagos végpontra el fog veszni. 

## <a name="design-for-high-availability"></a>Magas rendelkezésre állás kialakítása

Fontos a kezdetektől a magas rendelkezésre állás érdekében az alkalmazás megtervezése. Tekintse meg az alkalmazás kialakítása és a vészhelyreállítási útmutató következő Azure-erőforrások:

* [Rugalmas alkalmazások tervezése az Azure](https://docs.microsoft.com/azure/architecture/resiliency/): Az Azure-ban magas rendelkezésre állású alkalmazások tervezése az a legfontosabb fogalmak áttekintése.
* [Rendelkezésre állási ellenőrzőlista](https://docs.microsoft.com/azure/architecture/checklist/availability): Ellenőrzőlista a ellenőrzése, hogy az alkalmazás megvalósítja a Tervező ajánlott magas rendelkezésre állás érdekében.
* [RA-GRS használatával magas rendelkezésre állású alkalmazások tervezése](storage-designing-ha-apps-with-ragrs.md): Tervezési útmutató a alkalmazásokat a RA-GRS előnyeinek kihasználása érdekében.
* [Oktatóanyag: A Blob storage magas rendelkezésre állású alkalmazás létrehozása](../blobs/storage-create-geo-redundant-storage.md): Oktatóanyag, amely bemutatja, hogyan hozhat létre, amely automatikusan átvált a végpontok kódhibáiként és helyreállítási műveletek közötti magas rendelkezésre állású alkalmazások szimulált vannak. 

Ezenkívül vegye figyelembe az ajánlott eljárások a magas rendelkezésre állás az Azure Storage-adatok mellett:

* **Lemezek:** Használat [Azure Backup](https://azure.microsoft.com/services/backup/) biztonsági mentése az Azure-beli virtuális gépek által használt virtuális gép lemezei. Emellett érdemes [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) regionális katasztrófa esetén a virtuális gépek védelme érdekében.
* **A blokkblobok használatát támogatják:** Kapcsolja be a [helyreállítható törlési](../blobs/storage-blob-soft-delete.md) objektumszintű törlések ellen, és felülírja, vagy másolja be egy másik régióban egy másik tárfiókba blokkblobok [AzCopy](storage-use-azcopy.md), [Azure PowerShell-lel ](storage-powershell-guide-full.md), vagy a [Azure Adatáthelyezés könyvtár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Fájlok:** Használat [AzCopy](storage-use-azcopy.md) vagy [Azure PowerShell-lel](storage-powershell-guide-full.md) a fájlok másolása egy másik tárfiók más régióban.
* **Táblák:** használata [AzCopy](storage-use-azcopy.md) táblák adatainak exportálása egy másik régióban egy másik tárfiókba.

## <a name="track-outages"></a>Kimaradások nyomon követése

Ügyfelek által előfizetett a [Azure szolgáltatásállapot-irányítópult](https://azure.microsoft.com/status/) állapotának és az Azure Storage és más Azure-szolgáltatások állapotának nyomon követéséhez.

A Microsoft azt javasolja, hogy az alkalmazás előkészítése az írási hibák lehetőségét, tervezzen. Az alkalmazás elérhetővé kell tennie az írási hibák úgy, hogy riasztást küld az elsődleges régióban leállás lehetőségét.

## <a name="understand-the-account-failover-process"></a>A fiók feladatátvételi folyamat ismertetése

Ügyfél által felügyelt fiók feladatátvétel (előzetes verzió) lehetővé teszi a feladatátvételt a teljes tárfiókot a másodlagos régióba, ha az elsődleges bármilyen okból elérhetetlenné válik. Feladatátvétel a másodlagos régióba kényszeríti, az ügyfelek megkezdődhet írja az adatokat a másodlagos végpontra a feladatátvétel befejezése után. A feladatátvétel jellemzően egy órát vesz igénybe.

### <a name="how-an-account-failover-works"></a>Egy fiók feladatátvételt működése

Normál körülmények ügyfél adatokat ír ugyanebbe az Azure Storage-fiók az elsődleges régióban, és az adatok replikálása aszinkron módon történik a másodlagos régióba. Az alábbi képen látható a forgatókönyvet, ha az elsődleges régióban érhető el:

![Az ügyfelek adatokat írni az elsődleges régióban lévő tárfiókba](media/storage-disaster-recovery-guidance/primary-available.png)

Ha az elsődleges végpont valamilyen okból elérhetetlenné válik, az ügyfél már nem írhatók, a storage-fiókot. Az alábbi képen látható a forgatókönyvet, ahol az elsődleges elérhetetlenné, de még nem történt:

![Az elsődleges nem érhető el, így az ügyfelek nem lehet adatokat írni.](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Az ügyfél kezdeményezi a fiók feladatátvételt a másodlagos végpontra. A feladatátvételi folyamat frissíti a DNS-bejegyzés Azure Storage által biztosított, hogy a másodlagos végpontra válik az új elsődleges végpont a storage-fiókok a következő képen látható módon:

![Ügyfél kezdeményezi a fiók feladatátvételt a másodlagos végpontra](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Írási hozzáférés GRS és RA-GRS fiókok helyreáll, a DNS-bejegyzés frissítve lett, és a kérelmek vannak az új elsődleges végpontra irányítja. Meglévő blobok, táblák, üzenetsorok és fájlokat a storage-Szolgáltatásvégpontok változatlan marad, a feladatátvétel után.

> [!IMPORTANT]
> A feladatátvétel befejezése után a tárfiók lett konfigurálva a helyileg redundáns új elsődleges végpontját. Folytatják a replikációt az új másodlagos, konfigurálja a fiókot használja a georedundáns tárolás újra (GRS vagy RA-GRS).
>
> Ne feledje, hogy az LRS-fiók konvertálása RA-GRS- vagy GRS egy költséget áll. Ez a költség az RA-GRS- vagy GRS egy feladatátvétel után használandó új elsődleges régióban a storage-fiók frissítése vonatkozik.  

### <a name="anticipate-data-loss"></a>Előrejelezheti az adatvesztést

> [!CAUTION]
> Egy fiók feladatátvételt általában magában foglalja a kis mértékű adatvesztést. Fontos megszegéseinek egy fiók feladatátvétel kezdeményezése.  

Adatok írása aszinkron módon történik az elsődleges régióból a másodlagos régióba, mert nincs mindig késleltetés előtt az elsődleges régióba írási a másodlagos régióba replikálja. Ha az elsődleges régió elérhetetlenné válik, a legújabb írást előfordulhat, hogy replikálása még nem rendelkezik lett a másodlagos régióba.

Kényszeríti a feladatátvételt, az elsődleges régióban lévő összes adat elvész a másodlagos régióba válik az új elsődleges régióban, és a storage-fiók van konfigurálva, a helyileg redundáns földrajzi beállítással rendelkeznek. A feladatátvétel történik, ha már replikálása a másodlagos minden adat változatlan marad. Azonban az elsődleges, amely még nem replikálódott a másodlagos bármilyen adat elvész véglegesen. 

A **utolsó szinkronizálás időpontja** tulajdonság azt jelzi, hogy a legtöbb legutóbbi ideje, hogy az elsődleges régióból történő adatok garantáltan alkalmazáskonfigurációjának a másodlagos régióba. A legutóbbi szinkronizálás időpontja előtt írt összes adat a másodlagos után előfordulhat, hogy nem minden bizonnyal a másodlagos és a lehetséges, hogy elvesznek a legutóbbi szinkronizálás időpontja írt adatok érhető el. Leállás Ez a tulajdonság segítségével megbecsülheti egy fiók feladatátvétel kezdeményezése felszámítunk adatvesztés mennyiségét. 

Ajánlott eljárásként tervezze meg az alkalmazását, hogy a várt adatok elvesztése kiértékelheti, hogy a legutóbbi szinkronizálás időpontja használhatja. Például ha az összes írási műveletet, majd összehasonlíthatja az idő a múlt írási műveletek utolsó jelentkezik szinkronizálási időpontját, hogy meghatározza, mely írási műveletek nem szinkronizált a másodlagos.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Legyen körültekintő, ha meghiúsul, térjen vissza az eredeti elsődleges

Átadja a feladatokat az elsődleges kiszolgálóról a másodlagos régióba, miután a tárfiók lett konfigurálva a helyileg redundáns az új elsődleges régióban. A geo-redundancia biztosítása érdekében a fiókot újra konfigurálhatja úgy, hogy használja a GRS vagy RA-GRS frissítésével. Ha a fiók újra a feladatátvételt követően a georedundáns tárolás van konfigurálva, az új elsődleges régió azonnal megkezdi az új, volt az eredeti feladatátvétel előtt az elsődleges másodlagos régióba replikálja az adatokat. Előtt az elsődleges-ben meglévő adatok a rendszer teljes mértékben replikálja az új másodlagos ideig is eltarthat.

Után a tárfiók a georedundancia átkonfigurálása, akkor lehet, hogy az új másodlagos az új elsődleges biztonsági mentésekből egy másik feladatátvételt kezdeményezzen. Ebben az esetben az eredeti elsődleges régióban, a feladatátvétel előtt az elsődleges régió újra lesz, és úgy van konfigurálva, hogy helyileg redundáns. A feladatátvétel utáni elsődleges régióban (az eredeti másodlagos) minden adat elveszett majd. Ha a tárfiókban lévő adatok még nem replikálódott az új másodlagos visszavétel előtt, sikerült esetén csökkenhet a fő adatvesztés. 

Súlyos adatvesztés elkerülése érdekében ellenőrizze a értékét a **utolsó szinkronizálás időpontja** tulajdonság visszavétel előtt. Hasonlítsa össze a legutóbb időpontokban az adatokat a legutóbbi szinkronizálás időpontja várt adatok elvesztése kiértékelheti, hogy az új elsődleges lett írva. 

## <a name="initiate-an-account-failover"></a>Fiók feladatátvételének indítása

Az Azure portal, PowerShell, Azure CLI-vel vagy az Azure Storage erőforrás-szolgáltató API-t egy fiók feladatátvételt kezdeményezhet. Feladatátvétel módjáról további információkért lásd: [egy fiók feladatátvétel (előzetes verzió)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Tudnivalók az előzetes verzió

fiók feladatátvételi GRS vagy RA-GRS használatával az Azure Resource Manager üzembe helyezések minden ügyfelünk esetében előzetes verzióban érhető el. Általános célú v1, általános célú v2 és Blob storage fióktípus esetében támogatottak. fiók feladatátvételi jelenleg érhető el ezekben a régiókban:

- USA 2. nyugati régiója
- USA nyugati középső régiója

Az előzetes verzió csak a nem éles használatra szolgál. Éles szolgáltatásiszint-szerződések (SLA) nem érhetők el jelenleg.

### <a name="register-for-the-preview"></a>Regisztráljon az előzetes verzió

Regisztráljon az előzetes verzióra, a következő parancsokat a PowerShellben. Ügyeljen arra, hogy lévő a helyőrzőt cserélje le a saját előfizetés-azonosító:

```PowerShell
Connect-AzureRmAccount -SubscriptionId <subscription-id>
Register-AzureRmProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Az előzetes verzióra jóváhagyását 1-2 nap telhet. Győződjön meg arról, hogy a regisztrációban jóvá lett hagyva, futtassa a következő parancsot:

```PowerShell
Get-AzureRmProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Néhány fontos megjegyzés 

Az ebben a szakaszban megismerheti, hogyan az alkalmazások és szolgáltatások hatással lehet egy feladatátvétel kényszeríti az előzetes verzió ideje alatt leírt további szempontok áttekintése.

#### <a name="azure-virtual-machines"></a>Azure virtuális gépek

Azure-beli virtuális gépek (VM) nincs egy fiók feladatátvétel részeként feladatátvételt. Ha az elsődleges régió elérhetetlenné válik, és átadja a feladatokat a másodlagos régióba, majd újra létre kell hozni olyan virtuális gépek a feladatátvételt követően kell. 

#### <a name="azure-unmanaged-disks"></a>Azure-os nem felügyelt lemezekre

Ajánlott eljárásként a Microsoft azt javasolja, nem felügyelt lemezek konvertálása a managed Disks szolgáltatásba. Azonban ha átadja a feladatokat egy fiókot, amely tartalmazza az Azure virtuális gépekhez csatolt nem felügyelt lemezekre van szüksége, szüksége lesz a virtuális gép leállítása a feladatátvétel kezdeményezése előtt.

Nem felügyelt lemezek és az Azure Storage szolgáltatásban tárolódnak. Ha egy virtuális Gépet az Azure-ban fut, a virtuális Géphez csatolt bármilyen nem felügyelt lemezek bérbe adott. Egy fiók feladatátvétel nem folytatható, ha egy blob bérletét. A feladatátvétel végrehajtásához kövesse az alábbi lépéseket:

1. Mielőtt hozzálátna, vegye figyelembe a nevek bármilyen nem felügyelt lemezek, a logikaiegység-számokhoz (LUN) és a virtuális gép, amelyhez csatlakozik. Így könnyebben csatlakoztassa újra a lemezeket a feladatátvétel után. 
2. A virtuális gép leállítására.
3. Törölje a virtuális Gépet, de a VHD-fájlokat a nem felügyelt lemezek megőrzése. Vegye figyelembe az idő, amikor a virtuális gép törlése.
4. Várja meg, amíg a **utolsó szinkronizálás időpontja** frissítve van, és a későbbi, mint az idő, amikor a virtuális gép törlése. Ebben a lépésben fontos, mert ha a másodlagos végpontra nem lett teljesen frissítve a VHD-fájlokat tartalmazó a feladatátvétel esetén, majd a virtuális gép nem működnek megfelelően az új elsődleges régióban.
5. A fiók feladatátvételt kezdeményezzen.
6. Várjon, amíg a fiók feladatátvétel befejeződött, és a másodlagos régióban az új elsődleges régió elérhetetlenné válik.
6. Storage-fiók létrehozása az új elsődleges régióban, és másolja azt a nem felügyelt lemezt.
7. Virtuális gép létrehozása az új elsődleges régióban, és csatlakoztassa újból a virtuális merevlemezeket.
8. Indítsa el az új virtuális Gépet.

Ne feledje, hogy az ideiglenes lemezen tárolt adatokat, ha a virtuális gép leállt.

#### <a name="azure-file-sync"></a>Azure File Sync

Az Azure File Sync fiók feladatátvételi támogatja. Azonban szüksége lesz a feladatátvétel befejezése után, konfigurálja újra az összes Azure File Sync-beállításokat.

### <a name="unsupported-features-or-services"></a>Nem támogatott szolgáltatások vagy szolgáltatások

A következő szolgáltatások vagy szolgáltatások nem támogatottak a fiók feladatátvételi előzetes kiadásban:

- Az Azure Data Lake Storage Gen2 hierarchikus fájlmegosztásokat nem lehet végrehajtani a feladatátvételt.
- Archivált blobokat tartalmazó tárfiókot nem lehet végrehajtani a feladatátvételt. Archivált nem tervezi a feladatátvételt egy önálló tárfiókot a blobok karbantartása.
- Prémium szintű blokkblobok tartalmazó tárfiókot nem lehet végrehajtani a feladatátvételt. Támogatja a blokkblobokat prémium szintű Storage-fiókok jelenleg nem támogatja georedundancia.

## <a name="copying-data-as-an-alternative-to-failover"></a>Adatok másolása a feladatátvételi helyett

Ha az RA-GRS konfigurálta a tárfiókot, majd rendelkezik olvasási hozzáférés az adatokhoz a másodlagos végpontot használja. Ha nem kívánja az átadja a feladatokat az elsődleges régióban leállás eszközöket használhat például [AzCopy](storage-use-azcopy.md), [Azure PowerShell-lel](storage-powershell-guide-full.md), vagy a [Azure Adatáthelyezés könyvtár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) , adatok másolása a másodlagos régióban a storage-fiókból egy nem érinti a régióban egy másik tárfiókba. Ezután mutasson a tárfiók az alkalmazások mindkét olvasási és írási.

## <a name="microsoft-managed-failover"></a>A Microsoft által felügyelt feladatátvétel

Szélsőséges esetben, ha egy régió jelentős katasztrófa miatt megszakadt, a Microsoft regionális feladatátvételt kezdeményezhet. Ebben az esetben az Ön részéről semmit nem kell. Mindaddig, amíg a Microsoft által felügyelt feladatátvétel befejeződött, nem kell írási hozzáférést a tárfiókhoz. Az alkalmazás is olvashat a másodlagos régióból, ha a tárfiók úgy van konfigurálva, az RA-GRS. 

## <a name="see-also"></a>Lásd még

* [Egy fiók feladatátvétel (előzetes verzió)](storage-initiate-account-failover.md)
* [Magas rendelkezésre állású alkalmazások tervezése az RA-GRS használatával](storage-designing-ha-apps-with-ragrs.md)
* [Oktatóanyag: A Blob storage magas rendelkezésre állású alkalmazás létrehozása](../blobs/storage-create-geo-redundant-storage.md) 
