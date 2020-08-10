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
ms.openlocfilehash: b1ec7661bc2823932328bd994ec7bc7f6167f13a
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88030384"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Egyidejűség kezelése a Microsoft Azure Storage szolgáltatásban

A modern internetalapú alkalmazások általában több felhasználóval is megtekintik és frissítik az adatfeldolgozást. Ehhez az alkalmazás-fejlesztőknek körültekintően kell megfontolniuk, hogyan biztosíthat kiszámítható felhasználói élményt a végfelhasználók számára, különösen olyan helyzetekben, ahol több felhasználó is frissítheti ugyanazt az információt. Három fő adatpárhuzamossági stratégia létezik, amelyeket a fejlesztők általában figyelembe vesznek:  

1. Optimista Egyidejűség – egy frissítést végrehajtó alkalmazás a frissítés részeként ellenőrzi, hogy az adatok módosultak-e, mivel az alkalmazás utoljára olvasta az adott adatforrást. Ha például két felhasználó egy wiki oldalt tekint meg, akkor a wiki platformnak biztosítania kell, hogy a második frissítés ne írja felül az első frissítést, és hogy a felhasználók tisztában legyenek azzal, hogy a frissítés sikeres volt-e. Ezt a stratégiát leggyakrabban a webes alkalmazásokban használják.
2. Pesszimista Egyidejűség – a frissítés elvégzését végző alkalmazás zárolást eredményez egy objektumon, amely megakadályozza, hogy más felhasználók frissítse az adatait, amíg a zárolás fel nem szabadít. Például egy fő-vagy alárendelt adatreplikálási forgatókönyvben, ahol csak a főkiszolgáló végez frissítéseket, a főkiszolgáló általában egy kizárólagos zárolást tart fenn az adatok hosszabb ideig, így biztosítva, hogy senki más nem tudja frissíteni.
3. Utolsó író WINS – egy olyan megközelítés, amely lehetővé teszi, hogy a frissítési műveletek ellenőrzés nélkül folytatódnak, ha bármely más alkalmazás az adatok első beolvasása óta frissítette az adatfrissítést. Ez a stratégia (vagy a formális stratégia hiánya) általában olyankor használatos, ahol az adatparticionálás úgy történik, hogy nem valószínű, hogy több felhasználó is hozzáfér ugyanahhoz az adatszolgáltatáshoz. Hasznos lehet a rövid élettartamú adatfolyamok feldolgozásakor is.  

Ez a cikk áttekintést nyújt arról, hogy az Azure Storage platform Hogyan egyszerűsíti a fejlesztést azáltal, hogy mind a három ilyen párhuzamossági stratégia első osztályú támogatását biztosítja.  

## <a name="azure-storage-simplifies-cloud-development"></a>Az Azure Storage egyszerűbbé teszi a felhőalapú fejlesztést

Az Azure Storage szolgáltatás mindhárom stratégiát támogatja, bár ez a funkció az optimista és a pesszimista párhuzamosság teljes körű támogatását is lehetővé teszi, mivel olyan erős konzisztencia-modellt biztosít, amely garantálja, hogy amikor a tárolási szolgáltatás véglegesíti az adatbeszúrási vagy frissítési műveletet, az összes további hozzáférés megjelenik a legújabb frissítésen. A végleges konzisztencia-modellt használó tárolási platformok késésben vannak, amikor egy felhasználó írást végez, és ha a frissített adatokat más felhasználók is láthatják, ezért az ügyfélalkalmazások fejlesztését bonyolítják, így megakadályozva a végfelhasználók számára a következetlenségek befolyásolását.  

A megfelelő egyidejűségi stratégiák kiválasztásán kívül érdemes tisztában lennie azzal is, hogy a tárolási platform hogyan különíti el a változásokat – különösen az objektum tranzakción belüli változásait. Az Azure Storage szolgáltatás pillanatkép-elkülönítést használ az olvasási műveletek egyazon partíción belüli írási műveletekkel való egyidejű megtörténésének engedélyezéséhez. A többi elkülönítési szinttől eltérően a pillanatkép-elkülönítés garantálja, hogy az összes olvasás az adatok konzisztens pillanatképét tekinti meg, még a frissítések előfordulásakor is – lényegében a legutóbbi véglegesített értékek visszaadásával a frissítési tranzakció feldolgozása közben.  

