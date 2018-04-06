---
title: A párhuzamosság kezelése a Microsoft Azure Storage szolgáltatásban
description: A Blob, a várólista, a tábla és a fájl szolgáltatások egyidejűségi kezelése
services: storage
documentationcenter: ''
author: jasontang501
manager: tadb
editor: tysonn
ms.assetid: cc6429c4-23ee-46e3-b22d-50dd68bd4680
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.openlocfilehash: 937cca66a0af0674b868e6a87681adbea330e91c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>A párhuzamosság kezelése a Microsoft Azure Storage szolgáltatásban
## <a name="overview"></a>Áttekintés
Internet alapú modern alkalmazások megtekintéséhez és adatok frissítése egyszerre több felhasználó általában rendelkeznek. Ehhez az alkalmazásfejlesztők számára alaposan gondolja át hogyan előre jelezhető környezetet biztosítson a végfelhasználóknak, különösen a forgatókönyvekben, ahol több felhasználó frissítheti ugyanazokat az adatokat. Nincsenek három fő adatok feldolgozási stratégiák fejlesztők általában figyelembe venni:  

1. Egyidejű hozzáférések optimista – utolsó egy kérelem végrehajtása egy frissítést a frissítés részeként ellenőrzi, hogy ha az adatok alkalmazása óta megváltozott olvasható adatok. Például ha két wiki lap megtekintésével tehetjük a frissítés ugyanazon az oldalon majd a wiki platform ellenőriznie kell, hogy a második frissítés nem ír felül az első frissítés –, és hogy mindkét tisztában legyenek azzal, hogy a frissítés sikeres volt-e vagy sem. Ezt a stratégiát leggyakrabban a webes alkalmazásokhoz.
2. Pesszimista feldolgozási – az alkalmazás keresése frissítésének lépnek a zárolási más felhasználók megakadályozza az adatok frissítése, amíg a zárolás objektum. Például, ahol csak a fő frissítések végrehajtása fő/alárendelt adatok replikáció esetén a fő általában tárolására kizárólagos zárolást ideje, annak érdekében, hogy senki más nem frissítheti az adatokat hosszú időn keresztül.
3. Utolsó író wins –, amely lehetővé teszi a frissítési műveleteket ellenőrzése, ha egyetlen más alkalmazáshoz sem az adatok óta frissített az alkalmazás első nélkül folytatja megközelítés beolvasni az adatokat. A stratégia (vagy egy hivatalos stratégia hiánya) általában használt ahol adatok particionálása úgy, hogy van-e nem valószínű, hogy több felhasználó érik el a ugyanazokat az adatokat. Lehet hasznos, ahol rövid élettartamú adatfolyamokat feldolgozott.  

Ez a cikk áttekintést hogyan az Azure Storage platform egyszerűbbé teszi a fejlesztés első osztályú támogatást biztosít a ezek párhuzamossági stratégiák három.  

## <a name="azure-storage--simplifies-cloud-development"></a>Az Azure Storage – egyszerűbbé teszi a felhőalapú fejlesztési
Az az Azure storage szolgáltatás összes három stratégiák, támogatja a teljes körű támogatást nyújt az optimista és pesszimista feldolgozási, mert azt úgy lett kialakítva, amely biztosítja, hogy ha az erős konzisztencia-modellt támogató képessége megkülönböztető azonban a Adatok insert társzolgáltatás megmutatják a véglegesítések számát, vagy frissítési művelet az összes további fér hozzá az, hogy adatokat megjelenik-e a legújabb frissítést. A végleges konzisztencia modellt használó tárolási platformok közötti késés lehet, ha egy írási történik, egy felhasználó, és így megfelel a az inkonzisztenciák elkerülése ügyfélalkalmazások fejlesztéséhez más felhasználók által látható a frissített adatokat befolyásolja a végfelhasználók számára.  

Egy megfelelő feldolgozási stratégia mellett fejlesztők is kell ügyelnie, hogyan egy tárolási platform elkülöníti módosításai – különösen az objektumon keresztül tranzakciók. Az Azure storage szolgáltatás pillanatkép-elkülönítés használatával megtörténjen-e írási műveletek belül egyetlen partícióra egyidejűleg az olvasási műveletek engedélyezése. Más elkülönítési szinten eltérően pillanatkép-elkülönítés biztosítja, hogy az, hogy minden olvasási tekintse meg az adatok alkalmazáskonzisztens pillanatfelvételi frissítések is megjelenhetnek – tulajdonképpen által egy frissítés során az utolsó véglegesített értékeket ad vissza tranzakció feldolgozása közben is.  

