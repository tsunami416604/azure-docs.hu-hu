---
title: A közös hozzáférésű jogosultságkódot (SAS) az Azure Storage használatával |} A Microsoft Docs
description: Ismerkedjen meg az Azure Storage-erőforrások, beleértve a blobok, üzenetsorok, táblák és fájlok hozzáférésének delegálása közös hozzáférésű jogosultságkódok (SAS) használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 7b5f4db51fca97f79f2b43bfcd5ce8dead3ba50b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470348"
---
# <a name="using-shared-access-signatures-sas"></a>Közös hozzáférésű jogosultságkódok (SAS) használata

Közös hozzáférésű jogosultságkód (SAS) biztosít jogokat a tárfiókban tárolt objektumok korlátozott hozzáférést más ügyfelek számára anélkül, hogy a fiókkulcs. Ebben a cikkben azt adja meg a SAS modell áttekintése, tekintse át az SAS-ajánlott eljárások és tekintse meg néhány példa.

SAS használatával az itt bemutatott ismertetettek további példákért lásd [Ismerkedés az Azure Blob Storage a .NET-ben](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) és más elérhető minták a [Azure-Kódminták](https://azure.microsoft.com/documentation/samples/?service=storage) könyvtár. A mintaalkalmazások letöltése és futtathatja őket, vagy tallózással keresse meg a kódot a Githubon.

## <a name="what-is-a-shared-access-signature"></a>Mi az a közös hozzáférésű jogosultságkód?
Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. A SAS használatával biztosíthat az ügyfelek erőforrásokhoz való hozzáférés a tárfiókban lévő a fiókkulcsok megosztása nélkül. Ez a lényege a közös hozzáférésű jogosultságkód alkalmazásokban való használatának – az SAS a fiókkulcsok veszélyeztetése nélkül teszi lehetővé a tárolási erőforrások megosztását.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

SAS szabályozható a hozzáférés típusa, többek között a SAS rendelkező ügyfelek számára biztosít:

* Az időköz keresztül, amely a SAS az érvényes, beleértve a kezdő és a lejárati időpont.
* A SAS által biztosított engedélyeket. Például a BLOB SAS előfordulhat, hogy adjon olvasási és írási engedélyekkel, hogy a blob, de nem törli az engedélyeket.
* Nem kötelező IP-cím vagy IP-címek tartománya, amelyből az Azure Storage fogja fogadni az SAS. Például előfordulhat, hogy adja meg egy IP-címtartományt a szervezethez tartozó.
* A protokoll, amelyben Azure Storage fogja fogadni az SAS. Ez nem kötelező paraméter használatával korlátozza a hozzáférést az ügyfelek HTTPS-en keresztül.

## <a name="when-should-you-use-a-shared-access-signature"></a>Mikor használjam a közös hozzáférésű jogosultságkód?
SAS is használhatja, ha lehetővé szeretné tenni a tárfiókban lévő erőforrásokhoz való hozzáférés bármely ügyfélnek, amelyek nem rendelkeznek a tárfiók hozzáférési kulcsait. A tárfiók is tartalmaz, egyszerre egy elsődleges és másodlagos hozzáférési kulcsot, adja meg, amelyek rendszergazdai hozzáférést a fiókjához, és a benne található összes erőforrást. A fiók annak lehetőségét, hogy a rosszindulatú vagy hanyagságból használja ezen kulcsok egyikét is közzéteheti nyílik meg. Közös hozzáférésű jogosultságkódok adjon meg egy biztonságos megoldás, amely lehetővé teszi az ügyfelek számára, hogy az olvasási, írási és törlési konkrétan megadta az engedélyek alapján, és meg kell a tárfiókban lévő adatokat.

Egy gyakori forgatókönyv, ahol egy SAS hasznos egy olyan szolgáltatás, ahol a felhasználók olvassa el, és a saját adatokat írni a tárfiókhoz. Egy forgatókönyv, ahol a storage-fiók felhasználói adatokat tárolja, a rendszer két jellemző tervezési minták:

1. Az ügyfelek töltse fel, és töltse le az adatokat egy előtér-proxy szolgáltatással, amely végrehajtja a hitelesítést. Az előtér-proxyszolgáltatás az előnnyel jár, hogy az üzleti szabályok érvényesítési, de a nagy mennyiségű adatok vagy nagy mennyiségű tranzakciók, amelyek egyeznek igény szerint méretezhető szolgáltatás létrehozása lehet költséges vagy nehézkes.

  ![A forgatókönyv diagramja: Előtér-proxyszolgáltatás](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Egy egyszerű szolgáltatás hitelesíti az ügyfelet, igény szerint, és ezután létrehoz egy SAS-kódot. Miután az ügyfél megkapta a SAS kódot, hozzáférhet a tárfiók erőforrásainak közvetlenül a SAS által és a SAS által engedélyezett alatt meghatározott engedélyekkel. A SAS-útválasztási minden adatot az előtér-proxy szolgáltatáson keresztül kell csökkenti.

  ![A forgatókönyv diagramja: SAS-szolgáltató szolgáltatás](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

A két megközelítést hibrid számos valós szolgáltatás használhatja. Például bizonyos adatok feldolgozása előfordulhat, hogy, és ellenőrzi az előtér-proxyn keresztül, míg egyéb adatok mentése és/vagy olvassa el, közvetlenül a SAS használatával.

Emellett szüksége lesz az adatforrás-objektum bizonyos esetekben a másolási műveletek a hozzáférés engedélyezése a SAS használatával:

* Amikor egy blob átmásolása egy másik, amely a különböző storage-fiókban található blob, hogy engedélyezze a hozzáférést a forrásblob SAS kell használnia. Hogy engedélyezze a hozzáférést a forrásblobot igény szerint is használhatja az SAS.
* Fájl másolása másik fájlba, amely a különböző storage-fiókban található esetén a forrásfájl elérésének hitelesítéséhez SAS kell használnia. Hogy engedélyezze a hozzáférést a célfájl igény szerint is használhatja az SAS.
* Amikor egy blob átmásolása egy fájl vagy egy fájlt egy blobba, kell használnia egy SAS az adatforrás-objektum elérésének hitelesítéséhez akkor is, ha a forrás és cél-objektumai találhatók ugyanabban a tárfiókban található.

## <a name="types-of-shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok típusai
Közös hozzáférési aláírások két típusú hozhat létre:

* **Service SAS.** A szolgáltatásalapú SAS csak egy társzolgáltatás (a Blob, a Queue, a Table vagy a File szolgáltatás) egy erőforrásához biztosít hozzáférést. Lásd: [hozhat létre, amely a szolgáltatás SAS](https://msdn.microsoft.com/library/dn140255.aspx) és [SAS példák](https://msdn.microsoft.com/library/dn140256.aspx) a SAS-jogkivonat létrehozásával kapcsolatos részletes információk.
* **SAS-fiók.** A fiók SAS delegáltak erőforrásokhoz való hozzáférés egy vagy több, a storage-szolgáltatás. A szolgáltatásalapú SAS-n keresztül elérhető műveleteket is rendelkezésre áll egy fiók SAS-n keresztül. Ezenkívül a fiók SAS kódjával is adhat hozzáférést, amely az egyes szolgáltatásokhoz, például a alkalmazni műveletekhez **Get/Set szolgáltatástulajdonságok** és **első szolgáltatási statisztikák**. A blobtárolók, táblák, üzenetsorok és fájlmegosztások olvasási, írási és törlési műveleteihez is hozzáférést biztosíthat, amelyeket a szolgáltatásalapú SAS nem engedélyez. Lásd: [hozhat létre, amely a fiók SAS](https://msdn.microsoft.com/library/mt584140.aspx) a fiók SAS-jogkivonat létrehozásával kapcsolatos részletes információk.

## <a name="how-a-shared-access-signature-works"></a>Hogyan működik a közös hozzáférésű jogosultságkód
Közös hozzáférésű jogosultságkód egy aláírt URI-t, amely egy vagy több tároló-erőforrások mutat, és a egy speciális lekérdezési paraméterek készletét tartalmazó jogkivonatok tartalmazza. A token jelzi, hogy milyen erőforrásokat érhetik el az ügyfelet. Lekérdezési paraméterek, az aláírás az SAS-paraméterek értékekből összeállított, és a fiókkulcs aláírva. Az aláírás az Azure Storage által a tárolási erőforrások elérésének hitelesítéséhez használatos.

Íme egy példa egy SAS URI, megjelenítése az erőforrás URI-t és a SAS-jogkivonat:

![SAS URI összetevői](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

A SAS-jogkivonat nem létrehozza a sztring a *ügyfél* ügyféloldali (lásd a [SAS példák](#sas-examples) hitelesítésikód-példák a következő szakaszban). A storage ügyféloldali kódtár, akkor létrehozhat egy SAS-jogkivonatát például nem követett az Azure Storage által bármilyen módon. SAS-tokeneket korlátlan számú az ügyféloldalon is létrehozhat.

Amikor egy ügyfél egy SAS URI-t biztosít az Azure Storage egy kérés részeként, a szolgáltatás ellenőrzi a SAS-paramétereket és aláírást annak ellenőrzéséhez, hogy ez érvényes a kérelem hitelesítéséhez. Ha a szolgáltatás ellenőrzi, hogy az aláírás érvénytelen, akkor a kérelme engedélyezett. Ellenkező esetben a kérelem rendszer elutasította, hibakód: 403 (tiltott).

## <a name="shared-access-signature-parameters"></a>Közös hozzáférésű jogosultságkód paraméterek
SAS-tokeneket és a fiók SAS tartalmazza néhány gyakori paramétereket, és is igénybe vehet néhány paramétert, amelyek eltérnek.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Közös fiók SAS paramétereket és a SAS-tokenek
* **API-verzió** egy nem kötelező paraméter, amely meghatározza a storage szolgáltatás verziót kell használni, a kérés végrehajtásához.
* **Szolgáltatásverzió** egyik kötelező paraméter, amely meghatározza a storage szolgáltatás verziót kell használni, a kérelem engedélyezéséhez.
* **Kezdő időpontja.** Ez az az idő, amikor a SAS érvényessé válik. Közös hozzáférésű jogosultságkód kezdési ideje nem kötelező. Kezdési idő, a rendszer az SAS-e hatékony azonnal. A kezdési időpontot kell kifejezni az UTC (egyezményes világidő), egy speciális UTC jelzéssel ("Z"), például `1994-11-05T13:15:30Z`.
* **Lejárati idő.** Ez az az idő, amelyet követően a SAS már nem érvényes. A bevált gyakorlat, hogy Ön egy SAS-lejárati idő megadása vagy társíthatja azt egy tárolt hozzáférési szabályzatot. A lejárati időpont kell kifejezni az UTC (egyezményes világidő), egy speciális UTC jelzéssel ("Z"), például `1994-11-05T13:15:30Z` (további információk alább).
* **Engedélyek.** A SAS vonatkozóan meghatározott engedélyek adja meg, milyen műveleteket az ügyfél hajthat végre, a storage erőforrás SAS használatával szemben. Elérhető engedélyek egy SAS-fiókot és a szolgáltatásalapú SAS esetében eltérőek.
* **IP.** Egy nem kötelező paraméter, amely meghatározza az IP-cím vagy egy Azure-on kívül az IP-címek (című [útválasztás munkamenetállapot-konfiguráció](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) az Express Route), amelyről a kérelmek fogadására.
* **Protokoll.** Egy nem kötelező paraméter, amely meghatározza a protokoll kérelemhez engedélyezett. Lehetséges értékek a következők HTTPS és a HTTP (`https,http`), azaz az alapértelmezett érték, vagy a HTTPS csak (`https`). Vegye figyelembe, hogy HTTP csak érték nem engedélyezett.
* **Aláírás.** Az aláírás a többi paraméter rész tokenként megadva, és ezután titkosítja a értékekből összeállított. Az aláírás az adott tárolási erőforrásokhoz való hozzáférés engedélyezésére szolgál.

### <a name="parameters-for-a-service-sas-token"></a>A szolgáltatás SAS-token paraméterek
* **Storage-erőforrások.** Tárolási erőforrások, amelynek is adhat hozzáférést egy olyan SAS a következők:
  * Tárolók és blobok
  * Fájlmegosztásokat és fájlokat
  * Üzenetsorok
  * Táblák és táblaentitások tartományát.

### <a name="parameters-for-an-account-sas-token"></a>Egy fiók SAS-token paraméterek
* **Szolgáltatás vagy a szolgáltatások.** Egy fiók SAS is adhat hozzáférést a tárolási szolgáltatások közül legalább egyet. Ha például egy fiók SAS, hogy biztosít hozzáférést a Blobok és fájlok szolgáltatáshoz is létrehozhat. Vagy létrehozhat egy SAS, hogy mind a négy delegáltak hozzáférést szolgáltatás (Blob, üzenetsor, tábla és fájl).
* **Tárolási erőforrástípusok.** Egy fiók SAS egy adott erőforrás helyett a tárolási erőforrások egy vagy több osztályt vonatkozik. Egy fiók SAS való hozzáférés delegálásához hozhat létre:
  * Szolgáltatásiszint-API-k, amelyeket a rendszer meghív, szemben a tárfiók típusú erőforrást. Ilyenek például **Get/Set-szolgáltatás tulajdonságai**, **első szolgáltatási statisztikák**, és **lista tárolók/üzenetsorok és táblák/megosztások**.
  * Tároló-szintű API-k, az úgynevezett az tároló objektumokon az egyes szolgáltatásokhoz: blob-tárolók, üzenetsorok, táblák és fájlmegosztások. Ilyenek például **tároló létrehozása vagy törlése**, **várólista létrehozása/törlése**, **tábla létrehozása vagy törlése**, **létrehozása/törlése megosztás**, és  **Blobok és fájlok és könyvtárak listázása**.
  * Objektum szintű API-k, amelyeket a rendszer meghív, blobok, üzenetsor üzenetei, táblaentitások és fájlok ellen. Például **Put Blob**, **Lekérdezésentitás**, **üzenetek beolvasása**, és **a Create File**.

## <a name="examples-of-sas-uris"></a>Példák a SAS URI azonosítóival

### <a name="service-sas-uri-example"></a>Példa Service SAS URI-t

Íme egy példa a SAS URI-t biztosító olvasási és írási engedélyek blob szolgáltatás. A táblázat az egyes részek megérteni, hogyan hozzájárul a SAS URI felszámolja:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Name (Név) | SAS része | Leírás |
| --- | --- | --- |
| Blob URI |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |A blob-cím. Vegye figyelembe, hogy HTTPS használatát javasoljuk. |
| Storage services verziója |`sv=2015-04-05` |Tárolási szolgáltatások 2012-02-12-es verzió, és később, ez a paraméter azt jelzi, verziót kell használni. |
| Kezdő időpont |`st=2015-04-29T22%3A18%3A26Z` |A megadott UTC időben. Ha azt szeretné, hogy a SAS azonnal érvényes legyen, hagyja ki a kezdési időpontot. |
| Lejárat időpontja |`se=2015-04-30T02%3A23%3A26Z` |A megadott UTC időben. |
| Erőforrás |`sr=b` |Egy blobot az erőforrás. |
| Engedélyek |`sp=rw` |A SAS által adott engedélyek Read (r) tartalmazza, és írás (w). |
| IP-címtartomány |`sip=168.1.5.60-168.1.5.70` |Az IP-címek tartománya, amelyből a rendszer egy kérelmet fogad. |
| Protokoll |`spr=https` |Csak a HTTPS PROTOKOLLT használó kérelmek engedélyezettek. |
| Aláírás |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |A blob való hozzáférés engedélyezésére használja. Az aláírás az egy HMAC-val egy karakterlánc-bejelentkezési és a kulcsot az SHA256 algoritmust a számított, és ezután a Base64-kódolással kódolva. |

### <a name="account-sas-uri-example"></a>Fiók SAS URI-t – példa

Íme egy példa egy olyan fiók, amely ugyanazokat a gyakori paramétereket használja a jogkivonatot a SAS. Ezeket a paramétereket a fentebbiekben leírt, mivel azok nem ebben a témakörben találhatók. Csak a paramétereket, amelyek adott, az alábbi táblázatban ismertetett SAS-fiókjába.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Name (Név) | SAS része | Leírás |
| --- | --- | --- |
| Erőforrás-URI |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |A Blob service-végpont, paraméterekkel lekérdezése (GET-meghívásakor) szolgáltatás tulajdonságai vagy (Ha a készlet nevű) szolgáltatás tulajdonságainak beállítása. |
| Szolgáltatások |`ss=bf` |Az SAS a Blobok és fájlok szolgáltatások vonatkozik. |
| Erőforrástípusok |`srt=s` |A SAS szolgáltatásiszint-műveletére vonatkozik. |
| Engedélyek |`sp=rw` |Az engedélyek hozzáférést olvasási és írási műveletek. |

Tekintve, hogy az engedélyek korlátozva a szolgáltatási szint, a SAS használatával elérhető műveletek a következők **beolvasása a Blob szolgáltatás tulajdonságai** (olvasás) és **állítsa Blob szolgáltatás tulajdonságai** (írás). Azonban egy másik erőforrás URI-t, az SAS-jogkivonatnak felhasználható is való hozzáférés delegálásához **beolvasása a Blob szolgáltatás statisztikáiról** (olvasás).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>A tárolt hozzáférési szabályzattal együtt SAS szabályozása
Közös hozzáférésű jogosultságkód két formáját valamelyikét hajthatja végre:

* **Ad hoc biztonsági Társítások:** Amikor létrehoz egy ad-hoc SAS, a kezdési idő, lejárati időpontot és engedélyeket a SAS minden megadott SAS URI-(vagy hallgatólagos, abban az esetben, ha nincs megadva a kezdési ideje). Az ilyen típusú SAS-fiók SAS-vagy szolgáltatásalapú SAS hozható létre.
* **SAS tárolt hozzáférési szabályzattal együtt:** Egy tárolt hozzáférési szabályzat van definiálva egy erőforrás-tároló – egy blob-tárolóba, table, queue, vagy fájlmegosztás – és egy megkötések kezeléséhez használható, vagy további közös hozzáférésű jogosultságkódot. SAS társítása egy tárolt hozzáférési szabályzatot, az SAS örökli a korlátozásokkal – a kezdési idő, lejárati időpontot és – a tárolt hozzáférési szabályzat definiált engedélyek.

> [!NOTE]
> Jelenleg a fiók SAS-ad-hoc SAS kell lennie. Tárolt hozzáférési szabályzatok még nem támogatottak a fiók SAS.

A különbség a két formáját fontos egyik legfontosabb előny a: visszavont tanúsítványok. Mivel az SAS URI-t egy URL-címet, bárki, amely lekéri az SAS is használható, függetlenül attól, aki eredetileg létrehozták. Ha SAS nyilvánosan közzé van téve, használat szerint, a világ bármely részéről. SAS erőforrásokhoz való hozzáférést biztosít a mindenki számára, aki rendelkezik, amíg négy dolog történik:

1. Az SAS a megadott lejárati idő elérésekor.
2. A SAS által hivatkozott tárolt hozzáférési házirendben megadott lejárati idő elérése (Ha egy tárolt hozzáférési szabályzat hivatkozik, és azt adja meg, hogy egy lejárati ideje). Ez akkor fordulhat elő, mert az időtartam, vagy módosította egy lejárati dátuma a múltban, amely visszavonja a SAS egyik módja a tárolt hozzáférési házirendet.
3. A tárolt hozzáférési szabályzatot a SAS által hivatkozott törlődik, amely visszavonja a SAS egy másik módja. Vegye figyelembe, hogy ha Ön hozza létre újból a tárolt hozzáférési szabályzat pontosan ugyanazzal a névvel, minden meglévő SAS-tokeneket újra érvényes lesz a tárolt hozzáférési házirenddel (feltéve, hogy, amely a SAS lejárati ideje nem ment át) tartozó engedélyek alapján. Ha visszavonja a SAS szándékozik, mindenképpen használjon másik nevet, ha a hozzáférési szabályzat a későbbiekben egy lejárati idővel hozza létre újra.
4. A fiók SAS létrehozásához használt kulcs újragenerálása van. Egy fiók kulcs újragenerálása hatására az összes alkalmazás-összetevők, a kulcs használatával történő hitelesítéséhez mindaddig, amíg a többi érvényes fiókkulcs vagy újonnan újragenerálta a fiókkulcs használhatja a mezők frissítése sikertelen.

> [!IMPORTANT]
> A fiókkulcs aláírásának létrehozására használt társítva egy közös hozzáférésű jogosultságkód URI-t, és a társított tárolt hozzáférési szabályzat (ha van). Ha nincs tárolt hozzáférési szabályzat van megadva, a csak visszavonása a közös hozzáférésű jogosultságkód módja a fiókkulcs módosításához.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>A SAS-ügyfélalkalmazás hitelesítése
Egy ügyfél, aki rendelkezik a SAS használatával engedélyezhetők a SAS egy storage-fiókot, amelynek nem rendelkeznek a fiókkulcsok egy kérelmet. SAS egy kapcsolati karakterláncot tartalmazza, vagy közvetlenül a megfelelő konstruktor vagy a metódus a használt.

### <a name="using-a-sas-in-a-connection-string"></a>Egy kapcsolati karakterláncot a SAS használatával
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>A konstruktor vagy metódus SAS használatával
Számos Azure Storage ügyféloldali kódtár konstruktorok és a metódus túlterheléssel nyújtanak a SAS-paramétert, így engedélyezheti, hogy a szolgáltatás SAS-kérést.

Például itt SAS URI segítségével hozzon létre egy hivatkozást a blokkblobok. Az SAS a csak a kéréshez szükséges hitelesítő adatokat biztosít. A blokkblob hivatkozását írási művelet használja:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>SAS használata esetén ajánlott eljárások
Közös hozzáférésű jogosultságkódok használatakor az alkalmazások kell figyelembe venni két potenciális kockázatokról:

* Ha egy SAS kiszivárgott, használat szerint bárki, aki szerzi be, amely potenciálisan kedvezőtlenül befolyásolhatja a storage-fiókjában.
* Ha a megadott SAS egy ügyfélalkalmazás lejár az alkalmazás nem tudja beolvasni a szolgáltatás egy új SAS, majd az alkalmazás működését is akadályozható meg.

Az alábbi javaslatok a közös hozzáférésű jogosultságkódok használata segíthet a kockázatok csökkentése érdekében:

1. **Mindig HTTPS PROTOKOLLT használnak** létrehozásához vagy egy SAS terjesztése. SAS átadott HTTP-n keresztül, és elfogni, a támadó egy man-in-the-middle támadással-e a SAS olvashatja, és aztán a segítségükkel ugyanúgy, mint az arra jogosult felhasználók rendelkezhet, potenciálisan bizalmas adatok veszélyeztetése vagy adatsérülés a rosszindulatú felhasználó számára lehetővé teszi.
2. **Tárolt hozzáférési szabályzatok hivatkozik, ahol csak lehetséges.** Tárolt hozzáférési szabályzatok lehetővé teszi az engedélyek visszavonása újragenerálni a tárfiókkulcsokat nélkül. Állítsa be a lejárati ezekkel nagyon távoli jövőbe esik (vagy végtelen), és ellenőrizze, távolabb áthelyezése a jövőre rendszeresen frissítjük.
3. **Rövid távú terveink lejárati idővel használja az ad-hoc SAS.** Így még akkor is, ha egy SAS sérült, érvénytelen, így csak rövid ideig. Ez az eljárás különösen fontos, ha Ön nem hivatkozhat egy tárolt hozzáférési szabályzatot. Rövid távú terveink lejárati idővel is korlátozhatja a csak írható egy blobot tölthet fel, a rendelkezésre álló idő korlátozásával adatok mennyisége.
4. **Az SAS, szükség esetén automatikusan megújulnak ügyfele van.** Az ügyfelek meg kell újítaniuk az SAS a lejárat előtt is annak érdekében, hogy az idő újrapróbálkozások engedélyezése, ha a szolgáltatást, amely a SAS nem érhető el. Ha a biztonsági Társítások hivatott azonnali, rövid ideig tartó műveletek, amelyek várhatóan az elévülési időszakon belül befejezni kis számú használható, majd ez lehet szükségtelen, mivel az SAS várhatóan nem kell újítani. Azonban ha ügyfél, amely rendszeresen lehetővé teszi a kérelmek SAS-n keresztül, majd a lejárat lehetőségét kerül play. (Korábban hangsúlyoztuk) van szükség az SAS lehet rövid életű elosztása érdekében a legfontosabb szempont, győződjön meg arról, hogy az ügyfél kér a megújítási korai ügyféladataik elég (fenntartásához sikeres megújítás előtt lejár az SAS miatt).
5. **Legyen óvatos az SAS kezdési ideje.** Ha egy SAS-t a kezdési időpont beállított **most**, majd miatt óra (az aktuális időpont alapján különböző gépek különbségek) döntés, sikertelen lehet figyelni időnként az első néhány perc alatt. Általánosságban elmondható állítsa be a kezdési időpont legalább 15 perccel korábbinak kell. Vagy nem állít be, minden, ami egyszerűbbé fog érvényes azonnal minden esetben a. Ugyanaz, valamint – a lejárati idő általánosan érvényes ne feledje, hogy azt megfigyelheti, akár 15 percet, órát torzulása bármelyik irányba, a kérelmet. A 2012-02-12 előtti REST verziót használó ügyfelei számára az engedélyezett maximális időtartam, amely nem hivatkozik egy tárolt hozzáférési szabályzat SAS 1 óra és szabályzatok megadását mint sikertelen lesz, amely hosszabb távú.
6. **Az erőforrás érhető el az adott legyen.** Biztonsági szempontból ajánlott, hogy adjon egy felhasználónak a minimálisan szükséges jogosultságokkal. Ha a felhasználó csak egyetlen entitás olvasási hozzáférésre van szüksége, adja meg számukra, hogy egyetlen entitás olvasási hozzáférést, és nem olvasási, írási és törlési hozzáférés az összes entitáshoz. Ez is segít, ha SAS sérült, mert a biztonsági Társítások kevesebb energiát a támadó tagoknál, így csökkentheti a sérülés.
7. **Ismerje meg, hogy a fiók felüli használati díjakat, beleértve a SAS használatával végzett díjat kell fizetni.** Írási hozzáférés egy blobba ad meg, ha egy felhasználó dönthet úgy, hogy feltölt egy 200 GB-os blobot is. Feljogosított őket olvasási hozzáféréssel is, ha azok dönthet úgy is 10 alkalommal letöltheti 2 TB-os kellene a kimenő adatforgalmi költségek az Ön számára. Adja meg újra, a korlátozott engedélyekkel segítségével mérsékelhetik a rosszindulatú felhasználók a lehetséges műveletek. Rövid élettartamú SAS használatával csökkentheti a fenyegetés (de kell szem előtt tartva torzulása a befejezésének órája).
8. **SAS használatával írt adatok érvényesítéséhez.** Amikor egy ügyfélalkalmazás a tárfiókba írja az adatokat, vegye figyelembe, hogy az az adatokkal kapcsolatos problémák lehetnek. Ha az alkalmazás adatokat érvényesítve vagy előtt, készen áll a használatra jogosult igényel, az ellenőrzés után írja az adatokat, és mielőtt az alkalmazás által kell végezni. Ez az eljárás sérült vagy rosszindulatú bájtnyi adat íródik a fiókjához, a felhasználó, aki megfelelően beszerzett SAS vagy egy felhasználó ártó szándékkal használja fel a kiszivárgott SAS szemben is védi.
9. **Nem mindig használja az SAS.** Néha egy adott művelet tárfiókra járó kockázatok nyomósabbak SAS előnyeit. Az ilyen műveletek hozzon létre egy középső rétegű szolgáltatás, amely a storage-fiókjába ír üzleti elvégzése után érvényesítést, hitelesítési és naplózási szabály. Néha sokkal egyszerűbb, egyéb módon-hozzáférés kezelése. Például, ha meg szeretné olvashatóvá összes BLOB-tárolóban nyilvánosan, akkor is használhatja a tároló nyilvános, ahelyett, hogy hozzáférést biztosító SAS minden ügyfél számára.
10. **A Storage Analytics segítségével figyelheti az alkalmazást.** Naplózás és mérőszámok segítségével bármely megnövekedett leállás miatt fellépő hitelesítési hibákkal figyelje meg, a SAS-szolgáltató szolgáltatásban vagy egy tárolt hozzáférési szabályzat nem szándékos eltávolítását. Tekintse meg a [Azure Storage csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) további információt.

## <a name="sas-examples"></a>SAS-példák
Az alábbiakban néhány példa a közös hozzáférésű jogosultságkódok, a fiók SAS mindkét típusú, és a szolgáltatásalapú SAS-t.

Ezekben a példákban a C# futtatásához kell hivatkoznia a projektben a következő NuGet-csomagok:

* [Az Azure Storage ügyféloldali kódtára a .NET-hez](http://www.nuget.org/packages/WindowsAzure.Storage), verzió 6.x vagy újabb (használja a fiók SAS).
* [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

További példák azt mutatják be, hogyan hozhat létre és tesztelhet egy SAS [Azure Storage-Kódminták](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Példa: Létrehozhat és használhat egy fiók SAS
Az alábbi példakód létrehoz egy fiókot, amely a Blobok és fájlok szolgáltatások esetében érvényes, és lehetővé teszi az ügyfél engedélyek olvasási, írási és list engedélyeket az eléréséhez a szolgáltatói API-k SAS. A fiók SAS korlátozza a protokoll HTTPS, ezért a kérelem kell tenni a HTTPS.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

A Blob szolgáltatás szolgáltatásiszint-API-k elérésére a fiók SAS használatával hozhatnak létre egy SAS- és a Blob storage-végpont használatával a tárfiók Blob objektumot.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Példa: A tárolt hozzáférési szabályzat létrehozása
A következő kódot egy tárolt hozzáférési szabályzatot egy tárolót hoz létre. A hozzáférési szabályzat segítségével a szolgáltatásalapú SAS korlátozások határozhatók meg a tárolóhoz és annak blobjaihoz.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Példa: Egy tároló szolgáltatásalapú SAS létrehozása
Az alábbi kód létrehoz egy SAS egy tárolón. Ha egy meglévő tárolt hozzáférési szabályzat neve van megadva, az adott házirendnek SAS társítva. Ha nincs tárolt hozzáférési szabályzat van megadva, majd a kódot a tároló ad hoc SAS hoz létre.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad-hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Példa: A blob SAS szolgáltatás létrehozása
Az alábbi kód létrehoz egy SAS-egy BLOB. Ha egy meglévő tárolt hozzáférési szabályzat neve van megadva, az adott házirendnek SAS társítva. Ha nincs tárolt hozzáférési szabályzat van megadva, majd a kódot a blob egy ad-hoc SAS hoz létre.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Összegzés
Közös hozzáférésű jogosultságkódok hasznosak, amelyeknek nem indokolt a fiókkulcs-ügyfelek, amelyek biztosítják az korlátozott engedélyekkel a tárfiókhoz. Mint ilyen a biztonsági modell az Azure Storage használata alkalmazások fontos részei legyenek. Ha követi az itt felsorolt ajánlott eljárásokat, a SAS használatával erőforrásokhoz való hozzáférés nagyobb rugalmasságot biztosítanak a tárfiókban található az alkalmazás biztonságának veszélyeztetése nélkül.

## <a name="next-steps"></a>További lépések
* [A közös hozzáférésű Jogosultságkódot, 2. rész: Hozzon létre, és használhatja az SAS-Blob-tárolóval](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Tárolók és blobok névtelen olvasási hozzáférésének kezelése](../blobs/storage-manage-access-to-resources.md)
* [Hozzáférés delegálása közös hozzáférésű jogosultságkód használatával](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Tábla és üzenetsor SAS bemutatása](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
