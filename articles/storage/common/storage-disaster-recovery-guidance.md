---
title: Vész-helyreállítási és tárfiók feladatátvétel (előzetes verzió)
titleSuffix: Azure Storage
description: Az Azure Storage támogatja a fiók feladatátvételt (előzetes verzió) a georedundáns tárfiókokhoz. A fiók feladatátvétel, kezdeményezheti a feladatátvételi folyamat a tárfiók, ha az elsődleges végpont elérhetetlenné válik.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7a8f6d0d3ab3b456c41128da9b689f6b7eda0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365363"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>Vész-helyreállítási és fiókfeladat-átvétel (előzetes verzió)

A Microsoft törekszik annak biztosítására, hogy az Azure-szolgáltatások mindig elérhetők legyenek. Azonban nem tervezett szolgáltatáskimaradások fordulhatnak elő. Ha az alkalmazás rugalmasságra van szükség, a Microsoft georedundáns tárolás használatát javasolja, hogy az adatok egy második régióba másolódjanak. Emellett az ügyfelek egy vész-helyreállítási terv a regionális szolgáltatás kimaradás kezelésére. A vész-helyreállítási terv fontos része a feladatátvétel előkészítése a másodlagos végpontra abban az esetben, ha az elsődleges végpont elérhetetlenné válik.

