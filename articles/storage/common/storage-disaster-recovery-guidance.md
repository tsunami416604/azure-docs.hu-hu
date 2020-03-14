---
title: Vész-helyreállítási és Storage-fiók feladatátvétele (előzetes verzió)
titleSuffix: Azure Storage
description: Az Azure Storage támogatja a fiók feladatátvételét (előzetes verzió) a Geo-redundáns tárolási fiókokhoz. A fiók feladatátvétele esetén kezdeményezheti a tárolási fiók feladatátvételi folyamatát, ha az elsődleges végpont elérhetetlenné válik.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f7a8f6d0d3ab3b456c41128da9b689f6b7eda0f7
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365363"
---
# <a name="disaster-recovery-and-account-failover-preview"></a>Vész-helyreállítás és fiók feladatátvétele (előzetes verzió)

A Microsoft igyekszik biztosítani, hogy az Azure-szolgáltatások mindig elérhetők legyenek. A nem tervezett szolgáltatások azonban előfordulhatnak. Ha az alkalmazása rugalmasságot igényel, a Microsoft a Geo-redundáns tárolás használatát javasolja, hogy az adatai egy második régióba legyenek másolva. Emellett az ügyfeleknek vészhelyzeti helyreállítási tervvel kell rendelkezniük a regionális szolgáltatások kimaradásának kezelésére. A vész-helyreállítási terv fontos része arra készül, hogy átadja a feladatátvételt a másodlagos végpontnak abban az esetben, ha az elsődleges végpont elérhetetlenné válik.

Az Azure Storage támogatja a fiók feladatátvételét (előzetes verzió) a Geo-redundáns tárolási fiókokhoz. A fiók feladatátvétele esetén kezdeményezheti a tárolási fiók feladatátvételi folyamatát, ha az elsődleges végpont elérhetetlenné válik. A feladatátvétel frissíti a másodlagos végpontot, hogy az a Storage-fiók elsődleges végpontja legyen. A feladatátvétel befejeződése után az ügyfelek megkezdhetik az új elsődleges végpont írását.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Ez a cikk a fiók feladatátvételével kapcsolatos fogalmakat és folyamatokat ismerteti, és bemutatja, hogyan készítheti elő a Storage-fiókját a lehető legkevesebb vásárlói hatású helyreállításra. Ha meg szeretné tudni, hogyan kezdeményezheti a fiók feladatátvételét a Azure Portal vagy a PowerShellben, tekintse meg a [fiók feladatátvételének kezdeményezése (előzetes verzió)](storage-initiate-account-failover.md)című témakört.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Válassza ki a megfelelő redundancia lehetőséget

Az Azure Storage több példányban tárolja a Storage-fiókot a tartósság és a magas rendelkezésre állás biztosítása érdekében. A fiókhoz választott redundancia-beállítás a szükséges rugalmasság mértékétől függ. A regionális kimaradások elleni védelemhez válassza a Geo-redundáns tárolás lehetőséget a másodlagos régió olvasási hozzáférésének beállításával vagy anélkül:  

A **geo-redundáns tárolás (GRS) vagy a Geo-Zone-redundáns tárolás (GZRS) (előzetes verzió)** aszinkron módon másolja át az adatait két földrajzi régióba, amelyek legalább száz mérföld távolságra vannak egymástól. Ha az elsődleges régió leállás miatt leáll, a másodlagos régió redundáns forrásként szolgál az adataihoz. A másodlagos végpont elsődleges végpontra történő átalakításához feladatátvételt indíthat.

Az olvasási hozzáférésű **geo-redundáns tárolás (ra-GRS) vagy az olvasási hozzáférésű földrajzi zóna – redundáns tárolás (ra-GZRS) (előzetes verzió)** a földrajzilag redundáns tárolást biztosít a másodlagos végpont olvasási hozzáférésének további előnyeivel. Ha áramkimaradás fordul elő az elsődleges végponton, az RA-GRS konfigurált és magas rendelkezésre állásra tervezett alkalmazások továbbra is olvashatók a másodlagos végpontról. A Microsoft javasolja az RA-GRS számára az alkalmazások maximális rugalmasságát.

