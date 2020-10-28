---
title: Az egyidejűség kezelése
titleSuffix: Azure Storage
description: Ismerje meg, hogyan kezelheti a párhuzamosságot az Azure Storage-ban a blob-, üzenetsor-, tábla-és Fájlszolgáltatások esetében. Ismerje meg a három fő adatpárhuzamossági stratégiát.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: b83a8bfbc79af344c4d158ee65134034db714e9c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783963"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Egyidejűség kezelése a Microsoft Azure Storage szolgáltatásban

A modern internetalapú alkalmazások általában több felhasználóval is megtekintik és frissítik az adatfeldolgozást. Az alkalmazások fejlesztőinek körültekintően kell megfontolniuk a kiszámítható felhasználói élmény biztosítását, különösen, ha több felhasználó is frissítheti ugyanazokat az információkat. Három fő adatpárhuzamossági stratégia létezik, amelyeket a fejlesztők általában figyelembe vesznek:

1. Optimista Egyidejűség – egy frissítést használó alkalmazás a frissítés részeként ellenőrzi, hogy az adott adatbázis módosult-e, mivel az alkalmazás utoljára olvasta az adott adatforrást. Ha például két felhasználó egy wiki oldalt tekint meg, akkor a wiki platformnak biztosítania kell, hogy a második frissítés ne írja felül az első frissítést, és hogy a felhasználók tisztában legyenek azzal, hogy a frissítés sikeres volt-e. Ezt a stratégiát leggyakrabban a webes alkalmazásokban használják.
2. Pesszimista Egyidejűség – egy frissítést megkereső alkalmazás zárolást hajt végre egy objektumon, így megakadályozva, hogy más felhasználók a zárolás felszabadítása előtt frissítse az adatait.
3. Utolsó író WINS – egy olyan megközelítés, amely lehetővé teszi a frissítési műveletek folytatását annak ellenőrzése nélkül, hogy bármely más alkalmazás frissítette-e az adatokhoz, mivel az alkalmazás először olvassa be az adatokból. Ezt a stratégiát gyakran használják az adatparticionálás során, így nem valószínű, hogy több felhasználó is hozzáfér ugyanahhoz az adatszolgáltatáshoz. Hasznos lehet a rövid élettartamú adatfolyamok feldolgozásakor is.

Ez a cikk áttekintést nyújt arról, hogy az Azure Storage Hogyan egyszerűsíti a fejlesztést mind a három párhuzamossági stratégia támogatásával.

## <a name="azure-storage-simplifies-cloud-development"></a>Az Azure Storage egyszerűbbé teszi a felhőalapú fejlesztést

Az Azure Storage mind a három stratégiát támogatja, bár ez a sajátossága annak, hogy teljes körű támogatást nyújtson az optimista és a pesszimista párhuzamosságokhoz. Az Azure Storage egy erős konzisztencia-modell betartására lett tervezve, amely garantálja, hogy amikor egy adatbeszúrási vagy-frissítési műveletet véglegesít, az összes további hozzáférés a legújabb frissítést fogja látni. A végleges konzisztencia-modellt használó tárolási platformok késése az egy felhasználó által végzett írás és a többi felhasználó által megtekinthető, ha a frissített adatértékek is megtalálhatók.

A fejlesztőknek tisztában kell lenniük azzal is, hogy a tárolási platform hogyan különíti el a változást – különösen a tranzakciókban lévő ugyanazon objektum változásaira. Az Azure Storage szolgáltatás pillanatkép-elkülönítést használ az olvasási műveletek egyazon partíción belüli írási műveletekkel való egyidejű megtörténésének engedélyezéséhez. A többi elkülönítési szinttől eltérően a pillanatkép-elkülönítés garantálja, hogy az összes olvasás az adatok konzisztens pillanatképét tekinti meg, még a frissítések előfordulásakor is – lényegében a legutóbbi véglegesített értékek visszaadásával a frissítési tranzakció feldolgozása közben.

## <a name="managing-concurrency-in-blob-storage"></a>Egyidejűség kezelése a blob Storage-ban

Megadhatja, hogy az optimista vagy a pesszimista párhuzamossági modellt használja-e a blobok és tárolók elérésének kezeléséhez a Blob serviceban. Ha nem ad meg explicit módon stratégiát, az utolsó írási WINS az alapértelmezett érték.

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>A blobok és tárolók optimista párhuzamossága