## <a name="managing-concurrency-in-blob-storage"></a>Egyidejűség kezelése a blob Storage-ban

Megadhatja, hogy az optimista vagy a pesszimista párhuzamossági modellt használja-e a blobok és tárolók elérésének kezeléséhez a Blob serviceban. Ha nem ad meg explicit módon egy stratégia utolsó írását, a WINS az alapértelmezett.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>A blobok és tárolók optimista párhuzamossága

A Storage szolgáltatás minden tárolt objektumhoz hozzárendel egy azonosítót. Ez az azonosító minden alkalommal frissül, amikor egy adott objektumon frissítési művelet történik. Az azonosítót a http GET válasz részeként a rendszer visszaküldi az ügyfélnek a HTTP protokollon belül definiált ETag (Entity címke) fejléc használatával. Egy ilyen objektum frissítését végző felhasználó az eredeti ETag és egy feltételes fejléccel is elküldhető, így biztosítható, hogy a frissítés csak akkor történjen meg, ha egy bizonyos feltétel teljesült – ebben az esetben a feltétel egy "If-Match" fejléc, amelyhez a Storage szolgáltatásnak meg kell győződnie arról, hogy a frissítési kérelemben megadott ETag megegyezik a tárolási szolgáltatásban tárolt értékkel.  

A folyamat körvonala a következő:  

1. Egy blob lekérése a Storage szolgáltatásból, a válasz tartalmaz egy HTTP ETag fejléc-értéket, amely az objektum aktuális verzióját azonosítja a Storage szolgáltatásban.
2. A blob frissítésekor adja meg az 1. lépésben kapott ETag értéket a szolgáltatásnak küldött kérelem **IF-Match** feltételes fejlécében.
3. A szolgáltatás összehasonlítja a kérelemben szereplő ETag értéket a blob aktuális ETag értékével.
4. Ha a blob aktuális ETag értéke eltér a kérelemben található **IF-Match** feltételes fejlécben szereplő ETAG, a szolgáltatás 412 hibát ad vissza az ügyfélnek. Ez azt jelzi, hogy az ügyfél számára egy másik folyamat frissítette a blobot, mivel az ügyfél beolvasta azt.
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