## <a name="managing-concurrency-in-blob-storage"></a>A Blob Storage tárolóban párhuzamossági kezelése
Dönthet úgy is, blobok és a tárolók a blob szolgáltatás hozzáférésének kezelése vagy optimista vagy pesszimista feldolgozási modellek segítségével. Ha explicit módon stratégia utolsó írás wins meg az alapértelmezett beállítás.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>A bináris objektumok és tárolók egyidejű hozzáférések optimista
A társzolgáltatás azonosítót rendel hozzá minden tárolt objektum. Ez az azonosító minden alkalommal, amikor a frissítési művelet történik egy objektum frissül. Az azonosító az ETag (entitáscímke) fejlécet, amely a HTTP protokoll van meghatározva egy HTTP GET választ részeként az ügyfél küld vissza. A felhasználó egy olyan objektumon frissítésében elküldheti az eredeti ETag feltételes fejlécet együtt annak érdekében, hogy egy frissítés akkor történik, ha egy bizonyos feltétel teljesül – ebben az esetben az feltétele egy "If-Match" fejlécet, ami a Társzolgáltatás t igényel o győződjön meg arról, a frissítés kérelemben megadott ETag értéke ugyanaz, mint amellyel a Társzolgáltatás tárolja.  

A folyamat során vázlatot a következőképpen történik:  

1. A blob lekérése a társzolgáltatás, a válasz egy HTTP ETag Fejlécérték, amely azonosítja a társzolgáltatás objektum aktuális verziója tartalmazza.
2. A blob frissíti, az 1. lépésben kapott ETag érték adja meg a **If-Match** küldött a szolgáltatás a kérelem feltételes fejlécet.
3. A szolgáltatás összehasonlítja a blob jelenlegi ETag-értékével a kérés a ETag értéket.
4. Ha az aktuális ETag érték a BLOB egy eltérő verziójú, mint az ETag a **If-Match** feltételes fejlécet a kérelem, a szolgáltatás egy 412 hibát ad vissza az ügyfélnek. Ez azt jelzi, az ügyfél számára, hogy egy másik folyamat frissítette a blob, mivel az ügyfél azt lekérése.
5. Ha az aktuális ETag érték a BLOB szereplő ETag címkével verziójával megegyező verzióra a **If-Match** feltételes fejlécet a kérelem, a szolgáltatás végzi a kért műveletet, és frissíti a blob megjelenítése, hogy hozott létre a jelenlegi ETag érték egy új verziója.  