Az Azure Storage támogatja a fiók feladatátvételt (előzetes verzió) a georedundáns tárfiókokhoz. A fiók feladatátvétel, kezdeményezheti a feladatátvételi folyamat a tárfiók, ha az elsődleges végpont elérhetetlenné válik. A feladatátvétel frissíti a másodlagos végpont lesz az elsődleges végpont a tárfiók. Miután a feladatátvétel befejeződött, az ügyfelek megkezdhetik az írást az új elsődleges végpontra.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Ez a cikk ismerteti a fogalmakat és a folyamat ot egy fiók feladatátvétel, és ismerteti, hogyan készítse elő a tárfiókot a helyreállítása a legkisebb ügyfél hatása. Ha tudni szeretné, hogyan kezdeményezhet egy fiókfeladat-átvételt az Azure Portalon vagy a PowerShellben, [olvassa el a Fiók feladatátvételének kezdeményezése (előzetes verzió) című témakört.](storage-initiate-account-failover.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Válassza ki a megfelelő redundancia lehetőséget

Az Azure Storage a tartósság és a magas rendelkezésre állás érdekében több példányban is megőrzi a tárfiókot. A fiókhoz választott redundanciabeállítás a szükséges rugalmasság mértékétől függ. A regionális kimaradások elleni védelemérdekében válassza a georedundáns tárolást, a másodlagos régióból való olvasási hozzáférés lehetőségével vagy anélkül:  

**A georedundáns tárolás (GRS) vagy a földrajzi zónaredundáns tárolás (GZRS) (előzetes verzió)** aszinkron módon másolja az adatokat két földrajzi régióban, amelyek legalább több száz mérföldtávolságra vannak egymástól. Ha az elsődleges régió ban kimaradás, majd a másodlagos régió szolgál redundáns forrásként az adatok. Kezdeményezhet egy feladatátvételt a másodlagos végpont elsődleges végpontká való átalakításához.

**Az olvasási hozzáférésű georedundáns tárolás (RA-GRS) vagy az olvasási hozzáférésű geozónaredundáns tárolás (RA-GZRS) (előzetes verzió)** georedundáns tárolást biztosít a másodlagos végponthoz való olvasási hozzáférés további előnyeivel. Ha az elsődleges végpontban kimaradás történik, az RA-GRS-re konfigurált és magas rendelkezésre állásra tervezett alkalmazások továbbra is olvashatnak a másodlagos végpontról. A Microsoft a RA-GRS-t ajánlja az alkalmazások maximális rugalmassága érdekében.

Az Azure Storage-ban történt redundanciáról az [Azure Storage redundanciája](storage-redundancy.md)című témakörben talál további információt.

> [!WARNING]
> A georedundáns tárolás adatvesztés veszélyével jár. Az adatok másolása a másodlagos régióba aszinkron módon történik, ami azt jelenti, hogy az elsődleges régióba írt adatok másodlagos régióba írása között késés van. Kimaradás esetén az elsődleges végpontra, amelyek még nem másoltak a másodlagos végpontba, írási műveletek elvesznek.

## <a name="design-for-high-availability"></a>Tervezés magas rendelkezésre álláshoz

Fontos, hogy tervezze meg az alkalmazást a magas rendelkezésre állás a kezdetektől fogva. Tekintse meg ezeket az Azure-erőforrások at az alkalmazás tervezéséhez és a vész-helyreállítási tervezéshez:

- [Rugalmas alkalmazások tervezése az Azure-hoz:](/azure/architecture/checklist/resiliency-per-service)A magas rendelkezésre állású alkalmazások Azure-beli tervezéséhez használható kulcsfontosságú fogalmak áttekintése.
- [Rendelkezésre állási ellenőrzőlista:](/azure/architecture/checklist/resiliency-per-service)Ellenőrzőlista annak ellenőrzésére, hogy az alkalmazás megvalósítja-e a legjobb tervezési eljárásokat a magas rendelkezésre állás érdekében.
- [Magas rendelkezésre állású alkalmazások tervezése RA-GRS használatával:](storage-designing-ha-apps-with-ragrs.md)Tervezési útmutató az alkalmazások készítéséhez az RA-GRS előnyeinek kihasználása érdekében.
- [Oktatóanyag: Magas rendelkezésre állású alkalmazást hozhat létre a Blob storage- funkcióval:](../blobs/storage-create-geo-redundant-storage.md)Oktatóanyag, amely bemutatja, hogyan hozhat létre egy magas rendelkezésre állású alkalmazást, amely automatikusan vált a végpontok között, mivel a hibák és a helyreállítások szimulálva vannak. 

Emellett tartsa szem előtt az Azure Storage-adatok magas rendelkezésre állásának fenntartásához szükséges ajánlott eljárásokat:

- **Lemezek:** Az [Azure Backup](https://azure.microsoft.com/services/backup/) használatával biztonsági másolatot készíthet az Azure virtuális gépei által használt virtuális géplemezekről. Érdemes az [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) használatával is védeni a virtuális gépeket regionális katasztrófa esetén.
- **Blobok blokkolása:** Kapcsolja be a [helyreállítható törlést](../blobs/storage-blob-soft-delete.md) az objektumszintű törlés és felülírások elleni védelem érdekében, vagy másolja a blokkblobokat egy másik régió egy másik tárfiókjába az [AzCopy](storage-use-azcopy.md), [az Azure PowerShell](storage-powershell-guide-full.md)vagy az [Azure Data Movement könyvtár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)használatával.
- **Fájlok:** Az [AzCopy](storage-use-azcopy.md) vagy [az Azure PowerShell](storage-powershell-guide-full.md) segítségével másolja a fájlokat egy másik régióban lévő másik tárfiókba.
- **Táblák:** az [AzCopy](storage-use-azcopy.md) segítségével exportálja a táblaadatokat egy másik régióban lévő másik tárfiókba.

## <a name="track-outages"></a>Pályakimaradások

Az ügyfelek előfizethetnek az [Azure Service Health Dashboard szolgáltatásra](https://azure.microsoft.com/status/) az Azure Storage és más Azure-szolgáltatások állapotának és állapotának nyomon követéséhez.

A Microsoft azt is javasolja, hogy tervezze meg az alkalmazást, hogy felkészüljenek az írási hibák lehetőségére. Az alkalmazás nak olyan módon kell elérhetővé tennie az írási hibákat, amely figyelmezteti az elsődleges régióban egy kimaradás lehetőségére.

## <a name="understand-the-account-failover-process"></a>A fiók feladatátvételi folyamatának megismerése

Az ügyfél által kezelt fiók feladatátvétele (előzetes verzió) lehetővé teszi, hogy a teljes tárfiókot a másodlagos régióba irányítsa, ha az elsődleges bármilyen okból elérhetetlenné válik. Ha egy feladatátvételt kényszerít a másodlagos régióba, az ügyfelek megkezdhetik az adatok írását a másodlagos végpontra a feladatátvétel befejezése után. A feladatátvétel általában körülbelül egy órát vesz igénybe.

### <a name="how-an-account-failover-works"></a>Hogyan működik a fiók feladatátvétele

Normál körülmények között az ügyfél adatokat ír egy Azure Storage-fiókaz elsődleges régióban, és az adatok másolása aszinkron módon a másodlagos régióba. Az alábbi képen látható a forgatókönyv, amikor az elsődleges régió elérhető:

![Az ügyfelek adatokat írnak az elsődleges régió tárfiókjába](media/storage-disaster-recovery-guidance/primary-available.png)

Ha az elsődleges végpont bármilyen okból elérhetetlenné válik, az ügyfél már nem tud írni a tárfiókba. Az alábbi képen látható az a forgatókönyv, amelyben az elsődleges elérhetetlenné vált, de még nem történt helyreállítás:

![Az elsődleges nem érhető el, így az ügyfelek nem tudnak adatokat írni](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Az ügyfél kezdeményezi a fiók feladatátvételt a másodlagos végpontra. A feladatátvételi folyamat frissíti az Azure Storage által biztosított DNS-bejegyzést, így a másodlagos végpont lesz a tárfiók új elsődleges végpontja, ahogy az az alábbi képen látható:

![Az ügyfél kezdeményezi a másodlagos végpontra irányuló fiókfeladat-átvételt](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

A DNS-bejegyzés frissítése és a kérelmek új elsődleges végpontra való átirányítása után a GRS- és RA-GRS-fiókok írási hozzáférése helyreáll. A blobok, táblák, várólisták és fájlok meglévő tárolási szolgáltatásvégpontjai a feladatátvétel után változatlanok maradnak.

> [!IMPORTANT]
> A feladatátvétel befejezése után a tárfiók helyileg redundánsaz új elsődleges végponton. Az új másodlagos replikáció folytatásához konfigurálja úgy a fiókot, hogy újra georedundáns tárolást használjon (RA-GRS vagy GRS).
>
> Ne feledje, hogy az LRS-fiók RA-GRS-re vagy GRS-re történő konvertálása költséggel jár. Ez a költség az új elsődleges régiótárfiók frissítésére vonatkozik az RA-GRS vagy a GRS használatával feladatátvétel után.  

### <a name="anticipate-data-loss"></a>Adatvesztés előrejelzése

> [!CAUTION]
> A fiók feladatátvétele általában adatvesztést jelent. Fontos, hogy tisztában legyen a fiókfeladat-átvétel megvonásának következményeivel.  

Mivel az adatok aszinkron módon vannak megírva az elsődleges régióból a másodlagos régióba, mindig van egy késleltetés, mielőtt az elsődleges régióba történő írást átmásolja a másodlagos régióba. Ha az elsődleges régió elérhetetlenné válik, előfordulhat, hogy a legutóbbi írási műveletek még nem lettek átmásolva a másodlagos régióba.

Feladatátvétel kényszerítésekor az elsődleges régióban lévő összes adat elvész, ahogy a másodlagos régió lesz az új elsődleges régió, és a tárfiók helyiredundánsként van konfigurálva. A másodlagosra már másolt összes adat megmarad, amikor a feladatátvétel megtörténik. Azonban az elsődleges, hogy nem is másolt a másodlagos adatok véglegesen elvesznek.

Az **Utolsó szinkronizálási idő** tulajdonság azt jelzi, hogy az elsődleges régióból származó adatok garantáltan mikor lettek a másodlagos régióba írva. Az utolsó szinkronizálási idő előtt írt összes adat elérhető a másodlagos, míg az utolsó szinkronizálási idő után írt adatok nem írt a másodlagos, és elveszhet. Használja ezt a tulajdonságot kimaradás esetén, hogy megbecsülje a fiók feladatátvételének megkötésével felmerülő adatvesztés mértékét.

Ajánlott eljárásként úgy tervezze meg az alkalmazást, hogy az utolsó szinkronizálási idő segítségével kiértékelhesse a várható adatvesztést. Ha például az összes írási műveletet naplózza, akkor összehasonlíthatja az utolsó írási műveletek idejét az utolsó szinkronizálási idővel annak meghatározásához, hogy mely írási műveletek nincsenek szinkronizálva a másodlagossal.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Legyen elővigyázatos, ha nem felel meg az eredeti elsődleges

Miután feladatátvételt az elsődleges a másodlagos régióban, a tárfiók van beállítva, hogy helyileg redundáns az új elsődleges régióban. A fiók georedundancia újra konfigurálható, ha frissíti grs vagy RA-GRS használatával. Ha a fiók a feladatátvétel után ismét georedundanciára van konfigurálva, az új elsődleges régió azonnal megkezdi az adatok másolását az új másodlagos régióba, amely az eredeti feladatátvétel előtt volt az elsődleges. Azonban eltarthat egy ideig, amíg a meglévő adatok az elsődleges teljes mértékben másolja az új másodlagos.

Miután a tárfiók georedundancia újra konfigurálása, lehetséges, hogy kezdeményezzen egy másik feladatátvételaz új elsődleges vissza az új másodlagos. Ebben az esetben a feladatátvételt megelőző eredeti elsődleges régió ismét az elsődleges régióvá válik, és helyileg redundánssá van konfigurálva. A feladatátvétel utáni elsődleges régióban (az eredeti másodlagos) lévő összes adat elvész. Ha a tárfiókban lévő adatok nagy része nem lett átmásolva az új másodlagos, mielőtt visszaszeretné adni a feladat-visszavételt, súlyos adatvesztést szenvedhet el.

A nagyobb adatvesztés elkerülése érdekében ellenőrizze az **Utolsó szinkronizálás iódje** tulajdonság értékét, mielőtt visszalenne. Hasonlítsa össze az utolsó szinkronizálási időt az utolsó alkalommal, amikor az adatokat írt az új elsődleges a várható adatvesztés kiértékeléséhez. 

## <a name="initiate-an-account-failover"></a>Fiók feladatátvételének indítása

Kezdeményezhet egy fiók feladatátvételt az Azure Portalon, a PowerShell, az Azure CLI vagy az Azure Storage erőforrás-szolgáltató API-t. A feladatátvétel kezdeményezésével kapcsolatos további tudnivalókért olvassa el a [Fiók feladatátvételének kezdeményezése (előzetes verzió) című témakört.](storage-initiate-account-failover.md)

## <a name="about-the-preview"></a>Az előnézet – kapcsolat

A fiókfeladat-átvétel előzetes verzióban érhető el a GRS vagy RA-GRS szolgáltatást használó összes ügyfél számára az Azure Resource Manager-telepítésekkel. Általános célú v1, Általános célú v2 és Blob tárfiók típusok támogatottak. A fiókfeladat-átvétel jelenleg minden nyilvános régióban elérhető. A fiókfeladat-átvétel jelenleg nem érhető el szuverén/nemzeti felhőkben.

Az előzetes verzió csak nem éles használatra készült. A termelési szolgáltatásiszint-szerződések (SLOS-ok) jelenleg nem érhetők el.

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

Tekintse át az ebben a szakaszban ismertetett további szempontokat, hogy tisztában legyen az alkalmazások és szolgáltatások érintettállásával, ha az előzetes verzióban feladatátvételt kényszerít.

#### <a name="storage-account-containing-archived-blobs"></a>Archivált blobokat tartalmazó tárfiók

Archivált blobokat tartalmazó tárfiókok támogatási fiók feladatátvételt. Miután a feladatátvétel befejeződött, a fiók grs-re vagy RA-GRS-re való visszakonvertálásához az összes archivált blobot először egy online rétegre kell hidratálni.

#### <a name="storage-resource-provider"></a>Tárolásierőforrás-szolgáltató

Miután a feladatátvétel befejeződött, az ügyfelek újra olvashatják és írhatják az Azure Storage-adatokat az új elsődleges régióban. Azonban az Azure Storage-erőforrás-szolgáltató nem veszi át a feladatát, így az erőforrás-kezelési műveletek továbbra is az elsődleges régióban kell zajlania. Ha az elsődleges régió nem érhető el, nem fog tudni felügyeleti műveleteket végrehajtani a tárfiókon.

Mivel az Azure Storage-erőforrás-szolgáltató nem adja át a feladatátvételt, a [Hely](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) tulajdonság visszaadja az eredeti elsődleges helyet a feladatátvétel befejezése után.

#### <a name="azure-virtual-machines"></a>Azure virtuális gépek

Az Azure virtuális gépek (VM-ek) nem feladatátvétel i számlafeladat-átvétel részeként. Ha az elsődleges régió elérhetetlenné válik, és a feladatátvétela a másodlagos régióba, majd újra létre kell hoznia a virtuális gépeket a feladatátvétel után. Emellett a fiók feladatátvételhez társított potenciális adatvesztés is előfordulhat. A Microsoft a következő [magas rendelkezésre állású](../../virtual-machines/windows/manage-availability.md) és [vész-helyreállítási](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md) útmutatót javasolja az Azure-beli virtuális gépekre vonatkozóan.

#### <a name="azure-unmanaged-disks"></a>Az Azure nem felügyelt lemezei

Ajánlott eljárásként a Microsoft azt javasolja, hogy a nem felügyelt lemezeket konvertálják felügyelt lemezekké. Azonban ha egy azure-beli virtuális gépekhez csatlakoztatott nem felügyelt lemezeket tartalmazó fiókot kell átvennie, a feladatátvétel megkezdése előtt le kell állítania a virtuális gépet.

A nem felügyelt lemezek lapblobként tárolódnak az Azure Storage-ban. Ha egy virtuális gép fut az Azure-ban, a virtuális géphez csatlakoztatott nem felügyelt lemezek bérletbe kerülnek. A fiók feladatátvételnem folytatható, ha egy blob bérlet van. A feladatátvétel végrehajtásához hajtsa végre az alábbi lépéseket:

1. Mielőtt elkezdené, vegye figyelembe a nem felügyelt lemezek nevét, logikai egységszámaik (LUN) és a virtuális gép nevét, amelyhez kapcsolódnak. Ezzel megkönnyíti a lemezek újbóli csatlakoztatását a feladatátvétel után.
2. Állítsa le a virtuális gép.
3. Törölje a virtuális gép, de megtartja a Virtuális merevlemez-fájlokat a nem felügyelt lemezek. Vegye figyelembe az időpontot, amikor törölte a virtuális gép.
4. Várjon, amíg az **utolsó szinkronizálási idő** frissül, és később, mint az az időpont, amikor törölte a virtuális gép. Ez a lépés azért fontos, mert ha a másodlagos végpont nem lett teljes mértékben frissítve a virtuális merevlemez-fájlokkal, amikor a feladatátvétel történik, majd a virtuális gép nem fog megfelelően működni az új elsődleges régióban.
5. A fiók feladatátvételének kezdeményezése.
6. Várjon, amíg a fiók feladatátvétel befejeződik, és a másodlagos régió lett az új elsődleges régió.
7. Hozzon létre egy virtuális gép az új elsődleges régióban, és csatlakoztassa újra a Virtuálisgépek.
8. Indítsa el az új virtuális gép.

Ne feledje, hogy az ideiglenes lemezen tárolt adatok elvesznek a virtuális gép leállításakor.

### <a name="unsupported-features-and-services"></a>Nem támogatott szolgáltatások és szolgáltatások

Az előzetes verzióban a fiókfeladat-átvételhez a következő funkciók és szolgáltatások nem támogatottak:

- Az Azure File Sync nem támogatja a tárfiók feladatátvételt. A Azure File Syncben felhőbeli végpontként használt Azure-fájlmegosztásokat tartalmazó tárfiókokon nem lehet feladatátvételt végezni. Feladatátvétel esetén a szinkronizálás leáll, és az újonnan rétegzett fájlok esetében váratlan adatvesztést is okozhat.
- Az ADLS Gen2 tárfiókok (a hierarchikus névtérrel rendelkező fiókok) jelenleg nem támogatottak.
- Prémium szintű blokkblobokat tartalmazó tárfiókot nem lehet feladatátvételre. A prémium szintű blokkblobokat támogató storage-fiókok jelenleg nem támogatják a földrajzi redundanciát.
- A [WORM-megváltoztatósági házirendet](../blobs/storage-blob-immutable-storage.md) engedélyező tárolókat tartalmazó tárfiók nem adható át feladatátvételre. Feloldott/zárolt időalapú megőrzési vagy jogi visszatartási szabályzatok megakadályozzák a feladatátvétel tartjuk fenn a megfelelőség érdekében.
- A feladatátvétel befejezése után a következő funkciók leállhatnak, ha eredetileg engedélyezve van: [Esemény-előfizetések](../blobs/storage-blob-event-overview.md), [Csatorna módosítása](../blobs/storage-blob-change-feed.md), [Életciklus-szabályzatok](../blobs/storage-lifecycle-management-concepts.md)és [Storage Analytics-naplózás.](storage-analytics-logging.md)

## <a name="copying-data-as-an-alternative-to-failover"></a>Adatok másolása a feladatátvétel alternatívájaként

Ha a tárfiók ra-GRS- hez van konfigurálva, akkor olvasási hozzáféréssel rendelkezik az adatokhoz a másodlagos végpont használatával. Ha nem szeretné, hogy feladatátvétel esetén az elsődleges régióban kimaradás, az eszközök, például az [AzCopy,](storage-use-azcopy.md) [az Azure PowerShell,](storage-powershell-guide-full.md)vagy az [Azure Data Movement könyvtár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) adatok másolása a tárfiókból a másodlagos régióban egy másik tárfiók egy nem érintett régióban. Ezután irányíthatja az alkalmazásokat, hogy a tárfiók olvasási és írási rendelkezésre állás.

> [!CAUTION]
> A fiókfeladat-átvétel nem használható az adatáttelepítési stratégia részeként.


## <a name="microsoft-managed-failover"></a>Microsoft által felügyelt feladatátvétel

Szélsőséges körülmények között, ha egy régió egy jelentős katasztrófa miatt vész el, a Microsoft regionális feladatátvételt kezdeményezhet. Ebben az esetben nincs szükség beavatkozásra az Ön részéről. Amíg a Microsoft által felügyelt feladatátvétel befejeződik, nem lesz írási hozzáférése a tárfiókhoz. Az alkalmazások a másodlagos régióból is olvashatnak, ha a tárfiók RA-GRS-re van konfigurálva. 

## <a name="see-also"></a>Lásd még

- [Fiók feladatátvételének kezdeményezése (előzetes verzió)](storage-initiate-account-failover.md)
- [Magas rendelkezésre állású alkalmazások tervezése az RA-GRS használatával](storage-designing-ha-apps-with-ragrs.md)
- [Oktatóanyag: Magas rendelkezésre állású alkalmazás létrehozása a Blob storage segítségével](../blobs/storage-create-geo-redundant-storage.md) 
