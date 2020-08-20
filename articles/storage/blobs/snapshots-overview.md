---
title: BLOB-Pillanatképek
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre egy blob írásvédett pillanatképét egy adott időpontban a blob-adatok biztonsági mentésére.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/19/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4c6c2774e0d71ec33449565efab797c040aa264f
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640599"
---
# <a name="blob-snapshots"></a>BLOB-Pillanatképek

A pillanatképek egy adott időpontban végrehajtott blob írásvédett verziója, amely egy adott időpontban történik.

> [!NOTE]
> A blob verziószámozása (előzetes verzió) egy másik módszert kínál a blob korábbi verzióinak karbantartására. További információ: [blob verziószámozása (előzetes verzió)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Tudnivalók a blob-pillanatképekről

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

A blob pillanatképe azonos az alap blobtal, azzal a különbséggel, hogy a blob URI-ja a blob URI-hoz fűzött **datetime** értékkel rendelkezik, hogy jelezze a pillanatkép készítésének időpontját. Ha például egy oldal blob URI `http://storagesample.core.blob.windows.net/mydrives/myvhd` -ja, a PILLANATKÉP URI-ja hasonló a következőhöz: `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Minden pillanatkép megosztja az alap blob URI-JÁT. Az alap blob és a pillanatkép közötti egyetlen különbség a hozzáfűzött **datetime** érték.
>

A Blobok tetszőleges számú pillanatképet tartalmazhatnak. A pillanatképek mindaddig megmaradnak, amíg explicit módon nem törlik őket, vagy a [blob törlési](/rest/api/storageservices/delete-blob) műveletének részeként. Az alap blobhoz társított Pillanatképek enumerálásával nyomon követheti az aktuális pillanatképeket.

Egy blob pillanatképének létrehozásakor a rendszer a blob rendszertulajdonságait a pillanatképre másolja, és ugyanazokat az értékeket. Az alap blob metaadatait a rendszer a pillanatképbe is másolja, kivéve, ha a létrehozáskor külön metaadatokat ad meg a pillanatképhez. A pillanatkép létrehozása után elolvashatja, másolhatja vagy törölheti, de nem módosítható.

Az alap blobhoz társított bérletek nem érintik a pillanatképet. Nem lehet bérletet beszerezni egy pillanatképen.

Egy VHD-fájl tárolja a virtuálisgép-lemezek aktuális információit és állapotát. Leválaszthat egy lemezt a virtuális gépről, vagy leállíthatja a virtuális gépet, majd pillanatképet készíthet a VHD-fájlról. Ezt a pillanatkép-fájlt később is használhatja a VHD-fájl lekéréséhez az adott időpontban, és újból létrehozhatja a virtuális gépet.

## <a name="understand-how-snapshots-accrue-charges"></a>A pillanatképek felmerülési módjának ismertetése

Pillanatkép létrehozása, amely egy blob írásvédett példánya, további adattárolási díjat eredményezhet a fiókjához. Az alkalmazás tervezésekor fontos tisztában lennie azzal, hogy ezek a díjak hogyan merülhetnek fel, így csökkentheti a költségeket.

### <a name="important-billing-considerations"></a>Fontos számlázási szempontok

A következő lista a pillanatképek létrehozásakor megfontolandó főbb pontokat tartalmazza.

- A Storage-fiók az egyedi blokkok vagy lapok díját terheli, függetlenül attól, hogy azok a blobban vagy a pillanatképben vannak-e. A fiókja nem számít fel további díjat a blobokhoz társított pillanatképekhez, amíg nem frissíti azt a blobot, amelyen alapulnak. Az alap blob frissítése után az a pillanatképtől eltér. Ebben az esetben az egyes Blobok vagy Pillanatképek egyedi blokkait vagy lapjait kell fizetnie.
- Ha egy blokkon belüli blokkot cserél le, a rendszer ezt a blokkot egy egyedi blokkként számítja fel. Ez akkor is igaz, ha a blokk ugyanazzal a blokk-AZONOSÍTÓval és ugyanazokkal az adatokkal rendelkezik, mint a pillanatképben. A blokk újbóli elkövetése után a rendszer az adott pillanatképtől eltér, és az adatokért kell fizetnie. Ugyanez a helyzet igaz egy olyan oldal blobján, amely azonos adattal frissült.
- Egy blokk blobjának frissítése egy olyan metódus meghívásával, amely felülírja a blob teljes tartalmát, a blob összes blokkját lecseréli. Ha az adott blobhoz pillanatkép van társítva, akkor az alap blobban és a pillanatképben lévő összes blokk már eltér egymástól, és a rendszer az összes blokkot felszámítja mindkét blobban. Ez akkor is igaz, ha az alap blobban lévő adatok és a pillanatkép azonos marad.
- Az Azure Blob service nem határozza meg, hogy két blokk tartalmaz-e azonos adathalmazt. Minden feltöltött és véglegesített blokk egyediként lesz kezelve, még akkor is, ha ugyanazokat az adatblokkokat és AZONOSÍTÓkat is tartalmazta. Mivel a díjak egyedi blokkokból állnak, fontos figyelembe venni, hogy a pillanatképet tartalmazó Blobok frissítése további egyedi blokkokat és további díjakat eredményez.

### <a name="minimize-costs-with-snapshot-management"></a>A költségek csökkentése a Snapshot Management szolgáltatással

Javasoljuk, hogy gondosan kezelje a pillanatképeket a további költségek elkerülése érdekében. Az alábbi ajánlott eljárásokat követve csökkentheti a pillanatképek tárolásához felmerülő költségeket:

- Egy blobhoz társított Pillanatképek törlése és újbóli létrehozása minden alkalommal, amikor frissíti a blobot, még akkor is, ha az alkalmazás megtervezése nem igényli a pillanatképek karbantartását. A blob Pillanatképek törlésével és újbóli létrehozásával biztosíthatja, hogy a blob és a pillanatképek ne legyenek elválasztva.
- Ha pillanatképeket tart fenn egy blobhoz, ne hívja meg azokat a metódusokat, amelyek felülírják a teljes blobot a blob frissítésekor. Ehelyett frissítse a lehető legkevesebb blokkot a költségek alacsonyra csökkentése érdekében.

### <a name="snapshot-billing-scenarios"></a>Pillanatkép-számlázási forgatókönyvek

A következő forgatókönyvek azt mutatják be, hogyan merülhetnek fel a díjak a blokkos blobok és a pillanatképek esetében.

#### <a name="scenario-1"></a>1\. példa

Az 1. forgatókönyvben az alap blob nem frissült a pillanatkép készítése után, ezért a díjak csak az 1., 2. és 3. egyedi blokkok esetében merülnek fel.

![Azure Storage-erőforrások](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>2\. példa

A 2. forgatókönyvben az alap blob frissült, de a pillanatkép nem. A 3. blokk frissült, annak ellenére, hogy ugyanazokat az adatokkal és ugyanazzal az AZONOSÍTÓval rendelkezik, nem ugyanaz, mint a 3. blokk a pillanatképben. Ennek eredményeképpen a fiók négy blokk után lesz felszámítva.

![Azure Storage-erőforrások](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>3\. példa

A 3. forgatókönyvben az alap blob frissült, de a pillanatkép nem. A 3. blokk lecserélve a 4-es blokkra az alap blobban, de a pillanatkép továbbra is a 3. blokkot tükrözi. Ennek eredményeképpen a fiók négy blokk után lesz felszámítva.

![Azure Storage-erőforrások](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>4\. példa

A 4. forgatókönyvben az alap blob teljesen frissítve lett, és az eredeti blokk egyikét sem tartalmazza. Ennek eredményeképpen a fiók minden nyolc egyedi blokk után díjat számít fel.

![Azure Storage-erőforrások](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Ne hívjon fel olyan metódusokat, amelyek felülírják a teljes blobot, hanem az egyes blokkok frissítésével a költségek alacsonyak maradnak.

## <a name="next-steps"></a>Következő lépések

- [BLOB-pillanatkép létrehozása és kezelése a .NET-ben](snapshots-manage-dotnet.md)
- [Azure-beli nem felügyelt VM-lemezek biztonsági mentése növekményes pillanatképekkel](../../virtual-machines/windows/incremental-snapshots.md)
