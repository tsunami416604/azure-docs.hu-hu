---
title: Nem módosítható blobtárhely – Azure Storage
description: Az Azure Storage worm (írás i. egyszer olvasása, sok olvasása) támogatást kínál a Blob (object) storage számára, amely lehetővé teszi a felhasználók számára, hogy az adatokat nem átható, nem módosítható állapotban tárolják egy adott időtartamon belül.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367618"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Üzleti legkritikusabb blobadatok tárolása állandó tárolóval

Az Azure Blob storage nem módosítható tárhelye lehetővé teszi a felhasználók számára, hogy az üzleti szempontból kritikus fontosságú adatobjektumokat WORM (Egyszer írás, Sok olvasás) állapotban tárolják. Ez az állapot az adatokat nem lehet eltéve, és nem módosítható a felhasználó által megadott időközönként. A megőrzési időköz időtartama alatt blobok hozhatók létre és olvashatók, de nem módosíthatók vagy törölhetők. Az általános célú v1- és általános célú v2-es, BlobStorage- és BlockBlobStorage-fiókok hoz rendelkezésre az összes Azure-régióban módosítható.

A jogi visszatartások beállításáról és törléséről, illetve az időalapú adatmegőrzési szabályzat létrehozásáról az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül, a [Blob storage-beli megváltoztathatatlansági szabályzatok beállítása és kezelése](storage-blob-immutability-policies-manage.md)című témakörben talál.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Nem módosítható Blob-tároló – kapcsolat

Az immutable storage segíti az egészségügyi&mdash;szervezetet, a pénzügyi&mdash;intézményeket és a kapcsolódó iparágakat, különösen a bróker-kereskedő szervezeteket az adatok biztonságos tárolásában. A nem módosítható tárolás bármely forgatókönyvben használható a kritikus adatok módosítás vagy törlés elleni védelme érdekében.

Jellemző alkalmazási területek:

- **Szabályozási megfelelőség:** Az Azure Blob storage nem módosítható tárhelye segít a szervezeteknek a SEC 17a-4(f), a CFTC 1.31(d), a FINRA és egyéb szabályozások címezésében. A Cohasset Associates által készített technikai tanulmány részletezi, hogy a nem módosítható tárhely hogyan felel meg ezeknek a szabályozási követelményeknek a [Microsoft Service Trust Portal portálon](https://aka.ms/AzureWormStorage)keresztül. Az [Azure Adatvédelmi központ](https://www.microsoft.com/trustcenter/compliance/compliance-overview) részletes információkat tartalmaz megfelelőségi tanúsítványainkról.

- **Biztonságos dokumentummegőrzés:** Az Azure Blob storage nem módosítható és nem törölhető egyetlen felhasználó sem, beleértve a fiókfelügyeleti jogosultságokkal rendelkező felhasználókat sem.

- **Jogi visszatartás:** Az Azure Blob storage nem módosítható tárhelye lehetővé teszi a felhasználók számára, hogy a peres eljárás vagy üzleti használat szempontjából kritikus bizalmas adatokat a kívánt ideig illetéktelen beavatkozás biztos állapotban tárolják, amíg a visszatartást el nem távolítják. Ez a funkció nem csak a jogi felhasználási esetekre korlátozódik, hanem eseményalapú visszatartásnak vagy vállalati zárolásnak is tekinthető, ahol szükség van az események kiváltó vagy vállalati házirendjein alapuló adatok védelmére.

A nem módosítható tárhely a következő funkciókat támogatja:

- **[Időalapú adatmegőrzési házirend támogatása](#time-based-retention-policies)**: A felhasználók beállíthatják, hogy a házirendek adott időközönként tárolja az adatokat. Ha egy időalapú adatmegőrzési házirend van beállítva, blobok hozhatók létre és olvashatók, de nem módosíthatók vagy törölhetők. A megőrzési időszak lejárta után a blobok törölhetők, de nem írhatók felül.

- **[Jogi visszatartási szabályzat támogatása:](#legal-holds)** Ha a megőrzési időköz nem ismert, a felhasználók beállíthatják a jogi visszatartást, hogy állandó adatokat tároljanak, amíg a jogi visszatartás tnemi.  Ha egy jogi visszatartási szabályzat van beállítva, blobok hozhatók létre és olvashatók, de nem módosíthatók vagy törölhetők. Minden jogi visszatartás egy felhasználó által definiált alfanumerikus címkéhez (például esetazonosítóhoz, eseménynévhez stb.) van társítva, amely azonosító karakterláncként használatos. 

- **Az összes blobszint támogatása:** A WORM-szabályzatok függetlenek az Azure Blob tárolási szintjétől, és az összes szintre vonatkoznak: gyakori, ritka elérésű és archiválási. Így a felhasználók a számítási feladatok szempontjából leginkább költségoptimalizált szinten tárolhatják adataikat, miközben továbbra sem tudják őket módosítani.

- **Tárolószintű konfiguráció:** A felhasználók konfigurálhatják az időalapú adatmegőrzési házirendeket és a legális visszatartási címkéket a tároló szintjén. A felhasználók egyszerű tárolószintű beállítások segítségével többek között létrehozhatnak és zárolhatnak időalapú adatmegőrzési szabályzatokat, meghosszabbíthatják a megőrzési időtartamot, és jogi célú visszatartást állíthatnak be vagy oldhatnak fel. Ezek a szabályzatok a tárolóban található összes blobra vonatkoznak, a meglévőkre és az újakra is.

- **Naplózás támogatása:** Minden tároló tartalmaz egy házirend-naplózási naplót. Legfeljebb hét időalapú adatmegőrzési parancsot jelenít meg a zárolt időalapú adatmegőrzési házirendekhez, és tartalmazza a felhasználói azonosítót, a parancstípusát, az időbélyegzőket és az adatmegőrzési időközt. A jogi célú visszatartások esetében a naplóbejegyzés tartalmazza a felhasználói azonosítót, a parancs típusát, az időbélyegeket és a jogi céllal történő zárolás címkéit. Ez a napló a szabályzat teljes élettartama alatt megmarad, a SEC 17a-4(f) szabályozási irányelveinek megfelelően. Az [Azure-tevékenységnapló](../../azure-monitor/platform/platform-logs-overview.md) átfogóbb naplót jelenít meg az összes vezérlősík-tevékenységről; az [Azure diagnosztikai naplók](../../azure-monitor/platform/platform-logs-overview.md) engedélyezése közben megőrzi és megjeleníti az adatsík-műveleteket. A felhasználó felelőssége, hogy gondoskodjon a naplók állandó tárolásáról, mivel szabályozási és egyéb célból is szükség lehet rájuk.

## <a name="how-it-works"></a>Működés

Az Azure Blob Storage nem módosítható tárolási funkciója két típusú WORM vagy nem módosítható szabályzatot támogat: az időalapú adatmegőrzést és a jogi célú visszatartást. Ha egy időalapú adatmegőrzési szabályzat vagy jogi visszatartás egy tárolón van alkalmazva, az összes meglévő blobok áthelyezése egy nem módosítható WORM állapotba kevesebb, mint 30 másodperc alatt. A házirend által védett tárolóba feltöltött összes új blob is átkerül egy nem módosítható állapotba. Ha az összes blob oka nem módosítható állapotban van, a nem módosítható házirend megerősítést nyer, és a nem módosítható tárolóban lévő felülírási vagy törlési műveletek nem engedélyezettek.

A tároló- és tárfiók törlése akkor sem engedélyezett, ha olyan blobok vannak egy tárolóban, amelyeket jogi visszatartás vagy zárolt időalapú házirend véd. A jogi visszatartási szabályzat védelmet nyújt a blob, a tároló és a tárfiók törlése ellen. A feloldott és a zárolt időalapú házirendek is védelmet nyújtanak a blobok törlésével szemben a megadott ideig. A feloldott és a zárolt időalapú házirendek csak akkor nyújtanak védelmet a tárolók törlése ellen, ha a tárolóban legalább egy blob létezik. Csak egy *lezárt* időalapú házirenddel rendelkező tároló nyújt védelmet a tárfiók törléseellen; a feloldott időalapú szabályzatokkal rendelkező tárolók nem nyújtanak tárfiók-törléselleni védelmet és megfelelőséget.

Az időalapú adatmegőrzési házirendek beállításáról és zárolásáról a [Blob storage-beli ségi házirendek beállítása és kezelése](storage-blob-immutability-policies-manage.md)című témakörben talál további információt.

## <a name="time-based-retention-policies"></a>Időalapú adatmegőrzési házirendek

> [!IMPORTANT]
> Egy időalapú adatmegőrzési *szabályzatot zárolni* kell ahhoz, hogy a blob a SEC 17a-4(f) és más szabályozási megfelelőség immutálhatatlan (írási és törlésvédett) állapotban legyen. Azt javasoljuk, hogy zárolja a szabályzatot ésszerű időn belül, általában kevesebb, mint 24 óra alatt. Az alkalmazott időalapú adatmegőrzési házirend kezdeti állapota fel van *oldva,* így a zárolás előtt tesztelheti a szolgáltatást, és módosíthatja a házirendet. Bár a *feloldott* állapot immutability védelmet biztosít, nem javasoljuk a *feloldott* állapot használatát a rövid távú szolgáltatáskísérleteken kívül más célra. 

Ha egy időalapú adatmegőrzési szabályzat egy tárolón van alkalmazva, a tárolóban lévő összes blob oka a *tényleges* megőrzési időszak alatt nem módosítható állapotban marad. A blobok tényleges megőrzési időszaka megegyezik a blob **létrehozási ideje** és a felhasználó által megadott megőrzési időköz közötti különbséggel. Mivel a felhasználóknak lehetőségük van meghosszabbítani az adatmegőrzési időtartamot, a nem módosítható tárolás a felhasználó által legutóbb megadott adatmegőrzési időtartamot fogja használni a tényleges megőrzési időtartam kiszámolásakor.

Tegyük fel például, hogy a felhasználó létrehoz egy időalapú adatmegőrzési szabályzatot öt éves megőrzési időközzel. Egy meglévő blob a tárolóban, _testblob1_, egy évvel ezelőtt jött létre; így a _testblob1_ tényleges megőrzési ideje négy év. Amikor egy új blob, _testblob2_, feltölti a tárolóba, a tényleges megőrzési időszak a _testblob2_ öt évvel a létrehozása óta.

A feloldott időalapú adatmegőrzési szabályzat csak a szolgáltatás teszteléséhez ajánlott, és a házirendet le kell zárni ahhoz, hogy megfeleljen a SEC 17a-4(f) és más szabályozási megfelelőségnek. Az időalapú adatmegőrzési szabályzat zárolása után a házirend nem távolítható el, és legfeljebb öt nő a tényleges megőrzési időszak megengedett.

Az adatmegőrzési házirendekre a következő korlátozások vonatkoznak:

- Tárfiók esetén a zárolt időalapú nem módosítható házirendekkel rendelkező tárolók maximális száma 10 000.
- A minimális megőrzési időköz 1 nap. A maximális érték 146 000 nap (400 év).
- Egy tároló esetén a zárolt időalapú nem módosítható házirendek megőrzési időközének meghosszabbításához szükséges szerkesztések maximális száma 5.
- Egy tároló esetén legfeljebb hét időalapú adatmegőrzési házirend naplózási naplók megmaradnak egy zárolt házirend.

### <a name="allow-protected-append-blobs-writes"></a>Védett hozzáfűző blobok írási műveletek engedélyezése

A hozzáfűző blobok adatblokkokból állnak, és a naplózási és naplózási forgatókönyvek által igényelt adathozzáfűző műveletekre vannak optimalizálva. A tervezés szerint a hozzáfűző blobok csak új blokkok hozzáadását engedélyezik a blob végéig. Függetlenül attól, hogy a mutabilitás, módosítása vagy törlése a meglévő blokkok egy hozzáfűző blob alapvetően nem megengedett. A hozzáfűző blobokról a [Blobok hozzáfűzése ( Hozzáfűzés – blobok – ismertet) témakörben olvashat bővebben.](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)

Csak az időalapú adatmegőrzési szabályzatok rendelkeznek egy `allowProtectedAppendWrites` beállítással, amely lehetővé teszi az új blokkok írását egy hozzáfűző blobba, miközben fenntartja a megváltoztathatatlanság elleni védelmet és megfelelőséget. Ha engedélyezve van, akkor közvetlenül a szabályzat védett tárolójában hozhat létre hozzáfűző blobot, és továbbra is hozzáadhat új adatblokkokat a meglévő hozzáfűző blobok végéhez az *AppendBlock* API használatával. Csak új blokkok adhatók hozzá, és a meglévő blokkok nem módosíthatók vagy törölhetők. Az időmegtartási megváltoztatóképesség-védelem továbbra is érvényes, megakadályozva a hozzáfűző blob törlését, amíg a tényleges megőrzési időszak le nem telik. A beállítás engedélyezése nincs hatással a blokkblobok vagy lapblobok megváltoztathatatlansági viselkedésére.

Mivel ez a beállítás egy időalapú adatmegőrzési szabályzat része, a hozzáfűző blobok továbbra is a *tényleges* megőrzési időszak időtartama alatt nem módosítható állapotban maradnak. Mivel az új adatok a hozzáfűző blob kezdeti létrehozása után is hozzáfűzhetők, van egy kis különbség a megőrzési időszak meghatározásában. A hatékony megőrzési a függelék blob **utolsó módosítási ideje** és a felhasználó által megadott megőrzési időköz közötti különbség. Hasonlóképpen, ha a megőrzési időköz meghosszabbodik, a nem módosítható tároló a felhasználó által megadott megőrzési időköz legutóbbi értékét használja a tényleges megőrzési időszak kiszámításához.

Tegyük fel például, hogy a felhasználó `allowProtectedAppendWrites` létrehoz egy időalapú adatmegőrzési házirendet engedélyezett és 90 napos megőrzési időközzel. Egy hozzáfűző blob, _logblob1,_ ma jön létre a tárolóban, az új naplók továbbra is hozzá kell adni a hozzáfűző blob a következő 10 nap; így a _logblob1_ tényleges megőrzési ideje 100 nap a mai naptól (az utolsó hozzáfűzés időpontja + 90 nap).

A feloldott időalapú adatmegőrzési házirendek lehetővé teszik a `allowProtectedAppendWrites` beállítás bármikor engedélyezését és letiltását. Az időalapú adatmegőrzési szabály `allowProtectedAppendWrites` zárolása után a beállítás nem módosítható.

A jogi visszatartási szabályzatok nem engedélyezhetők, `allowProtectedAppendWrites` és a jogi visszatartások semmissé teszik az "allowProtectedAppendWrites" tulajdonságot. Ha egy engedélyezett időalapú adatmegőrzési házirendre `allowProtectedAppendWrites` alkalmaz egy jogi visszatartást, az *AppendBlock* API sikertelen lesz, amíg a jogi visszatartást fel nem oldják.

## <a name="legal-holds"></a>Jogi célú visszatartások

A jogi visszatartatások olyan ideiglenes visszatartatások, amelyek jogi vizsgálati célokra vagy általános védelmi politikákra használhatók. Minden jogi visszatartási szabályzatot egy vagy több címkhez kell társozni. A címkék névvel ellátott azonosítóként, például esetazonosítóként vagy eseményként használatosak a raktér céljának kategorizálására és leírására.

Egy tároló rendelkezhet egy jogi visszatartással és egy időalapú adatmegőrzési házirenddel is. Az adott tárolóban található összes blob mindaddig nem módosítható állapotban marad, amíg az összes jogi célú visszatartást fel nem oldják, még akkor is, ha azok tényleges adatmegőrzési időtartama lejár. A blobok továbbá mindaddig nem módosítható állapotban maradnak, amíg a tényleges megőrzési időtartamuk le nem jár, még a jogi célú visszatartások feloldását követően is.

A jogi ravonatkozó jogszabályokra a következő korlátozások vonatkoznak:

- Tárfiók esetén a legális visszatartás-beállítással rendelkező tárolók maximális száma 10 000.
- Egy tároló esetében a legális visszatartáscímkék maximális száma 10.
- A jogi visszatartáscímkének minimális hossza három alfanumerikus karakter. A maximális hossz 23 alfanumerikus karakter.
- Egy tároló esetében legfeljebb 10 jogi visszatartás házirend-naplózási naplók megmaradnak a szabályzat időtartama alatt.

## <a name="scenarios"></a>Forgatókönyvek
Az alábbi táblázat a blob-tárolási műveletek típusait mutatja be, amelyek le vannak tiltva a különböző nem módosítható forgatókönyvekhez. További információt az [Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentációjában talál.

|Forgatókönyv  |Blob állapota  |Blob-műveletek megtagadva  |Tároló- és fiókvédelem
|---------|---------|---------|---------|
|A blob tényleges adatmegőrzési időtartama még nem járt le és/vagy jogi célú visszatartás van érvényben     |Nem módosítható: törlés- és írásvédett         | Tegye blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Törlése Konténer, Blob törlése, Blob metaadatok beállítása, Put Page, Blob tulajdonságainak beállítása, Pillanatkép Blob, Növekményes Copy Blob, Hozzáfűzés blokk<sup>2</sup>         |A tároló törlése megtagadva; A tárfiók törlése megtagadva         |
|A blob tényleges megőrzési időköze lejárt, és nincs beállítva jogi visszatartás    |Csak írásvédett (a törlési műveletek engedélyezettek)         |Tegye blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Set Blob Metadata, Put Page, Blob tulajdonságainak beállítása, Snapshot Blob, Növekményes copy blob, hozzáfűzésblokk<sup>2</sup>         |A tároló törlése megtagadva, ha legalább 1 blob létezik a védett tárolóban; A tárfiók törlése csak *zárolt* időalapú házirendek esetén megtagadva         |
|Nincs WORM házirend alkalmazva (nincs időalapú megőrzés és nincs jogi visszatartási címke)     |Változtatható         |None         |None         |

<sup>1</sup> A blob szolgáltatás lehetővé teszi, hogy ezek a műveletek egy új blob egyszer hozzon létre. Minden ezt követő felülírási műveletek egy meglévő blob elérési út egy nem módosítható tárolóban nem engedélyezettek.

<sup>2</sup> A hozzáfűző blokk csak olyan időalapú `allowProtectedAppendWrites` adatmegőrzési házirendekhez engedélyezett, amelyeken a tulajdonság engedélyezve van. További információt a [Védett hozzáfűző blobok írásai](#allow-protected-append-blobs-writes) engedélyezése című szakaszban talál.

## <a name="pricing"></a>Díjszabás

A funkció használatáért nem számítunk fel további díjat. A nem módosítható adatok ára ugyanúgy történik, mint a módosítható adatok. Az Azure Blob storage díjszabási részleteiről az [Azure Storage díjszabási lapján](https://azure.microsoft.com/pricing/details/storage/blobs/)talál.

## <a name="faq"></a>GYIK

**Tud dokumentációt nyújtani a WORM megfelelőségéről?**

Igen. A dokumentumok megfelelőségének dokumentálása érdekében a Microsoft megtartott egy vezető független értékelő céget, amely a rekordok kezelésére és az információirányításra specializálódott, a Cohasset Associates, hogy kiértékelje a nem módosítható Blob-tárolót és annak a reprezentatióra vonatkozó követelményeknek való megfelelését. pénzügyi szolgáltatási ágazat. A Cohasset megerősítette, hogy a nem módosítható Blob-tároló, ha az időalapú blobok WORM állapotban tartására szolgál, megfelel az 1.31(c)-(d), a FINRA 4511- és a SEC 17a-4 szabály vonatkozó tárolási követelményeinek. A Microsoft ezt a szabálykészletet célozta meg, mivel ezek jelentik a pénzügyi intézmények rekordmegőrzésének legelőbb útmutatóját világszerte. A Cohasset-jelentés a [Microsoft Service Trust Center ben](https://aka.ms/AzureWormStorage)érhető el. Ha igazolást szeretne kérni a Microsofttól a WORM megváltoztathatatlansági megfelelőségről, forduljon az Azure ügyfélszolgálatához.

**A szolgáltatás csak a blokkblobokra és a hozzáfűző blobokra, illetve a lapblobokra vonatkozik?**

A nem módosítható tároló bármilyen blobtípussal használható, mivel az a tároló szintjén van beállítva, de azt javasoljuk, hogy worm-ot használjon olyan tárolókhoz, amelyek főként blokkblobokat és hozzáfűző blobokat tárolnak. A tárolóban lévő meglévő blobokat egy újonnan beállított WORM-házirend fogja védeni. De minden új lapblobokat létre kell hozni a WORM tárolón kívül, majd másolni kell. Miután egy WORM tárolóba másolt, a lapblob további módosítása nem engedélyezett. Worm-házirend beállítása egy olyan tárolón, amely virtuális merevlemezeket (lapblobokat) tárol bármely aktív virtuális gép számára, nem ajánlott, mivel zárolja a virtuális gép lemezét. Azt javasoljuk, hogy alaposan tekintse át a dokumentációt, és tesztelje a forgatókönyveket, mielőtt bármilyen időalapú szabályzatot zárolna.

**Létre kell hoznom egy új tárfiókot a funkció használatához?**

Nem, nem, nem módosítható tároló bármely meglévő vagy újonnan létrehozott általános célú v1, általános célú v2, BlobStorage vagy BlockBlobStorage-fiókokkal használható. Általános célú v1 tárfiókok támogatottak, de azt javasoljuk, hogy az általános célú v2 frissítése, hogy kihasználhassa a további funkciók. A meglévő általános célú v1-es tárfiók frissítéséről a [Tárfiók frissítése](../common/storage-account-upgrade.md)című témakörben talál további információt.

**Alkalmazhatok jogi visszatartást és időalapú adatmegőrzési szabályt is?**

Igen, egy tároló egyszerre rendelkezhet jogi visszatartással és időalapú adatmegőrzési házirenddel is; az "allowProtectedAppendWrites" beállítás azonban addig nem lesz érvényes, amíg a jogi visszatartás nincs törölve. Az adott tárolóban található összes blob mindaddig nem módosítható állapotban marad, amíg az összes jogi célú visszatartást fel nem oldják, még akkor is, ha azok tényleges adatmegőrzési időtartama lejár. A blobok továbbá mindaddig nem módosítható állapotban maradnak, amíg a tényleges megőrzési időtartamuk le nem jár, még a jogi célú visszatartások feloldását követően is. 

**A jogi visszatartásra vonatkozó irányelvek csak a bírósági eljárásokra vonatkozóak, vagy vannak-e más használati forgatókönyvek?**

Nem, a Jogi tartás csak egy nem időalapú adatmegőrzési szabály általános kifejezése. Nem csak peres eljárásokban használható fel. A jogi visszatartási házirendek a fontos vállalati WORM-adatok védelme érdekében hasznosak a felülírás és a törlés letiltásához, ahol a megőrzési időszak ismeretlen. Használhatja vállalati szabályzatként a kritikus fontosságú WORM-számítási feladatok védelmére, vagy átmeneti szabályzatként használhatja, mielőtt egy egyéni eseményindító időalapú adatmegőrzési házirend használatát igényelne. 

**Eltávolíthatok egy _zárolt_ időalapú adatmegőrzési szabályt vagy jogi visszatartást?**

Csak a feloldott időalapú adatmegőrzési házirendek távolíthatók el a tárolóból. Ha egy időalapú adatmegőrzési szabály zárolva van, nem távolítható el; csak hatékony megőrzési időszak meghosszabbítása megengedett. A jogi visszatartási címkék törölhetők. Ha az összes jogi címkét törli, a jogi mentesség törlődik.

**Mi történik, ha időalapú adatmegőrzési házirenddel vagy jogi visszatartással rendelkező tárolót próbálok törölni?**

A Tároló törlése művelet sikertelen lesz, ha legalább egy blob létezik a tárolóban egy zárolt vagy feloldott időalapú adatmegőrzési házirendet, vagy ha a tároló rendelkezik egy jogi visszatartás. A Tároló törlése művelet csak akkor lesz sikeres, ha nincsenek blobok a tárolón belül, és nincsenek jogi visszatartat. 

**Mi történik, ha olyan tárolóval kísérelek meg törölni egy tárolót, amely időalapú adatmegőrzési szabályzattal vagy jogi visszatartással rendelkezik?**

A tárfiók törlése sikertelen lesz, ha legalább egy tároló egy jogi visszatartás-készlet vagy egy **zárolt** időalapú házirend. A feloldott időalapú házirenddel rendelkező tároló nem nyújt védelmet a tárfiók törlése ellen. A tárfiók törlése előtt el kell távolítania az összes jogi visszatartamot, és törölnie kell az összes **zárolt** tárolót. A tárolók törlésével kapcsolatos információkért lásd az előző kérdést. Az [Azure Resource Manager zárolásaival](../../azure-resource-manager/management/lock-resources.md)további törlési védelmet is alkalmazhat a tárfiókhoz.

**Áthelyezhetem az adatokat a különböző blobszintek között (gyakori, ritka elérésű, archiválási) ha a blob nem módosítható állapotban van?**

Igen, a Blobszint beállítása paranccsal áthelyezheti az adatokat a blobszintek között, miközben az adatokat a megfelelő, nem módosítható állapotban tartja. A nem módosítható tárhely et támogatja a gyakori elérésű, ritka elérésű és archív blobszintek.

**Mi történik, ha elmulasztok fizetni, és a megőrzési időtartam még nem járt le?**

Fizetés elmaradása esetén a microsoftos szerződés feltételeiben meghatározottak szerint a normál adatmegőrzési szabályok érvényesek. Általános információt a [Microsoft adatkezelése](https://www.microsoft.com/en-us/trust-center/privacy/data-management)című témakörben talál. 

**Létezik a funkció kipróbálására lehetőséget biztosító ingyenes próba- vagy türelmi időszak?**

Igen. Időalapú adatmegőrzési szabály létrehozásakor, akkor egy *feloldott* állapotban van. Ebben az állapotban a megőrzési időköz kívánt módosítását, például a házirend növelését vagy csökkentését, sőt akár törölheti is. A házirend zárolása után zárolva marad, amíg a megőrzési időköz le nem jár. Ez a zárolt házirend megakadályozza a megőrzési időköz törlését és módosítását. Javasoljuk, hogy a *feloldott* állapotot csak próbacélokra használja, és 24 órán belül zárolja a szabályzatot. Ezek a gyakorlatok segítenek a SEC 17a-4(f) és más előírások betartásában.

**Használhatom a helyreállítható törlést az elnemmódosítható blobszabályzatok mellett?**

Igen, ha a megfelelőségi követelmények lehetővé teszik a helyreállítható törlés engedélyezését. [Az Azure Blob storage ideiglenes törlése](storage-blob-soft-delete.md) a tárfiókon belüli összes tárolóra vonatkozik, függetlenül a jogi visszatartástól vagy az időalapú adatmegőrzési szabályzattól. Javasoljuk, hogy engedélyezzen a helyreállítható törlést a további védelem érdekében, mielőtt bármilyen nem módosítható WORM-házirendet alkalmazna és megerősítene.

## <a name="next-steps"></a>További lépések

- [A Blob storage-hoz való megváltoztathatatlansági házirendek beállítása és kezelése](storage-blob-immutability-policies-manage.md)
- [Szabályok beállítása a blobadatok automatikus rétegezésére és törlésére az életciklus-kezeléssel](storage-lifecycle-management-concepts.md)
- [Az Azure Storage-blobok helyreállítható törlése](../blobs/storage-blob-soft-delete.md)
- [Az Azure Resource Manager zárolásaival megvédheti az előfizetéseket, az erőforráscsoportokat és az erőforrásokat.](../../azure-resource-manager/management/lock-resources.md)
