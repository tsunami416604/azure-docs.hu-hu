---
title: Megosztott hozzáférésű jogosultságkódokat (SAS) az Azure Storage használata |} Microsoft Docs
description: Ismerkedjen meg az Azure Storage-erőforrások, például a BLOB, üzenetsorok, táblák és fájlok hozzáférést biztosíthat a közös hozzáférésű jogosultságkód (SAS) segítségével.
services: storage
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: 46fd99d7-36b3-4283-81e3-f214b29f1152
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2017
ms.author: cshoe
ms.openlocfilehash: d3f8b3261f9e2e86dbcaa41b92111545abeffe54
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="using-shared-access-signatures-sas"></a>Közös hozzáférésű jogosultságkód (SAS) használatával

A közös hozzáférésű jogosultságkód (SAS) biztosít jogokat a tárfiókban lévő objektumokhoz való korlátozott hozzáférésre más ügyfelek számára anélkül, hogy a fiókkulcs. Ez a cikk azt a SAS-modell áttekintése, SAS ajánlott eljárásokat, és tekintse meg néhány példa.

Kód itt bemutatott ruházzák SAS használatával tekintse meg a [Ismerkedés az Azure Blob Storage a .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) és más elérhető mintákat a [Azure mintakódok](https://azure.microsoft.com/documentation/samples/?service=storage) könyvtár. A mintaalkalmazások letöltése és futtathatja őket, vagy keresse meg a kódot a Githubon.

## <a name="what-is-a-shared-access-signature"></a>Mi az a közös hozzáférésű jogosultságkód?
A közös hozzáférésű jogosultságkód a tárfiókban lévő erőforrások delegált hozzáférést biztosít. SAS-kód, az ügyfelek hozzáférést biztosíthat erőforrásokhoz a tárfiókban lévő megosztása a kulcsait nélkül. Ez a lényege a közös hozzáférésű jogosultságkód alkalmazásokban való használatának – az SAS a fiókkulcsok veszélyeztetése nélkül teszi lehetővé a tárolási erőforrások megosztását.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

SAS-kód lehetővé teszi részletes szabályozását a típusú rendelkező az SA-kat, beleértve az ügyfelek számára biztosítson hozzáférést:

* A keresztül, amely a SAS érvényességi időtartama, beleértve a kezdési idő és a lejárati időpont.
* A biztonsági Társítások engedélyekre. Például blob SAS-kód előfordulhat, hogy adjon olvasási és írási engedéllyel, hogy a blob, de nem törli az engedélyeket.
* Nem kötelező IP-cím vagy az IP-címek tartománya, amelyből Azure Storage elfogadja az SA-kat. Például megadhatja egy adott IP-címek a szervezethez tartozó.
* A protokoll, amelyben Azure Storage fogja fogadni az SA-kat. Ez nem kötelező paraméter használatával korlátozza a hozzáférést az ügyfelek HTTPS-kapcsolaton keresztül.

## <a name="when-should-you-use-a-shared-access-signature"></a>Mikor kell használni a közös hozzáférésű jogosultságkód?
SAS-kód is használhatja, ha lehetővé szeretné tenni a tárfiókban lévő erőforrásokhoz való hozzáférés minden olyan ügyfél, amelyek nem rendelkeznek a tárfiók tárelérési kulcsok. A tárfiók egy elsődleges és másodlagos elérési kulcsot, mindkettő felügyeleti hozzáférési jogot a fióknak, és hozzá tartozó összes erőforrást tartalmaz. Ezek a kulcsok az ilyen megnyitja a lehetőségét, amely rosszindulatú vagy gondatlan használja a fiókját. Közös hozzáférésű jogosultságkód adjon meg egy biztonságos megoldás, amely engedélyezi az ügyfeleknek olvasási, írási és törlési az Ön kifejezetten biztosított engedélyek alapján, és fiókkulcs nélkül a tárfiókban lévő adatokat.

Egy gyakori forgatókönyv, ahol a biztonsági Társítások akkor hasznos, egy olyan szolgáltatás, ahol a felhasználók olvashatják és saját adataikat a tárolási fiók írásához. Forgatókönyv esetében, ahol a tárfiók tárolja a felhasználói adatok a rendszer két jellemző tervezési mintáról olvashat:

1. Ügyfelek töltse fel, és töltse le adatokat, amely végrehajtja a hitelesítési előtér-proxy szolgáltatáson keresztül. Az előtér-proxyszolgáltatás azzal az előnnyel jár, hogy az üzleti szabályok érvényesítési, de nagy mennyiségű adatok vagy tranzakciók nagy az igény szerinti egyeztetéshez is méretezhető szolgáltatás létrehozása lehet költséges vagy nehézkes.

  ![A forgatókönyv diagramja: előtér-proxyszolgáltatás](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Egy egyszerűsített szolgáltatás hitelesíti az ügyfelet, igény szerint, és akkor hoz létre a SAS-kód. Miután az ügyfél akkor kapja meg az SA-kat, közvetlenül a alatt engedélyez az SA-kat és a SAS határozzák engedélyeivel tárfiók erőforrásainak eléréséhez. A SAS azzal csökkenti az összes adat az előtér-proxy szolgáltatás útválasztási van szükség.

  ![A forgatókönyv diagramja: SAS-szolgáltató szolgáltatás](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Számos valós szolgáltatás használhat egy hibrid két megoldás. Például bizonyos adatok feldolgozása lehet, és az előtér-proxyn keresztül történő érvényesítése közben egyéb adatok mentése és/vagy olvasása közvetlenül a SAS használatával.

Ezen felül kell hitelesítenie a forrásobjektumot, a másolási műveletek bizonyos esetekben a SAS használatával:

* Egy blobot egy másik, amely egy másik tárfiókban található blobba másolásakor SAS kell használnia a forrás blob hitelesítéséhez. A SAS segítségével opcionálisan hitelesítéséhez, valamint a cél blob.
* Egy fájl másik fájlba, amely egy másik tárfiókban található másolásakor a SAS-kód hitelesítéséhez a forrásfájl kell használnia. SAS segítségével hitelesítéséhez, valamint a célfájl nem kötelező.
* Egy blobot egy fájlba, vagy egy fájlt egy blobba másolásakor kell használnia egy SAS hitelesítenie a forrásobjektumot, még akkor is, ha a forrás és cél-objektumai találhatók a tárfiókon belül.

## <a name="types-of-shared-access-signatures"></a>Közös hozzáférésű jogosultságkód típusai
Közös hozzáférésű jogosultságkód két típusa hozható létre:

* **Service SAS.** A szolgáltatásalapú SAS csak egy társzolgáltatás (a Blob, a Queue, a Table vagy a File szolgáltatás) egy erőforrásához biztosít hozzáférést. Lásd: [hozhat létre, egy szolgáltatás SAS](https://msdn.microsoft.com/library/dn140255.aspx) és [szolgáltatás SAS példák](https://msdn.microsoft.com/library/dn140256.aspx) részletes információ a SAS-jogkivonat létrehozásával.
* **SAS-fiók.** A fiók SAS delegáltak erőforrások elérését egy vagy több, a tárolási szolgáltatások. A biztonsági Társítások szolgáltatáson keresztül elérhető műveleteket is elérhetők SAS fiók használatával. Emellett SAS fiókkal is adhat hozzáférést egy adott szolgáltatáshoz, például a vonatkozó műveletek **Get vagy Set szolgáltatástulajdonságok** és **első szolgáltatás-statisztikák**. A blobtárolók, táblák, üzenetsorok és fájlmegosztások olvasási, írási és törlési műveleteihez is hozzáférést biztosíthat, amelyeket a szolgáltatásalapú SAS nem engedélyez. Lásd: [hozhat létre egy fiókot SAS](https://msdn.microsoft.com/library/mt584140.aspx) hozhat létre, a fiók SAS-jogkivonat részletesebb információt.

## <a name="how-a-shared-access-signature-works"></a>A közös hozzáférésű jogosultságkód működése
A közös hozzáférésű jogosultságkód egy vagy több tároló-erőforrások mutat, és a lekérdezési paraméterek különleges készletét tartalmazó jogkivonatok tartalmazó aláírt URI. A jogkivonat azt jelzi, hogyan az erőforrásokat érhetik el az ügyfelet. Az aláírás lekérdezés-paraméterek egyike a SAS-paraméterek értékekből összeállított és a fiók kulccsal aláírva. Az aláírás hitelesítéséhez a biztonsági Társítások Azure Storage használják.

Példa SAS URI, megjeleníti az erőforrás URI azonosítója és a SAS-jogkivonat:

![A SAS URI-összetevők](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

A SAS-jogkivonat: hoz létre a karakterlánc a *ügyfél* ügyféloldali (lásd a [SAS példák](#sas-examples) kódpéldák szakaszát). Egy SAS-jogkivonatot hoz létre, és a storage ügyféloldali kódtár, például a nyomkövetés nem Azure Storage bármilyen módon. SAS-tokenje korlátlan számú az ügyféloldalon is létrehozhat.

Ha az ügyfél egy SAS URI-t Azure Storage megad egy kérelem részeként, a szolgáltatás ellenőrzi a SAS-paraméterek és győződjön meg arról, hogy a kérés hitelesítéséhez érvényes aláírást. Ha a szolgáltatás ellenőrzi, hogy az aláírás érvényes, akkor a kérelem hitelesítése. Ellenkező esetben elutasította a kérést az hibakód 403 (tiltott).

## <a name="shared-access-signature-parameters"></a>Megosztott hozzáférési aláírást paraméterek
A fiók SAS és a SAS-tokenje néhány általános paramétereket tartalmaz, és is igénybe vehet néhány különböző paraméterek.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>A paraméterek közös SAS-fiókhoz és a SAS-tokenje
* **API-verzió** egy nem kötelező paraméter, amely meghatározza a tárolás szolgáltatás verziót kell használni a kérelem végrehajtása.
* **Verziójú** egyik kötelező paraméter, amely meghatározza a tároló verziója használandó hitelesíteni a kérelmet.
* **Kezdési időpontja.** Ez az az idő, ahol a biztonsági Társítások hatályba lép. A közös hozzáférésű jogosultságkód kezdési időpontja nem kötelező megadni. Ha a kezdő időpont nincs megadva, a biztonsági Társítások azonnal hatékony. A kezdési időpontot kell megadni az UTC (egyezményes világidő), egy különös UTC jelzéssel ("Z"), például `1994-11-05T13:15:30Z`.
* **Lejárati idő.** Ez az az idő, amely után a biztonsági Társítások már nem érvényes. A bevált gyakorlat része, hogy adja meg a lejárat időpontjának tartozó SAS korlátozására, vagy rendelje hozzá azt a tárolt házirend. A lejárati időpont kell megadni az UTC (egyezményes világidő), egy különös UTC jelzéssel ("Z"), például `1994-11-05T13:15:30Z` (további információk alatt).
* **Engedélyek.** Az engedélyeket a biztonsági Társítások megadott azt jelzi, hogy az ügyfél a tárolási erőforrások a SAS használatával szemben hajthat végre műveleteket. Elérhető engedélyek a SAS fiók és a szolgáltatásalapú SAS térnek el egymástól.
* **IP.** Egy nem kötelező paraméter, amely megadja egy IP-címet vagy egy Azure-on kívüli az IP-címek (című szakaszában talál [útválasztás munkamenet-konfiguráció állapota](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) az Express Route), amelyből kérelmek fogadására.
* **Protokoll.** Egy nem kötelező paraméter, amely meghatározza a protokoll érkező kérelmek végrehajtására megengedett. Lehetséges értékei a HTTPS és a HTTP (`https,http`), amely a az alapértelmezett érték, vagy HTTPS csak (`https`). Vegye figyelembe, hogy HTTP csak nem megengedett értéket.
* **Aláírás.** Az aláírás a többi paraméter megadott rész jogkivonatot, és titkosítja, majd értékekből összeállított. A biztonsági Társítások hitelesítésére szolgál.

### <a name="parameters-for-a-service-sas-token"></a>A szolgáltatás SAS-jogkivonat paraméterek
* **Tároló-erőforrás.** Tároló-erőforrások, amelynek is adhat hozzáférést egy olyan biztonsági Társítások tartalmazzák:
  * Tárolók és blobok
  * A fájlmegosztások és fájlok
  * Üzenetsorok
  * Táblák és a táblaentitásokat tartományait.

### <a name="parameters-for-an-account-sas-token"></a>Egy fiók SAS-jogkivonat paramétereinek
* **Szolgáltatás vagy a szolgáltatások.** Egy fiók SAS is adhat hozzáférést a tárolási szolgáltatások közül. Például, hogy a delegáltak hozzáférést a Blob és a fájl szolgáltatás fiók SAS is létrehozhat. Vagy, hogy mind a négy delegáltak hozzáférést szolgálja (Blob, várólista, a táblának és fájl) Készletével hozhat létre.
* **Erőforrás-típusú tárolókat.** Egy fiók SAS egy vagy több osztályt a tárolási erőforrások ahelyett, hogy egy adott erőforrás vonatkozik. Egy fiók való hozzáférés delegálásához SAS hozhat létre:
  * Szolgáltatás szintű API-k, a tárolási fiók erőforrások elleni néven. Példák **Get vagy Set szolgáltatástulajdonságok**, **első szolgáltatás-statisztikák**, és **tárolók/várólisták/táblák, megosztások listája**.
  * Tároló szintű API-k, a tároló objektumokon az egyes szolgáltatásokhoz néven: blob-tároló, a várólisták, a táblák és fájlmegosztások. Példák **létrehozása vagy törlése tároló**, **létrehozása vagy törlése-várólista**, **tábla létrehozása vagy törlése**, **létrehozása vagy törlése megosztás**, és **lista Blobok/fájlok és könyvtárak**.
  * Objektum szintű API-k, blobok, az üzenetsor-üzeneteket, a táblaentitásokat és a fájlok elleni néven. Például **Put Blob**, **lekérdezés entitás**, **üzeneteket beolvasni**, és **fájl létrehozása**.

## <a name="examples-of-sas-uris"></a>Példák SAS URI-azonosítók

### <a name="service-sas-uri-example"></a>Szolgáltatás SAS URI-példa

Íme egy példa a SAS URI-t, amely olvasási és írási engedéllyel a blob szolgáltatás. A tábla megértése, hogyan hozzájárul a SAS URI részét képező összes megszakad:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Name (Név) | SAS részére | Leírás |
| --- | --- | --- |
| Blob URI |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |A blob címe. Vegye figyelembe, hogy a HTTPS-kapcsolaton keresztül erősen ajánlott. |
| Storage services, verziószám: |`sv=2015-04-05` |A tárolási szolgáltatások 2012-02-12-es és újabb, ez a paraméter azt jelzi, melyik verziót kell használni. |
| Kezdés időpontja |`st=2015-04-29T22%3A18%3A26Z` |UTC idő szerint megadva. Ha azt szeretné, hogy a biztonsági Társítások azonnal érvényes legyen, hagyja ki a kezdési időpontot. |
| Lejárat időpontja |`se=2015-04-30T02%3A23%3A26Z` |UTC idő szerint megadva. |
| Erőforrás |`sr=b` |Az erőforrás egy blob. |
| Engedélyek |`sp=rw` |A biztonsági Társítások engedélyekre Read (r) és írjunk (w). |
| IP-címtartomány |`sip=168.1.5.60-168.1.5.70` |Az az IP-címek tartománya, amelyből a kérelem fogja elfogadni. |
| Protokoll |`spr=https` |Csak a HTTPS-kapcsolaton keresztül kérelmek engedélyezettek. |
| Aláírás |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |A blob való hozzáférés hitelesítéséhez használt. Az aláírás egy HMAC egy karakterlánc-bejelentkezési és az SHA-256 algoritmussal kulcs felett, és ezután a Base64 kódolás használatával kódolt. |

### <a name="account-sas-uri-example"></a>Fiók SAS URI-példa

Íme egy példa egy olyan fiók, amely a gyakori paramétereket használ a jogkivonat SAS. Mivel ezek a paraméterek fent ismerteti, hogy a dokumentum nem ismerteti. Csak a paraméterek, amelyek adott, az alábbi táblázat ismerteti a SAS-fiókhoz.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Name (Név) | SAS részére | Leírás |
| --- | --- | --- |
| Erőforrás URI azonosítója |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |A Blob-szolgáltatásvégpont, a szolgáltatás Tulajdonságok (Ha hívása GET) vagy (készlettel meghívásakor) szolgáltatási tulajdonságainak beállítása paraméterekkel. |
| Szolgáltatások |`ss=bf` |A Blob és a fájl szolgáltatásra vonatkozik, a biztonsági Társítások |
| Típusú erőforrások |`srt=s` |A biztonsági Társítások szolgáltatásszintű műveletekhez vonatkozik. |
| Engedélyek |`sp=rw` |Az engedélyek hozzáférést olvasási és írási műveletet. |

Fényében, hogy a szolgáltatási szint engedélyek korlátozódnak, vannak-e elérhető műveletek a biztonsági Társítások **Blob szolgáltatástulajdonságok** (olvasás) és **Blob szolgáltatás tulajdonságainak beállítása** (írás). Azonban egy másik erőforrás URI, azonos SAS-jogkivonat is használható való hozzáférés delegálására **lekérése Blob szolgáltatás statisztikák** (olvasás).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>A biztonsági Társítások tárolt hozzáférési házirenddel vezérlése
A közös hozzáférésű jogosultságkód két űrlap valamelyikét hajthatja végre:

* **Ad hoc biztonsági Társítások:** hoz létre az ad hoc SAS-a kezdési ideje, a lejárat időpontjának, és a SAS engedélyeinek összes SAS URI azonosítójában megadott (vagy vélelmezett, abban az esetben, ha nincs megadva kezdő időpont). Az ilyen típusú SAS SAS fiók vagy a szolgáltatásalapú SAS hozhatók létre.
* **SAS tárolt hozzáférési házirenddel:** egy tárolt hozzáférési házirend egy erőforrás tároló – egy blob tároló van definiálva, tábla, várólista, vagy fájlmegosztásba--és legalább egy közös hozzáférésű jogosultságkód megkötéseit kezelésére használható. SAS-kód társítása a tárolt házirend, a SAS örökli a megkötések – a kezdő időpont lejárati idejének és engedélyek – a tárolt házirend.

> [!NOTE]
> Egy fiók SAS jelenleg egy ad hoc biztonsági Társítások kell lennie. Hozzáférési házirendek még nem támogatottak fiók SAS tárolja.

A különbség a két űrlap fontos egyik-forgatókönyvben: visszavont tanúsítványok. Mivel a SAS URI-t egy URL-címet, bárki beolvassa a biztonsági Társítások használható, függetlenül attól, aki eredetileg létrehozott. Ha SAS-kód nyilvánosságra, bárki a világ használhatná. SAS-kód ad hozzáférést az erőforrásokhoz való bárki csak négy dolog történik, amelyek rendelkeznek:

1. A lejárat időpontjának a SAS megadott elérésekor.
2. A lejárat időpontjának a tárolt hozzáférési házirendet a biztonsági Társítások által hivatkozott meg (Ha a tárolt házirend hivatkozik, és adja meg a lejárat időpontjának) elérésekor. Ez akkor fordulhat elő, az időtartam, vagy mert módosította a tárolt hozzáférési házirendet egy lejárati dátuma a múltban, amely az egyik módja a biztonsági Társítások visszavonni.
3. A tárolt házirend SAS által hivatkozott törölve van, amely másik módja is visszavonja a biztonsági Társítások. Vegye figyelembe, hogy a tárolt házirend pontosan ugyanazzal a névvel hozza létre, ha minden meglévő SAS-tokenje újra lesz érvényes (feltéve, hogy, hogy a lejárati idő a biztonsági Társítások nem ment) tárolt hozzáférési házirendhez társított engedélyeinek megfelelően. Ha visszavonja a biztonsági Társítások szándékos volt, ügyeljen arra, hogy más nevet használjon, ha a hozzáférési házirendben a jövőben egy lejárati idővel hozza létre újra.
4. A biztonsági Társítások létrehozásához használt fiók kulcs újragenerálják. Egy fiók kulcs újragenerálása hatására az összes alkalmazás-összetevő kulccsal történő hitelesítéshez, amíg a többi érvényes fiókkulcs vagy az újonnan újragenerált fiókkulcs használhatja a mezők frissítése sikertelen.

> [!IMPORTANT]
> A közös hozzáférésű jogosultságkód URI társított aláírásának létrehozására használt fiók a kulccsal, és a társított tárolja hozzáférési házirend (ha van ilyen). Ha nincs tárolt házirend van megadva, csak visszavonni egy közös hozzáférésű jogosultságkódot, módosíthatja a fiókkulcsot.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>A SAS-kód az ügyfélalkalmazás hitelesítése
Ügyfél, aki rendelkezik egy SAS a SAS használatával hitelesíteni a kérelmet egy tárfiókot, amelynek nem rendelkeznek a kulcsait. SAS-kód szerepel a kapcsolati karakterláncot, vagy közvetlenül a megfelelő konstruktort vagy metódust használt.

### <a name="using-a-sas-in-a-connection-string"></a>A kapcsolati karakterláncban a SAS használatával
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Egy konstruktort vagy metódust a SAS használatával
Több Azure Storage ügyfél könyvtár konstruktorok és metódus túlterhelések kínál a SAS-paramétert, az, hogy a SAS-kód a szolgáltatásnak küldött kérelemben hitelesítheti.

Például itt SAS URI létrehozásához használt blokkblobba hivatkozást. Az SA-kat biztosít a csak a kérelemhez szükséges hitelesítő adatokat. A blokk blobhivatkozást írási művelet használja:

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

## <a name="best-practices-when-using-sas"></a>Gyakorlati tanácsok SAS használatával
Közös hozzáférésű jogosultságkód az alkalmazásokban való használatakor kell figyelembe vennie két lehetséges kockázatokról:

* Ha SAS-kód kiszivárgott, használat bárki, aki szerzi be, ami kedvezőtlenül befolyásolhatja a potenciálisan a tárfiók.
* Ha a megadott SAS-kód ügyfélalkalmazás lejár az alkalmazás nem tudja új SAS-kód beolvasása a szolgáltatástól, majd az alkalmazás működését is akadályozható meg.

A következő javaslatok a közös hozzáférésű jogosultságkód segítségével a kockázatok csökkentése érdekében:

1. **Mindig HTTPS PROTOKOLLT használnak** létrehozásához, vagy egy SAS terjesztése. Ha SAS-kód átadott HTTP Protokollon keresztül, és elfogta, egy támadó a-átjárójának támadás elolvashassák az SA-kat, és majd a használatával ugyanúgy, mint az arra jogosult felhasználók rendelkezhetnek, potenciálisan bizalmas adatok veszélyeztetése, vagy lehetővé teszi a rosszindulatú felhasználó adatsérülés a.
2. **Hivatkozás tárolt hozzáférési házirendeket, ahol csak lehetséges.** Tárolt hozzáférési házirendek ad lehetőséget anélkül, hogy újragenerálni a tárfiókkulcsokat visszavonni az engedélyeket. A lejárati be ezeket a jövőben nagyon sokkal (vagy végtelen), és győződjön meg arról nagyobb helyezze át a jövőben rendszeresen frissíteni.
3. **Az ad hoc biztonsági Társítások near-term lejárati időpontjait használja.** Ily módon még akkor is, ha SAS-kód sérült, érvénytelen, így csak rövid ideig. Ez az eljárás különösen fontos, ha a tárolt házirend nem hivatkozhatnak. NEAR-term lejárati időpontjait is korlátozza a csak írható blob töltse fel azt a rendelkezésre álló időt korlátozásával adatmennyiséget.
4. **Ügyfelek automatikusan megújíthatja a SAS szükség van.** Ügyfelek kell megújítani a lejárat előtt SAS ahhoz, hogy lehetővé tegyék az újrapróbálkozások ideje, ha az SA-kat biztosít a szolgáltatás nem érhető el. Ha a biztonsági Társítások arra szolgál, hogy néhány azonnali, rövid élettartamú műveletek belül a lejárati idővel várhatóan használható, majd ennek az lehet szükségtelen, mivel a biztonsági Társítások nem várt kell újítani. Azonban ha ügyfél, amely rendszeresen lehetővé teszi a kérelmek SAS segítségével, majd lejárati lehetőségét előre szerepet. A legfontosabb szempont, hogy a SAS-t kell rövid élettartamú szükség elosztása érdekében (korábban is hangsúlyoztuk) Győződjön meg arról, hogy az ügyfél kérelmezi a megújítási korai szükségességének elég (az SA-kat sikeres megújítási előtt lejáró miatt megszűnésének megelőzése érdekében).
5. **Ügyeljen arra, SAS kezdési időponttal.** Ha a kezdési időpontot állít egy SAS-t a **most**, majd miatt óra (aktuális ideje alapján különböző gépek különbségeit) döntés, sikertelen lehet figyelni időnként az első kevés perc. Általánosságban elmondható állítsa be a kezdő időpontja múltbeli legalább 15 perc lehet. Vagy, nem minden, ami megkönnyítő azonnal minden esetekben érvényes. Ugyanez a lejárati idő, valamint – általában érvényes ne feledje, hogy jelenhet meg 15 percig óra eltérésére kérelmet az mindkét irányban. Az ügyfelek előtt 2012-02-12 REST verziójával SAS-kód, amely nem hivatkozik a tárolt házirend engedélyezett maximális időtartam 1 óra, de megadó hosszabb távú sikertelen lesz, mint a szabályzatoknak.
6. **Az erőforrás érhető el az adott legyen.** Biztonsági szempontból ajánlott arra, hogy ellássa a felhasználó a minimálisan szükséges jogosultságokkal. Ha a felhasználó csak egyetlen entitáshoz olvasási engedéllyel kell rendelkeznie, adja meg számukra, hogy egyetlen entitás olvasási hozzáféréssel, és nem olvasási, írási és törlési hozzáférés entitásokhoz. Azt is lehetővé teszi a sérülés csökkentheti, ha SAS-kód sérült, mert a biztonsági Társítások kevesebb energiát egy támadó a tagoknál.
7. **Ismerje meg, hogy a fiók lesz terhelve bármely használatra, beleértve, amely a SAS használatával történik.** Ha megad egy blob írási hozzáféréssel, a felhasználó dönthet 200 GB-os blob feltöltése. Feljogosított őket olvasási hozzáféréssel is, ha lehetséges, hogy választ 10-szer, töltse le az ezzel járó 2 TB, a kimenő forgalom költségek. Ebben az esetben adja meg a rosszindulatú felhasználók a potenciális műveletek csökkentése érdekében korlátozott engedélyekkel. Rövid élettartamú SAS segítségével csökkentheti a fenyegetést (de vegye figyelembe az óra eltérésére a befejezési).
8. **SAS használatával írt adatok ellenőrzése.** Amikor egy ügyfél-alkalmazás írja az adatokat a tárfiók, tartsa szem előtt, hogy az adatok problémák lehetnek. Ha az alkalmazás adatokat érvényesítve vagy engedélye ahhoz, hogy készen áll a használatra, végre kell hajtania az ellenőrzés az adatok írása után, és az alkalmazás használata előtt. Ez az eljárás sérült vagy rosszindulatú adat íródik a fiókjához, a felhasználó, aki megfelelően beszerzett SAS, vagy a felhasználó a kiszivárgott SAS kihasználva is véd.
9. **SAS mindig ne használjon.** Egyes esetekben a tárfiók egy adott művelethez kapcsolódó kockázatokat járó SAS előnyeit. Az ilyen műveleteket, írja az üzleti végrehajtása után a tárfiókhoz középső rétegbeli szolgáltatás létrehozása érvényesítési, hitelesítési és naplózási szabály. Néha sokkal egyszerűbb, egyéb módon kezelésére. Például, ha azt szeretné, hogy összes BLOB a tárolóban lévő nyilvánosan olvasható, hogy a tároló nyilvános, ahelyett, hogy hozzáférést biztosító Aláírást minden ügyfél számára.
10. **Storage Analytics segítségével figyelheti az alkalmazást.** Naplózási és metrikák használatával bármely csúcs az igények kimaradás miatt fellépő hitelesítési hibákkal láthatja a SAS-szolgáltató szolgáltatás, vagy egy tárolt házirend véletlen eltávolítását. Tekintse meg a [Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) további információt.

## <a name="sas-examples"></a>SAS-példák
Az alábbiakban néhány példa a megosztott hozzáférési aláírásokkal, SAS fiók mindkét típusú, és a szolgáltatásalapú SAS.

Ezekben a példákban a C# futtatásához a következő NuGet-csomagok a projekt hivatkoznia kell:

* [Az Azure Storage ügyféloldali kódtára a .NET](http://www.nuget.org/packages/WindowsAzure.Storage), verziója 6.x vagy újabb (használandó fiók SAS).
* [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

További példák, amelyek bemutatják, hogyan és az SAS-kód [mintakódok Azure tárolási](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Példa: Létrehozása és SAS fiók használatára
Az alábbi példakód létrehoz egy fiókot, amely érvényes a Blob és a fájl szolgáltatáshoz, és az ügyfél biztosítja az olvasási engedélyek, írási és listázási engedély szolgáltatásiszint-API-k elérésére SAS. A fiók SAS korlátozza a protokoll HTTPS-en, a kérelem kell tenni a HTTPS.

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

A Blob szolgáltatás szolgáltatásiszint-API-k elérésére SAS fiók használatához szeretne létrehozni egy Blob objektumot a tárfiók SAS és a Blob storage endpoint használatával.

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

### <a name="example-create-a-stored-access-policy"></a>Példa: Tárolt hozzáférési házirend létrehozása
Az alábbi kód létrehoz egy tárolt hozzáférési házirendet tárolóba. A hozzáférési házirend segítségével korlátozásokat a szolgáltatásalapú SAS adja meg a tároló, vagy a benne található blobokat.

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

### <a name="example-create-a-service-sas-on-a-container"></a>Példa: A szolgáltatásalapú SAS létrehozása a tárolóba
Az alábbi kód létrehoz egy SAS-tárolóba. Ha egy meglévő tárolt házirend neve van megadva, az adott házirendnek SAS társítva. Ha a nem tárolt házirend valósul meg, majd a kódot a tároló az ad hoc biztonsági Társítások hoz létre.

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

### <a name="example-create-a-service-sas-on-a-blob"></a>Példa: A szolgáltatásalapú SAS létrehozása a blob
Az alábbi kód létrehoz egy SAS-a blob. Ha egy meglévő tárolt házirend neve van megadva, az adott házirendnek SAS társítva. Ha a nem tárolt házirend valósul meg, majd a kódot a blob egy ad hoc biztonsági Társítások hoz létre.

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
Korlátozott engedélyekkel a tárfiókhoz biztosít az ügyfelek nem rendelkezhet a fiókkulcs közös hozzáférésű jogosultságkód hasznosak. Ilyen a biztonsági modellt az Azure Storage használó alkalmazások fontos részei. Ha az itt felsorolt ajánlott eljárást követi, SAS segítségével erőforrásokhoz való hozzáférés nagyobb rugalmasságot biztosítanak a tárfiókban lévő az alkalmazás a biztonság csökkenése nélkül.

## <a name="next-steps"></a>További lépések
* [Közös hozzáférésű Jogosultságkód, 2. rész: Létrehozása és SAS-kód használata a Blob-tároló](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Tárolók és blobok névtelen olvasási hozzáférés kezelése](../blobs/storage-manage-access-to-resources.md)
* [Hozzáférés delegálása közös hozzáférésű jogosultságkód használatával](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Tábla- és várólista SAS bemutatása](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
