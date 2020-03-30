---
title: Az egyidejűség kezelése
titleSuffix: Azure Storage
description: Ismerje meg, hogyan kezelheti a Blob, a Várólista, a Tábla és a Fájl szolgáltatások egyidejűségét.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9879f98e72e22fc0745a9e91f29216cbe74ab8fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255300"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Egyidejűség kezelése a Microsoft Azure Storage szolgáltatásban

A modern internetalapú alkalmazások általában egyszerre több felhasználóval tekintik meg és frissítik az adatokat. Ehhez az alkalmazásfejlesztőknek alaposan át kell gondolniuk, hogyan biztosíthatnak kiszámítható felhasználói élményt, különösen olyan esetekben, amikor több felhasználó frissítheti ugyanazokat az adatokat. A fejlesztők általában három fő adat-egyidejűségi stratégiát vesznek figyelembe:  

1. Optimista egyidejűség – A frissítést végző alkalmazások a frissítés részeként ellenőrzik, hogy az adatok megváltoztak-e az alkalmazás utolsó olvasása óta. Ha például két, egy wikioldalt megtekintő felhasználó frissít ugyanarra az oldalra, akkor a wikiplatformnak biztosítania kell, hogy a második frissítés ne írja felül az első frissítést – és hogy mindkét felhasználó megértse, hogy a frissítés sikeres volt-e vagy sem. Ezt a stratégiát leggyakrabban webes alkalmazásokban használják.
2. Pesszimista egyidejűség – A frissítést végrehajtani kereső alkalmazások zárolják az objektumot, amely megakadályozza, hogy más felhasználók a zárolás felengedéséig frissítsék az adatokat. Például egy fő/alárendelt adatreplikációs forgatókönyvben, ahol csak a főkiszolgáló hajtja végre a frissítéseket, a főkiszolgáló általában hosszabb ideig kizárólagos zárolást tart az adatokon, hogy senki más ne tudja frissíteni.
3. Utolsó író nyer – Olyan megközelítés, amely lehetővé teszi, hogy a frissítési műveletek folytatása ellenőrzése nélkül, ha bármely más alkalmazás frissítette az adatokat, mivel az alkalmazás először olvassa be az adatokat. Ezt a stratégiát (vagy a formális stratégia hiányát) általában akkor használják, ha az adatokat úgy particionálják, hogy nem valószínű, hogy több felhasználó is hozzáfér ugyanazokhoz az adatokhoz. Hasznos lehet akkor is, ha rövid életű adatfolyamok feldolgozása folyamatban van.  

Ez a cikk áttekintést nyújt arról, hogy az Azure Storage platform leegyszerűsíti a fejlesztést azáltal, hogy első osztályú támogatást nyújt mindhárom egyidejűségi stratégiához.  

## <a name="azure-storage-simplifies-cloud-development"></a>Az Azure Storage leegyszerűsíti a felhőfejlesztést

Az Azure storage szolgáltatás mindhárom stratégiát támogatja, bár az optimista és pesszimista egyidejűség teljes körű támogatását élvezi, mivel olyan erős konzisztenciamodellt A storage service véglegesíti az adatbeszúrási vagy -frissítési műveletet, az adatokhoz való további hozzáférések a legújabb frissítést fogják látni. A végleges konzisztenciamodellt használó tárolóplatformok nak van egy késése aközött, amikor egy felhasználó írást hajt végre, és amikor a frissített adatokat más felhasználók láthatják, így megnehezíti konzisztenciáit annak érdekében, hogy megelőzzék az inkonzisztenciák a végfelhasználókat érintő.  

A megfelelő egyidejűségi stratégia kiválasztása mellett a fejlesztőknek tisztában kell lenniük azzal is, hogy a tárolási platform hogyan különíti el a változásokat – különösen ugyanazon objektum módosításait a tranzakciók között. Az Azure storage szolgáltatás pillanatkép-elkülönítést használ, hogy lehetővé tegye az olvasási műveletek egyetlen partíción belüli írási műveletekkel egyidejűegyidejűleg történő történni. Más elkülönítési szintekkel ellentétben a pillanatkép elkülönítése garantálja, hogy minden olvasás az adatok konzisztens pillanatképét látja, még akkor is, ha frissítések történnek – lényegében az utolsó véglegesített értékek visszaadásával a frissítési tranzakció feldolgozása közben.  