A Storage szolgáltatás minden tárolt objektumhoz hozzárendel egy azonosítót. Ez az azonosító minden alkalommal frissül, amikor egy adott objektumon egy frissítési művelet történik. Az azonosítót a http GET válasz részeként a rendszer visszaküldi az ügyfélnek a HTTP protokollon belül definiált ETag (Entity címke) fejléc használatával. Egy ilyen objektum frissítését végző felhasználó az eredeti ETag és egy feltételes fejléccel is elküldheti a frissítést, így biztosítva, hogy egy adott feltétel teljesült – ebben az esetben a feltétel egy "If-Match" fejléc, amelyhez a Storage szolgáltatásnak meg kell győződnie arról, hogy a frissítési kérelemben megadott ETag értéke megegyezik a tárolási szolgáltatásban tárolt értékkel.

A folyamat körvonala a következő:

1. Egy blob lekérése a Storage szolgáltatásból, a válasz tartalmaz egy HTTP ETag fejléc-értéket, amely az objektum aktuális verzióját azonosítja a Storage szolgáltatásban.
2. A blob frissítésekor adja meg az 1. lépésben kapott ETag értéket a szolgáltatásnak küldött kérelem **IF-Match** feltételes fejlécében.
3. A szolgáltatás összehasonlítja a kérelemben szereplő ETag értéket a blob aktuális ETag értékével.
4. Ha a blob aktuális ETag értéke eltér a kérelemben található **IF-Match** feltételes fejlécben szereplő ETAG, a szolgáltatás 412 hibát ad vissza az ügyfélnek. Ez a hiba azt jelzi, hogy az ügyfél egy másik folyamat frissítette a blobot, mivel az ügyfél beolvasta azt.
5. Ha a blob aktuális ETag-értéke megegyezik a kérelemben szereplő feltételes fejlécben található ETag **, a szolgáltatás** végrehajtja a kért műveletet, és frissíti a blob aktuális ETAG értékét annak megjelenítéséhez, hogy az új verziót hozott létre.

A következő C# kódrészlet (az ügyféloldali tár 4.2.0 használatával) egy egyszerű példát mutat be egy **IF-Match AccessCondition** létrehozásához a ETAG érték alapján, amely a korábban lekért vagy beszúrt blob tulajdonságaiból érhető el. Ezután a **AccessCondition** objektum használatával frissíti a blobot: a **AccessCondition** objektum hozzáadja az **IF-Match** fejlécet a kérelemhez. Ha egy másik folyamat frissítette a blobot, a Blob service egy HTTP 412-as (előfeltétel-sikertelen) állapotjelző üzenetet ad vissza. A teljes minta innen tölthető le: az [Azure Storage használatával történő Egyidejűség kezelése](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Az Azure Storage Emellett támogatja a feltételes fejléceket is, például **Ha a-Modified-Since** , **IF-remodified-since** , **If-None-Match** és a fejlécek kombinációit. További információ: [feltételes fejlécek megadása a blob Service-műveletekhez](/rest/api/storageservices/Specifying-Conditional-Headers-for-Blob-Service-Operations).

A következő táblázat összefoglalja azokat a tároló-műveleteket, amelyek elfogadják a feltételes fejléceket, például ha a kérésben **egyeznek** , és amelyek ETAG értéket adnak vissza a válaszban.

| Művelet | A tároló ETag értékét adja vissza. | Feltételes fejlécek elfogadása |
|:--- |:--- |:--- |
| Tároló létrehozása |Igen |Nem |
| Tároló tulajdonságainak beolvasása |Igen |Nem |
| Tároló metaadatainak beolvasása |Igen |Nem |
| Tároló metaadatainak beállítása |Igen |Igen |
| Tároló ACL lekérése |Igen |Nem |
| Tároló ACL beállítása |Igen |Igen (*) |
| Tároló törlése |Nem |Igen |
| Bérlet tárolója |Igen |Igen |
| Blobok listázása |Nem |Nem |

(*) A SetContainerACL által meghatározott engedélyek gyorsítótárazva vannak, és az engedélyek frissítése 30 másodpercet vesz igénybe, hogy az adott időszak frissítései ne legyenek konzisztensek.

