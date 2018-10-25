---
title: A párhuzamosság kezelése a Microsoft Azure Storage szolgáltatásban
description: A Blob, üzenetsor, tábla és fájl szolgáltatások egyidejűség kezelése
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.component: common
ms.openlocfilehash: 91eb9c12a8913c0a96ee7c3133dc5f982c42cad7
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025304"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>A párhuzamosság kezelése a Microsoft Azure Storage szolgáltatásban
## <a name="overview"></a>Áttekintés
Internet alapú modern alkalmazások megtekintése és adatok frissítése egyszerre több felhasználó általában rendelkeznek. Alaposan gondolja kiszámítható felhasználói élményt nyújtson a végfelhasználóik számára, különösen a forgatókönyvek, ahol több felhasználó is frissítheti a ugyanazokat az adatokat az útmutató segítségével az alkalmazásfejlesztők ehhez. Nincsenek a fejlesztők általában érdemes három fő egyidejűségi stratégiák:  

1. Optimista párhuzamosság – egy kérelem végrehajtása egy frissítést a frissítés részeként ellenőrzi, hogy ha az adatok változtak-e az alkalmazás óta utolsó olvassa el az adatokat. Például ha két a felhasználók számára látható egy wikioldal frissítés ugyanazon az oldalon majd a wiki platform biztosítania kell, hogy a második frissítés nem ír felül az első frissítés – és, hogy mindkét felhasználók megérteni, hogy a frissítés sikeres volt-e vagy sem. Ez a stratégia leggyakrabban használják a webes alkalmazások.
2. A pesszimista egyidejűségi – zárolást egy objektumot, más felhasználók meggátolja, hogy az adatok frissítése, amíg nem szabadul fel a zárolást a frissítés végrehajtásához egy alkalmazás keresi vesz igénybe. Például egy elsődleges és tartalék kiszolgálók közötti adatok replikációs forgatókönyvben, ahol csak a fő frissítések végrehajtása a fő általában feladatelemeket kizárólagos zárolást hosszabb ideje, annak érdekében, hogy senki más nem frissítheti az adatokat.
3. Legutolsó író wins – az adatok beolvasása, amely lehetővé teszi, hogy bármely frissítési műveleteket, ha más alkalmazás az adatok óta frissített az alkalmazás első ellenőrzése nélkül folytatja a műveletet. A stratégia (vagy egy hivatalos stratégia hiánya) általában használt adatok particionálása ahol oly módon, hogy nincs-e nem valószínű, hogy több felhasználó hozzá fog férni ugyanazokat az adatokat. Is lehet hasznos, ahol rövid ideig tartó adatfolyamok feldolgozott.  

Ez a cikk áttekintést hogyan teszi egyszerűbbé a az Azure Storage-platform a fejlesztési, mindhárom alábbi egyidejűségi stratégiák első osztályú támogatást biztosít.  

## <a name="azure-storage--simplifies-cloud-development"></a>Az Azure Storage – egyszerűsíti a Felhőbeli fejlesztéshez
Bár ragadni ezt a lehetőséget a teljes körű támogatást nyújt az optimista és pesszimista egyidejűségi, mert egy erős konzisztencia modell, amely garantálja, hogy amikor kihasználásának úgy lett kialakítva, hogy az Azure storage szolgáltatás támogatja az összes három stratégia, a Storage szolgáltatás adatok insert véglegesítések, vagy frissítési művelet az összes további céljából hozzáfér, adatokat fog látni a legújabb frissítést. Storage-platformokat, amelyek a végleges konzisztenciájú modellt használja közötti késéssel rendelkeznek, egy felhasználó, és a frissített adatokat tekinthet meg más felhasználók, így megfelel a fejlesztését az inkonzisztenciák elkerülése érdekében az ügyfélalkalmazások által végrehajtott írási Ez hatással lenne a végfelhasználók számára.  

Egy megfelelő párhuzamossági stratégiát jelölje a fejlesztők is figyelembe kell, hogyan különíti el a tárolási platform módosítások – különösen tranzakciók között ugyanazon az objektumon. Az Azure storage szolgáltatás pillanatkép-elkülönítés használatával lehetővé teszi az olvasási műveletek történjen, már létesítve lett az írási műveletek ugyanazon a partíción belül. Ellentétben más elkülönítési szinten a pillanatkép-elkülönítés garantálja, hogy az olvasások történnek a frissítések – lényegében az utolsó véglegesített értékeket egy frissítés során visszatérő tranzakció feldolgozása közben is látják az adatok egy egységes pillanatképet.  

