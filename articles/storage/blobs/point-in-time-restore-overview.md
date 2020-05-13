---
title: Időponthoz való visszaállítás a blokk Blobok számára (előzetes verzió)
titleSuffix: Azure Storage
description: A blokkos Blobok időponthoz való visszaállítása védelmet nyújt a véletlen törléssel vagy a sérüléssel szemben azáltal, hogy lehetővé teszi a Storage-fiókok előző állapotba való visszaállítását egy adott időpontban.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 66682e953e4e262604d1b0c07720ebaab5995364
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195220"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Időponthoz való visszaállítás a blokk Blobok számára (előzetes verzió)

Az időponthoz való visszaállítás védelmet nyújt a véletlen törlés vagy a sérülés ellen azáltal, hogy lehetővé teszi a blob-adatblokkok korábbi állapotba való visszaállítását. Az időponthoz való visszaállítás olyan esetekben hasznos, amikor egy felhasználó vagy alkalmazás véletlenül törli az adatvesztést, vagy ha egy alkalmazáshiba sérült az adatok között. Az időponthoz való visszaállítás olyan tesztelési forgatókönyveket is lehetővé tesz, amelyekben a további tesztek futtatása előtt egy ismert állapotba kell visszaállítani az adatkészletet.

Ha szeretné megtudni, hogyan engedélyezhető az időponthoz tartozó visszaállítás egy Storage-fiókhoz, tekintse meg az időponthoz való visszaállítást a [blokkos Blobok számára (előzetes verzió)](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Az időpontra történő visszaállítás működése

Az időponthoz tartozó visszaállítás engedélyezéséhez létre kell hoznia egy felügyeleti szabályzatot a Storage-fiókhoz, és meg kell adnia egy megőrzési időtartamot. A megőrzési időszak alatt visszaállíthatja a blokk blobokat a jelen állapotból egy korábbi időpontban lévő állapotba.

Egy időponthoz tartozó visszaállítás kezdeményezéséhez hívja meg a blob- [tartományok visszaállítása](/rest/api/storagerp/storageaccounts/restoreblobranges) műveletet, és adja meg a visszaállítási pontot UTC idő szerint. Megadhatja a visszaállítandó tároló-és blob-nevek lexicographical, vagy kihagyhatja a tartományt a Storage-fiókban lévő összes tároló visszaállításához. A **blob-tartományok visszaállítása** művelet egy visszaállítási azonosítót ad vissza, amely egyedileg azonosítja a műveletet.

Az Azure Storage elemzi a megadott Blobok összes módosítását a kért visszaállítási pont között, az UTC időpontban és a jelen pillanatban megadva. A visszaállítási művelet atomi, így az összes módosítás visszaállításával vagy meghibásodásával teljesen sikeres lesz. Ha vannak olyan Blobok, amelyek nem állíthatók vissza, a művelet meghiúsul, és az érintett tárolók olvasási és írási műveletei folytatódnak.

Ha visszaállítási műveletet kér, az Azure Storage blokkolja az adatműveleteket a művelet időtartamára visszaállított tartományban lévő blobokban. Az olvasási, írási és törlési műveletek blokkolva vannak az elsődleges helyen. A másodlagos hely olvasási műveletei a visszaállítási művelet során folytatódnak, ha a Storage-fiók földrajzilag replikálódik.

Egyszerre csak egy visszaállítási műveletet lehet futtatni a Storage-fiókban. A visszaállítási művelet nem szakítható meg, ha folyamatban van, de egy második visszaállítási művelet is végrehajtható az első művelet visszavonásához.

Egy adott időponthoz tartozó visszaállítás állapotának megtekintéséhez hívja meg a visszaállítási **állapot lekérése** műveletet a **blob-tartományok visszaállítása** művelet által VISSZAadott visszaállítási azonosítóval.

Ne feledje, hogy a visszaállítási műveletekre a következő korlátozások vonatkoznak:

- Egy olyan blokk, amely egy [put blokkon](/rest/api/storageservices/put-block) keresztül lett feltöltve, vagy az [URL-címről](/rest/api/storageservices/put-block-from-url), de nem a [put blokk](/rest/api/storageservices/put-block-list)használatával véglegesítve van, nem része a blobnak, ezért a visszaállítási művelet részeként nem lesz visszaállítva.
- Az aktív bérlettel rendelkező Blobok nem állíthatók vissza. Ha egy aktív bérlettel rendelkező blob szerepel a visszaállítani kívánt Blobok tartományában, a visszaállítási művelet atomi módon sikertelen lesz.
- A pillanatképeket a rendszer nem hozza létre vagy törli a visszaállítási művelet részeként. A rendszer csak az alap blobot állítja vissza az előző állapotába.
- Ha egy blob a jelen pillanatban és a visszaállítási pont közötti időszakban a gyors és a lassú elérési szint között mozgott, a blob vissza lesz állítva az előző szintjére. Az archiválási szintre áthelyezett Blobok azonban nem lesznek visszaállítva.

> [!CAUTION]
> Az időponthoz való visszaállítás támogatja a csak blokkos Blobok műveleteinek visszaállítását. A tárolók műveletei nem állíthatók vissza. Ha töröl egy tárolót a Storage-fiókból úgy, hogy meghívja a tároló [törlése](/rest/api/storageservices/delete-container) műveletet az időponthoz tartozó visszaállítási előzetes verzióban, a tároló nem állítható vissza visszaállítási művelettel. Az előzetes verzióban a tároló törlése helyett törölje az egyes blobokat, ha vissza szeretné állítani őket.

### <a name="prerequisites-for-point-in-time-restore"></a>Az időponthoz tartozó visszaállítás előfeltételei

Az időponthoz tartozó visszaállításhoz a következő Azure Storage-funkciók engedélyezése szükséges:

- [Helyreállítható törlés](soft-delete-overview.md)
- [Hírcsatorna módosítása (előzetes verzió)](storage-blob-change-feed.md)
- [BLOB verziószámozása (előzetes verzió)](versioning-overview.md)

Engedélyezze ezeket a szolgáltatásokat a Storage-fiókhoz az időponthoz tartozó visszaállítás engedélyezése előtt. Győződjön meg arról, hogy az engedélyezése előtt regisztrálja az adatmódosítási hírcsatorna és a blob verziószámozási előzetes verzióját.

### <a name="retention-period-for-point-in-time-restore"></a>Időponthoz tartozó visszaállítás megőrzési időtartama

Amikor engedélyezi az időponthoz való visszaállítást egy Storage-fiókhoz, meg kell adnia egy megőrzési időtartamot. A Storage-fiókban lévő Blobok a megőrzési időszak alatt visszaállíthatók.

A megőrzési idő az időponthoz tartozó visszaállítás engedélyezésekor kezdődik. Ne feledje, hogy a Blobok nem állíthatók vissza a megőrzési időszak kezdete előtt állapotba. Ha például az 1. május 1-től engedélyezte az időponthoz való visszaállítást 30 nap megtartásával, akkor a május 15-én legfeljebb 15 napig állíthatja vissza. Június 1-jén az adatok 1 és 30 nap között állíthatók vissza.

Az időponthoz tartozó visszaállítás megőrzési időtartamának legalább egy nappal kisebbnek kell lennie a helyreállítható törléshez megadott megőrzési időtartamnál. Ha például a helyreállítható törlés megőrzési időtartama 7 nap, akkor az időponthoz tartozó visszaállítás megőrzési időtartama 1 és 6 nap között lehet.

### <a name="permissions-for-point-in-time-restore"></a>Az időponthoz való visszaállításhoz szükséges engedélyek

A visszaállítási művelet elindításához az ügyfélnek írási engedéllyel kell rendelkeznie a Storage-fiókban lévő összes tárolóhoz. Ha engedélyeket szeretne adni a Azure Active Directory (Azure AD) szolgáltatással való visszaállítási művelet engedélyezéséhez, rendelje hozzá a **Storage-fiók közreműködői** szerepkört a rendszerbiztonsági tag számára a Storage-fiók, az erőforráscsoport vagy az előfizetés szintjén.

## <a name="about-the-preview"></a>Az előzetes verzió ismertetése

Az időponthoz való visszaállítás csak az általános célú v2-alapú Storage-fiókok esetében támogatott. Csak a gyakori és ritka elérésű hozzáférési szinteken lévő adatok állíthatók vissza az időponthoz tartozó visszaállítással.

A következő régiók támogatják az időponthoz történő visszaállítást az előzetes verzióban:

- Közép-Kanada
- Kelet-Kanada
- Közép-Franciaország

Az előzetes verzió az alábbi korlátozásokat tartalmazza:

- A prémium blokkos Blobok visszaállítása nem támogatott.
- A Blobok archiválási szinten való visszaállítása nem támogatott. Ha például a forró rétegben lévő blobot két nappal ezelőtt áthelyezték az archiválási rétegbe, és a visszaállítási művelet három nappal ezelőtt visszaállítja a-t, a blob nem lesz visszaállítva a forró szintre.
- Azure Data Lake Storage Gen2 lapos és hierarchikus névterek visszaállítása nem támogatott.
- A Storage-fiókok ügyfél által megadott kulcsokkal történő visszaállítása nem támogatott.

> [!IMPORTANT]
> Az időponthoz tartozó visszaállítás előzetes verziója csak nem éles használatra készült. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.

### <a name="register-for-the-preview"></a>Regisztráljon az előzetes verzióra

Az előzetes verzióra való regisztráláshoz futtassa a következő parancsokat a Azure PowerShellról:

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="check-registration-status"></a>Regisztráció állapotának bejelölése

A regisztráció állapotának megtekintéséhez futtassa a következő parancsokat:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed
```

## <a name="pricing-and-billing"></a>Árak és számlázás

Az időponthoz való visszaállítás számlázása a visszaállítási művelet végrehajtásához feldolgozott adatok mennyiségétől függ. A feldolgozott adatmennyiség a visszaállítási pont és a jelen pillanatban történt változások száma alapján történik. Tegyük fel például, hogy a blob-adat tárolási fiókban való letiltásának viszonylag állandó változása miatt az 1 nap múlva visszaadott visszaállítási művelet 1 – 10 nap múlva vissza fog térni a visszaállításra.

A visszaállítási művelet költségeit a helyreállítási időszak során módosított adatmennyiség becsléséhez tekintse át a változási hírcsatorna naplójában. Ha például a változási csatorna megőrzési időtartama 30 nap, és a változási csatorna mérete 10 MB, akkor a korábbi 10 napos időpontra való visszaállítás körülbelül egyharmadát veszi igénybe az adott régióban egy LRS-fiókhoz. A korábban 27 nappal korábbi időpontra történő visszaállítás a felsorolt díjak körülbelül kilenc tizedét fogja kifizetni.

Az időponthoz tartozó visszaállítás díjszabásáról további információt a [Blobok díjszabásának blokkolása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakörben talál.

## <a name="ask-questions-or-provide-feedback"></a>Kérdések feltevése vagy visszajelzés küldése

Ha kérdéseket szeretne feltenni az időponthoz tartozó visszaállítás előzetes verziójával kapcsolatban, vagy visszajelzést szeretne küldeni, forduljon a Microsofthoz pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>További lépések

- [Időponthoz való visszaállítás engedélyezése és kezelése a blokk Blobok számára (előzetes verzió)](point-in-time-restore-manage.md)
- [A hírcsatorna-támogatás módosítása az Azure Blob Storage (előzetes verzió)](storage-blob-change-feed.md)
- [A Blobok helyreállítható törlésének engedélyezése](soft-delete-enable.md)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)