A következő táblázat összefoglalja azokat a blob-műveleteket, amelyek elfogadják a kérésben szereplő feltételes fejléceket, például **Ha-egyeznek** , és amelyek ETAG értéket adnak vissza a válaszban.

| Művelet | ETag értéket ad vissza. | Feltételes fejlécek elfogadása |
|:--- |:--- |:--- |
| Put Blob |Igen |Igen |
| BLOB beolvasása |Igen |Igen |
| Get Blob Properties |Igen |Igen |
| BLOB tulajdonságainak beállítása |Igen |Igen |
| BLOB metaadatainak beolvasása |Igen |Igen |
| BLOB metaadatainak beállítása |Igen |Igen |
| Címbérleti blob (*) |Igen |Igen |
| Snapshot Blob |Igen |Igen |
| Copy Blob |Igen |Igen (a forrás és a cél blob esetében) |
| BLOB másolásának megszakítása |Nem |Nem |
| Delete Blob |Nem |Igen |
| Put blokk |Nem |Nem |
| Tiltási lista |Igen |Igen |
| Tiltási lista lekérése |Igen |Nem |
| Oldal elhelyezése |Igen |Igen |
| Oldalak tartományának beolvasása |Igen |Igen |

(*) A címbérleti blob nem változtatja meg a blob ETag.

### <a name="pessimistic-concurrency-for-blobs"></a>A Blobok pesszimista párhuzamossága

Egy blob kizárólagos használatra történő zárolásához szerezzen be [bérletet](/rest/api/storageservices/Lease-Blob) . Bérlet beolvasásakor meg kell adnia a bérlet időtartamát. Az időszak 15 – 60 másodperc vagy végtelen értékre, amely egy exkluzív zárolásra vonatkozik. Újítson meg egy véges bérletet a kibővítéséhez. Bérlet felszabadítása, ha elkészült. Blob Storage automatikusan felszabadítja a véges bérleteket, amikor lejárnak.

A bérletek lehetővé teszik a különböző szinkronizálási stratégiák támogatását. A stratégiák közé tartoznak az *Exkluzív írási/közös olvasási* , *kizárólagos írási/kizárólagos olvasási* és *közös írási/kizárólagos olvasási* műveletek. Ha a bérlet létezik, az Azure Storage kizárólagos írási (Put, set és DELETE művelet) kikényszeríti a kizárólagosságot az olvasási műveletekhez, ezért a fejlesztőnek biztosítania kell, hogy minden ügyfélalkalmazás címbérlet-azonosítót használjon, és hogy egyszerre csak egy ügyfél érvényes címbérleti AZONOSÍTÓval rendelkezik. Azok az olvasási műveletek, amelyek nem tartalmazzák a címbérlet-azonosítót, megosztott olvasást eredményeznek.

A következő C#-kódrészlet egy példát mutat be arra, hogy egy blobon 30 másodpercig szerezzen be egy kizárólagos bérletet, frissíti a blob tartalmát, majd felszabadítsa a bérletet. Ha már létezik érvényes bérlet a blobon egy új bérlet beolvasásakor, a Blob service "HTTP (409) ütközés" állapotot ad vissza. Az alábbi kódrészlet egy **AccessCondition** objektumot használ a címbérleti információk beágyazására, amikor a a blobnak a Storage szolgáltatásban való frissítésére vonatkozó kérést küld.  A teljes minta innen tölthető le: az [Azure Storage használatával történő Egyidejűség kezelése](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Ha a bérlet AZONOSÍTÓjának átadása nélkül próbál meg írási műveletet végrehajtani egy bérelt blobon, a kérelem 412-as hibával meghiúsul. Ha a bérlet a **UploadText** metódus meghívása előtt lejár, de továbbra is átadja a bérlet azonosítóját, a kérelem **412** -as hibával meghiúsul. A bérlet lejárati idejének és a címbérleti azonosítók kezelésével kapcsolatos további információkért tekintse meg a [bérleti blob](/rest/api/storageservices/Lease-Blob) Rest dokumentációját.

A következő blob-műveletek használhatnak címbérleteket a pesszimista Egyidejűség kezeléséhez:

* Put Blob
* BLOB beolvasása
* Get Blob Properties
* BLOB tulajdonságainak beállítása
* BLOB metaadatainak beolvasása
* BLOB metaadatainak beállítása
* Delete Blob
* Put blokk
* Tiltási lista
* Tiltási lista lekérése
* Oldal elhelyezése
* Oldalak tartományának beolvasása
* Pillanatkép-blob – a bérlet azonosítója nem kötelező, ha létezik bérlet
* A blob-bérlet AZONOSÍTÓjának másolása szükséges, ha a cél blobon van egy bérlet
* A Blobok másolásának megszakítása akkor szükséges, ha a cél blobon létezik végtelen bérlet
* Blobbérlet

### <a name="pessimistic-concurrency-for-containers"></a>A tárolók pesszimista egyidejűsége

A tárolók bérletei lehetővé teszik ugyanazokat a szinkronizálási stratégiákat, mint a Blobok ( *kizárólagos írási/megosztott olvasási* , *kizárólagos írási/kizárólagos olvasási* és *közös írási/kizárólagos olvasási* ), a Blobokkal ellentétben azonban a tárolási szolgáltatás csak a kizárólagosságot alkalmazza a törlési műveletekre. Egy aktív bérlettel rendelkező tároló törléséhez az ügyfélnek tartalmaznia kell az aktív címbérlet AZONOSÍTÓját a törlési kérelemmel. Az összes többi tároló-művelet sikeres egy bérelt tárolón anélkül, hogy a bérlet AZONOSÍTÓját, amely esetben megosztott műveleteket hajt végre. Ha a frissítés (Put vagy set) vagy az olvasási műveletek kizárólagossága szükséges, a fejlesztőknek biztosítaniuk kell, hogy az összes ügyfél címbérlet-azonosítót használjon, és hogy egyszerre csak egy ügyfél érvényes címbérleti AZONOSÍTÓval rendelkezik.

A következő tároló-műveletek használhatnak címbérleteket a pesszimista Egyidejűség kezeléséhez:

* Tároló törlése
* Tároló tulajdonságainak beolvasása
* Tároló metaadatainak beolvasása
* Tároló metaadatainak beállítása
* Tároló ACL lekérése
* Tároló ACL beállítása
* Bérlet tárolója

További információkért lásd:

* [Feltételes fejlécek megadása Blob Service-műveletekhez](/rest/api/storageservices/Specifying-Conditional-Headers-for-Blob-Service-Operations)
* [Bérlet tárolója](/rest/api/storageservices/Lease-Container)
* [Blobbérlet](/rest/api/storageservices/Lease-Blob)

## <a name="managing-concurrency-in-table-storage"></a>A Egyidejűség kezelése a Table Storage-ban

Az Table service optimista egyidejűségi ellenőrzéseket használ az entitások használatakor, az Blob servicetól eltérően, ahol explicit módon kell kiválasztania az optimista Egyidejűség-ellenőrzéseket. A tábla és a blob szolgáltatások közötti különbség az, hogy csak az entitások egyidejűségi viselkedését tudja kezelni, de a Blob service a tárolók és a Blobok párhuzamosságát is kezelheti.

Ha optimista párhuzamosságot szeretne használni, és annak ellenőrzése, hogy egy másik folyamat módosította-e az entitást a Table Storage szolgáltatásból való lekérése óta, akkor használhatja a ETag értéket, amelyet akkor kap, ha a Table Service egy entitást ad vissza. A folyamat körvonala a következő:

1. Egy entitás beolvasása a Table Storage szolgáltatásból, a válasz tartalmaz egy ETag értéket, amely azonosítja az adott entitáshoz társított aktuális azonosítót a Storage szolgáltatásban.
2. Az entitás frissítésekor adja meg az 1. lépésben kapott ETag értéket a szolgáltatásnak küldött kérés kötelező **IF-Match** fejlécében.
3. A szolgáltatás összehasonlítja a kérelemben szereplő ETag értéket az entitás aktuális ETag értékével.
4. Ha az entitás aktuális ETag értéke eltér a kérelemben szereplő kötelező **IF-Match** fejlécben szereplő ETAG, a szolgáltatás 412 hibát ad vissza az ügyfélnek. Ez azt jelzi, hogy az ügyfél számára egy másik folyamat frissítette az entitást, mivel az ügyfél beolvasta azt.
5. Ha az entitás aktuális ETag értéke megegyezik a kérelemben szereplő kötelező **IF-Match** fejlécben szereplő ETAG, vagy az **IF-Match** fejléc tartalmazza a helyettesítő karaktert (*), a szolgáltatás elvégzi a kért műveletet, és frissíti az entitás aktuális ETAG értékét annak megjelenítéséhez, hogy a frissítés megtörtént-e.