## <a name="managing-concurrency-in-blob-storage"></a>Egyidejűség kezelése a Blob storage-ban

Választhat, hogy optimista vagy pesszimista egyidejűségi modellek használatával kezelheti a blobokhoz és tárolókhoz való hozzáférést a Blob szolgáltatásban. Ha nem adja meg kifejezetten a stratégia utolsó írási wins az alapértelmezett.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimista egyidejűség blobok és tárolók esetén

A Storage szolgáltatás azonosítót rendel minden tárolt objektumhoz. Ez az azonosító minden alkalommal frissül, amikor egy objektumon frissítési műveletet hajtanak végre. Az azonosító a HTTP GET-válasz részeként kerül vissza az ügyfélnek a HTTP protokollban definiált ETag (entitáscímke) fejléc használatával. Az ilyen objektumon frissítést végző felhasználó beküldheti az eredeti ETag-et egy feltételes fejléccel együtt, hogy a frissítés csak akkor történjen meg, ha egy bizonyos feltétel teljesült – ebben az esetben a feltétel egy "If-Match" fejléc, amelyhez a Storage Service szükséges. annak biztosítására, hogy a frissítési kérelemben megadott ETag értéke megegyezik a Storage szolgáltatásban tárolt értékkel.  

Ennek a folyamatnak a vázlata a következő:  

1. Lekérni egy blobot a tárolási szolgáltatásból, a válasz tartalmaz egy HTTP ETag fejléc értéket, amely azonosítja az objektum aktuális verzióját a storage szolgáltatásban.
2. A blob frissítésekénél adja meg az 1. **If-Match**
3. A szolgáltatás összehasonlítja az ETag-értéket a kérelemben a blob aktuális ETag értékével.
4. Ha a blob aktuális ETag értéke más verziójú, mint az ETag a kérelem **If-Match** feltételes fejlécében, a szolgáltatás 412-es hibát ad vissza az ügyfélnek. Ez azt jelzi az ügyfélnek, hogy egy másik folyamat frissítette a blobot, mivel az ügyfél lekérte azt.
5. Ha a blob aktuális ETag értéke megegyezik az ETag-tel a kérelem **If-Match** feltételes fejlécében, a szolgáltatás végrehajtja a kért műveletet, és frissíti a blob aktuális ETag értékét, hogy megmutassa, hogy új verziót hozott létre.  

A következő C# kódrészlet (a Client Storage Library 4.2.0 használatával) egy egyszerű példát mutat be, hogyan hozhat létre egy **If-Match AccessCondition-t** az ETag-érték alapján, amely egy korábban lekért vagy beszúrt blob tulajdonságaiból érhető el. Ezután az **AccessCondition** objektumot használja a blob frissítésekor: az **AccessCondition** objektum hozzáadja az **If-Match** fejlécet a kérelemhez. Ha egy másik folyamat frissítette a blobot, a Blob szolgáltatás egy HTTP 412 -es (előfeltétel sikertelen) állapotüzenetet ad vissza. A teljes mintát itt töltheti le: [Az egyidejűség kezelése az Azure Storage használatával.](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Az Azure Storage további feltételes fejlécek, például **if-modified-since**, **if-unmodified-since** és **if-none-match,** valamint ezek kombinációi is támogatja. További információt a [Feltételes fejlécek megadása a Blob Service Operations szolgáltatáshoz című témakörben talál.](https://msdn.microsoft.com/library/azure/dd179371.aspx)  

Az alábbi táblázat összefoglalja azokat a tárolóműveleteket, amelyek feltételes fejléceket fogadnak el, például **az If-Match-et** a kérelemben, és amelyek etag értéket adnak vissza a válaszban.  

| Művelet | Konténer etag értékét adja vissza. | Feltételes fejlécek elfogadása |
|:--- |:--- |:--- |
| Tároló létrehozása |Igen |Nem |
| Tároló tulajdonságainak beszereznie |Igen |Nem |
| Tárolómetaadatok bekése |Igen |Nem |
| Tároló metaadatainak beállítása |Igen |Igen |
| Tároló aCL bekése |Igen |Nem |
| Tároló acl beállítása |Igen |Igen (*) |
| Tároló törlése |Nem |Igen |
| Bérlő tárolója |Igen |Igen |
| Blobok listája |Nem |Nem |

