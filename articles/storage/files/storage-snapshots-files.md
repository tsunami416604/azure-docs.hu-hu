---
title: Az Azure Files megosztási pillanatképeinek áttekintése | Microsoft dokumentumok
description: A megosztáspillanatkép egy csak olvasható verziója egy Azure Files-megosztás, amely egy adott időpontban, a megosztás biztonsági másolatot készíthet.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333177"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Az Azure Files megosztási pillanatképeinek áttekintése 
Az Azure Files lehetővé teszi a fájlmegosztások megosztási pillanatképeinek készítését. A megosztási pillanatképek rögzítik a megosztási állapotot az adott időpontban. Ebben a cikkben bemutatjuk, milyen képességek megosztáspillanatképek nyújt, és hogyan lehet kihasználni őket az egyéni használati eset.

## <a name="when-to-use-share-snapshots"></a>Mikor kell használni a megosztási pillanatképeket?

### <a name="protection-against-application-error-and-data-corruption"></a>Az alkalmazáshibák és az adatsérülés elleni védelem
A fájlmegosztásokat használó alkalmazások olyan műveleteket hajtanak végre, mint az írás, az olvasás, a tárolás, az átvitel és a feldolgozás. Ha egy alkalmazás helytelenül van konfigurálva, vagy nem szándékos hiba kerül bevezetésre, véletlen felülírás vagy sérülés történhet néhány blokkban. Ezek a forgatókönyvek elleni védelem érdekében az új alkalmazáskód üzembe helyezése előtt megosztási pillanatképet készíthet. Ha az új központi telepítés hiba vagy alkalmazáshiba lép fel, visszatérhet az adott fájlmegosztáson lévő adatok egy korábbi verziójához. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Véletlen törlés ekkel vagy nem szándékos módosításokkal szembeni védelem
Képzelje el, hogy egy fájlmegosztásban lévő szövegfájlon dolgozik. A szövegfájl bezárása után a módosítások visszavonása megszűnik. Ezekben az esetekben vissza kell állítania a fájl egy korábbi verzióját. A megosztási pillanatképek segítségével helyreállíthatja a fájl korábbi verzióit, ha véletlenül átnevezték vagy törölték.

### <a name="general-backup-purposes"></a>Általános biztonsági mentési célok
Miután létrehozott egy fájlmegosztást, rendszeres időközönként létrehozhat egy megosztási pillanatképet a fájlmegosztásról, hogy azt adatok biztonsági mentéséhez használhassa. A megosztási pillanatkép, ha rendszeres időközönként, segít fenntartani a korábbi verziói az adatok, amelyek felhasználhatók a jövőbeli naplózási követelmények vagy a vész-helyreállítási.

## <a name="capabilities"></a>Funkciók
A megosztási pillanatkép az adatok egy adott időpontban, csak olvasható másolata. A REST API használatával pillanatképeket hozhat létre, törölhet és kezelhet. Ugyanazok a képességek érhetők el az ügyfélkönyvtárban, az Azure CLI-ben és az Azure Portalon is. 

