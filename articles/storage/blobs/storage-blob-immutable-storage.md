---
title: Az Azure Storage-Blobok nem módosítható tárolói | Microsoft Docs
description: Az Azure Storage szolgáltatás a blob (Object) tároló számára biztosít féreg (egyszer írható, olvasható) támogatást, amely lehetővé teszi a felhasználók számára, hogy a megadott intervallumban nem törölhető, nem módosítható állapotban tárolja az adattárolást.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 06e1d881a14367c579bd58ffae04dc0970eb041a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941951"
---
# <a name="store-business-critical-data-in-azure-blob-storage"></a>Üzleti szempontból kritikus fontosságú adattárolás tárolása az Azure Blob Storage-ban

Az Azure Blob Storage nem módosítható tárolója lehetővé teszi, hogy a felhasználók üzleti szempontból kritikus fontosságú adatobjektumokat tároljanak egy FÉREGben (egyszer írható, olvasható) állapot. Ez az állapot a felhasználó által megadott intervallumban nem törölhető és nem módosítható adatvesztést tesz lehetővé. A blob-objektumok a megőrzési időtartam időtartama alatt létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. A nem módosítható tárterület minden Azure-régióban általános célú v2 és Blob Storage fiókok esetében engedélyezett.

## <a name="overview"></a>Áttekintés

A nem módosítható tárterület segíti az egészségügyi szervezetet, a pénzügyi intézményeket és a kapcsolódó iparágakat – különösen a közvetítő-kereskedő szervezeteket –, hogy biztonságosan tárolják az adattárolást. Bármilyen forgatókönyvben is kihasználható, hogy a kritikus fontosságú adatkezelést a módosítással vagy törléssel lehessen védeni. 

Jellemző alkalmazási területek:

- **Szabályozási megfelelőség**: Az Azure Blob Storage-hoz tartozó nem módosítható tárterület segíti a szervezeteket a SEC 17a-4 (f), a CFTC 1.31 (d), a FINRA és más rendeletek esetében. A Cohasset által használt technikai tanulmány részletesen ismerteti, hogyan kezeli ezeket a szabályozási követelményeket a [Microsoft szolgáltatás megbízhatósági portálján](https://aka.ms/AzureWormStorage)keresztül. A [Azure biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter/compliance/compliance-overview) részletes információkat tartalmaz a megfelelőségi tanúsítványokról.

- **Biztonságos dokumentumok megőrzése**: Az Azure Blob Storage-hoz nem módosítható tároló biztosítja, hogy az egyes felhasználók nem módosíthatják és nem törölhetik az összes felhasználót, beleértve a fiók rendszergazdai jogosultságokkal rendelkező felhasználókat is.

- **Jogi megtartás**: Az Azure Blob Storage-hoz nem módosítható tároló lehetővé teszi, hogy a felhasználók a peres vagy üzleti felhasználás szempontjából kritikus fontosságú adatokat tároljanak a kívánt időtartamra, amíg el nem távolítják a mentességet. Ez a funkció nem korlátozódik kizárólag a jogi használati esetekre, hanem eseményvezérelt vagy vállalati zárolásra is gondolhat, ahol szükség van az adatvédelemre az esemény-eseményindítók vagy a vállalati szabályzatok alapján.

A nem változtatható tároló a következőket támogatja:

- **[Időalapú adatmegőrzési szabályzatok támogatása](#time-based-retention)** : A felhasználók házirendeket állíthatnak be az adattároláshoz egy adott intervallumra vonatkozóan. Időalapú adatmegőrzési szabályzat beállításakor a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. A megőrzési időszak lejárta után a blobokat törölheti, de nem lehet felülírni.

- **[Jogszabályi szabályzatok támogatása](#legal-holds)** : Ha a megőrzési időköz nem ismert, a felhasználók megadhatják, hogy a jogcímek az adatok tárolására immutably, amíg nem törlik a jogi mentességet.  Ha be van állítva egy jogszabályi szabályzat, a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. Minden jogi Hold egy felhasználó által definiált alfanumerikus címkével (például egy eset-AZONOSÍTÓval, az esemény nevével stb.) van társítva, amelyet azonosító sztringként használ. 

- **Az összes blob-rétegek támogatása**: A WORM-szabályzatok függetlenek az Azure Blob Storage-rétegtől, és az összes rétegre érvényesek: gyakori, ritka elérésű és archív. A felhasználók az adatok módosíthatatlansági megtartása mellett a leginkább költséghatékonyan optimalizált szintet is áthelyezhetik.

- **Tároló szintű konfiguráció**: A felhasználók időalapú adatmegőrzési szabályzatokat és jogi megtartási címkéket állíthatnak be a tároló szintjén. Az egyszerű tárolók szintjének használatával a felhasználók időalapú adatmegőrzési szabályzatokat hozhatnak létre és zárolnak, megtarthatja a megőrzési időközöket, beállíthatja és törölheti a jogcímeket, és így tovább Ezek a házirendek a tároló összes blobján érvényesek, a meglévő és az új is.

- **Naplózási naplózás támogatása**: Minden tároló tartalmaz egy házirend-naplózási naplót. Legfeljebb hét időalapú adatmegőrzési parancsot jelenít meg a zárolt időalapú adatmegőrzési házirendek esetében, és tartalmazza a felhasználói azonosítót, a parancs típusát, az időbélyegzőket és a megőrzési időt. A jogcímek esetében a napló tartalmazza a felhasználói azonosítót, a parancs típusát, az időbélyegeket és a jogi megtartási címkéket. Ezt a naplót a házirend élettartama érdekében a SEC 17a-4 (f) szabályozási irányelveknek megfelelően megőrzi a rendszer. Az [Azure-tevékenység naplója](../../azure-monitor/platform/activity-logs-overview.md) az összes vezérlő síkja tevékenységének átfogóbb naplóját jeleníti meg; az [Azure diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md) engedélyezése és az adatsík-műveletek megtartása és megjelenítése. A felhasználók felelőssége, hogy ezeket a naplókat tartósan tárolják, mivel ezek a szabályok vagy egyéb célokra szükségesek.

## <a name="how-it-works"></a>Működés

Az Azure Blob Storage-hoz nem módosítható tárolók két típusú férget vagy nem módosítható szabályzatot támogatnak: az időalapú adatmegőrzést és a jogi tárolást. Ha egy tárolón időalapú adatmegőrzési szabályzatot vagy jogi megtartást alkalmaz, az összes meglévő blob nem módosítható féreg állapotba kerül 30 másodpercnél kevesebb ideig. A tárolóba feltöltött összes új blob is a nem módosítható állapotba kerül. Ha az összes blobot áthelyezte a megváltoztathatatlan állapotba, a rendszer megerősíti a megváltoztathatatlan házirendet, és a nem módosítható tárolóban lévő meglévő és új objektumok összes felülírási vagy törlési művelete nem engedélyezett.

A tároló és a fiók törlése szintén nem engedélyezett, ha vannak olyan Blobok, amelyek egy megváltoztathatatlan házirenddel védettek. A tároló törlése művelet sikertelen lesz, ha legalább egy blob már létezik zárolt időalapú adatmegőrzési házirenddel vagy jogi megtartással. A tárfiók törlése sikertelen lesz, ha legalább egy jogi célú visszatartással ellátott WORM-tárolót vagy aktív adatmegőrzési időtartammal rendelkező blobot tartalmaz. 

### <a name="time-based-retention"></a>Időalapú adatmegőrzés

> [!IMPORTANT]
> Az időalapú adatmegőrzési szabályzatot *zárolni* kell ahhoz, hogy a blob megfelelő, megváltoztathatatlan (írási és törlési) állapotban legyen a SEC 17a-4 (f) és más szabályozási megfelelőség esetében. Javasoljuk, hogy a szabályzatot ésszerű időn belül, általában 24 óránál rövidebb ideig zárolja. Az alkalmazott időalapú adatmegőrzési szabályzat kezdeti állapota *zárolva*van, így a zárolása előtt tesztelheti a szolgáltatást, és módosíthatja a szabályzatot. Míg a *zárolt* állapot módosíthatatlansági védelmet biztosít, a rövid távú szolgáltatásokra vonatkozó kísérletektől eltérő célra nem ajánlott a *feloldva* állapotot használni. 

Ha egy tárolón időalapú adatmegőrzési szabályt alkalmaz, a tárolóban lévő összes blob a *tényleges* megőrzési időtartamig nem változtatható állapotban marad. A meglévő Blobok tényleges megőrzési időtartama megegyezik a blob módosítási ideje és a felhasználó által megadott megőrzési időtartam közötti különbséggel.

Az új blobok esetében az adatmegőrzési időtartam egyenlő a felhasználó által megadott adatmegőrzési intervallummal. Mivel a felhasználók kiterjeszthetik a megőrzési időtartamot, a nem módosítható tároló a felhasználó által megadott megőrzési időtartam legutóbbi értékét használja a tényleges megőrzési időtartam kiszámításához.

> [!TIP]
> **Példa:** A felhasználó egy időalapú adatmegőrzési szabályzatot hoz létre öt év megőrzési időtartammal.
>
> A tárolóban lévő meglévő blob ( _testblob1_) egy éve jött létre. A _testblob1_ érvényes megőrzési ideje négy év.
>
> A rendszer feltölt egy új blobot ( _testblob2_), amely most már fel van töltve a tárolóba. Az új blob tényleges megőrzési ideje öt év.

A kinyitott időalapú adatmegőrzési szabályzat csak a szolgáltatások tesztelésére ajánlott, és a házirendet zárolni kell ahhoz, hogy meg lehessen felelni a SEC 17a-4 (f) és más szabályozási megfelelőségnek. Az időalapú adatmegőrzési szabályzat zárolása után a szabályzat nem távolítható el, és a tényleges megőrzési időtartam legfeljebb 5 nő lehet. Az időalapú adatmegőrzési szabályzatok beállításával és zárolásával kapcsolatos további információkért tekintse meg az [első lépéseket](#getting-started) ismertető szakaszt.

### <a name="legal-holds"></a>Jogi célú visszatartások

A jogi szabályozás beállításakor minden meglévő és új blob nem módosítható állapotban marad mindaddig, amíg nem törli a jogi megtartást. A jogcímek beállításával és törlésével kapcsolatos további információkért tekintse meg az [első lépéseket](#getting-started) ismertető szakaszt.

Egy tároló egyszerre rendelkezhet jogi és időalapú adatmegőrzési házirenddel is. A tárolóban lévő összes blob a megváltoztathatatlan állapotban marad mindaddig, amíg az összes jogi tartalékot nem törlik, még akkor is, ha a hatályos megőrzési idő lejárt. Ezzel szemben a Blobok nem módosítható állapotban maradnak, amíg a hatályos megőrzési idő le nem jár, még akkor is, ha az összes jogi tartalék törölve lett.

A következő táblázat a különböző változtathatatlan forgatókönyvek esetében letiltott blob-típusokat tartalmazza. További információt az [Azure Blob Service API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentációjában talál.

|Forgatókönyv  |BLOB állapota  |A blob-műveletek nem engedélyezettek  |
|---------|---------|---------|
|A blob tényleges adatmegőrzési időtartama még nem járt le és/vagy jogi célú visszatartás van érvényben     |Nem módosítható: törlés- és írásvédett         | Helyezze az<sup>1. blobot</sup>, helyezze<sup>az 1.</sup>blokkot, az<sup>1</sup>., a tároló törlése, a blob törlése, a blob metaadatainak beállítása, a Put oldal, a blob tulajdonságainak beállítása, a pillanatkép-blob, a növekményes másolási blob, a Letiltás         |
|A blobon beállított tényleges megőrzési időtartam lejárt     |Csak írásvédett (a törlési műveletek engedélyezettek)         |Helyezze az 1<sup>. blobot, helyezze</sup>az 1<sup>., az</sup><sup>1</sup>. blokkot, a blob metaadatainak beállítása, a Put oldal, a blob tulajdonságainak beállítása, a pillanatkép-blob, a növekményes másolási blob, a blokk hozzáfűzése         |
|Minden jogi eszköz törölve van, és a tárolón nincs megadva időalapú adatmegőrzési szabály     |Változtatható         |Nincsenek         |
|Nincs létrehozva féreg-házirend (időalapú megőrzés vagy jogi megtartási idő)     |Változtatható         |Nincsenek         |

<sup>1</sup> az alkalmazás lehetővé teszi, hogy ezek a műveletek egyszer új blobot hozzanak létre. Egy nem módosítható tárolóban lévő blob elérési útban lévő összes további felülírási művelet nem engedélyezett.

## <a name="supported-values"></a>Támogatott értékek

### <a name="time-based-retention"></a>Időalapú adatmegőrzés
- A Storage-fiók esetében a zárolt, időalapú, rögzített házirendekkel rendelkező tárolók maximális száma 1 000.
- A minimális megőrzési időköz 1 nap. A maximális érték 146 000 nap (400 év).
- Tároló esetén a zárolt időalapú megváltoztathatatlan házirendek megőrzési időtartamának meghosszabbítására szolgáló szerkesztési adatok maximális száma 5.
- Tároló esetén a zárolt szabályzatok számára legfeljebb 7 időalapú adatmegőrzési szabály van megtartva.

### <a name="legal-hold"></a>Jogi megtartás
- A Storage-fiók esetében a jogszabályi megtartási beállítással rendelkező tárolók maximális száma 1 000.
- A tárolók esetében a jogi megtartó címkék maximális száma 10.
- A jogi megtartási címke minimális hossza 3 alfanumerikus karakter. A maximális hossz 23 alfanumerikus karakter.
- A tárolók esetében a szabályzat időtartama alatt legfeljebb 10 jogszabályi szabályzatot tartalmazó napló marad.

## <a name="pricing"></a>Díjszabás

A szolgáltatás használata nem jár további díjszabással. A megváltoztathatatlan adatszolgáltatások díjszabása ugyanúgy történik, mint a normál és a megváltoztathatatlan adatátviteli mód. Az Azure Blob Storage díjszabását az [Azure Storage díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/storage/blobs/)tekintheti meg.

## <a name="getting-started"></a>Első lépések
A nem módosítható tárterület csak általános célú v2 és Blob Storage fiókok esetében érhető el. Ezeket a fiókokat [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)használatával kell felügyelni. Meglévő általános célú v1 Storage-fiók frissítésével kapcsolatos információkért lásd: [Storage-fiók frissítése](../common/storage-account-upgrade.md).

Az [Azure Portal](https://portal.azure.com), az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)és a [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) legújabb kiadásai támogatják az Azure Blob Storage-hoz tartozó nem módosítható tárolókat. Az [ügyféloldali kódtár támogatása](#client-libraries) is elérhető.

### <a name="azure-portal"></a>Azure Portal

1. Hozzon létre egy új tárolót, vagy válasszon ki egy már meglévőt a nem módosítható állapotban tartandó blobok tárolására.
 A tárolónak egy GPv2-vagy blob Storage-fiókban kell lennie.
2. A tároló beállításainál válassza a **hozzáférési szabályzat** lehetőséget. Ezután válassza a **+ házirend hozzáadása** a nem módosítható **blob Storage**-ban lehetőséget.

    ![Tároló beállításai a portálon](media/storage-blob-immutable-storage/portal-image-1.png)

3. Az időalapú adatmegőrzés engedélyezéséhez válassza az **időalapú megőrzés** lehetőséget a legördülő menüből.

    !["Időalapú megőrzés" kiválasztva a "házirend típusa" alatt](media/storage-blob-immutable-storage/portal-image-2.png)

4. Adja meg a megőrzési időtartamot napokban (1 és 146000 nap közötti elfogadható értékek).

    !["Megőrzési időszak frissítése a következőre" mező](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    A házirend kezdeti állapota zárolva van, így a zárolás előtt tesztelheti a szolgáltatást, és módosíthatja a szabályzatot. A szabályzat zárolása elengedhetetlen ahhoz, hogy megfeleljen a (z) SEC 17a-4 előírásoknak.

5. Zárolja a szabályzatot. Kattintson a jobb gombbal a három pontra ( **..** .), és a következő menü jelenik meg további műveletekkel:

    ![A menü zárolási szabályzata](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Válassza a **zárolási házirend** elemet, és erősítse meg a zárolást. A szabályzat zárolva van, és nem törölhető, csak a megőrzési időtartam kiterjesztései lesznek engedélyezve. A blob-törlések és felülbírálások nem engedélyezettek. 

    ![A menü zárolási szabályzatának megerősítése](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. A jogcímek engedélyezéséhez válassza a **+ házirend hozzáadása**lehetőséget. Válassza a **jogi megtartás** lehetőséget a legördülő menüből.

    !["Jogi megtartás" a "szabályzat típusa" alatt található menüben](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Hozzon létre egy jogi megtartást egy vagy több címkével.

    !["Címke neve" mező a házirend típusa alatt](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. A jogi megtartás törléséhez egyszerűen távolítsa el az alkalmazott jogi megtartási azonosító címkét.

### <a name="azure-cli"></a>Azure CLI

A szolgáltatás a következő parancssori csoportokba tartozik: `az storage container immutability-policy` és. `az storage container legal-hold` Futtassa `-h` őket a parancsok megtekintéséhez.

### <a name="powershell"></a>PowerShell

Az az. Storage modul támogatja a nem módosítható tárolókat.  A szolgáltatás engedélyezéséhez kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a telepített PowerShellGet legújabb verziója van `Install-Module PowerShellGet –Repository PSGallery –Force`telepítve:.
2. Távolítsa el a Azure PowerShell korábbi telepítését.
3. Azure PowerShell telepítése: `Install-Module Az –Repository PSGallery –AllowClobber`.

A cikk későbbi részében található [PowerShell-kód](#sample-powershell-code) című szakasz a funkció használatát mutatja be.

## <a name="client-libraries"></a>Ügyfélkódtárak

A következő ügyféloldali kódtárak támogatják az Azure Blob Storage nem módosítható tárolóját:

- [.NET ügyféloldali kódtár verziója 7.2.0 – előzetes verzió és újabb](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node. js ügyféloldali kódtár verziója 4.0.0 és újabb verziók](https://www.npmjs.com/package/azure-arm-storage)
- [Python ügyféloldali kódtár verziója 2.0.0 Release Candidate 2 és újabb verziók](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java ügyféloldali kódtár](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>GYIK

**Megadhatja a féreg megfelelőségének dokumentációját?**

Igen. A megfelelőség dokumentálása érdekében a Microsoft megőrizte a nyilvántartások kezelésével és a Cohasset-társításokkal foglalkozó vezető független értékelő vállalatot, amely az Azure-ban nem módosítható Blob Storage értékelésére és a követelményekkel kapcsolatos követelményeknek való megfelelésre specializálódott a pénzügyi szolgáltatások iparának. A Cohasset ellenőrizte, hogy az Azure-ban nem módosítható Blob Storage, ha az időalapú Blobok egy féreg állapotában való megőrzésére szolgál, megfelel a CFTC-szabály 1.31 (c)-(d), a FINRA szabály 4511-es és a SEC-szabály 17a-4-re vonatkozó tárolási követelményeinek. A Microsoft ezt a szabályt célozza meg, mivel a pénzügyi intézmények számára a rekordok megőrzésére leginkább jellemző útmutatást jelentik. A Cohasset-jelentés a [Microsoft szolgáltatás-Adatvédelmi központban](https://aka.ms/AzureWormStorage)érhető el. Ha a Microsoft igazolást kér a féreg megfelelőségéről, forduljon az Azure ügyfélszolgálatához.

**A szolgáltatás csak a Blobok blokkolására, illetve a Blobok oldalára és hozzáfűzésére is vonatkozik?**

A nem módosítható tároló bármely blob-típussal használható, mert a tároló szintjén van beállítva, de javasoljuk, hogy a féreg olyan tárolókat használjon, amelyek főleg a blokk blobokat tárolják. A blokk Blobokkal ellentétben az új lapokat tartalmazó blobokat és a hozzáfűzési blobokat a féreg-tárolón kívül kell létrehozni, majd az-ba kell másolni. Miután átmásolta ezeket a blobokat egy férget tárolóba, a hozzáfűzési blobhoz vagy egy oldal blobjának módosításaihoz való további *Hozzáfűzés* nem engedélyezett. Ezért a féreg házirendjét olyan tárolón kell beállítani, amely a VHD-ket (blobokat) tárolja minden aktív Virtual Machines esetében, mert a virtuális gép lemezét zárolni fogja.

**Létre kell hozni egy új Storage-fiókot a funkció használatához?**

Nem, a nem módosítható tárterületet bármilyen meglévő vagy újonnan létrehozott általános célú v2 vagy blob Storage-fiókkal használhatja. Ez a funkció a GPv2-és Blob Storage-fiókok blokk-Blobokkal való használatra készült. Általános célú v1-es Storage-fiókok nem támogatottak, de egyszerűen frissíthetők általános célú v2-re. Meglévő általános célú v1 Storage-fiók frissítésével kapcsolatos információkért lásd: [Storage-fiók frissítése](../common/storage-account-upgrade.md).

**Alkalmazhatok jogi és időalapú adatmegőrzési szabályzatot is?**

Igen, a tároló egyszerre rendelkezhet jogi és időalapú adatmegőrzési házirenddel is. A tárolóban lévő összes blob a megváltoztathatatlan állapotban marad mindaddig, amíg az összes jogi tartalékot nem törlik, még akkor is, ha a hatályos megőrzési idő lejárt. Ezzel szemben a Blobok nem módosítható állapotban maradnak, amíg a hatályos megőrzési idő le nem jár, még akkor is, ha az összes jogi tartalék törölve lett.

**Csak bírósági eljáráshoz vagy más felhasználási helyzetekben van jogi szabályozás?**

Nem, a jogi fenntartás csak a nem időalapú adatmegőrzési szabályhoz használt általános kifejezés. Nem szükséges kizárólag peres eljárással kapcsolatos eljáráshoz használni. A jogszabályi szabályzatok hasznosak a felülírás és a törlés letiltásához a fontos vállalati féreg-adatok védelméhez, ha a megőrzési időtartam ismeretlen. Vállalati szabályzatként használhatja a kritikus fontosságú féreg számítási feladatait, vagy átmeneti házirendként használhatja azt megelőzően, hogy az egyéni esemény eseményindítójának egy időalapú adatmegőrzési szabályzatot kell használnia. 

**El tudom távolítani a *zárolt* időalapú adatmegőrzési szabályzatot vagy a jogi megtartást?**

Csak a zárolt időalapú adatmegőrzési szabályzatok távolíthatók el a tárolóból. Az időalapú adatmegőrzési szabályzat zárolása után nem távolítható el; csak a tényleges megőrzési időszakra vonatkozó bővítmények engedélyezettek. A jogi megtartási címkék törölhetők. Ha az összes jogi címkét törli, a rendszer eltávolítja a jogi megtartást.

**Mi történik, ha megpróbálok törölni egy tárolót, amelyre *zárolt* időalapú adatmegőrzési szabályzat vagy jogi célú visszatartás vonatkozik?**

A tároló törlése művelet sikertelen lesz, ha legalább egy blob már létezik zárolt időalapú adatmegőrzési házirenddel vagy jogi megtartással. A tároló törlése művelet csak akkor lesz sikeres, ha nincs aktív megőrzési intervallummal rendelkező blob, és nincs jogi tartalék. A tároló törlése előtt törölnie kell a blobokat.

**Mi történik, ha megpróbálok törölni egy olyan WORM-tárolót tartalmazó tárfiókot, amelyre *zárolt* időalapú adatmegőrzési szabályzat vagy jogi célú visszatartás vonatkozik?**

A tárfiók törlése sikertelen lesz, ha legalább egy jogi célú visszatartással ellátott WORM-tárolót vagy aktív adatmegőrzési időtartammal rendelkező blobot tartalmaz. A Storage-fiók törlése előtt törölnie kell az összes férget tartalmazó tárolót. További információ a tárolók törléséről: előző kérdés.

**Áthelyezhetem az adatokat különböző blobrétegek között (gyakran, ritkán és alig használt rétegek), ha a blob nem módosítható állapotban van?**

Igen, a blob-rétegek beállítása paranccsal áthelyezheti az adategységeket a blob szintjeire, miközben a megfelelő, megváltoztathatatlan állapotban tartja az adategységeket. A nem módosítható tárolók a gyakori, ritka elérésű és archív blob-szinteken támogatottak.

**Mi történik, ha elmulasztok fizetni, és a megőrzési időtartam még nem járt le?**

Nem fizetés esetén a normál adatmegőrzési szabályzatok a Microsofttal kötött szerződés feltételei és kikötései szerint lesznek érvényesek.

**Létezik a funkció kipróbálására lehetőséget biztosító ingyenes próba- vagy türelmi időszak?**

Igen. Időalapú adatmegőrzési szabály létrehozásakor a rendszer *zárolt* állapotba kerül. Ebben az állapotban bármilyen módosítást elvégezhet a megőrzési időtartamon, például növelheti vagy csökkentheti a szabályzatot, és törölheti is azt. A szabályzat zárolása után a zárolva marad, amíg a megőrzési időtartam lejár. Ez a zárolt házirend megakadályozza a törlést és a módosítást a megőrzési időtartamra. Azt javasoljuk, hogy a *zárolt* állapotot kizárólag próbaverziós célokra használja, és a szabályzatot 24 órás időtartamon belül zárolja. Ezek a gyakorlatok segítenek a SEC 17a-4 (f) és más rendeletek betartásában.

**Használhatom a Soft delete szolgáltatást a nem módosítható blob-házirendek mellett?**

Igen. Az [Azure Blob Storage](storage-blob-soft-delete.md) -hoz készült Soft delete egy Storage-fiókban lévő összes tárolóra érvényes, függetlenül a jogi vagy időalapú adatmegőrzési szabályoktól. Javasoljuk, hogy a nem módosítható féreg-házirendek alkalmazása és megerősítése előtt a további védelem érdekében engedélyezze a Soft delete használatát. 

**Hol érhető el a funkció?**

A nem módosítható tárterület az Azure nyilvános, Kínában és kormányzati régióiban érhető el. Ha a nem módosítható tároló nem érhető el a régióban, forduljon a támogatási szolgálathoz és az e-mailekhez azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Minta PowerShell-kód

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következő PowerShell-parancsfájl a hivatkozás. Ez a szkript létrehoz egy új Storage-fiókot és-tárolót. Ezután bemutatja, hogyan állíthatja be és törölheti a jogcímeket, hogyan hozhat létre és zárolhat egy időalapú adatmegőrzési szabályzatot (más néven módosíthatatlansági szabályzat), és kiterjesztheti a megőrzési időt.

Az Azure Storage-fiók beállítása és tesztelése:

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

Jogi tartalékok beállítása és törlése:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Módosíthatatlansági szabályzatok létrehozása vagy frissítése:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Módosíthatatlansági szabályzatok beolvasása:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Módosíthatatlansági házirendek zárolása (Add-Force a kérés elvetéséhez):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Módosíthatatlansági házirendek kiterjesztése:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Zárolt módosíthatatlansági szabályzat eltávolítása (Add-Force a kérés elvetéséhez):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