(*) A SetContainerACL által meghatározott engedélyek gyorsítótárba kerülnek, és az engedélyek frissítései 30 másodpercet vesznek igénybe, hogy propagálják, amely időszak alatt a frissítések nem konzisztensek.  

Az alábbi táblázat összefoglalja azokat a blob-műveleteket, amelyek feltételes fejléceket fogadnak el, például az **If-Match-et** a kérelemben, és amelyek etag értéket adnak vissza a válaszban.

| Művelet | ETag értéket ad eredményül. | Feltételes fejlécek elfogadása |
|:--- |:--- |:--- |
| Put Blob |Igen |Igen |
| Blob beszerezése |Igen |Igen |
| Get Blob Properties |Igen |Igen |
| Blob tulajdonságainak beállítása |Igen |Igen |
| Blob metaadatok bekésezése |Igen |Igen |
| Blob metaadatainak beállítása |Igen |Igen |
| Bérlőblob (*) |Igen |Igen |
| Snapshot Blob |Igen |Igen |
| Copy Blob |Igen |Igen (forrás- és célblob esetén) |
| Blob másolásának megszakítása |Nem |Nem |
| Delete Blob |Nem |Igen |
| Blokk berakása |Nem |Nem |
| Blokklista felrakása |Igen |Igen |
| Blokklista bekerülése |Igen |Nem |
| Oldal berakása |Igen |Igen |
| Oldaltartományok betöltése |Igen |Igen |

(*) A bérletblob nem módosítja az ETag-ot egy blobon.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pesszimista párhuzamosság blobok esetén