A Table szolgáltatás megköveteli, hogy az ügyfél tartalmazzon egy **IF-Match** fejlécet a frissítési kérelmekben. Azonban lehetséges egy feltétel nélküli frissítés (utolsó író WINS-stratégia) kényszerítése és a párhuzamosságok mellőzése, ha az ügyfél az **IF-Match** fejlécet a kérelemben szereplő helyettesítő karakter (*) értékre állítja.

A következő C#-kódrészlet olyan ügyfél-entitást mutat be, amely korábban létrehozott vagy lekérte az e-mail-címüket. A kezdeti INSERT vagy replace művelet tárolja a ETag értéket az ügyfél objektumban, és mivel a minta ugyanazt az objektumot használja, amikor végrehajtja a csere műveletet, automatikusan elküldi a ETag értéket a Table szolgáltatásnak, amely lehetővé teszi a szolgáltatás számára a párhuzamosságok megsértésének ellenőrzését. Ha egy másik folyamat frissítette az entitást a Table Storage-ban, a szolgáltatás egy HTTP 412 (előfeltétel-sikertelen) állapotjelző üzenetet ad vissza.  A teljes minta innen tölthető le: az [Azure Storage használatával történő Egyidejűség kezelése](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

A Egyidejűség-ellenőrzés explicit letiltásához a Replace művelet végrehajtása előtt állítsa az **Employee** objektum **ETAG** tulajdonságát "*" értékre.

```csharp
customer.ETag = "*";
```

Az alábbi táblázat összefoglalja, hogyan használják a tábla entitások a ETag értékeket:

| Művelet | ETag értéket ad vissza. | If-Match kérelem fejlécét igényli |
|:--- |:--- |:--- |
| Lekérdezési entitások |Igen |Nem |
| Entitás beszúrása |Igen |Nem |
| Entitás frissítése |Igen |Igen |
| Entitás egyesítése |Igen |Igen |
| Entitás törlése |Nem |Igen |
| Entitás beszúrása vagy cseréje |Igen |Nem |
| Entitás beszúrása vagy egyesítése |Igen |Nem |

Vegye figyelembe, hogy az **entitás beszúrása és cseréje** , illetve az **entitások beszúrása és egyesítése** művelet *nem* végez Egyidejűség-ellenőrzést, mert nem küldenek ETAG értéket a Table szolgáltatásnak.

A táblázatokat használó fejlesztőknek általában optimista párhuzamosságot kell alkalmazniuk. Ha a táblákhoz való hozzáférés során pesszimista zárolásra van szüksége, rendeljen hozzá egy kiválasztott blobot minden táblához, és próbálja meg bérletet létrehozni a blobon a tábla működésének megkezdése előtt. Ehhez a megközelítéshez az alkalmazásnak biztosítania kell, hogy az összes adatelérési útvonal beszerezze a bérletet a táblán való működés előtt. Azt is vegye figyelembe, hogy a minimális bérleti idő 15 másodperc, ami alapos figyelmet igényel a méretezhetőségre.

További információkért lásd:

* [Entitások műveletei](/rest/api/storageservices/Operations-on-Entities)

## <a name="managing-concurrency-in-the-queue-service"></a>A párhuzamosság kezelése a várólista-szolgáltatásban

Egy olyan forgatókönyv, amelyben a párhuzamosság a várólista-kezelési szolgáltatás egyik problémája, hogy több ügyfél is beolvassa az üzeneteket egy várólistából. Amikor egy üzenet beolvasása a sorból történik, a válasz tartalmazza az üzenetet és egy pop-beérkezési értéket, amely az üzenet törléséhez szükséges. Az üzenet nem törlődik automatikusan a várólistából, de a lekérése után a láthatósági időkorlát paraméterben megadott időintervallumban nem látható a többi ügyfél. Az üzenetet lekérő ügyfélnek a feldolgozás után törölni kell az üzenetet, és a válasz TimeNextVisible eleme által megadott idő előtt, amelyet a láthatósági időkorlát paraméterének értéke alapján számítunk ki. A láthatósági időkorlát értékét a rendszer az üzenet lekérésének időpontjához adja, hogy meghatározza a TimeNextVisible értékét.