Az alábbi C# kódrészletet (Storage Ügyfélkódtár 4.2.0 használatával) mutatja egy egyszerű példa bemutatja, hogyan hozható létre egy **If-Match AccessCondition** az ETag érték, amelyhez a korábban vagy blob tulajdonságai alapján le, vagy szúrja be. Ezután a **AccessCondition** objektum a blob frissítésekor: a **AccessCondition** objektumot ad hozzá a **If-Match** fejléc kérésre. Ha egy másik folyamat frissítette a blob, a blob szolgáltatás egy HTTP 412 (előfeltétel nem teljesült) állapotüzenetet adja vissza. Letöltheti a teljes mintát itt: [kezelése egyidejű használata az Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the etag in response.
string orignalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No etag, provided so orignal blob is overwritten (thus generating a new etag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the orignal ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(orignalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

A tároló szolgáltatást is támogatja a további feltételes fejlécek például **If-Modified-Since**, **If-Unmodified-Since** és **If-None-Match** , valamint ezek kombinációi. További információkért lásd: [megadó feltételes fejlécek Blob szolgáltatási műveletek](http://msdn.microsoft.com/library/azure/dd179371.aspx) az MSDN Webhelyén.  

A következő táblázat összefoglalja a tároló műveletek, például a feltételes fejlécek elfogadó **If-Match** , valamint a kérelmet a válaszban szereplő ETag értéket adnak vissza.  

| Művelet | Tároló ETag értékét adja vissza | Feltételes fejlécek fogad el |
|:--- |:--- |:--- |
| Tároló létrehozása |Igen |Nem |
| A tároló tulajdonságainak beolvasása |Igen |Nem |
| Tároló metaadatot beszerezni |Igen |Nem |
| Állítsa be a Csomagtároló metaadatai |Igen |Igen |
| Tároló hozzáférés-vezérlési lista beolvasása |Igen |Nem |
| Tároló hozzáférés-vezérlési lista beállítása |Igen |Igen (*) |
| Törli a tárolót |Nem |Igen |
| Címbérlet tároló |Igen |Igen |
| Lista Blobok |Nem |Nem |

(*) A SetContainerACL által meghatározott engedélyekkel gyorsítótárba kerüljenek-e, és ezeket az engedélyeket a frissítések érvénybe propagálására, 30 másodperces, mely időszakban frissítések nem garantált, hogy konzisztens.  

A következő táblázat összefoglalja a blob műveletek, például a feltételes fejlécek elfogadó **If-Match** , valamint a kérelmet a válaszban szereplő ETag értéket adnak vissza.

| Művelet | ETag értéket ad vissza | Feltételes fejlécek fogad el |
|:--- |:--- |:--- |
| Put Blob |Igen |Igen |
| Get Blob |Igen |Igen |
| A Blob tulajdonságainak beolvasása |Igen |Igen |
| A Blob tulajdonságainak beállítása |Igen |Igen |
| A Blob metaadatot beszerezni |Igen |Igen |
| Állítsa be a Blob metaadatai |Igen |Igen |
| Címbérlet Blob (*) |Igen |Igen |
| Snapshot Blob |Igen |Igen |
| Copy Blob |Igen |Igen (a forrás és cél blob) |
| A Blob másolási megszakítása |Nem |Nem |
| Delete Blob |Nem |Igen |
| PUT letiltása |Nem |Nem |
| PUT tiltólista |Igen |Igen |
| Tiltólista beolvasása |Igen |Nem |
| Helyezze a lap |Igen |Igen |
| Get tartományokat |Igen |Igen |

(*) Címbérlet Blob nem módosítja a blob ETag.  

### <a name="pessimistic-concurrency-for-blobs"></a>A blobok pesszimista feldolgozási
Kizárólagos használatra blob zárolásához, szerezzen be egy [bérleti](http://msdn.microsoft.com/library/azure/ee691972.aspx) rajta. Amikor bérletet szerezni, megadhatja, hogy mennyi ideig kell a címbérlet: Ez lehet a 15-60 másodperc vagy végtelen, amely kizárólagos zárolást összege között. A kiterjesztéshez véges címbérlet megújítása is, és semmilyen címbérlet is megjelenhetnek, amikor elkészült, azt. A blob szolgáltatás véges címbérleteket automatikusan feloldja a lejárat után.  

Címbérleteket engedélyezése különböző szinkronizálási stratégiák támogatja, beleértve a kizárólagos írási / olvasási, kizárólagos írási megosztott / kizárólagos olvasási és írási megosztott / kizárólagos olvasása. Ahol a címbérlet létezik a tároló szolgáltatás érvényesíti kizárólagos írási (put, állítsa be és törlési műveletek) azonban biztosítása kizárólagosság az olvasási műveletek igényel a fejlesztő, hogy minden ügyfélalkalmazások használja a címbérlet-Azonosítót, és egyszerre csak egy ügyfél van egy érvényes bérleti azonosítóját. Olvassa el, amelyek nem tartalmazzák a címbérlet azonosító eredményt megosztott olvasási műveletek.  

Az alábbi C# kódrészletet az beszerzése egy kizárólagos bérleti 30 másodpercig a blob, a blob tartalmát frissítése, és majd felszabadítása a címbérlet példáját mutatja be. Ha már létezik egy érvényes bérleti blobot meg egy új bérleti jogot szerezni, a blob szolgáltatás eredményt adja vissza, az "HTTP (409) ütközés" állapot. A következő kódrészletet használja egy **AccessCondition** foglalják magukban a címbérleti információkat, amikor egy kérést a blob storage szolgáltatási frissíteni az objektumot.  Letöltheti a teljes mintát itt: [kezelése egyidejű használata az Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Bérelt blob egy írási művelet a címbérlet azonosító továbbításához nélkül kísérli meg, ha a kérelem egy 412 hibaüzenettel meghiúsul. Ha a bérlete lejár hívása előtt vegye figyelembe, hogy a **UploadText** metódust, de továbbra is át a címbérlet-azonosító, a kérelem is sikertelen, és egy **412** hiba. Címbérlet lejárati idejét és a címbérlet azonosítók kezelésével kapcsolatos további információkért lásd: a [bérleti Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) REST dokumentációját.  

A következő blob műveletek címbérleteket segítségével pesszimista feldolgozási kezelheti:  

* Put Blob
* Get Blob
* A Blob tulajdonságainak beolvasása
* A Blob tulajdonságainak beállítása
* A Blob metaadatot beszerezni
* Állítsa be a Blob metaadatai
* Delete Blob
* PUT letiltása
* PUT tiltólista
* Tiltólista beolvasása
* Helyezze a lap
* Get tartományokat
* Pillanatkép a Blob - címbérlet azonosítója nem kötelező, ha a címbérlet létezik
* A Blob - azonosító szükséges, ha a címbérlet megtalálható-e a cél blob bérleti másolása
* Megszakítási másolási Blob - bérleti azonosító szükséges, ha egy végtelen címbérleti megtalálható-e a cél blob
* Lease Blob  

### <a name="pessimistic-concurrency-for-containers"></a>A tárolók pesszimista feldolgozási
A tárolók címbérleteket engedélyezése blobok a támogatott szinkronizálási ugyanezen stratégiák (kizárólagos írási és olvasási, kizárólagos írási megosztott / kizárólagos olvasási és írási megosztott kizárólagos olvasási /) azonban blobok eltérően a társzolgáltatás csak kikényszeríti kizárólagosság a a törlési műveletek. Törli a tárolóhoz egy aktív bérleti jog vonatkozik, egy ügyfél a törlési kérelem aktív bérleti Azonosítójú tartalmaznia kell. Minden más tároló művelet sikerült bérelt tárolóba többek között a címbérleti azonosító nélkül megosztott ebben az esetben azok a műveletek. Ha szükség a frissítés (put vagy beállítása) vagy az olvasási műveletek kizárólagosság majd fejlesztők győződjön meg arról az összes ügyfél használni a címbérlet-Azonosítót, és hogy csak egy ügyfél egyszerre rendelkezik-e egy érvényes bérleti.  

A következő tároló műveletek címbérleteket segítségével pesszimista feldolgozási kezelheti:  

* Törli a tárolót
* A tároló tulajdonságainak beolvasása
* Tároló metaadatot beszerezni
* Állítsa be a Csomagtároló metaadatai
* Tároló hozzáférés-vezérlési lista beolvasása
* Tároló hozzáférés-vezérlési lista beállítása
* Címbérlet tároló  

További információkért lásd:  

* [A Blob szolgáltatás műveletek feltételes fejlécek megadása](http://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Címbérlet tároló](http://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Címbérlet Blob ](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>A Table szolgáltatásban párhuzamossági kezelése
A table szolgáltatás optimista párhuzamossági alapértelmezett viselkedésként ellenőrzi, amikor dolgozunk entitások, ellentétben a blob szolgáltatás, ha explicit módon választania kell az egyidejű hozzáférések optimista ellenőrzéséhez használ. A más a tábla és a blob szolgáltatás közötti különbség, hogy csak kezelheti az entitások viselkedésének párhuzamossági mivel a blob szolgáltatás segítségével kezelheti a tárolók és blobok CONCURRENCY paraméterének értékét.  

Egyidejű hozzáférések optimista használja, és ellenőrizze, hogy egy másik folyamat entitás módosítása, mivel a table storage szolgáltatásból való lekérése, amikor az entitást adja vissza, a table szolgáltatás ETag érték is használhatja. A folyamat során vázlatot a következőképpen történik:  

1. Egy entitás lekérdezni a table storage szolgáltatásból, a válasz az egy ETag érték, amely azonosítja az alkalmazás a társzolgáltatás társított aktuális azonosítót tartalmazza.
2. Ha az entitás módosítására, vegye fel a kötelező az 1. lépésben kapott ETag érték **If-Match** küldött a szolgáltatás a kérelem fejlécében.
3. A szolgáltatás összehasonlítja az entitás jelenlegi ETag-értékével a kérés a ETag értéket.
4. Ha a jelenlegi ETag entitás értéke eltér a kötelező az ETag **If-Match** fejléc a következő a kérelem, a szolgáltatás egy 412 hibát ad vissza az ügyfélnek. Ez azt jelzi, az ügyfél számára, hogy egy másik folyamat frissítette az entitás, mivel az ügyfél azt lekérése.
5. Ha a jelenlegi ETag az entitás értéke ugyanaz, mint a kötelező az ETag **If-Match** a kérelem fejlécében vagy a **If-Match** fejlécet tartalmaz (*) helyettesítő karakter, a szolgáltatás hajtja végre a a kért műveletet, és frissíti az aktuális ETag érték megjelenítéséhez, hogy frissült az entitás.  

Vegye figyelembe, hogy a blob szolgáltatás eltérően a table szolgáltatás tartalmazza az ügyfél igényel egy **If-Match** fejléc a következő frissítési kérelmek. Azonban is lehet kényszeríteni egy feltétel nélküli (utolsó író wins stratégia) frissítése és feldolgozási ellenőrzések megkerülését, ha az ügyfél beállítja a **If-Match** fejlécének helyettesítő karakter (*) a kérelemben.  

Az alábbi C# kódrészletben láthatja, hogy korábban létre vagy visszavonni a frissített e-mail címüket rendelkező ügyfél entitás. A kezdeti beszúrása vagy művelet tárolja a felhasználói objektum ETag érték beolvasása, és a minta a objektum példányt használja, a Csere műveletet végrehajtásakor, akkor automatikusan visszaküldi az ETag érték a table szolgáltatás, a szolgáltatás engedélyezése Egyidejűség megsértése kereséséhez. Ha egy másik folyamat frissítette az entitás table storage-ban, a szolgáltatás egy HTTP 412 (előfeltétel nem teljesült) állapotüzenetet adja vissza.  Letöltheti a teljes mintát itt: [kezelése egyidejű használata az Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Explicit módon letiltja a konkurencia ellenőrzése, célszerű a **ETag** tulajdonsága a **alkalmazott** objektum "*" a név felülírandó a művelet végrehajtása előtt.  

```csharp
customer.ETag = "*";  
```

A következő táblázat összefoglalja, hogyan a tábla entitás műveletek ETag értékeket használja:

| Művelet | ETag értéket ad vissza | If-Match fejléc igényel |
|:--- |:--- |:--- |
| Lekérdezés entitások |Igen |Nem |
| Entitás beszúrása |Igen |Nem |
| Entitás frissítése |Igen |Igen |
| Entitás egyesítése |Igen |Igen |
| Entitás törlése |Nem |Igen |
| Entitás cseréje vagy beszúrása |Igen |Nem |
| Az INSERT vagy egyesítés entitás |Igen |Nem |

Vegye figyelembe, hogy a **Insert vagy az entitás cseréje** és **Insert vagy az egyesítési entitás** műveletek tegye *nem* bármely párhuzamossági ellenőrzéseket hajtanak végre, mert a tábla nem küldenek egy ETag érték a szolgáltatás.  

Általában a fejlesztők táblák használata hagyatkozzon kizárólag az egyidejű hozzáférések optimista méretezhető alkalmazások fejlesztése során. Pesszimista zárolás van szükség, ha egyik módszer fejlesztők táblák elérése esetén Rendeljen mindegyikhez kijelölt blob, majd próbálja meg érvénybe a címbérlet blobot, mielőtt azok a tábla is igénybe vehet. Ezt a módszert használja az alkalmazás összes adatok elérési utak beszerzése előtt a táblán működő bérleti biztosításához szükséges. Meg kell vegye figyelembe azt is, amely a minimális bérleti idő 15 másodperc, amely kell gondosan meg kell fontolni a méretezhetőség érdekében.  

További információkért lásd:  

* [Entitások műveletek](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>A Queue szolgáltatás az egyidejű kezelése
Milyen párhuzamossági a fontos szolgáltatási várólista egy például az is, ha több ügyfelet keres üzenetek várólistából való várólistából. Ha egy üzenetet a sorból beolvasott, a válasz tartalmazza, az üzenet és a fogadást a pop értéket, amely azonban szükséges az üzenet törlése. Az üzenet nem automatikusan törli a várólistáról, de után azt beolvasása, nincs látható más ügyfelek számára a a visibilitytimeout paraméter által megadott időtartam alatt. Az ügyfél, amely lekéri az üzenet az üzenet törlése után feldolgozása megtörtént, és a TimeNextVisible által megadott időpont előtt elem, amelynek kiszámítása a válasz alapján a visibilitytimeout paraméter várt. Visibilitytimeout értékének hozzáadódik az idő, amelynél a lekért TimeNextVisible értékének meghatározása.  

A queue szolgáltatás nem rendelkezik optimista vagy pesszimista egyidejű támogatása, és a feldolgozása a sorból beolvasott üzenetekből OK ügyfelek biztosítania kell az idempotent módon dolgozza fel az üzeneteket. Utolsó író wins stratégia frissítési műveletek, például a SetQueueServiceProperties, SetQueueMetaData, SetQueueACL és UpdateMessage szolgál.  

További információkért lásd:  

* [Várólista szolgáltatás REST API](http://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Üzenet](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>A szolgáltatás a feldolgozási kezelése
A szolgáltatás elérhető két különböző protokollvégpontokat – a többi pedig az SMB használatával. A többi szolgáltatás nem rendelkezik optimista zárolással vagy a pesszimista zárolás támogatása, és minden frissítés utolsó író wins stratégiát követi. Csatlakoztassa a fájlmegosztások SMB-ügyfelek használhatják fel a fájl rendszer mechanizmusok megosztott fájlok – például a végrehajtásához a pesszimista zárolás elérése. Ha egy SMB-ügyfél megnyit egy fájlt, adja meg a fájl eléréséhez és a megosztáshoz mód. Egy fájl hozzáférés beállításnak a "Write" vagy "Olvasási/írási" egy fájlmegosztás módot együtt a "None" azt eredményezi, hogy a fájl egy SMB-ügyfél zárolta a fájlt bezárásáig. Ha REST-művelet kísérletet egy fájlt, amelyben egy SMB-ügyfél rendelkezik-e a fájl zárolva a többi szolgáltatás-állapotkódot (Ütközés) 409 hibakód SharingViolation vissza.  

Ha egy SMB-ügyfél megnyit egy fájlt törlésre, jelöli a fájlt, amíg más SMB-ügyfél törlése függőben lévő fájl megnyitott kezelőkkel be van zárva. A fájl törlése függőben van megjelölve, amíg bármely REST művelet, hogy a fájl-állapotkódot (Ütközés) 409 hibakód SMBDeletePending ad vissza. Állapotkód: 404-es (nem található) a rendszer nem adja vissza, mert lehetséges, hogy az SMB-ügyfél eltávolítása előtt a fájl bezárásakor a függőben lévő törlési jelzőt. Állapotkód: 404-es (nem található) más szóval csak várható, ha a fájl el lett távolítva. Vegye figyelembe, hogy közben a függőben lévő törlési állapot SMB, akkor nem szerepelni fog a tulajdonságlista-fájlok eredmények. Vegye figyelembe azt is, hogy a többi fájl törlése és a többi törlése Directory műveletek véglegesítése i és a törlés Függőben állapotba eredményez.  

További információkért lásd:  

* [Zárolja fájl kezelése](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Összegzés és további lépések
A Microsoft Azure Storage szolgáltatás úgy tervezték, hibát okoz, vagy tervezési feltételezéseket például feldolgozási és, amely rendelkezik lépnek érvénybe adatok konzisztenciájának rethink fejlesztők kényszerítése nélkül igényeinek a legösszetettebb az online alkalmazások kapnak.  

Ebben a blogban hivatkozik a teljes mintaalkalmazás:  

* [Azure Storage - mintaalkalmazás használatával párhuzamossági kezelése](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

További információ az Azure Storage lásd:  

* [A Microsoft Azure Storage kezdőlap](https://azure.microsoft.com/services/storage/)
* [A Microsoft Azure Storage bemutatása](storage-introduction.md)
* Bevezetés a tárolás [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tábla](../../cosmos-db/table-storage-how-to-use-dotnet.md), [várólisták](../storage-dotnet-how-to-use-queues.md), és [fájlok](../storage-dotnet-how-to-use-files.md)
* Tároló-architektúra – [az Azure Storage: egy magas rendelkezésre állású felhőalapú tárolási szolgáltatásba erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

