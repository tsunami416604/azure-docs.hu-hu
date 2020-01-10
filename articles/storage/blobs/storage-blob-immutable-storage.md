---
title: Nem változtatható blob Storage – Azure Storage
description: Az Azure Storage szolgáltatás a blob (Object) tároló számára biztosít féreg (egyszer írható, olvasható) támogatást, amely lehetővé teszi a felhasználók számára, hogy a megadott intervallumban nem törölhető, nem módosítható állapotban tárolja az adattárolást.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 9d0919651842a6f6f935c9f1e338c9d335b80f47
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749163"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Üzleti szempontból kritikus fontosságú blob-alapú adattárolás tárolása a nem módosítható tárolóval

Az Azure Blob Storage nem módosítható tárolója lehetővé teszi, hogy a felhasználók üzleti szempontból kritikus fontosságú adatobjektumokat tároljanak egy FÉREGben (egyszer írható, olvasható) állapot. Ez az állapot a felhasználó által megadott intervallumban nem törölhető és nem módosítható adatvesztést tesz lehetővé. A megőrzési időtartam időtartama alatt a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. A nem módosítható tárterület az összes Azure-régióban az általános célú v2 és a blob Storage-fiókok esetében érhető el.

A jogcímek beállításával és törlésével, illetve a Azure Portal, a PowerShell vagy az Azure CLI használatával történő időalapú adatmegőrzési szabályzat létrehozásával kapcsolatos további információkért lásd: [módosíthatatlansági házirendek beállítása és kezelése a blob Storage](storage-blob-immutability-policies-manage.md)-hoz.

## <a name="about-immutable-blob-storage"></a>A nem változtatható blob Storage-ról

A nem módosítható tárterület segíti az egészségügyi szervezetet, a pénzügyi intézményeket és a kapcsolódó iparágakat&mdash;különösen a közvetítő kereskedő szervezeteknek&mdash;az adattárolást. A nem módosítható tárolók bármilyen forgatókönyvben is kihasználhatók a kritikus fontosságú adatmódosítások vagy Törlés elleni védelemhez.

Jellemző alkalmazási területek:

- **Jogszabályi megfelelőség**: az Azure Blob Storage-hoz tartozó nem módosítható tárterület segíti a szervezeteket a SEC 17a-4 (f), a CFTC 1.31 (d), a FINRA és más rendeletek esetében. A Cohasset által használt technikai tanulmány részletesen ismerteti, hogyan kezeli ezeket a szabályozási követelményeket a [Microsoft szolgáltatás megbízhatósági portálján](https://aka.ms/AzureWormStorage)keresztül. A [Azure biztonsági és adatkezelési központ](https://www.microsoft.com/trustcenter/compliance/compliance-overview) részletes információkat tartalmaz a megfelelőségi tanúsítványokról.

- **Biztonságos dokumentumok megőrzése**: az Azure Blob Storage nem módosítható tárolója biztosítja, hogy az adatok bármely felhasználó számára nem módosíthatók vagy törölhetők, beleértve a fiók rendszergazdai jogosultságokkal rendelkező felhasználókat is.

- **Jogi szabályozás**: az Azure Blob Storage-hoz tartozó nem módosítható tárolás lehetővé teszi a felhasználók számára, hogy a megfelelő időtartamig a jogszabályi vagy üzleti felhasználás szempontjából kritikus fontosságú adatokat tároljanak, amíg a mentességet el nem távolítják. Ez a funkció nem korlátozódik kizárólag a jogi használati esetekre, hanem eseményvezérelt vagy vállalati zárolásra is gondolhat, ahol szükség van az adatvédelemre az esemény-eseményindítók vagy a vállalati szabályzatok alapján.

A nem módosítható tároló a következő funkciókat támogatja:

- **[Időalapú adatmegőrzési szabályzatok támogatása](#time-based-retention-policies)** : a felhasználók házirendeket állíthatnak be az adatok egy adott intervallumra való tárolásához. Időalapú adatmegőrzési szabályzat beállításakor a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. A megőrzési időszak lejárta után a blobokat törölheti, de nem lehet felülírni.

- **[Jogszabályi szabályzatok támogatása](#legal-holds)** : Ha a megőrzési időköz nem ismert, a felhasználók megállíthatják, hogy a jogi tartalékok a nem módosítható adatok tárolására szolgálnak.  Ha be van állítva egy jogszabályi szabályzat, a Blobok létrehozhatók és olvashatók, de nem módosíthatók és nem törölhetők. Minden jogi Hold egy felhasználó által definiált alfanumerikus címkével (például egy eset-AZONOSÍTÓval, az esemény nevével stb.) van társítva, amelyet azonosító sztringként használ. 

- Az **összes blob-réteg támogatása**: a Worm-szabályzatok függetlenek az Azure Blob Storage-rétegtől, és az összes rétegre érvényesek: gyors, ritka és archív. Így a felhasználók a számítási feladatok szempontjából leginkább költségoptimalizált szinten tárolhatják adataikat, miközben továbbra sem tudják őket módosítani.

- **Tároló szintű konfiguráció**: a felhasználók időalapú adatmegőrzési szabályzatokat és a jogi megtartási címkéket is konfigurálhatják a tároló szintjén. A felhasználók egyszerű tárolószintű beállítások segítségével többek között létrehozhatnak és zárolhatnak időalapú adatmegőrzési szabályzatokat, meghosszabbíthatják a megőrzési időtartamot, és jogi célú visszatartást állíthatnak be vagy oldhatnak fel. Ezek a szabályzatok a tárolóban található összes blobra vonatkoznak, a meglévőkre és az újakra is.

- **Naplózási naplózás támogatása**: minden tároló tartalmaz egy házirend-naplót. Legfeljebb hét időalapú adatmegőrzési parancsot jelenít meg a zárolt időalapú adatmegőrzési házirendek esetében, és tartalmazza a felhasználói azonosítót, a parancs típusát, az időbélyegzőket és a megőrzési időt. A jogi célú visszatartások esetében a naplóbejegyzés tartalmazza a felhasználói azonosítót, a parancs típusát, az időbélyegeket és a jogi céllal történő zárolás címkéit. Ezt a naplót a házirend élettartama érdekében a SEC 17a-4 (f) szabályozási irányelveknek megfelelően megőrzi a rendszer. Az [Azure-tevékenység naplója](../../azure-monitor/platform/platform-logs-overview.md) az összes vezérlő síkja tevékenységének átfogóbb naplóját jeleníti meg; az [Azure diagnosztikai naplók](../../azure-monitor/platform/platform-logs-overview.md) engedélyezése és az adatsík-műveletek megtartása és megjelenítése. A felhasználó felelőssége, hogy gondoskodjon a naplók állandó tárolásáról, mivel szabályozási és egyéb célból is szükség lehet rájuk.

## <a name="how-it-works"></a>Működési elv

Az Azure Blob Storage nem módosítható tárolási funkciója két típusú WORM vagy nem módosítható szabályzatot támogat: az időalapú adatmegőrzést és a jogi célú visszatartást. Ha egy tárolón időalapú adatmegőrzési szabályzatot vagy jogi megtartást alkalmaz, az összes meglévő blob nem módosítható féreg állapotba kerül 30 másodpercnél kevesebb ideig. A tárolóba feltöltött összes új blob is a nem módosítható állapotba kerül. Ha az összes blobot áthelyezte a megváltoztathatatlan állapotba, a rendszer megerősíti a megváltoztathatatlan házirendet, és a nem módosítható tárolóban lévő meglévő és új objektumok összes felülírási vagy törlési művelete nem engedélyezett.

A tároló-és a Storage-fiók törlése nem engedélyezett, ha a tárolóban vagy a Storage-fiókban egy nem módosítható házirend által védett blob található. A tároló törlési művelete sikertelen lesz, ha legalább egy blob zárolt időalapú adatmegőrzési házirenddel vagy jogi megtartással van ellátva. A Storage-fiók törlési művelete sikertelen lesz, ha van legalább egy olyan féreg tárolója, amelynek van jogi korlátja, vagy egy blob aktív megőrzési intervallummal rendelkezik.

### <a name="time-based-retention-policies"></a>Időalapú adatmegőrzési szabályzatok

> [!IMPORTANT]
> Az időalapú adatmegőrzési szabályzatot *zárolni* kell ahhoz, hogy a blob megfelelő, megváltoztathatatlan (írási és törlési) állapotban legyen a SEC 17a-4 (f) és más szabályozási megfelelőség esetében. Javasoljuk, hogy a szabályzatot ésszerű időn belül, általában 24 óránál rövidebb ideig zárolja. Az alkalmazott időalapú adatmegőrzési szabályzat kezdeti állapota *zárolva*van, így a zárolása előtt tesztelheti a szolgáltatást, és módosíthatja a szabályzatot. Míg a *zárolt* állapot módosíthatatlansági védelmet biztosít, a rövid távú szolgáltatásokra vonatkozó kísérletektől eltérő célra nem ajánlott a *feloldva* állapotot használni. 

Ha egy tárolón időalapú adatmegőrzési szabályt alkalmaz, a tárolóban lévő összes blob a *tényleges* megőrzési időtartamig nem változtatható állapotban marad. A meglévő blobok esetében a tényleges adatmegőrzési időtartam megegyezik a blob létrehozási ideje és a felhasználó által megadott megőrzési intervallum különbözetével.

Az új blobok esetében az adatmegőrzési időtartam egyenlő a felhasználó által megadott adatmegőrzési intervallummal. Mivel a felhasználóknak lehetőségük van meghosszabbítani az adatmegőrzési időtartamot, a nem módosítható tárolás a felhasználó által legutóbb megadott adatmegőrzési időtartamot fogja használni a tényleges megőrzési időtartam kiszámolásakor.

Tegyük fel például, hogy egy felhasználó egy időalapú adatmegőrzési szabályzatot hoz létre öt év megőrzési időtartammal. A tárolóban meglévő blob, a _testblob1_egy éve jött létre. A _testblob1_ érvényes megőrzési ideje négy év. Amikor új blobot töltenek fel a tárolóba, a rendszer öt évig az új blob tényleges megőrzési időszakát _testblob2_.

A kinyitott időalapú adatmegőrzési szabályzat csak a szolgáltatások tesztelésére ajánlott, és a házirendet zárolni kell ahhoz, hogy meg lehessen felelni a SEC 17a-4 (f) és más szabályozási megfelelőségnek. Az időalapú adatmegőrzési szabályzat zárolása után a szabályzat nem távolítható el, és a tényleges megőrzési időtartam legfeljebb öt nő lehet. Az időalapú adatmegőrzési szabályzatok beállításával és zárolásával kapcsolatos további információkért lásd: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](storage-blob-immutability-policies-manage.md).

Az adatmegőrzési szabályokra az alábbi korlátozások vonatkoznak:

- A Storage-fiók esetében a zárolt, időalapú, rögzített házirendekkel rendelkező tárolók maximális száma 1 000.
- A minimális megőrzési időköz egy nap. A maximális érték 146 000 nap (400 év).
- Tároló esetén a zárolt időalapú megváltoztathatatlan házirendek megőrzési időtartamának meghosszabbítására szolgáló szerkesztési adatok maximális száma 5.
- Tároló esetén a zárolt szabályzatok számára legfeljebb hét időalapú adatmegőrzési szabály van megtartva.

### <a name="legal-holds"></a>Jogi célú visszatartások

A jogi szabályozás beállításakor minden meglévő és új blob nem módosítható állapotban marad mindaddig, amíg nem törli a jogi megtartást. A jogcímek beállításával és törlésével kapcsolatos további információkért lásd: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](storage-blob-immutability-policies-manage.md).

Egy tároló egyszerre rendelkezhet jogi és időalapú adatmegőrzési házirenddel is. Az adott tárolóban található összes blob mindaddig nem módosítható állapotban marad, amíg az összes jogi célú visszatartást fel nem oldják, még akkor is, ha azok tényleges adatmegőrzési időtartama lejár. A blobok továbbá mindaddig nem módosítható állapotban maradnak, amíg a tényleges megőrzési időtartamuk le nem jár, még a jogi célú visszatartások feloldását követően is.

A következő táblázat a blob Storage-műveletek azon típusait mutatja be, amelyek a különböző változtathatatlan forgatókönyvek esetében le vannak tiltva. További információkért tekintse meg az [Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentációját.

|Alkalmazási helyzet  |BLOB állapota  |A blob-műveletek nem engedélyezettek  |
|---------|---------|---------|
|A blob tényleges adatmegőrzési időtartama még nem járt le és/vagy jogi célú visszatartás van érvényben     |Nem módosítható: törlés- és írásvédett         | Helyezze az<sup>1. blobot</sup>, helyezze<sup>az 1.</sup>blokkot, az<sup>1</sup>., a tároló törlése, a blob törlése, a blob metaadatainak beállítása, a Put oldal, a blob tulajdonságainak beállítása, a pillanatkép-blob, a növekményes másolási blob, a Letiltás         |
|A blobon beállított tényleges megőrzési időtartam lejárt     |Csak írásvédett (a törlési műveletek engedélyezettek)         |Helyezze az 1<sup>. blobot, helyezze</sup>az 1<sup>., az</sup><sup>1</sup>. blokkot, a blob metaadatainak beállítása, a Put oldal, a blob tulajdonságainak beállítása, a pillanatkép-blob, a növekményes másolási blob, a blokk hozzáfűzése         |
|Minden jogi eszköz törölve van, és a tárolón nincs megadva időalapú adatmegőrzési szabály     |Változtatható         |None         |
|Nincs létrehozva féreg-házirend (időalapú megőrzés vagy jogi megtartási idő)     |Változtatható         |None         |

<sup>1</sup> az alkalmazás lehetővé teszi, hogy ezek a műveletek egyszer új blobot hozzanak létre. Egy nem módosítható tárolóban lévő blob elérési útban lévő összes további felülírási művelet nem engedélyezett.

A következő korlátozások érvényesek a jogi részesedésre:

- A Storage-fiók esetében a jogszabályi megtartási beállítással rendelkező tárolók maximális száma 1 000.
- A tárolók esetében a jogi megtartó címkék maximális száma 10.
- A jogi megtartási címke minimális hossza három alfanumerikus karakter. A maximális hossz 23 alfanumerikus karakter.
- A tárolók esetében a szabályzat időtartama alatt legfeljebb 10 jogszabályi szabályzatot tartalmazó napló marad.

## <a name="pricing"></a>Díjszabás

A szolgáltatás használata nem jár további díjszabással. A megváltoztathatatlan adatkezelési díjak ugyanúgy változhatnak, mint a változékony adatszolgáltatások. Az Azure Blob Storage szolgáltatás díjszabását az [Azure Storage díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/storage/blobs/)tekintheti meg.

## <a name="faq"></a>Gyakori kérdések

**Megadhatja a féreg megfelelőségének dokumentációját?**

Igen. A megfelelőség dokumentálása érdekében a Microsoft megőrizte a nyilvántartások kezelésével és az Cohasset-társításokkal foglalkozó vezető független értékelő vállalatot, amellyel kiértékelheti a nem módosítható blob-tárolókat, és megfelel a pénzügyi szolgáltatások iparága. A Cohasset ellenőrizte, hogy a nem módosítható blob-tároló, amikor az időalapú Blobok egy féreg állapotában való megőrzésére szolgál, megfelel a CFTC-szabály 1.31 (c)-(d), a FINRA szabály 4511-es és a SEC szabály 17a-4. A Microsoft ezt a szabályt célozza meg, mivel a pénzügyi intézmények számára a rekordok megőrzésére leginkább jellemző útmutatást jelentik. A Cohasset-jelentés a [Microsoft szolgáltatás-Adatvédelmi központban](https://aka.ms/AzureWormStorage)érhető el. Ha a Microsoft igazolást kér a féreg megfelelőségéről, forduljon az Azure ügyfélszolgálatához.

**A szolgáltatás csak a Blobok blokkolására, illetve a Blobok oldalára és hozzáfűzésére is vonatkozik?**

A nem módosítható tároló bármely blob-típussal használható, mert a tároló szintjén van beállítva, de javasoljuk, hogy a féreg olyan tárolókat használjon, amelyek főleg a blokk blobokat tárolják. A blokk Blobokkal ellentétben az új lapokat tartalmazó blobokat és a hozzáfűzési blobokat a féreg-tárolón kívül kell létrehozni, majd az-ba kell másolni. Miután átmásolta ezeket a blobokat egy férget tárolóba, a hozzáfűzési blobhoz vagy egy oldal blobjának módosításaihoz való további *Hozzáfűzés* nem engedélyezett. Ha olyan tárolón állít be egy WORM Policy-szabályzatot, amely minden aktív virtuális gép számára tárolja a VHD-ket (blobokat), akkor a rendszer nem veszi igénybe a virtuálisgép-lemez zárolását.

**Létre kell hozni egy új Storage-fiókot a funkció használatához?**

Nem, a nem módosítható tárterületet bármilyen meglévő vagy újonnan létrehozott általános célú v2-vagy blob Storage-fiókkal használhatja. Ez a funkció a GPv2 és a blob Storage-fiókokban lévő blokkos Blobokkal való használatra szolgál. Az általános célú v1 Storage-fiókok nem támogatottak, de egyszerűen frissíthetők az általános célú v2-re. Egy meglévő általános célú v1 Storage-fiók frissítésével kapcsolatos információkért lásd: [Storage-fiók frissítése](../common/storage-account-upgrade.md).

**Alkalmazhatok jogi és időalapú adatmegőrzési szabályzatot is?**

Igen, a tároló egyszerre rendelkezhet jogi és időalapú adatmegőrzési házirenddel is. Az adott tárolóban található összes blob mindaddig nem módosítható állapotban marad, amíg az összes jogi célú visszatartást fel nem oldják, még akkor is, ha azok tényleges adatmegőrzési időtartama lejár. A blobok továbbá mindaddig nem módosítható állapotban maradnak, amíg a tényleges megőrzési időtartamuk le nem jár, még a jogi célú visszatartások feloldását követően is.

**Csak bírósági eljáráshoz vagy más felhasználási helyzetekben van jogi szabályozás?**

Nem, a jogi fenntartás csak a nem időalapú adatmegőrzési szabályhoz használt általános kifejezés. Nem szükséges kizárólag peres eljárással kapcsolatos eljáráshoz használni. A jogszabályi szabályzatok hasznosak a felülírás és a törlés letiltásához a fontos vállalati féreg-adatok védelméhez, ha a megőrzési időtartam ismeretlen. Vállalati szabályzatként használhatja a kritikus fontosságú féreg számítási feladatait, vagy átmeneti házirendként használhatja azt megelőzően, hogy az egyéni esemény eseményindítójának egy időalapú adatmegőrzési szabályzatot kell használnia. 

**El tudom távolítani a _zárolt_ időalapú adatmegőrzési szabályzatot vagy a jogi megtartást?**

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

A nem módosítható tárterület az Azure nyilvános, Kínában és kormányzati régióiban érhető el. Ha a nem módosítható tároló nem érhető el a régióban, forduljon az ügyfélszolgálathoz és az e-mail-azurestoragefeedback@microsoft.comhoz.

## <a name="next-steps"></a>Következő lépések

[BLOB Storage-módosíthatatlansági szabályzatok beállítása és kezelése](storage-blob-immutability-policies-manage.md)