Az üzenetsor-szolgáltatás nem támogatja az optimista vagy a pesszimista párhuzamosságot, és emiatt az ügyfelek várólistából beolvasott üzenetek feldolgozásával biztosítaniuk kell, hogy az üzenetek idempotens módon legyenek feldolgozva. A legutóbbi író WINS-stratégia olyan frissítési műveletekhez használható, mint például a SetQueueServiceProperties, a SetQueueMetaData, a SetQueueACL és a UpdateMessage.

További információkért lásd:

* [A Queue szolgáltatás REST API-ja](/rest/api/storageservices/Queue-Service-REST-API)
* [Üzenetek beolvasása](/rest/api/storageservices/Get-Messages)

## <a name="managing-concurrency-in-azure-files"></a>Egyidejűség kezelése Azure Files

A file Service két különböző protokoll-végponttal (SMB és REST) érhető el. A REST szolgáltatás nem támogatja az optimista zárolást vagy a pesszimista zárolást, és az összes frissítés a legutóbbi író WINS-stratégiát követi majd. A fájlmegosztás csatlakoztatására alkalmas SMB-ügyfelek a fájlrendszer zárolási mechanizmusaival kezelhetik a megosztott fájlokhoz való hozzáférést – beleértve a pesszimisták zárolásának lehetőségét is. Amikor egy SMB-ügyfél megnyit egy fájlt, a fájl-hozzáférési és megosztási módot is megadja. Ha "Write" vagy "Read/Write" fájl-hozzáférési lehetőséget ad meg, és a "None" fájlmegosztás mód is megjelenik, akkor a rendszer az SMB-ügyfél zárolja a fájlt, amíg a fájl be nem zárul. Ha a REST-művelet olyan fájlon próbálkozik, ahol az SMB-ügyfél zárolta a fájlt, a REST szolgáltatás a 409 (ütközés) állapotkódot fogja visszaadni a hibakód SharingViolation.

Amikor egy SMB-ügyfél egy fájlt nyit meg a törléshez, az a fájlt függőben lévő törlésként jelöli meg, amíg az összes többi SMB-ügyfél nem nyitja meg a fájlt. Míg a fájl függőben lévő törlésként van megjelölve, a fájl REST művelete a 409 (ütközés) állapotkódot fogja visszaadni a hibakód SMBDeletePending. Az 404-as állapotkód (nem található) nem tért vissza, mert lehetséges, hogy az SMB-ügyfél el szeretné távolítani a függőben lévő törlési jelzőt a fájl bezárása előtt. Más szóval a 404 (nem található) állapotkód csak a fájl eltávolításakor várt. Vegye figyelembe, hogy amíg egy fájl egy, az SMB függőben lévő törlési állapotban van, nem fog szerepelni a fájlok listájának eredményei között. Azt is vegye figyelembe, hogy a REST delete fájl-és REST-törlési műveleteit a rendszer atomian véglegesíti, és nem eredményez függőben lévő törlési állapotot.

További információkért lásd:

* [Fájlok zárolásának kezelése](/rest/api/storageservices/Managing-File-Locks)

## <a name="next-steps"></a>Következő lépések

Az ebben a blogban hivatkozott teljes minta alkalmazáshoz:

* [Egyidejűség kezelése az Azure Storage használatával – minta alkalmazás](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

Az Azure Storage-ról további információt a következő témakörben talál:

* [Microsoft Azure Storage Kezdőlap](https://azure.microsoft.com/services/storage/)
* [A Microsoft Azure Storage bemutatása](storage-introduction.md)
* A [blob](../blobs/storage-quickstart-blobs-dotnet.md), a [tábla](../../cosmos-db/tutorial-develop-table-dotnet.md), a [várólisták](../queues/storage-dotnet-how-to-use-queues.md)és a [fájlok](../files/storage-dotnet-how-to-use-files.md) tárolási első lépések
* Tárolási architektúra – [Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency) -ellenőrzéssel