## <a name="managing-concurrency-in-blob-storage"></a>Az egyidejűség kezelése a Blob storage-ban
Kérheti, blobok és tárolók az a blob szolgáltatáshoz való hozzáférés kezeléséhez vagy optimista vagy pesszimista egyidejűségi modellek használata. Ha explicit módon nem ad egy stratégia utolsó írás wins az alapértelmezett érték.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>A blobok és tárolók Optimista párhuzamosság
A Storage szolgáltatás azonosítót rendel minden tárolt objektum. Ez az azonosító frissül minden alkalommal, amikor egy objektum frissítési művelet történik. Az azonosító az ügyfél részeként az ETag (entitáscímkét) fejlécet, amely a HTTP-protokoll van meghatározva egy HTTP GET választ küld vissza. Egy felhasználó egy frissítést végez ilyen objektum elküldhető együtt egy feltételes fejléc eredeti Etaggel annak biztosításához, hogy a frissítés akkor történik, ha egy bizonyos feltétel teljesül, – ebben az esetben az feltétele egy "If-Match" fejlécet, amely igényel a Társzolgáltatásnak-t o győződjön meg arról, az ETag címke, a frissítés kérelemben megadott értéke ugyanaz, mint amellyel a Storage szolgáltatásban tárolt.  

Ez a folyamat röviden ismerteti a következőképpen történik:  

1. A blob lekérése a storage szolgáltatás, a válasz egy HTTP-ETag fejlécet érték, amely azonosítja az objektumot a storage szolgáltatás jelenlegi verziója tartalmazza.
2. Frissítjük a blobot, ha az ETag-érték az 1. lépésben kapott tartalmazzák a **If-Match** feltételes fejlécében a kérelmet küld a szolgáltatásnak.
3. A szolgáltatás az ETag-érték a kérés a blob ETag aktuális értékét hasonlítja össze.
4. Ha a jelenlegi ETag-érték a BLOB nem található ETag címkével eltérő verzióval a **If-Match** feltételes fejléc a kérelemben, a szolgáltatás egy 412 hibát ad vissza az ügyfélnek. Ez azt jelzi, hogy az ügyfél számára, hogy egy másik folyamat frissítette a blob, mivel az ügyfél a lekérdezés.
5. Ha a jelenlegi ETag-érték a BLOB nem található ETag címkével verzióval azonos verziójúnak a **If-Match** feltételes fejléc a kérelemben, a szolgáltatás elvégzi a kért műveletet, és frissíti az aktuális, megjelenítéséhez a rendszer létrehozta a BLOB ETag-érték egy új verziója.  

Az alábbi C# (a Storage Ügyfélkódtár használatával 4.2.0) látható egy egyszerű példa bemutatja, hogyan hozhat létre egy **If-Match AccessCondition** , amely egy blobot, amely korábban vagy tulajdonságait szeretné elérni, az ETag-érték alapján beolvasott vagy beszúrni. Ezután a **AccessCondition** objektumot, amikor frissíti a blob: a **AccessCondition** objektumot ad hozzá a **If-Match** fejlécet a kérelemhez. Egy másik folyamat frissítette a blobot, ha a blobszolgáltatás egy HTTP 412 (előfeltétel nem teljesült) állapotüzenetet adja vissza. Letöltheti a teljes minta: [az Azure Storage felügyelete egyidejűségi](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
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