Az Azure Storage Emellett támogatja a további feltételes fejléceket is, például **Ha-módosítva-óta**, **Ha-nem módosítva – óta** és **If-None-Match** , valamint ezek kombinációit. További információ: [feltételes fejlécek megadása a blob Service-műveletekhez](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

A következő táblázat összefoglalja azokat a tároló-műveleteket, amelyek elfogadják a feltételes fejléceket, például ha a kérésben **egyeznek** , és amelyek ETAG értéket adnak vissza a válaszban.  

| Művelet | A tároló ETag értékét adja vissza. | Feltételes fejlécek elfogadása |
|:--- |:--- |:--- |
| Tároló létrehozása |Igen |Nem |
| Tároló tulajdonságainak beolvasása |Igen |Nem |
| Tároló metaadatainak beolvasása |Igen |Nem |
| Tároló metaadatainak beállítása |Igen |Igen |
| Tároló ACL lekérése |Igen |Nem |
| Tároló ACL beállítása |Yes |Igen (*) |
| Tároló törlése |Nem |Igen |
| Bérlet tárolója |Igen |Igen |
| Blobok listázása |Nem |Nem |

(*) A SetContainerACL által meghatározott engedélyek gyorsítótárazva vannak, és az engedélyek frissítései 30 másodpercet vesznek igénybe, hogy az adott időszak frissítései ne legyenek konzisztensek.  

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
| Copy Blob |Yes |Igen (a forrás és a cél blob esetében) |
| BLOB másolásának megszakítása |Nem |Nem |
| Delete Blob |Nem |Igen |
| Put blokk |Nem |Nem |
| Tiltási lista |Igen |Igen |
| Tiltási lista lekérése |Igen |Nem |
| Oldal elhelyezése |Igen |Igen |
| Oldalak tartományának beolvasása |Igen |Igen |

(*) A címbérleti blob nem változtatja meg a blob ETag.  

### <a name="pessimistic-concurrency-for-blobs"></a>A Blobok pesszimista párhuzamossága

Egy blob kizárólagos használatra történő zárolásához megszerezheti a [bérletet](https://msdn.microsoft.com/library/azure/ee691972.aspx) . Bérlet beszerzése esetén meg kell adnia, hogy mennyi ideig szükséges a bérlet: ez lehet 15 – 60 másodperc vagy végtelen érték között, amely egy exkluzív zárolásnak minősül. Megújíthat egy véges bérletet, amellyel kiterjesztheti, és bármely bérletet kiszabadíthat, ha elkészült. A Blob service automatikusan felszabadítja a véges bérleteket, amikor lejárnak.  

A bérletek lehetővé teszik a különböző szinkronizálási stratégiák támogatását, beleértve az exkluzív írási/közös olvasási, kizárólagos írási/kizárólagos olvasási és közös írási/kizárólagos olvasást. Ha egy bérlet létezik, a tárolási szolgáltatás kikényszeríti az exkluzív írásokat (Put, set és DELETE művelet), azonban az olvasási műveletek kizárólagosságának biztosításához a fejlesztőnek biztosítania kell, hogy az összes ügyfélalkalmazás címbérlet-azonosítót használjon, és hogy egyszerre csak egy ügyfél érvényes címbérleti AZONOSÍTÓval rendelkezik. Azok az olvasási műveletek, amelyek nem tartalmaznak címbérlet-azonosítót, megosztott olvasást eredményeznek.  

A következő C#-kódrészlet egy példát mutat be arra, hogy egy blobon 30 másodpercig szerezzen be egy kizárólagos bérletet, frissíti a blob tartalmát, majd felszabadítsa a bérletet. Ha a blobban már van érvényes bérlet, amikor új bérletet próbál meg beszerezni, a Blob service "HTTP (409) ütközés" állapotot ad eredményül. Az alábbi kódrészlet egy **AccessCondition** objektumot használ a címbérleti információk beágyazására, amikor a a blobnak a Storage szolgáltatásban való frissítésére vonatkozó kérést küld.  A teljes minta innen tölthető le: az [Azure Storage használatával történő Egyidejűség kezelése](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Ha a bérlet AZONOSÍTÓjának átadása nélkül próbál meg írási műveletet végrehajtani egy bérelt blobon, a kérelem 412-as hibával meghiúsul. Vegye figyelembe, hogy ha a bérlet a **UploadText** metódus meghívása előtt lejár, de továbbra is átadja a címbérlet azonosítóját, a kérelem **412** -as hibával meghiúsul. A bérlet lejárati idejének és a címbérleti azonosítók kezelésével kapcsolatos további információkért tekintse meg a [bérleti blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) Rest dokumentációját.  

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

A tárolók bérletei lehetővé teszik ugyanazokat a szinkronizálási stratégiákat, mint a Blobok (kizárólagos írási/megosztott olvasási, kizárólagos írási/kizárólagos olvasási és közös írási/kizárólagos olvasás), azonban a Blobokkal ellentétben a tárolási szolgáltatás csak a kizárólagosságot alkalmazza a törlési műveletekre. Egy aktív bérlettel rendelkező tároló törléséhez az ügyfélnek tartalmaznia kell az aktív címbérlet AZONOSÍTÓját a törlési kérelemmel. Az összes többi tároló-művelet sikeres egy bérelt tárolón anélkül, hogy a bérlet AZONOSÍTÓját, amely esetben megosztott műveleteket hajt végre. Ha a frissítés (Put vagy set) vagy az olvasási műveletek kizárólagossága szükséges, a fejlesztőknek biztosítaniuk kell, hogy az összes ügyfél címbérlet-azonosítót használjon, és hogy egyszerre csak egy ügyfél érvényes címbérleti AZONOSÍTÓval rendelkezik.  

A következő tároló-műveletek használhatnak címbérleteket a pesszimista Egyidejűség kezeléséhez:  

* Tároló törlése
* Tároló tulajdonságainak beolvasása
* Tároló metaadatainak beolvasása
* Tároló metaadatainak beállítása
* Tároló ACL lekérése
* Tároló ACL beállítása
* Bérlet tárolója  

További információ:  

* [Feltételes fejlécek megadása Blob Service-műveletekhez](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Bérlet tárolója](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Blobbérlet](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>A Egyidejűség kezelése a Table Storage-ban

Az Table service optimista egyidejűségi ellenőrzéseket használ az entitások használatakor, az Blob servicetól eltérően, ahol explicit módon kell kiválasztania az optimista Egyidejűség-ellenőrzéseket. A tábla-és blob-szolgáltatások közötti másik különbség az, hogy csak az entitások egyidejűségi viselkedését tudja kezelni, míg a Blob service a tárolók és a Blobok párhuzamosságát is kezelheti.  

Ha optimista párhuzamosságot szeretne használni, és annak ellenőrzése, hogy egy másik folyamat módosította-e az entitást a Table Storage szolgáltatásból való lekérése óta, akkor használhatja a ETag értéket, amelyet akkor kap, ha a Table Service egy entitást ad vissza. A folyamat körvonala a következő:  

1. Egy entitás beolvasása a Table Storage szolgáltatásból, a válasz tartalmaz egy ETag értéket, amely azonosítja az adott entitáshoz társított aktuális azonosítót a Storage szolgáltatásban.
2. Az entitás frissítésekor adja meg az 1. lépésben kapott ETag értéket a szolgáltatásnak küldött kérés kötelező **IF-Match** fejlécében.
3. A szolgáltatás összehasonlítja a kérelemben szereplő ETag értéket az entitás aktuális ETag értékével.
4. Ha az entitás aktuális ETag értéke eltér a kérelemben szereplő kötelező **IF-Match** fejlécben szereplő ETAG, a szolgáltatás 412 hibát ad vissza az ügyfélnek. Ez azt jelzi, hogy az ügyfél számára egy másik folyamat frissítette az entitást, mivel az ügyfél beolvasta azt.
5. Ha az entitás aktuális ETag értéke megegyezik a kérelemben szereplő kötelező **IF-Match** fejlécben szereplő ETAG, vagy az **IF-Match** fejléc tartalmazza a helyettesítő karaktert (*), a szolgáltatás végrehajtja a kért műveletet, és frissíti az entitás aktuális ETAG értékét annak megjelenítéséhez, hogy a frissítés megtörtént-e.  

Vegye figyelembe, hogy a Blob servicetól eltérően a Table Service-nek tartalmaznia kell egy **IF-Match** fejlécet a frissítési kérelmekben. Azonban lehetséges egy feltétel nélküli frissítés (utolsó író WINS-stratégia) kényszerítése és a párhuzamosságok mellőzése, ha az ügyfél az **IF-Match** fejlécet a kérelemben szereplő helyettesítő karakter (*) értékre állítja.  

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

| Művelet | ETag értéket ad vissza. | A-Match kérelem fejlécének megadását igényli |
|:--- |:--- |:--- |
| Lekérdezési entitások |Igen |Nem |
| Entitás beszúrása |Igen |Nem |
| Entitás frissítése |Igen |Igen |
| Entitás egyesítése |Igen |Igen |
| Entitás törlése |Nem |Igen |
| Entitás beszúrása vagy cseréje |Igen |Nem |
| Entitás beszúrása vagy egyesítése |Igen |Nem |

Vegye figyelembe, hogy a **INSERT vagy replace entitás** és az **INSERT vagy Merge entitás** műveletek *nem* végeznek Egyidejűség-ellenőrzéseket, mert nem küldenek ETAG értéket a Table szolgáltatásnak.  

A táblázatokat használó általános fejlesztőknek optimista párhuzamosságot kell alkalmazniuk a méretezhető alkalmazások fejlesztésekor. Ha pesszimista zárolásra van szükség, az egyik módszer a fejlesztők számára is igénybe vehet, ha a táblákhoz való hozzáféréskor egy kijelölt blobot rendel hozzá az egyes táblákhoz, és a táblán való működés előtt megpróbál bérletet készíteni a blobon. Ehhez a megközelítéshez az alkalmazásnak meg kell győződnie arról, hogy az összes adatelérési útvonal beszerezze a bérletet a táblán való működés előtt. Azt is vegye figyelembe, hogy a minimális bérleti idő 15 másodperc, ami alapos megfontolást igényel a méretezhetőség érdekében.  

További információ:  

* [Entitások műveletei](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>A párhuzamosság kezelése a várólista-szolgáltatásban

Egy olyan forgatókönyv, amelyben a párhuzamosság a várólista-kezelési szolgáltatás egyik problémája, hogy több ügyfél is beolvassa az üzeneteket egy várólistából. Amikor egy üzenet beolvasása a sorból történik, a válasz tartalmazza az üzenetet és egy pop-beérkezési értéket, amely az üzenet törléséhez szükséges. Az üzenet nem törlődik automatikusan a várólistából, de a lekérése után a visibilitytimeout paraméter által megadott időintervallumban nem látható a többi ügyfél. Az üzenetet lekérő ügyfélnek a feldolgozás után törölni kell az üzenetet, és a válasz TimeNextVisible eleme által megadott idő előtt, amelyet a visibilitytimeout paraméter értéke alapján számítunk ki. A visibilitytimeout értékét a rendszer az üzenet lekérésének időpontjához adja, hogy meghatározza a TimeNextVisible értékét.  

A várólista-szolgáltatás nem támogatja az optimista vagy a pesszimista párhuzamosságot, és emiatt az ügyfelek várólistából beolvasott üzenetek feldolgozásával biztosítaniuk kell, hogy az üzenetek idempotens módon legyenek feldolgozva. A legutóbbi író WINS-stratégia olyan frissítési műveletekhez használható, mint például a SetQueueServiceProperties, a SetQueueMetaData, a SetQueueACL és a UpdateMessage.  

További információ:  

* [A Queue szolgáltatás REST API-ja](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Üzenetek beolvasása](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Egyidejűség kezelése Azure Files

A file Service két különböző protokoll-végponttal (SMB és REST) érhető el. A REST szolgáltatás nem támogatja az optimista zárolást vagy a pesszimista zárolást, és az összes frissítés a legutóbbi író WINS-stratégiát követi majd. A fájlmegosztást csatlakoztató SMB-ügyfelek használhatnak fájlrendszer-zárolási mechanizmusokat a megosztott fájlokhoz való hozzáférés kezeléséhez – beleértve a pesszimisták zárolásának lehetőségét is. Amikor egy SMB-ügyfél megnyit egy fájlt, a fájl-hozzáférési és megosztási módot is megadja. Ha "Write" vagy "Read/Write" fájl-hozzáférési lehetőséget ad meg, és a "None" fájlmegosztás mód is megjelenik, akkor a rendszer az SMB-ügyfél zárolja a fájlt, amíg a fájl be nem zárul. Ha a REST-művelet olyan fájlon próbálkozik, ahol az SMB-ügyfél zárolta a fájlt, a REST szolgáltatás a 409 (ütközés) állapotkódot fogja visszaadni a hibakód SharingViolation.  

Amikor egy SMB-ügyfél egy fájlt nyit meg a törléshez, az a fájlt függőben lévő törlésként jelöli meg, amíg az összes többi SMB-ügyfél nem nyitja meg a fájlt. Míg a fájl függőben lévő törlésként van megjelölve, a fájl REST művelete a 409 (ütközés) állapotkódot fogja visszaadni a hibakód SMBDeletePending. Az 404-as állapotkód (nem található) nem lesz visszaadva, mert lehetséges, hogy az SMB-ügyfél el szeretné távolítani a függőben lévő törlési jelzőt a fájl bezárása előtt. Más szóval a 404 (nem található) állapotkód csak a fájl eltávolításakor várt. Vegye figyelembe, hogy amíg egy fájl egy, az SMB függőben lévő törlési állapotban van, nem fog szerepelni a fájlok listájának eredményei között. Azt is vegye figyelembe, hogy a REST delete fájl-és REST-törlési műveleteit a rendszer atomian véglegesíti, és nem eredményez függőben lévő törlési állapotot.  

További információ:  

* [Fájlok zárolásának kezelése](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>További lépések

Az ebben a blogban hivatkozott teljes minta alkalmazáshoz:  

* [Egyidejűség kezelése az Azure Storage használatával – minta alkalmazás](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Az Azure Storage-ról további információt a következő témakörben talál:  

* [Microsoft Azure Storage Kezdőlap](https://azure.microsoft.com/services/storage/)
* [A Microsoft Azure Storage bemutatása](storage-introduction.md)
* A [blob](../blobs/storage-dotnet-how-to-use-blobs.md), a [tábla](../../cosmos-db/table-storage-how-to-use-dotnet.md), a [várólisták](../storage-dotnet-how-to-use-queues.md)és a [fájlok](../storage-dotnet-how-to-use-files.md) tárolási első lépések
* Tárolási architektúra – [Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](https://docs.microsoft.com/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency) -ellenőrzéssel