További információ az Azure Storage-beli redundanciáról: [Azure Storage redundancia](storage-redundancy.md).

> [!WARNING]
> A Geo-redundáns tárolás az adatvesztés kockázatát hordozza. Az Adatmásolást aszinkron módon másolja a rendszer a másodlagos régióba, ami azt jelenti, hogy az elsődleges régióba írt adatsorok a másodlagos régióba íródnak. Leállás esetén a másodlagos végpontra még nem másolt írási műveletek elvesznek az elsődleges végpontra.

## <a name="design-for-high-availability"></a>Tervezés magas rendelkezésre álláshoz

Fontos, hogy az alkalmazást a kezdettől kezdve a magas rendelkezésre állás érdekében tervezze meg. Tekintse át ezeket az Azure-erőforrásokat az alkalmazások tervezéséhez és a vész-helyreállítás megtervezéséhez:

- [Rugalmas alkalmazások tervezése az Azure](/azure/architecture/checklist/resiliency-per-service)-hoz: áttekintés az Azure-beli, magasan elérhető alkalmazások tervezéséhez szükséges alapfogalmakról.
- [Rendelkezésre állási ellenőrzőlista](/azure/architecture/checklist/resiliency-per-service): ellenőrzőlista annak ellenőrzéséhez, hogy az alkalmazása megvalósítja-e a legjobb tervezési eljárásokat a magas rendelkezésre állás érdekében.
- [Magasan elérhető alkalmazások tervezése ra-GRS](storage-designing-ha-apps-with-ragrs.md): tervezési útmutató az alkalmazások létrehozásához az ra-GRS használatával.
- [Oktatóanyag: hozzon létre egy magasan elérhető alkalmazást a blob Storage szolgáltatással](../blobs/storage-create-geo-redundant-storage.md): ez az oktatóanyag bemutatja, hogyan hozhat létre egy olyan, magasan elérhető alkalmazást, amely automatikusan átvált a végpontok között a meghibásodások és a helyreállítások szimulálása érdekében. 

Emellett vegye figyelembe ezeket az ajánlott eljárásokat az Azure Storage-beli adattárolási adatai magas rendelkezésre állásának fenntartásához:

- **Lemezek:** A [Azure Backup](https://azure.microsoft.com/services/backup/) használatával biztonsági mentést készíthet az Azure-beli virtuális gépek által használt VM-lemezekről. Vegye fontolóra a [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) használatát a virtuális gépek biztonságának biztosítása érdekében regionális katasztrófa esetén is.
- **Blobok letiltása:** Ha a [AzCopy](storage-use-azcopy.md), a [Azure PowerShell](storage-powershell-guide-full.md)vagy az [Azure adatátviteli függvénytárat](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)használja, a [Soft delete](../blobs/storage-blob-soft-delete.md) bekapcsolásával védelmet biztosíthat az objektum-szintű törlések és a felülírások ellen, vagy más régióba másolja a blokk blobokat egy másikba.
- **Fájlok:** A [AzCopy](storage-use-azcopy.md) vagy a [Azure PowerShell](storage-powershell-guide-full.md) segítségével másolja át a fájlokat egy másik különböző régióban lévő Storage-fiókba.
- **Táblák:** a [AzCopy](storage-use-azcopy.md) használatával exportálhatja a tábla-és más tárolási fiókokat egy másik régióban.

## <a name="track-outages"></a>Kimaradások nyomon követése

Az ügyfelek előfizethetnek a [Azure Service Health irányítópultra](https://azure.microsoft.com/status/) az Azure Storage és más Azure-szolgáltatások állapotának és állapotának nyomon követéséhez.

A Microsoft azt is javasolja, hogy tervezze meg az alkalmazást, hogy előkészítse az írási hibák lehetőségét. Az alkalmazásnak olyan módon kell kitennie az írási hibákat, amely riasztást küld az elsődleges régión belüli kimaradás lehetőségéről.

## <a name="understand-the-account-failover-process"></a>A fiók feladatátvételi folyamatának megismerése

Az ügyfél által felügyelt fiók feladatátvétele (előzetes verzió) lehetővé teszi, hogy a teljes Storage-fiókját a másodlagos régióra irányítsa, ha az elsődleges elérhetetlenné válik bármilyen okból. Ha a feladatátvételt a másodlagos régióra kényszeríti, az ügyfelek megkezdhetik az adatírást a másodlagos végpontnak a feladatátvétel befejeződése után. A feladatátvétel általában körülbelül egy órát vesz igénybe.

### <a name="how-an-account-failover-works"></a>A fiók feladatátvételének működése

Normális körülmények között az ügyfél az elsődleges régióban lévő Azure Storage-fiókba ír egy adatot, és a rendszer aszinkron módon másolja át az adatot a másodlagos régióba. Az alábbi képen látható az az eset, amikor az elsődleges régió elérhető:

![Az ügyfelek az elsődleges régióban lévő Storage-fiókba írják az adatbevitelt](media/storage-disaster-recovery-guidance/primary-available.png)

Ha az elsődleges végpont bármilyen okból elérhetetlenné válik, az ügyfél már nem tud írni a Storage-fiókba. Az alábbi képen látható az az eset, amikor az elsődleges elérhetetlenné vált, de még nem történt helyreállítás:

![Az elsődleges érték nem érhető el, így az ügyfelek nem tudnak írni adatbevitelt](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Az ügyfél kezdeményezi a fiók feladatátvételét a másodlagos végpontra. A feladatátvételi folyamat frissíti az Azure Storage által biztosított DNS-bejegyzést, hogy a másodlagos végpont a Storage-fiók új elsődleges végpontja legyen, ahogy az a következő képen látható:

![Az ügyfél a fiók feladatátvételét másodlagos végpontra indítja](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

A rendszer a DNS-bejegyzés frissítése után visszaállítja a GRS és az RA-GRS fiókok írási hozzáférését, és a kérelmeket az új elsődleges végpontra irányítja. A meglévő Storage Service-végpontok a Blobok, táblák, várólisták és fájlok esetében változatlanok maradnak a feladatátvétel után.

> [!IMPORTANT]
> A feladatátvétel befejeződése után a Storage-fiók úgy van konfigurálva, hogy helyileg redundáns legyen az új elsődleges végponton. Ha folytatni szeretné a replikálást az új másodlagosra, konfigurálja úgy a fiókot, hogy a Geo-redundáns tárolót újra használhassa (vagy RA-GRS vagy GRS).
>
> Ne feledje, hogy egy LRS-fiók RA-GRS vagy GRS-re való konvertálása költségekkel jár. Ez a díj az új elsődleges régióban lévő Storage-fiók frissítésére vonatkozik az RA-GRS vagy a GRS feladatátvétel utáni használata esetén.  

### <a name="anticipate-data-loss"></a>Az adatvesztés várható

> [!CAUTION]
> A fiók feladatátvétele általában valamilyen adatvesztéssel jár. Fontos megérteni a fiók feladatátvételének megkezdésének következményeit.  

Mivel az adatok aszinkron módon íródnak az elsődleges régióból a másodlagos régióba, mindig késés van, mielőtt a rendszer átmásolja az elsődleges régióba való írást a másodlagos régióba. Ha az elsődleges régió elérhetetlenné válik, előfordulhat, hogy a legutóbbi írások még nem lettek átmásolva a másodlagos régióba.

Ha kényszeríti a feladatátvételt, az elsődleges régióban lévő összes érték elvész, mivel a másodlagos régió lesz az új elsődleges régió, és a Storage-fiók úgy van beállítva, hogy helyileg redundáns legyen. A rendszer megőrzi a másodlagosra másolt összes adathalmazt, ha a feladatátvétel történik. Az elsődlegesnek írt, de a másodlagosra nem másolt összes adattal véglegesen elvész.

A **legutóbbi szinkronizálás időpontja** tulajdonság azt jelzi, hogy az elsődleges régióból érkező adatok mikor lettek kiírva a másodlagos régióba. A rendszer a legutóbbi szinkronizálási idő előtt írt összes adattartalmat elérhetővé vált a másodlagos kiszolgálón, míg a legutóbbi szinkronizálási idő után írt adatsorok nem lettek a másodlagosba írva, és elveszhetnek. Ezt a tulajdonságot akkor használja, ha leállás esetén a fiók feladatátvételének elindításával becsülheti meg a felmerülő adatvesztés mértékét.

Ajánlott eljárásként tervezze meg az alkalmazást úgy, hogy az utolsó szinkronizálási időt használja a várt adatvesztés kiértékeléséhez. Ha például az összes írási műveletet naplózza, akkor a legutóbbi írási műveletek időpontját összehasonlíthatja a legutóbbi szinkronizálás időpontjával, és meghatározhatja, hogy mely írásokat nem szinkronizálta a rendszer a másodlagosra.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Körültekintően járjon el, ha az eredeti elsődlegesnek nem sikerül visszatérnie

Az elsődlegesről a másodlagos régióba való feladatátvételt követően a Storage-fiók úgy van konfigurálva, hogy helyileg redundáns legyen az új elsődleges régióban. Az GRS vagy RA-GRS használatára való frissítésével konfigurálhatja a fiókot a Geo-redundancia érdekében. Ha a fiók a feladatátvételt követően ismét a Geo-redundancia beállításra van konfigurálva, az új elsődleges régió azonnal megkezdi az adatok másolását az új másodlagos régióba, amely az eredeti feladatátvétel előtt volt az elsődleges. Azonban eltarthat egy ideig, amíg az elsődlegesben lévő meglévő adatértékek teljes egészében át nem másolódnak az új másodlagosra.

Miután a Storage-fiókot újrakonfigurálta a Geo-redundancia érdekében, lehetséges, hogy egy másik feladatátvételt kezdeményez az új elsődleges vissza az új másodlagosra. Ebben az esetben a feladatátvételt megelőzően az elsődleges régió lesz újra az elsődleges régió, és úgy van beállítva, hogy helyileg redundáns legyen. A feladatátvétel utáni elsődleges régióban (az eredeti másodlagos) lévő összes érték elvész. Ha a Storage-fiókban lévő legtöbb adat nem lett átmásolva az új másodlagosra a feladat-visszavétel előtt, akkor jelentős adatvesztést okozhat.

A jelentős adatvesztés elkerülése érdekében a feladat-visszavétel előtt tekintse meg a **Legutóbbi szinkronizálási idő** tulajdonság értékét. A várt adatvesztés kiértékeléséhez hasonlítsa össze a legutóbbi szinkronizálási időt, hogy a legutóbbi alkalommal megtörténjen az adatgyűjtés az új elsődlegesre. 

## <a name="initiate-an-account-failover"></a>Fiók feladatátvételének indítása

A Azure Portal, a PowerShell, az Azure CLI vagy az Azure Storage erőforrás-szolgáltató API-ból kezdeményezheti a fiók feladatátvételét. A feladatátvétel elindításával kapcsolatos további információkért lásd: a [fiók feladatátvételének kezdeményezése (előzetes verzió)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Az előzetes verzió ismertetése

A fiók feladatátvétele az GRS-t vagy RA-GRS-t használó összes ügyfél számára előzetes verzióban érhető el Azure Resource Manager üzemelő példányokkal. Az általános célú v1, az általános célú v2 és a blob Storage-fiókok típusai támogatottak. A fiók feladatátvétele jelenleg az összes nyilvános régióban elérhető. A fiók feladatátvétele jelenleg nem érhető el szuverén/országos felhőkben.

Az előzetes verzió csak nem éles használatra készült. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

Tekintse át az ebben a szakaszban ismertetett további szempontokat annak megismeréséhez, hogy az alkalmazások és szolgáltatások milyen hatással lehetnek a feladatátvétel kényszerítésére az előzetes verzió ideje alatt.

#### <a name="storage-account-containing-archived-blobs"></a>Archivált blobokat tartalmazó Storage-fiók

Az archivált blobokat tartalmazó Storage-fiókok támogatják a fiók feladatátvételét. A feladatátvétel befejeződése után vissza kell alakítani a fiókot a GRS vagy RA-GRS az összes archivált blobot először egy online rétegbe kell rehidratálni.

#### <a name="storage-resource-provider"></a>Tárolásierőforrás-szolgáltató

A feladatátvétel befejezése után az ügyfelek újra elolvashatják és írhatják az Azure Storage-adataikat az új elsődleges régióban. Az Azure Storage erőforrás-szolgáltatója azonban nem hajtja végre a feladatátvételt, ezért az erőforrás-kezelési műveleteket továbbra is az elsődleges régióban kell végrehajtani. Ha az elsődleges régió nem érhető el, nem fogja tudni végrehajtani a kezelési műveleteket a Storage-fiókon.

Mivel az Azure Storage erőforrás-szolgáltató nem hajtja végre a feladatátvételt, a [Location (hely](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) ) tulajdonság az eredeti elsődleges helyet adja vissza a feladatátvétel befejeződése után.

#### <a name="azure-virtual-machines"></a>Azure virtuális gépek

Az Azure Virtual Machines (VM) nem végez feladatátvételt a fiók feladatátvételének részeként. Ha az elsődleges régió elérhetetlenné válik, és feladatátvételt hajt végre a másodlagos régióban, akkor a feladatátvételt követően újra létre kell hoznia a virtuális gépeket. Emellett lehetséges, hogy a fiók feladatátvételével kapcsolatos adatvesztés történik. A Microsoft az Azure-beli virtuális gépekre jellemző, [magas rendelkezésre állású](../../virtual-machines/windows/manage-availability.md) és vész- [helyreállítási](../../virtual-machines/virtual-machines-disaster-recovery-guidance.md) útmutatót javasolja.

#### <a name="azure-unmanaged-disks"></a>Nem felügyelt Azure-lemezek

Ajánlott eljárásként a Microsoft a nem felügyelt lemezek felügyelt lemezekre való átalakítását javasolja. Ha azonban olyan fiókot kell átadnia, amely nem felügyelt lemezeket tartalmaz az Azure-beli virtuális gépekhez, a feladatátvétel kezdeményezése előtt le kell állítania a virtuális gépet.

A nem felügyelt lemezek blobként tárolódnak az Azure Storage-ban. Ha egy virtuális gép az Azure-ban fut, a virtuális géphez csatolt nem felügyelt lemezek bérlete megtörténik. A fiók feladatátvétele nem folytatható, ha a blobon bérlet van. A feladatátvétel végrehajtásához kövesse az alábbi lépéseket:

1. Mielőtt elkezdené, jegyezze fel a nem felügyelt lemezek nevét, a logikai egységek számát (LUN), valamint azt a virtuális gépet, amelyhez csatolva van. Ezzel megkönnyíti a lemezek újracsatlakoztatását a feladatátvétel után.
2. Állítsa le a virtuális gépet.
3. Törölje a virtuális gépet, de őrizze meg a nem felügyelt lemezek VHD-fájljait. Figyelje meg, hogy mikor törölte a virtuális gépet.
4. Várjon, amíg a **Legutóbbi szinkronizálási idő** frissült, és a virtuális gép törlésének időpontjánál későbbi. Ez a lépés azért fontos, mert ha a másodlagos végpontot nem frissítették teljes mértékben a VHD-fájlokkal a feladatátvétel bekövetkeztekor, előfordulhat, hogy a virtuális gép nem működik megfelelően az új elsődleges régióban.
5. A fiók feladatátvételének kezdeményezése.
6. Várjon, amíg a fiók feladatátvétele befejeződik, és a másodlagos régió az új elsődleges régió lesz.
7. Hozzon létre egy virtuális gépet az új elsődleges régióban, és csatlakoztassa újra a virtuális merevlemezeket.
8. Indítsa el az új virtuális gépet.

Ne feledje, hogy az ideiglenes lemezen tárolt összes adatmennyiség elvész a virtuális gép leállításakor.

### <a name="unsupported-features-and-services"></a>Nem támogatott funkciók és szolgáltatások

Az előzetes verzióhoz tartozó fiók feladatátvétele nem támogatja a következő szolgáltatásokat és szolgáltatásokat:

- A Azure File Sync nem támogatja a Storage-fiók feladatátvételét. A Azure File Syncben felhőbeli végpontként használt Azure-fájlmegosztásokat tartalmazó tárfiókokon nem lehet feladatátvételt végezni. Feladatátvétel esetén a szinkronizálás leáll, és az újonnan rétegzett fájlok esetében váratlan adatvesztést is okozhat.
- ADLS Gen2 Storage-fiókok (a hierarchikus névteret engedélyező fiókok) jelenleg nem támogatottak.
- A prémium szintű blokk blobokat tartalmazó Storage-fiókok feladatátvétele nem végezhető el. A prémium szintű blokk blobokat támogató Storage-fiókok jelenleg nem támogatják a Geo-redundanciát.
- Nem lehet felvenni egy olyan Storage-fiókot, amely bármely, a [módosíthatatlansági házirendet](../blobs/storage-blob-immutable-storage.md) engedélyező tárolót tartalmaz. Zárolt/zárolt időalapú adatmegőrzési vagy jogszabályi szabályzatok megakadályozzák a feladatátvételt a megfelelőség fenntartása érdekében.
- A feladatátvétel befejeződése után a következő funkciók nem működnek, ha eredetileg engedélyezve vannak: [esemény-előfizetések](../blobs/storage-blob-event-overview.md), a [hírcsatorna módosítása](../blobs/storage-blob-change-feed.md), [életciklus-szabályzatok](../blobs/storage-lifecycle-management-concepts.md)és [Storage Analytics naplózás](storage-analytics-logging.md).

## <a name="copying-data-as-an-alternative-to-failover"></a>Adatok másolása a feladatátvétel alternatívájaként

Ha a Storage-fiókja RA-GRS van konfigurálva, akkor a másodlagos végpont használatával olvasási hozzáféréssel rendelkezik az adataihoz. Ha nem szeretné átvenni a feladatátvételt az elsődleges régió meghibásodása esetén, olyan eszközöket használhat, mint például a [AzCopy](storage-use-azcopy.md), a [Azure PowerShell](storage-powershell-guide-full.md)vagy az [Azure adatátviteli függvénytár](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) , amellyel a másodlagos régióban lévő Storage-fiókjából egy másik Storage-fiókba másolhatja az adatait egy nem érintett régióban. Ezután a Storage-fiókra irányíthatja az alkalmazásokat az olvasási és az írási rendelkezésre álláshoz is.

> [!CAUTION]
> A fiók feladatátvételét nem szabad az adatáttelepítési stratégia részeként használni.


## <a name="microsoft-managed-failover"></a>Microsoft által felügyelt feladatátvétel

Szélsőséges körülmények között, amikor egy régiót súlyos katasztrófa okoz, a Microsoft regionális feladatátvételt kezdeményezhet. Ebben az esetben nincs szükség beavatkozásra a részen. Amíg a Microsoft által felügyelt feladatátvétel nem fejeződött be, nem rendelkezik írási hozzáféréssel a Storage-fiókhoz. Az alkalmazások a másodlagos régióból is beolvashatók, ha a Storage-fiókja RA-GRS van konfigurálva. 

## <a name="see-also"></a>Lásd még

- [Fiók feladatátvételének kezdeményezése (előzetes verzió)](storage-initiate-account-failover.md)
- [Magas rendelkezésre állású alkalmazások tervezése az RA-GRS használatával](storage-designing-ha-apps-with-ragrs.md)
- [Oktatóanyag: kiválóan elérhető alkalmazás létrehozása blob Storage-val](../blobs/storage-create-geo-redundant-storage.md) 