A Storage szolgáltatás támogatását is magában foglalja a feltételes további fejlécek például **If-módosítás-óta**, **If-társítandó-óta** és **If-None-Match** , valamint ezek kombinációi. További információkért lásd: [feltételes fejlécek megadásával a Blob szolgáltatási műveletek](http://msdn.microsoft.com/library/azure/dd179371.aspx) az MSDN Webhelyén.  

A következő táblázat összefoglalja a tárolóművelet, mint például a feltételes fejlécek elfogadó **If-Match** a kérelmet, amely a válaszban ETag értéket adnak vissza.  

| Művelet | Tároló ETag-érték visszaadása | Fogadja el a feltételes fejlécek |
|:--- |:--- |:--- |
| Tároló létrehozása |Igen |Nem |
| A tároló tulajdonságainak beolvasása |Igen |Nem |
| Tároló metaadatainak beolvasása |Igen |Nem |
| Állítsa be a tároló metaadatai |Igen |Igen |
| Tároló ACL lekérése |Igen |Nem |
| ACL tároló beállítása |Igen |Igen (*) |
| Tároló törlése |Nem |Igen |
| Címbérlet-tárolók |Igen |Igen |
| Blobok listázása |Nem |Nem |

(*) A SetContainerACL által meghatározott engedélyekkel vannak gyorsítótárazva, és ezeket az engedélyeket a frissítések érvénybe propagálása 30 másodperc, hogy mely időszakban frissítések nem garantált konzisztens.  

A következő táblázat összefoglalja a blob-műveletek, például feltételes fejlécek elfogadó **If-Match** a kérelmet, amely a válaszban ETag értéket adnak vissza.

| Művelet | Az ETag értéket ad vissza | Fogadja el a feltételes fejlécek |
|:--- |:--- |:--- |
| Put Blob |Igen |Igen |
| A Blob lekérése |Igen |Igen |
| A Blob tulajdonságainak lekérése |Igen |Igen |
| A Blob tulajdonságainak megadása |Igen |Igen |
| Blob metaadatainak beolvasása |Igen |Igen |
| Állítsa be a Blob metaadatai |Igen |Igen |
| A Blobbérlet (*) |Igen |Igen |
| Snapshot Blob |Igen |Igen |
| Blob másolása |Igen |Igen (a forrás- és blob) |
| A Blob másolásához megszakítása |Nem |Nem |
| Delete Blob |Nem |Igen |
| PUT letiltása |Nem |Nem |
| PUT tiltólista |Igen |Igen |
| Blokk-lista lekérése |Igen |Nem |
| PUT lap |Igen |Igen |
| Laptartomány-beolvasási |Igen |Igen |

(*) Címbérleti Blobhoz nem módosítja az ETag egy BLOB.  

### <a name="pessimistic-concurrency-for-blobs"></a>A pesszimista egyidejűségi blobok
Kizárólagos használatra blob zárolásához, szerezzen be egy [bérleti](http://msdn.microsoft.com/library/azure/ee691972.aspx) rajta. Amikor bérletet, megadhatja, hogy mennyi ideig kell a bérletet: Ez lehet 15 – 60 másodpercet vagy végtelen, amely kizárólagos zárolást szolgál. Megújítható a kiterjesztéshez véges bérletét, és semmilyen címbérlet fel lehet szabadítani, ha végzett vele. A blob szolgáltatás automatikusan felszabadítja véges bérletek a lejárat után.  

A bérletek engedélyezése különböző szinkronizálási stratégiák is támogatja, beleértve az exkluzív írási / olvasási, exkluzív írási megosztott / kizárólagos olvasási és írási megosztott / kizárólagos olvasási. Ahol a bérlet létezik a storage szolgáltatás érvényesíti exkluzív írási műveletek (put, beállítása és törlési műveletek) azonban biztosító kizárólagosságot az olvasási műveletek igényel a fejlesztő győződjön meg arról, hogy minden ügyfél alkalmazást használja a bérlet Azonosítóját, és egyszerre csak egy ügyfél rendelkezik egy érvényes a bérlet azonosítóját. Olvassa el, ne foglalja bele a bérlet azonosítója eredmény megosztott olvasási műveleteket.  

Az alábbi C#-kódrészlet egy kizárólagos bérlet beszerzése egy BLOB 30 másodpercig, a blob tartalmának frissítése és majd a bérlet feloldása egy példát mutat be. Már van egy érvényes bérletet a blob új bérletet meg, ha a blobszolgáltatás egy "HTTP (409) ütközés" állapot eredményt adja vissza. A következő kódrészlet egy **AccessCondition** objektum magába foglalja a címbérleti információkat, amikor azt egy kérelmet a blob, a storage szolgáltatás frissítéséhez.  Letöltheti a teljes minta: [az Azure Storage felügyelete egyidejűségi](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

A bérelt blob írási művelet a bérlet Azonosítóját át nem kísérli meg, ha a kérelem sikertelen, és a 412. Vegye figyelembe, hogy ha a bérlet meghívása előtt lejár a **UploadText** módszer, de továbbra is át a bérlet Azonosítóját, a kérelem is sikertelen, az egy **412** hiba. Címbérlet lejárati idő és a bérlet azonosítóját kezelésével kapcsolatos további információkért lásd: a [címbérleti Blobhoz](http://msdn.microsoft.com/library/azure/ee691972.aspx) REST-dokumentációnkhoz.  

A következő blob műveletek bérletek használatával pesszimista egyidejűség kezelése:  

* Put Blob
* A Blob lekérése
* A Blob tulajdonságainak lekérése
* A Blob tulajdonságainak megadása
* Blob metaadatainak beolvasása
* Állítsa be a Blob metaadatai
* Delete Blob
* PUT letiltása
* PUT tiltólista
* Blokk-lista lekérése
* PUT lap
* Laptartomány-beolvasási
* Blob pillanatkép - bérlet Azonosítóját kötelező, ha a bérlet létezik
* Blob - azonosító szükséges, ha egy bérlet létezik-e a cél BLOB lease másolása
* A Blob másolásához megszakítási - bérlet Azonosítóját szükséges, ha egy végtelen bérlet létezik-e a cél blob
* Címbérleti Blobhoz  

### <a name="pessimistic-concurrency-for-containers"></a>A pesszimista egyidejűség-tárolókhoz
Bérletek a tárolókat, mint a blobok támogatott szinkronizálási ugyanezen stratégiák engedélyezése (kizárólagos írási és olvasási, exkluzív írási megosztott / kizárólagos olvasási és írási megosztott / kizárólagos olvasási) azonban blobok ellentétben a storage szolgáltatás csak kényszerít kizárólagosság törlési műveletek. Töröl egy tárolót, a rendszer aktív bérletet, egy ügyfél tartalmaznia kell a törlési kérelem a aktív bérlet Azonosítóját. A bérlet Azonosítóját használata nélkül egy bérelt tárolón sikeres tároló minden egyéb művelet ebben az esetben vannak közös műveleteket. Kötelező kizárólagosság (put vagy set) frissítési vagy olvasási műveletek esetén győződjön meg a fejlesztők kell, hogy minden ügyfél használja a bérlet Azonosítóját, és csak egy ügyfél egyszerre rendelkezik egy érvényes bérletet.  

A következő tárolóművelet bérletek használatával pesszimista egyidejűség kezelése:  

* Tároló törlése
* A tároló tulajdonságainak beolvasása
* Tároló metaadatainak beolvasása
* Állítsa be a tároló metaadatai
* Tároló ACL lekérése
* ACL tároló beállítása
* Címbérlet-tárolók  

További információkért lásd:  

* [Blob szolgáltatás műveletek által használt feltételes fejlécek megadása](http://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Címbérlet-tárolók](http://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Címbérleti Blobhoz ](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>A Table Service szolgáltatásban az egyidejűség kezelése
A table service használja az optimista egyidejűség ellenőrzi az alapértelmezett viselkedésként entitásokkal, a blob szolgáltatás, explicit módon választania kell az optimista egyidejűséget ellenőrzéséhez eltérően működik. A tábla- és blobtárolókat szolgáltatások közötti különbség az, hogy csak kezelheti az entitások egyidejűségi viselkedését, mivel a blob szolgáltatással kezelheti az egyidejűséget, tárolók és blobok.  

Használjon optimista egyidejűséget, és ellenőrizze, hogy egy másik folyamat módosítása egy entitás, a table storage szolgáltatásból való lekérdezés óta, amikor a table service adja vissza egy entitás ETag-érték is használhatja. Ez a folyamat röviden ismerteti a következőképpen történik:  

1. Kérje le egy entitást a table storage szolgáltatásból, a válasz egy ETag-érték, amely azonosítja a társzolgáltatás az adott entitással kapcsolatos aktuális azonosítóját tartalmazza.
2. Amikor frissíti az entitás, például a kötelező az 1. lépésben kapott ETag-érték **If-Match** küldeni a szolgáltatásnak a kérelem fejlécében.
3. A szolgáltatás az ETag-érték a kérés az entitás jelenlegi ETag értékét hasonlítja össze.
4. Ha az entitás jelenlegi ETag-érték eltér a kötelező a Etaggel **If-Match** fejléc a kérelemben, a szolgáltatás egy 412 hibát ad vissza az ügyfélnek. Ez azt jelzi, hogy az ügyfél számára, hogy egy másik folyamat frissítette az entitást, mivel az ügyfél a lekérdezés.
5. Ha a jelenlegi ETag-érték az entitás nem ugyanaz, mint a kötelező a Etaggel **If-Match** a kérelemben szereplő tartományfejléc vagy a **If-Match** fejlécet tartalmaz a helyettesítő karakter (*), a szolgáltatás hajtja végre a a kért műveletet, és frissíti az entitás megjelenítéséhez, hogy frissült a jelenlegi ETag-érték.  

Vegye figyelembe, hogy a blob szolgáltatás eltérően a table service igényel tartalmazza az ügyfél egy **If-Match** fejléc a következő frissítési kérelmet. Azonban lehetséges kényszerítése egy feltétel (legutolsó író wins stratégia) frissíti, és az egyidejűség-ellenőrzés megkerülését, ha beállítja az ügyfél a **If-Match** fejlécet a helyettesítő karakter (*) a kérésben.  

Az alábbi C# látható egy ügyfélentitást vagy korábban létrehozott vagy frissített e-mail-címükkel kellene beolvasni. A kezdeti beszúrása vagy művelet tárolja az ügyfél objektum ETag-érték lekéréséhez, és a mintát használja ugyanazt az objektumpéldányt, a Csere műveletet végrehajtása során, mert azt automatikusan elküldi az ETag-érték vissza a table service, a szolgáltatás engedélyezése Ellenőrizze, hogy egyidejűségi megsértése. Ha egy másik folyamat frissítette az entitást a táblatárolóba, a szolgáltatás egy HTTP 412 (előfeltétel nem teljesült) állapotüzenetet adja vissza.  Letöltheti a teljes minta: [az Azure Storage felügyelete egyidejűségi](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Az explicit módon letiltja az egyidejűség-ellenőrzést, állítsa be a **ETag** tulajdonságát a **alkalmazott** az objektum "*" a csere művelet végrehajtása előtt.  

```csharp
customer.ETag = "*";  
```

A következő táblázat összefoglalja, hogyan a tábla entitás műveletek ETag értékek használata:

| Művelet | Az ETag értéket ad vissza | If-Match kérelem fejléce igényel |
|:--- |:--- |:--- |
| Entitáslekérdezés |Igen |Nem |
| Entitás beszúrása |Igen |Nem |
| Entitások frissítése |Igen |Igen |
| Entitás egyesítése |Igen |Igen |
| Entitás törlése |Nem |Igen |
| Entitás beszúrása vagy cseréje |Igen |Nem |
| Entitás beszúrása vagy egyesítése |Igen |Nem |

Vegye figyelembe, hogy a **entitás cseréje vagy beszúrása** és **vagy egyesítési entitás beszúrása** műveletek tegye *nem* bármely egyidejűségi ellenőrzések elvégzéséhez, mert a tábla nem küldenek egy ETag-érték a szolgáltatás.  

Általában a fejlesztők táblák használata támaszkodhat optimista egyidejűséget skálázható alkalmazások fejlesztése során. A pesszimista zárolással van szükség, ha egy megközelítés a fejlesztők táblák elérése esetén minden egyes kijelölt blob hozzárendelése, majd próbálja meg a blob bérletét hajtania mielőtt azok a tábla is igénybe vehet. Ez a megközelítés szükséges az alkalmazás számára biztosítani az összes adat elérési utak előtt lépked végig az a tábla a bérlet beszerzése. Emellett vegye figyelembe, hogy a minimális bérleti ideje-e 15 másodperc, amely a méretezhetőség érdekében alapos megfontolás igényel.  

További információkért lásd:  

* [Műveleti entitások](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>A Queue szolgáltatás az egyidejűség kezelése
Milyen egyidejűségi a fontos az üzenetsor-szolgáltatás egy forgatókönyv, ahol több ügyfél hívnak le üzeneteket az üzenetsorból. Egy üzenetet az üzenetsorból beolvasásakor a válasz tartalmazza, az üzenet és a egy pop beérkezési értéket, amely az üzenet törléséhez. Az üzenet nem törlődnek automatikusan, az üzenetsorból, de rendelkezik lekérve, után már nem látható más ügyfelek számára a a visibilitytimeout paraméter által megadott időtartam alatt. Az ügyfél, amely lekéri az üzenet az üzenet törlése után a feldolgozásukat és a TimeNextVisible által megadott idő előtti elem, amelynek kiszámítása a válasz alapján a visibilitytimeout paraméter várt. Az idő, amikor a lekért TimeNextVisible értékének meghatározása visibilitytimeout értéke kerül.  

A queue szolgáltatás nem rendelkezik az optimista vagy pesszimista egyidejűségi támogatása, és ennek dolgoz fel üzeneteket az üzenetsorból lekért OK ügyfelek biztosítania kell az üzenetek idempotens módon feldolgozása. A frissítési műveletek, például SetQueueServiceProperties, SetQueueMetaData, SetQueueACL és UpdateMessage legutolsó író wins stratégia használható.  

További információkért lásd:  

* [Queue szolgáltatás REST API-val](http://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Üzenetek beolvasása](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>A szolgáltatás az egyidejűség kezelése
A szolgáltatás két különböző protokollvégpontokat – a SMB és a REST használatával is elérhetők. A REST-szolgáltatás nem rendelkezik optimista vagy pesszimista zárolással támogatása, és minden frissítés egy utolsó író wins stratégiát követi. Fájlmegosztásokat csatlakoztatni az SMB-ügyfelek használhatják a file system mechanizmusok megosztott fájlok – például hajthatnak végre pesszimista zárolással való hozzáférés kezelése. Ha egy SMB-ügyfél megnyit egy fájlt, adja meg a fájl hozzáférés- és a megosztáshoz mód. Fájlhozzáférés "Write" vagy "Olvasási/írási" és a egy fájlmegosztás mód "None" beállítás azt eredményezi, hogy a fájl egy SMB-ügyfél által a fájl bezárásáig zárolva. Ha a REST-művelet egy fájlt, amelyben egy SMB-ügyfél rendelkezik-e a fájl zárolva próbálta meg végrehajtani a REST-szolgáltatás SharingViolation. Hibakód: 409 (Ütközés) állapotkódot ad vissza.  

Ha egy SMB-ügyfél megnyit egy fájlt a delete, azt jelöli meg a fájlt, amíg az összes többi SMB-ügyfél törlése függőben lévő megnyitott kezelőkkel fájl nincs megnyitva. Fájl törlése folyamatban van megjelölve, amíg minden olyan REST-művelet a fájl SMBDeletePending. Hibakód: 409 (Ütközés) állapotkódot ad vissza. Mivel előfordulhat, hogy az SMB-ügyfél eltávolítása előtt a fájl bezárásakor a függőben lévő törlési jelző állapotkód: 404 (nem található) nem adja vissza. 404 (nem található) állapotkódot más szóval csak várható, ha a fájl el lett távolítva. Vegye figyelembe, hogy amíg egy fájlt egy SMB, a függő állapotú törlés van, akkor nem szerepelni fog a tulajdonságlista-fájlok eredmények. Ezenkívül vegye figyelembe, hogy a többi fájl törlése és REST törlése könyvtárműveletek szolgáltatásfrissítést elkötelezettek törlés Függőben állapotba eredményez.  

További információkért lásd:  

* [Zárolja fájl kezelése](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Összegzés és további lépések
A Microsoft Azure Storage szolgáltatás úgy lett kialakítva igényeinek a legösszetettebb online alkalmazások veszélyezteti, vagy például egyidejűség és adatkonzisztencia által akkor végrehajtandó fő tervezési feltételezések újragondolja a fejlesztők kényszerítése nélkül nyújtani.  

A blog hivatkozott teljes mintaalkalmazás:  

* [Az Azure Storage - mintaalkalmazás használata az egyidejűség kezelése](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

További információ az Azure Storage lásd:  

* [A Microsoft Azure Storage kezdőlapja](https://azure.microsoft.com/services/storage/)
* [A Microsoft Azure Storage bemutatása](storage-introduction.md)
* Bevezetés a Storage [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tábla](../../cosmos-db/table-storage-how-to-use-dotnet.md), [üzenetsorok](../storage-dotnet-how-to-use-queues.md), és [fájlok](../storage-dotnet-how-to-use-files.md)
* Tároló-architektúra – [az Azure Storage: egy magas rendelkezésre állású felhőalapú tárolási szolgáltatás az erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