Blob zárolásához kizárólagos használatra, beszerezhet egy [bérletet](https://msdn.microsoft.com/library/azure/ee691972.aspx) rajta. Amikor lízinget szerez, megadhatja, hogy mennyi ideig van szüksége a bérletre: ez lehet 15 és 60 másodperc közötti vagy végtelen, ami kizárólagos zárolásnak felel meg. Megújíthatja a véges bérletet, hogy meghosszabbítsa azt, és bármikor felszabadíthatja a bérletet, ha végzett vele. A Blob szolgáltatás automatikusan felszabadítja a véges bérleteket, amikor azok lejárnak.  

A bérletek lehetővé teszik a különböző szinkronizálási stratégiák támogatását, beleértve az exkluzív írási / megosztott olvasást, az exkluzív írást / kizárólagos írást és a megosztott írást / kizárólagos olvasást. Ha létezik címbérlet, a tárolási szolgáltatás kizárólagos írási (put, set and delete műveleteket) kényszerít ki, azonban az olvasási műveletek kizárólagosságának biztosítása megköveteli a fejlesztőtől annak biztosítását, hogy minden ügyfélalkalmazás címbérlet-azonosítót használjon, és hogy egyszerre csak egy ügyfélnek legyen egy érvényes bérleti azonosító. Olvasási műveletek, amelyek nem tartalmazzák a címbérlet-azonosító eredménye a megosztott olvasás.  

A következő C# kódrészlet egy példa egy kizárólagos bérlet beszerzése 30 másodpercig egy blob, a blob tartalmának frissítése, majd a bérlet felszabadítása. Ha már van érvényes bérlet a blobban, amikor megpróbál beszerezni egy új bérletet, a Blob szolgáltatás egy "HTTP (409) Ütközés" állapoteredményt ad vissza. A következő kódrészlet egy **AccessCondition** objektumot használ a címbérlet-adatok beágyazásához, amikor a blob frissítésére vonatkozó kérelmet küld a storage szolgáltatásban.  A teljes mintát itt töltheti le: [Az egyidejűség kezelése az Azure Storage használatával.](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Ha a bérletazonosító átadása nélkül kísérel meg írási műveletet egy bérelt blobon, a kérelem 412-es hibával sikertelen lesz. Vegye figyelembe, hogy ha a címbérlet lejár, mielőtt meghívja az **UploadText** metódust, de még mindig átadja a címbérleti azonosítót, a kérelem **412-es** hibával is sikertelen lesz. A címbérlet lejárati idejének és a bérleti azonosítók kezeléséről a [Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST dokumentációjában olvashat bővebben.  

A következő blobműveletek pesszimista egyidejűség kezelésére használhatják a címbérleteket:  

* Put Blob
* Blob beszerezése
* Get Blob Properties
* Blob tulajdonságainak beállítása
* Blob metaadatok bekésezése
* Blob metaadatainak beállítása
* Delete Blob
* Blokk berakása
* Blokklista felrakása
* Blokklista bekerülése
* Oldal berakása
* Oldaltartományok betöltése
* Pillanatképblob - a címbérlet-azonosító nem kötelező, ha létezik bérlet
* Blob másolása – címbérlet-azonosító szükséges, ha létezik címbérlet a célblobon
* Blob másolásának megszakítása – címazonosító szükséges, ha végtelen bérlet létezik a célblobon
* Blobbérlet  

### <a name="pessimistic-concurrency-for-containers"></a>Pesszimista egyidejűség konténerek

A tárolók bérletei lehetővé teszik, hogy ugyanazokat a szinkronizálási stratégiákat támogassák, mint a blobok (kizárólagos írás / megosztott olvasás, kizárólagos írás / kizárólagos írás / kizárólagos olvasás) azonban ellentétben a blobok a tárolási szolgáltatás csak kényszeríti kizárólagosság törlési műveletek. Aktív bérlettel rendelkező tároló törléséhez az ügyfélnek tartalmaznia kell az aktív címbérlet-azonosítót a törlési kérelemmel. Minden más tárolóművelet sikeres egy bérelt tárolón anélkül, hogy a címbérlet-azonosítót, amely esetben megosztott műveletek. Ha a frissítés (put or set) vagy az olvasási műveletek kizárólagossága szükséges, akkor a fejlesztőknek gondoskodniuk kell arról, hogy minden ügyfél címbérlet-azonosítót használjon, és hogy egyszerre csak egy ügyfél rendelkezik érvényes címbérlet-azonosítóval.  

A következő tárolóműveletek pesszimista egyidejűség kezelésére használhatják a címbérleteket:  

* Tároló törlése
* Tároló tulajdonságainak beszereznie
* Tárolómetaadatok bekése
* Tároló metaadatainak beállítása
* Tároló aCL bekése
* Tároló acl beállítása
* Bérlő tárolója  

További információkért lásd:  

* [Feltételes fejlécek megadása Blob Service-műveletekhez](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Bérlő tárolója](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Blobbérlet](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Egyidejűség kezelése a table storage-ban

A Table szolgáltatás optimista egyidejűségi ellenőrzéseket használ alapértelmezett viselkedésként, amikor entitásokkal dolgozik, ellentétben a Blob szolgáltatással, ahol kifejezetten optimista egyidejűségi ellenőrzéseket kell végrehajtania. A másik különbség a tábla és a Blob-szolgáltatások között az, hogy csak az entitások egyidejűségi viselkedését kezelheti, míg a Blob szolgáltatással kezelheti a tárolók és a blobok egyidejűségét.  

Optimista egyidejűség használatához és annak ellenőrzéséhez, hogy egy másik folyamat módosított-e egy entitást, mivel a table storage szolgáltatásból lekérte, használhatja a táblaszolgáltatás eredményére adott ETag értéket. Ennek a folyamatnak a vázlata a következő:  

1. Egy entitás lekérése a táblatárolási szolgáltatásból, a válasz tartalmaz egy ETag értéket, amely azonosítja az entitáshoz társított aktuális azonosítót a tárolási szolgáltatásban.
2. Az entitás frissítésekénadja meg az 1. **If-Match**
3. A szolgáltatás összehasonlítja a kérelemben lévő ETag értéket az entitás aktuális ETag értékével.
4. Ha az entitás aktuális ETag értéke eltér a kérelem kötelező **If-Match** fejlécében lévő ETag-től, a szolgáltatás 412-es hibát ad vissza az ügyfélnek. Ez azt jelzi az ügyfélnek, hogy egy másik folyamat frissítette az entitást, mivel az ügyfél lekérte azt.
5. Ha az entitás aktuális ETag értéke megegyezik a kérelem kötelező **If-Match** fejlécében szereplő ETag-címkével, vagy az **If-Match** fejléc tartalmazza a helyettesítő karaktert (*), a szolgáltatás végrehajtja a kért műveletet, és frissíti az entitás aktuális ETag értékét, hogy megmutassa, hogy frissült.  

Vegye figyelembe, hogy a Blob szolgáltatással ellentétben a table service megköveteli, hogy az ügyfél egy **If-Match** fejlécet tartalmazzon a frissítési kérelmekben. Azonban lehetőség van egy feltétel nélküli frissítés kényszerítésére (az utolsó író nyer stratégia) és megkerülheti az egyidejűségi ellenőrzéseket, ha az ügyfél beállítja az **If-Match** fejlécet a helyettesítő karakterre (*) a kérelemben.  

A következő C# kódrészlet egy olyan ügyfélentitást jelenít meg, amelyet korábban hoztak létre vagy lekért, és frissítették az e-mail címüket. A kezdeti beszúrási vagy lekérési művelet tárolja az ETag értéket az ügyfél objektumban, és mivel a minta ugyanazt az objektumpéldányt használja a csereművelet végrehajtásakor, automatikusan visszaküldi az ETag értéket a table service-nek, lehetővé téve a szolgáltatás számára. egyidejűség megsértésének ellenőrzése. Ha egy másik folyamat frissítette az entitást a táblatárolóban, a szolgáltatás egy HTTP 412 (Előfeltétel sikertelen) állapotüzenetet ad vissza.  A teljes mintát itt töltheti le: [Az egyidejűség kezelése az Azure Storage használatával.](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Az egyidejűségi ellenőrzés kifejezett letiltásához a csereművelet végrehajtása előtt az **alkalmazottobjektum** **ETag** tulajdonságát "*" értékre kell állítania.  

```csharp
customer.ETag = "*";  
```

Az alábbi táblázat összefoglalja, hogy a táblaentitás műveletei hogyan használják az ETag-értékeket:

| Művelet | ETag értéket ad eredményül. | If-Match kérelemfejléc szükséges |
|:--- |:--- |:--- |
| Entitások lekérdezése |Igen |Nem |
| Entitás beszúrása |Igen |Nem |
| Entitás frissítése |Igen |Igen |
| Entitás egyesítése |Igen |Igen |
| Entitás törlése |Nem |Igen |
| Entitás beszúrása vagy cseréje |Igen |Nem |
| Entitás beszúrása vagy egyesítése |Igen |Nem |

Ne feledje, hogy az **Entitás beszúrása vagy cseréje,** valamint az **Entitás beszúrása vagy egyesítése** műveletek *nem* végeznek egyidejűségi ellenőrzéseket, mert nem küldenek ETag értéket a table service-nek.  

A táblákat használó fejlesztőknek általában optimista egyidejűségre kell támaszkodniuk a méretezhető alkalmazások fejlesztése során. Ha pesszimista zárolásra van szükség, a fejlesztők a táblák elérésekor egy kijelölt blob hozzárendelése minden táblához, és megpróbál-hoz egy bérlet a blob, mielőtt az asztalon működik. Ez a megközelítés nem követeli meg az alkalmazás biztosítja, hogy minden adatelérési útvonalak beszerzése a bérlet előtt az asztalon való működés előtt. Azt is vegye figyelembe, hogy a minimális bérleti idő 15 másodperc, amely gondosan figyelembe veszi a méretezhetőség.  

További információkért lásd:  

* [Entitások műveletei](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Egyidejűség kezelése a várólista-szolgáltatásban

Az egyik forgatókönyv, amelyben az egyidejűség aggodalomra ad okot a várólistán szolgáltatás, ahol több ügyfél beolvasása üzeneteket egy várólistából. Amikor egy üzenetet beolvas a várólistából, a válasz tartalmazza az üzenetet és egy pop nyugta értéket, amely az üzenet törléséhez szükséges. Az üzenet nem törlődik automatikusan a várólistából, de a beolvasásután a többi ügyfél számára nem látható a visibilitytimeout paraméter által megadott időintervallumban. Az üzenetet lekérő ügyfélnek a feldolgozás után és a válasz TimeNextVisible eleme által megadott időpont előtt kell törölnie az üzenetet, amelyet a program a visibilitytimeout paraméter értéke alapján számít ki. A láthatósági időtúllépés értéke hozzáadódik ahhoz az időponthoz, amikor az üzenet beolvasása a TimeNextVisible értékének meghatározásához kerül.  

A várólista-szolgáltatás nem támogatja sem az optimista, sem a pesszimista egyidejűséget, és ezért a várólistából beolvasott üzeneteket feldolgozó ügyfeleknek biztosítaniuk kell az üzenetek idempotens módon történő feldolgozását. Az utolsó írónyerési stratégia olyan frissítési műveletekhez használatos, mint a SetQueueServiceProperties, a SetQueueMetaData, a SetQueueACL és az UpdateMessage.  

További információkért lásd:  

* [A Queue szolgáltatás REST API-ja](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Üzenetek beküldése](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Egyidejűség kezelése az Azure-fájlokban

A fájlszolgáltatás két különböző protokollvégponttal érhető el – SMB és REST. A REST szolgáltatás nem támogatja sem az optimista zárolást, sem a pesszimista zárolást, és az összes frissítés egy utolsó írói győzelmi stratégiát követ. A fájlmegosztásokat csatlakoztató SMB-ügyfelek a fájlrendszer zárolási mechanizmusait használhatják a megosztott fájlokhoz való hozzáférés kezelésére – beleértve a pesszimista zárolást is. Amikor egy SMB-ügyfél megnyit egy fájlt, megadja a fájlelérési és megosztási módot is. Ha a Fájlelérési beállítást "Írás/írás" beállítással, valamint a "Nincs" fájlmegosztási módot állítja be, a fájlt a fájl bezárásáig zárolja egy SMB-ügyfél. Ha rest-műveletet kísérel meg egy olyan fájlon, amelyen egy SMB-ügyfél zárolta a fájlt, a REST szolgáltatás 409-es állapotkódot (Ütközés) ad vissza sharingviolation hibakóddal.  

Amikor egy SMB-ügyfél megnyit egy fájlt törlésre, a fájlt függőben lévő törlésként jelöli meg, amíg a fájl összes többi SMB-ügyfélmegnyitott leírója be nem zárul. Amíg egy fájl függőben lévő törlésként van megjelölve, a fájl rest-műveletei a 409-es (Ütközés) állapotkódot adják vissza, amelynek hibakódja SMBDeletePending. A 404-es (nem található) állapotkódot nem adja vissza a rendszer, mivel az SMB-ügyfél a fájl bezárása előtt eltávolíthatja a függőben lévő törlésjelzőt. Más szóval a 404-es (nem található) állapotkód csak a fájl eltávolítása után várható. Ne feledje, hogy amíg egy fájl SMB függőben lévő törlési állapotban van, nem fog szerepelni a Fájlok listázása eredmények között. Vegye figyelembe azt is, hogy a REST Delete File és a REST Delete Directory műveletek atomilag véglegesítve vannak, és nem eredményeznek függőben lévő törlési állapotot.  

További információkért lásd:  

* [Fájlzárolások kezelése](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>További lépések

A teljes minta alkalmazás hivatkozott ebben a blogban:  

* [Egyidejűség kezelése az Azure Storage használatával – Mintaalkalmazás](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Az Azure Storage szolgáltatásról további információt a következő témakörben talál:  

* [A Microsoft Azure Storage kezdőlapja](https://azure.microsoft.com/services/storage/)
* [A Microsoft Azure Storage bemutatása](storage-introduction.md)
* A [Blob,](../blobs/storage-dotnet-how-to-use-blobs.md) [a Tábla,](../../cosmos-db/table-storage-how-to-use-dotnet.md) [a várólisták](../storage-dotnet-how-to-use-queues.md)és a fájlok – első lépések [– tárolása](../storage-dotnet-how-to-use-files.md)
* Tárolási architektúra – [Azure Storage: Magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztenciával](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