Egy megosztás pillanatképeit a REST API és az SMB használatával is megtekintheti. A könyvtár vagy fájl verzióinak listáját lekérheti, és egy adott verziót közvetlenül meghajtóként csatlakoztathat (csak Windows rendszeren érhető el - [lásd: Korlátok](#limits)). 

A megosztáspillanatkép létrehozása után olvasható, másolható vagy törölhető, de nem módosítható. Nem másolhat egy teljes megosztáspillanatképet egy másik tárfiókba. Ezt a fájlt fájlonként, az AzCopy vagy más másolási mechanizmus használatával kell megtennie.

A megosztási pillanatkép-funkció a fájlmegosztás szintjén érhető el. A lekérés az egyes fájlok szintjén érhető el, hogy lehetővé tegye az egyes fájlok visszaállítását. A teljes fájlmegosztás t visszaállíthatja az SMB, a REST API, a portál, az ügyféltár vagy a PowerShell/CLI eszközhasználatával.

A fájlmegosztás megosztási pillanatképe megegyezik az alapfájl-megosztással. Az egyetlen különbség az, hogy a **DateTime** érték hozzáfűzve a megosztásuri-hoz, amely jelzi a megosztási pillanatkép készítésének időpontját. Ha például egy fájlmegosztási URI http://storagesample.core.file.windows.net/mysharea következőhöz hasonló:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

A megosztási pillanatképek mindaddig megmaradnak, amíg explicit módon nem törlődnek. A megosztáspillanatképek nem élhetik túl az alapfájlmegosztást. Az aktuális pillanatképek nyomon követéséhez az alapfájlmegosztáshoz társított pillanatképek számbavethetése. 

Amikor megosztási pillanatképet hoz létre egy fájlmegosztásról, a megosztás rendszertulajdonságaiban lévő fájlokat a rendszer ugyanazzal az értékkel másolja a megosztáspillanatképbe. Az alapfájlok és a fájlmegosztás metaadatai is átkerülnek a megosztás pillanatképébe, kivéve, ha a megosztás pillanatképéhez külön metaadatokat ad meg a megosztás pillanatképéhez létrehozáskor.

Nem törölhet olyan megosztási pillanatképeket tartalmazó megosztást, ha először törli az összes megosztási pillanatképet.

## <a name="space-usage"></a>Helyhasználat 
A megosztási pillanatképek növekményes jellegűek. Csak azok az adatok, amelyek a legutóbbi megosztási pillanatkép mentése után megváltoztak. Ez minimálisra csökkenti a megosztáspillanatkép létrehozásához szükséges időt, és megtakarítja a tárolási költségeket. Az objektumra, tulajdonságra vagy metaadat-frissítési műveletre irányuló írási műveleteket a rendszer a "módosított tartalom" részének számítja, és a megosztás pillanatképében tárolja. 

A tárhely megőrzése érdekében törölheti a megosztáspillanatképet arra az időszakra, amikor a lemorzsolódás volt a legmagasabb.

Annak ellenére, hogy a megosztási pillanatképek mentése növekményesen van mentve, csak a legutóbbi megosztási pillanatképet kell megőriznie a megosztás visszaállításához. Megosztáspillanatkép törlésekor csak az adott megosztáspillanatképegyedi adatai törlődnek. Az aktív pillanatképek tartalmazzák az összes olyan információt, amely az adatok böngészéséhez és visszaállításához szükséges (a megosztási pillanatkép készítésének időpontjától) az eredeti helyre vagy egy másik helyre. Az elem szintjén visszaállíthatja.

A pillanatképek nem számítanak bele az 5 TB-os megosztási korlátba. Nincs korlátozva, hogy mennyi helyet megosztási pillanatképek foglalnak el összesen. A tárfiók-korlátok továbbra is érvényesek.

## <a name="limits"></a>Korlátok
Az Azure Files által ma lehetővé tesz megosztási pillanatképek maximális száma 200. 200 megosztási pillanatkép után törölnie kell a régebbi megosztási pillanatképeket az újak létrehozásához. 

Nincs korlátozva, hogy az egyidejű hívások megosztási pillanatképek létrehozásához. Nincs korlátozva, hogy mennyi helyet oszthat meg egy adott fájlmegosztás pillanatképei. 

Ma nem lehet csatlakoztatni a megosztási pillanatképeket Linuxon. Ennek az az oka, hogy a Linux SMB-ügyfél nem támogatja a pillanatképek csatlakoztatását, mint a Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Adatok másolása megosztási pillanatképből egy megosztásra
A fájlokat tartalmazó műveletek másolása és a pillanatképek megosztása az alábbi szabályokat követi:

A fájlmegosztás pillanatfelvételében lévő egyes fájlokat átmásolhatja az alapmegosztásra vagy bármely más helyre. A fájl korábbi verzióit visszaállíthatja, vagy a teljes fájlmegosztást úgy állíthatja vissza, hogy fájlonként másol a megosztás pillanatképéből. A megosztási pillanatkép nem kerül elő léptetésre alapmegosztásra. 

A megosztáspillanatkép a másolás után érintetlen marad, de az alapfájlmegosztás felülíródik a megosztáspillanatképben rendelkezésre álló adatok másolatával. Az összes visszaállított fájl beleszámít a "megváltozott tartalomba".

A megosztáspillanatképben lévő fájlokat más néven más célhelyre másolhatja. Az eredményül kapott célfájl írható fájl, nem megosztási pillanatkép. Ebben az esetben az alapfájl-megosztás érintetlen marad.

Ha egy célfájlt egy másolattal felülírnak, az eredeti célfájlhoz társított megosztási pillanatképek érintetlenek maradnak.

## <a name="general-best-practices"></a>Általános ajánlott eljárások 
Amikor az Azure-on használja az infrastruktúrát, amikor csak lehetséges, automatizálja a biztonsági mentéseket az adatok helyreállításához. Az automatizált műveletek megbízhatóbbak, mint a manuális folyamatok, hozzájárulva az adatvédelem és a helyreállíthatóság javításához. Használhatja a REST API-t, az ügyfél SDK-t vagy a parancsfájlok automatizáláshoz.

A megosztáspillanatkép-ütemező telepítése előtt alaposan fontolja meg a megosztáspillanatkép gyakoriságát és a megőrzési beállításokat, hogy elkerülje a szükségtelen díjakat.

A megosztási pillanatképek csak fájlszintű védelmet nyújtanak. A megosztási pillanatképek nem akadályozzák meg a fat-finger törlést egy fájlmegosztási vagy tárfiókon. A tárfiók véletlen törlések elleni védelme érdekében zárolhatja a tárfiókot vagy az erőforráscsoportot.

## <a name="next-steps"></a>További lépések
- Megosztási pillanatképek használata a következő helyeken:
    - [Powershell](storage-how-to-use-files-powershell.md)
    - [parancssori felület](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Pillanatkép megosztása – gyakori kérdések](storage-files-faq.md#share-snapshots)
