---
title: Az Azure Storage-Blobok nem módosítható storage |} A Microsoft Docs
description: Az Azure Storage kínál FÉREG (egyszer írható, olvassa el számos) támogatása (objektum) a Blob storage, amely lehetővé teszi a felhasználók számára nem törölhető, nem módosítható állapotban tárolva az adatok egy megadott időszakkal.
services: storage
author: xyh1
ms.service: storage
ms.topic: article
ms.date: 03/02/2019
ms.author: hux
ms.subservice: blobs
ms.openlocfilehash: 86e28c3561968b1411a3baa9ec0daecfab6ac73f
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202877"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Az Azure Blob storage-ban üzleti szempontból kritikus fontosságú adatok Store

Az Azure Blob storage nem módosítható storage lehetővé teszi a felhasználók az üzleti szempontból kritikus fontosságú adatok objektumok tárolására FÉREG (egyszer írható, olvassa el számos) állapotban. Ebben az állapotban teszi az adatok nem törölhető és nem módosítható egy felhasználó által megadott időtartam alatt. BLOB-objektumok is létrehozott, és olvassa el, de nem módosítható és nem törölhető, a megőrzési időtartam idejére. Nem módosítható tárolás engedélyezve van az általános célú v2 és Blob Storage-fiókok az összes Azure-régióban.

## <a name="overview"></a>Áttekintés

Nem módosítható storage segíti az egészségügyi szervezet, a pénzügyi intézmények és a kapcsolódó iparágak--különösen átvitelszervező-kereskedő szervezetek--biztonságosan tárolhatja az adatokat. Azt is jól használható az bármilyen forgatókönyvhöz, módosítás vagy törlés ellen kritikus adatok védelmének. 

Jellemző alkalmazási területek:

- **A jogszabályoknak való megfelelőség**: Nem módosítható tárolás az Azure Blob storage segítségével a szervezetek cím mp 17a-4(f), CFTC 1.31(d), finra az Egyesült és más szabályozások előírásainak betartását. Egy technikai tanulmány szerint Cohasset társítja e szabályozási követelmények keresztül letölthető hogyan nem módosítható tárolási címek részletesen a [Microsoft Szolgáltatásmegbízhatósági portált](https://aka.ms/AzureWormStorage). A [Azure adatvédelmi központ](https://www.microsoft.com/trustcenter/compliance/compliance-overview) megfelelőségi tanúsítványaink részletes információkat tartalmaz.

- **A dokumentum megőrzési biztonságos**: Nem módosítható, az Azure Blob storage tárolási biztosítja, hogy adatokat nem módosítható és nem törölhető bármely felhasználó, beleértve a fiók rendszergazdai jogosultságokkal rendelkező felhasználókat.

- **Jogi céllal zároltak közé**: Az Azure Blob storage nem módosítható storage lehetővé teszi a felhasználók szempontjából kritikus fontosságú pereskedés vagy üzleti használja, a visszatartás eltávolítását a kívánt időszakra hamisíthatatlan állapotban lévő bizalmas adatok tárolásához. Ez a funkció nem csak az jogi használati esetek, de is is értelmezhetők, egy esemény alapú céllal vagy egy vállalati lock, ahol eseményindítókat vagy a vállalati házirend alapján adatok védelmére szükség.

Nem módosítható storage támogatja a következőket:

- **[Időalapú adatmegőrzési házirend támogatási](#time-based-retention)**: Felhasználói adatok tárolására a megadott intervallumban házirendek állíthatja be. Időalapú adatmegőrzési esetén beállítása, blobok is létrehozott és olvassa el, de nem módosítható és nem törölhető. Miután a megőrzési időtartam lejárt, blobok törölhetők, de nem írható felül.

- **[Jogi célú visszatartási szabályzatot támogatási](#legal-holds)**: Ha a megőrzési időtartam nem ismert, felhasználói adatok immutably tárolásához, a jogi céllal zároltak közé történő törléséig jogi célú visszatartással állíthatja be.  Egy jogi céllal zároltak közé a házirend be van állítva, ha blobok is létrehozott és olvassa el, de nem módosítható és nem törölhető. Minden jogi céllal zároltak közé társítva, egy felhasználó által definiált alfanumerikus karakterek (például Ügyfélazonosítóként eseménynév, stb.), amely egy karakterlánc-azonosítót. 

- **Az összes blob rétegek támogatás**: FÉREG házirendek az Azure Blob storage-rétegét független, és a alkalmazni kell az összes szint: gyakori és ritka elérésű, valamint az archivált adatok. Felhasználók hogyan helyezhetik át az adatokat a legtöbb költség optimalizált szint számítási feladatai adatok módosíthatatlansági fenntartása mellett.

- **Tároló szintű konfigurációs**: Felhasználók konfigurálható az időalapú adatmegőrzési házirendek, és a jogi célú visszatartási címkék a tároló szintjén. Egyszerű tároló eszközszintű beállítások használatával felhasználók is létrehozhatja és zárolhatja az időalapú adatmegőrzési házirendek, kiterjesztheti a visszatartási időközöket, set és törölje a jogi célú visszatartással és egyéb. Ezeket a szabályzatokat alkalmazni a meglévő és az új tároló összes blobjának.

- **Audit naplózás támogatást**: A tárolók az auditnapló tartalmazza. Zárolt időalapú adatmegőrzési házirendek, három naplók megőrzési időszak bővítmények legfeljebb öt időalapú adatmegőrzés parancsok jeleníti meg. Időalapú adatmegőrzés a napló tartalmazza a felhasználói azonosító, a parancs típusa, az időbélyegeket és a megőrzési időtartam. Jogi célú visszatartással a napló tartalmazza a felhasználói azonosító, a parancs típusa, az időbélyegzőket és jogi célú visszatartási címkék. Ez a napló őrizzük meg a tároló mp 17a-4(f) szabályozási előírások alapján élettartamát. A [Azure-tevékenységnapló](../../azure-monitor/platform/activity-logs-overview.md) a vezérlési sík tevékenységek; engedélyezésekor átfogóbb naplóját jeleníti meg [Azure diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md) megőrzi, és megjeleníti az adatok síkjával végzett műveletek. Feladata a felhasználó, szabályozási és más célokra is szükség lehet, tartósan tárolja ezeket a naplókat.

## <a name="how-it-works"></a>Működés

Az Azure Blob storage nem módosítható storage támogatja a FÉREG vagy nem módosítható házirendek két típusa: időalapú adatmegőrzés és jogi célú visszatartással. Amikor egy időalapú adatmegőrzési szabály vagy a jogi céllal zároltak közé alkalmaz egy tárolóban, a kevesebb mint 30 másodperc múlva nem módosítható FÉREG állapotba kerülnek át az összes meglévő blobok. A tárolóba feltöltött összes új blobok is a nem módosítható állapotba kerül át. Összes BLOB a nem módosítható állapotba került, a nem módosítható házirend ellenőrzése és az összes felülírása és törlése után a meglévő és új objektumok nem módosítható a tárolóban műveletek nem engedélyezettek.

### <a name="time-based-retention"></a>Időalapú adatmegőrzés

> [!IMPORTANT]
> Időalapú adatmegőrzési kell *zárolva* kell lennie egy nem módosítható a BLOB (írási és törlési védett) állapot mp 17a-4(f) és egyéb szabályozásoknak való megfelelőséget. Azt javasoljuk, hogy egy ésszerű időn belül, általában 24 órán belül a házirend zárolása. Nem javasoljuk a *oldják a zárolást* állapot rövid távú funkció kísérletek kívül más célra.

Időalapú adatmegőrzési szabály alkalmazásakor a tárolón a tárolóban lévő összes BLOB időtartama nem módosítható állapotban marad a *hatékony* megőrzési ideje. Meglévő blobok hatékony megőrzési időtartama a különbség a blob-módosítás időpontja és a felhasználó által megadott megőrzési időtartam megegyezik.

Az új blobok esetében az adatmegőrzési időtartam egyenlő a felhasználó által megadott adatmegőrzési intervallummal. Felhasználók kiterjesztheti a megőrzési időtartam, mert nem módosítható tároló a tényleges megőrzési időtartam kiszámításához használja a legújabb érték a felhasználó által megadott megőrzési időtartam.

> [!TIP]
> **Példa** A felhasználó a megőrzési időtartam öt évig időalapú adatmegőrzési hoz létre.
>
> A meglévő blobot a tárolóban, _testblob1_, egy évre visszamenőleg lett létrehozva. A tényleges megőrzési időszak _testblob1_ négy év van meghatározva.
>
> Egy új blob _testblob2_, a tároló most már fel van töltve. Az új blob hatékony megőrzési időtartama öt év meghatározva.

### <a name="legal-holds"></a>Jogi célú visszatartások

Megadva jogi célú visszatartás beállításakor minden meglévő és új blobok nem módosítható állapotban marad, amíg nem törlődik a jogi céllal zároltak közé. Set, és törölje a jogi célú visszatartással vonatkozó további információkért lásd: a [bevezetés](#getting-started) szakaszban.

Egy tároló lehet megadva jogi célú visszatartás és a egy időalapú adatmegőrzési szabály egy időben. A tárolóban lévő összes BLOB állapotban marad a nem módosítható mindaddig, amíg minden jogi célú visszatartással nincsenek bejelölve, akkor is, ha azok hatékony megőrzési ideje lejárt. Ezzel szemben egy blob marad nem módosítható állapotban addig, amíg a tényleges megőrzési időtartam lejár, annak ellenére, hogy minden jogi célú visszatartással törölve lett.

Az alábbi táblázat a Művelettípusok blob, a másik nem módosítható forgatókönyvek esetén nem engedélyezett. További információkért lásd: a [Azure Blob Service API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentációját.

|Forgatókönyv  |BLOB állapota  |BLOB művelet nem engedélyezett  |
|---------|---------|---------|
|A blob tényleges adatmegőrzési időtartama még nem járt le és/vagy jogi célú visszatartás van érvényben     |Nem módosítható: törlés- és írásvédett         | Blob kihelyezése<sup>1</sup>, blokk Put<sup>1</sup>, Put tiltólista<sup>1</sup>, tároló, Delete Blob Set Blob metaadatainak, lapra helyezze, állítsa be a Blob tulajdonságai, a Blob pillanatkép, a növekményes másolási Blob, Hozzáfűzés letiltása         |
|A blobon beállított tényleges megőrzési időtartam lejárt     |Csak írásvédett (a törlési műveletek engedélyezettek)         |Blob kihelyezése<sup>1</sup>, blokk Put<sup>1</sup>, Put tiltólista<sup>1</sup>, Blob metaadatainak, lapra helyezze, állítsa be a Blob tulajdonságai, a Blob pillanatkép, a növekményes másolási Blob, fűzze hozzá a letiltása         |
|Minden jogi tárolja az üres, és időalapú adatmegőrzési szabályzat nem található a tárolóban     |Változtatható         |None         |
|Nincs FÉREG szabályzat jön létre (időalapú adatmegőrzés vagy jogi céllal zároltak közé)     |Változtatható         |None         |

<sup>1</sup> az alkalmazás lehetővé teszi, hogy egy új blob létrehozása után ezeket a műveleteket. Minden későbbi felülírja a meglévő blob elérési útnak egy nem módosítható tároló műveletek nem engedélyezettek.

## <a name="pricing"></a>Díjszabás

Nem jár további költségekkel a szolgáltatást. Nem módosítható adatok rendszeres, mutable adatként ugyanúgy díjszabása. Az Azure Blob Storage díjszabásáról, tekintse meg a [Azure Storage díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Első lépések

A legújabb verzióiban a [az Azure portal](https://portal.azure.com), [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), és [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases) nem módosítható storage támogatja az Azure Blob storage. [Ügyfél klienskódtár-támogatásával](#client-libraries) is biztosított.

> [!NOTE]
>
> Csak az általános célú v2 és Blob Storage-fiókok számára érhető el tárhely nem módosítható. E fiók segítségével kell kezelni [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Egy meglévő általános célú v1 storage-fiók frissítésével kapcsolatos információkért lásd: [tárfiók frissítése](../common/storage-account-upgrade.md).

### <a name="azure-portal"></a>Azure Portal

1. Hozzon létre egy új tárolót, vagy válasszon ki egy már meglévőt a nem módosítható állapotban tartandó blobok tárolására.
 A tároló a GPv2- vagy blob storage-fiókban kell lennie.
2. Válassza ki **hozzáférési szabályzat** a tároló beállításaiban. Válassza ki **+ szabályzat hozzáadása** alatt **nem módosítható blobtároló**.

    ![Tárolóbeállítások a portálon](media/storage-blob-immutable-storage/portal-image-1.png)

3. Időalapú adatmegőrzés engedélyezéséhez jelölje be **időalapú adatmegőrzés** a legördülő menüből.

    !["Időalapú adatmegőrzés" a "Házirend-típus" területen kijelölt](media/storage-blob-immutable-storage/portal-image-2.png)

4. Adja meg a megőrzési időtartam napban (elfogadható értékek a 146000 1 nap).

    !["A frissítés megőrzési időszak" mezőbe](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    A szabályzat kezdeti állapota nem oldják a zárolást a szolgáltatás tesztelése, és hajtsa végre a módosításokat a szabályzat előtt zárolni tudja lehetővé teszi. A szabályzat zárolás elengedhetetlen az előírásoknak, például a 17a – 4/mp-ben.

5. A szabályzat zárolása. Kattintson a jobb gombbal a három pontra (**...** ), és további műveletek az alábbi menü jelenik meg:

    !["Szabályzat zárolás" menüben](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

    Válassza ki **zárolási szabályzat**. A szabályzat zárolva van, és nem lehet törölni, csak a megőrzési időtartam bővítmény engedélyezve lesz.

6. Válassza ki ahhoz, hogy a jogi célú visszatartással, **+ szabályzat hozzáadása**. Válassza ki **megadva jogi célú visszatartás** a legördülő menüből.

    !["Jogi célú visszatartási" a "Szabályzat típusa" a menü](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

7. Hozzon létre egy vagy több címkét egy jogi céllal zároltak közé.

    !["A címke neve" mező mellett a házirend típusát](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

8. Ha törölni szeretne egy jogi céllal zároltak közé, egyszerűen alkalmazott megadva jogi célú visszatartás azonosító címke eltávolítása.

### <a name="azure-cli"></a>Azure CLI

A szolgáltatás a következő parancs csoportokat tartalmazza: `az storage container immutability-policy` és `az storage container legal-hold`. Futtatás `-h` meg azokat a parancsokat.

### <a name="powershell"></a>PowerShell

Az Az.Storage előzetes modul nem módosítható storage támogatja.  A funkció engedélyezéséhez kövesse az alábbi lépéseket:

1. Gondoskodjon arról, hogy a legújabb verziója telepítve van a PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Távolítsa el az Azure PowerShell korábbi telepítéseknek.
3. Az Azure PowerShell telepítése: `Install-Module Az –Repository PSGallery –AllowClobber`.
4. Az Azure PowerShell Storage moduljának előzetes verziójának telepítéséhez: `Install-Module Az.Storage -AllowPrerelease -Repository PSGallery -AllowClobber`

A [minta PowerShell-kód](#sample-powershell-code) szakaszt a cikk későbbi részében a funkció használatát mutatja be.

## <a name="client-libraries"></a>Ügyfélkódtárak

A következő ügyfélkódtárak nem módosítható storage támogatja az Azure Blob storage:

- [.NET ügyféloldali kódtár verzió 7.2.0-preview és újabb verziók](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [NODE.js ügyféloldali kódtár 4.0.0-s verzió vagy újabb](https://www.npmjs.com/package/azure-arm-storage)
- [Python ügyféloldali kódtár 2.0.0-s verzió kiadásra jelölt verziójára frissít, 2 és újabb verziók](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java-Klienskódtár](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="supported-values"></a>Támogatott értékek

- A minimális megőrzési időtartam érték egy nap. A maximális hossz 146,000 nap (400 év).
- A tárfiók zárolt nem módosítható szabályzatokkal tárolók maximális számának 1000.
- Egy storage-fiókok a tárolók a jogi céllal zároltak közé beállítású maximális számát az 1000.
- Egy tárolóban jogi céllal zároltak közé címkék maximális száma 10.
- A jogi céllal zároltak közé címke hossza legfeljebb 23 alfanumerikus karaktereket. A minimális hossz három karaktert.
- A tárolóhoz a maximális megőrzési időköz-bővítmények zárolt nem módosítható szabályzatok maximális számát az harmadik.
- Zárolt nem módosítható házirend a tárolóhoz legfeljebb öt időalapú adatmegőrzési szabályzat naplói és a egy legfeljebb 10 jogi tartsa házirend naplók megmaradnak a tároló időtartamára.

## <a name="faq"></a>GYIK

**Biztosítható dokumentáció FÉREG megfelelőségi?**

Igen. A megfelelés, a dokumentum a Microsoft a vezető független értékelés cég rögzíti és információs irányítási Cohasset nem módosítható az Azure Blob-tároló és az adott követelményeknek való megfelelőségét kiértékelheti, hogy társítja őrződnek meg a pénzügyi szolgáltatások iparágban. Cohasset ellenőrzi, hogy az Azure nem módosítható Blobtároló, időalapú Blobok FÉREG állapotban tartani használatakor megfelel-e a megfelelő tárolási követelmények CFTC szabály 1.31(c)-(d), a finra az Egyesült szabály 4511 és 17a – 4. szabály (mp). A Microsoft a szabálykészletet célzott, mivel a legtöbb részletes útmutatást globálisan rekordok megőrzési a pénzügyi intézmények számára. A Cohasset jelentés érhető el a [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage).

**A funkció csak blokkblobokat, vagy a lapon, és a hozzáfűző blobok is vonatkozik?**

Nem módosítható, bármely typ objektu blob storage használható, de javasoljuk, használjon a leginkább a blokkblobok esetében. Ellentétben a blokkblobok a lap blobok és a hozzáfűző blobok kell létrehozni egy FÉREG tárolón kívül, és majd át lesznek másolva a. Másolását követően ezek a blobok egy FÉREG tárolóba nem további *hozzáfűzi* , egy hozzáfűző blob vagy egy lapblob módosítások engedélyezettek.

**Minden egyes alkalommal új tárfiókot kell létrehoznom a funkció használatához?**

Bármely meglévő vagy újonnan létrehozott általános célú v2 és Blob Storage-fiókok nem módosítható storage is használhatja. Ez a funkció a blokkblobok, a GPv2 és Blob Storage-fiókok használati szól.

**Jogi céllal zároltak közé és időalapú adatmegőrzési szabály is használhatom?**

Egy tároló lehet megadva jogi célú visszatartás és a egy időalapú adatmegőrzési szabály egy időben. A tárolóban lévő összes BLOB állapotban marad a nem módosítható mindaddig, amíg minden jogi célú visszatartással nincsenek bejelölve, akkor is, ha azok hatékony megőrzési ideje lejárt. Ezzel szemben egy blob marad nem módosítható állapotban addig, amíg a tényleges megőrzési időtartam lejár, annak ellenére, hogy minden jogi célú visszatartással törölve lett.

**Azok a jogi céllal zároltak közé szabályzatok csak a jogi eljárás vagy vannak-e más használati forgatókönyvek?**

Nem, a jogi tartsa még csak nem időalapú adatmegőrzési használt általános kifejezés. Kizárólag a pereskedés használja, nem kell kapcsolatos eljárást. Jogi céllal zároltak közé házirendek hasznosak letiltása, felülírása és törlése a FÉREG adatok, fontos vállalati védelme Ha a megőrzési időtartam nem ismert. Hogy használhatja azt egy vállalati szabályzat az üzletmenet szempontjából kritikus fontosságú FÉREG számítási feladatok védelméhez, vagy használja a tesztelési házirend szerint, mielőtt egy egyéni eseményindítót időalapú adatmegőrzési használatát igényli. 

**Mi történik, ha megpróbálok törölni egy tárolót, amelyre *zárolt* időalapú adatmegőrzési szabályzat vagy jogi célú visszatartás vonatkozik?**

A tároló törlése művelet sikertelen lesz, ha legalább egy blob már létezik a zárolt időalapú adatmegőrzési szabály vagy a jogi céllal zároltak közé. A tároló törlése művelet csak akkor, ha az nincs egy aktív megőrzési időtartam a blob létezik, és nincsenek a jogi célú visszatartással nem fog sikerülni. A tároló törlése előtt törölnie kell a blobokat.

**Mi történik, ha megpróbálok törölni egy olyan WORM-tárolót tartalmazó tárfiókot, amelyre *zárolt* időalapú adatmegőrzési szabályzat vagy jogi célú visszatartás vonatkozik?**

A tárfiók törlése sikertelen lesz, ha legalább egy jogi célú visszatartással ellátott WORM-tárolót vagy aktív adatmegőrzési időtartammal rendelkező blobot tartalmaz.  A tárfiók törlése előtt törölnie kell az összes FÉREG tárolót. A tároló törléséhez információkért lásd: az előző kérdést.

**Áthelyezhetem az adatokat különböző blobrétegek között (gyakran, ritkán és alig használt rétegek), ha a blob nem módosítható állapotban van?**

Igen, használhatja a Blobszint beállítása parancs blob szolgáltatásszinten adatok áthelyezése a megfelelő nem módosítható állapotban az adatok megőrzésével. Nem módosítható tárolás támogatott a gyakori és ritka elérésű, valamint az archivált blob.

**Mi történik, ha elmulasztok fizetni, és a megőrzési időtartam még nem járt le?**

Nemfizetés esetén normál adatmegőrzési házirendek az a Microsoft a szerződés feltételei érvényesek meghatározott.

**Létezik a funkció kipróbálására lehetőséget biztosító ingyenes próba- vagy türelmi időszak?**

Igen. Időalapú adatmegőrzési szabály létrehozásakor rendszer egy *oldják a zárolást* állapota. Ebben az állapotban is bármilyen kívánt módosítsa a megőrzési időtartam, például növelése vagy csökkentése és még akkor is törli a szabályzatot. A szabály zárolása, miután marad zárolt mindaddig, amíg a megőrzési időtartam lejár. A zárolt házirend miatt, törlése és módosítása a visszatartási időköz. Határozottan javasoljuk, hogy használja a *oldják a zárolást* kizárólag kísérleti célokra állapotát és a egy 24 órás időtartamon belül a házirend zárolása. Ezen eljárások segítségével az mp-ben 17a-4(f) és más szabályozások előírásainak betartását.

**Használható együtt nem módosítható a blob-házirendeket a helyreállítható törlés?**

Igen. [Helyreállítható Törlés az Azure Blob storage](storage-blob-soft-delete.md) az összes tárolót egy tárfiókban, függetlenül a jogi céllal zároltak közé vagy időalapú adatmegőrzési szabály vonatkozik. Azt javasoljuk, hogy mielőtt bármilyen nem módosítható FÉREG házirendek alkalmazása és megerősítik, amely lehetővé teszi, hogy további védelem a helyreállítható törlés. 

**A szolgáltatás országos és kormányzati felhőkben is elérhető?**

Nem módosítható tárolás az Azure-beli nyilvános, Kína és kormányzati régiókban érhető el. A tároló nem módosítható az Ön régiójában nem érhető el, ha forduljon a támogatási és e-mailek azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Minta PowerShell-kód

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következő PowerShell-parancsfájlt referenciaként van. Ez a szkript létrehoz egy új tárfiókot és tárolót. Majd bemutatja, hogyan állítsa be és törölje a jogi célú visszatartással, hozzon létre és zárolhatja az időalapú adatmegőrzési (más néven vonatkozó módosíthatatlansági szabályzat) és kiterjesztése a megőrzési időtartam.

Állítsa be, és tesztelje az Azure Storage-fiók:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Adja meg, és törölje a jogi célú visszatartással:

```powershell
# Set a legal hold
Add-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Hozzon létre vagy módosíthatatlansági szabályzattal frissítése:
```powershell
# with an account name or container name
Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Kérje le a módosíthatatlansági szabályzattal:
```powershell
# Get an immutability policy
Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzStorageContainerImmutabilityPolicy -Container $containerObject
```

Zárolási módosíthatatlansági szabályzattal (add - Force elvetése a rendszer kéri):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Módosíthatatlansági szabályzattal kiterjesztése:
```powershell

# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Egy vonatkozó módosíthatatlansági szabályzat eltávolítása az (add - Force elvetése a rendszer kéri):
```powershell
# with an immutability policy object
$policy = Get-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
