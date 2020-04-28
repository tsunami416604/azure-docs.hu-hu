---
title: A Azure Files megosztási pillanatképének áttekintése | Microsoft Docs
description: A megosztási pillanatkép egy Azure Files-megosztás írásvédett verziója, amelyet egy adott időpontban végeznek el a megosztás biztonsági mentésének módját illetően.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e09887b8000a0aeb52879d5306bc0a00da5141f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176141"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Az Azure Files megosztási pillanatképeinek áttekintése 
Azure Files lehetővé teszi a fájlmegosztás pillanatképének megosztását. A megosztási Pillanatképek a megosztás állapotát az adott időpontban rögzítik. Ebben a cikkben bemutatjuk, hogy milyen képességeket oszthat meg a pillanatképek, és hogyan használhatja fel őket az egyéni használati esetekben.

## <a name="when-to-use-share-snapshots"></a>Mikor kell használni a megosztási pillanatképeket

### <a name="protection-against-application-error-and-data-corruption"></a>Az alkalmazás hibája és az adatsérülés elleni védelem
A fájlmegosztást használó alkalmazások olyan műveleteket hajtanak végre, mint például az írás, az olvasás, a tárolás, az átvitel és a feldolgozás. Ha egy alkalmazás helytelenül van konfigurálva, vagy egy nem szándékos hiba van bevezetve, akkor a véletlen felülírás vagy a sérülés néhány blokkban történhet. Az ilyen helyzetek elleni védelem érdekében az új alkalmazás kódjának üzembe helyezése előtt megtekintheti a megosztási pillanatképet. Ha az új központi telepítés során hiba vagy alkalmazáshiba van bevezetve, visszatérhet az adott fájlmegosztás adatainak egy korábbi verziójához. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Véletlen törlések vagy nem szándékolt módosítások elleni védelem
Képzelje el, hogy egy fájlmegosztás szövegfájljában dolgozik. A szövegfájl bezárása után elveszíti a módosítások visszavonásának lehetőségét. Ezekben az esetekben a fájl egy korábbi verzióját kell helyreállítani. A megosztási Pillanatképek használatával visszaállíthatja a fájl korábbi verzióit, ha véletlenül átnevezték vagy törölték.

### <a name="general-backup-purposes"></a>Általános biztonsági mentési célok
Miután létrehozott egy fájlmegosztást, rendszeres időközönként létrehozhat egy megosztási pillanatképet a fájlmegosztás számára az adatok biztonsági mentésére való használatra. A megosztási pillanatkép, amely rendszeres időközönként elvégezte az adatok korábbi verzióinak fenntartását, amelyek a jövőbeli auditálási követelményekhez vagy a vész-helyreállításhoz használhatók.

## <a name="capabilities"></a>Funkciók
A megosztási pillanatkép az adatok egy időponthoz tartozó, írásvédett másolata. A pillanatképeket a REST API használatával hozhatja létre, törölheti és kezelheti. Ugyanezek a képességek az ügyféloldali függvénytárban, az Azure CLI-ben és a Azure Portal is elérhetők. 

A megosztások pillanatképeit a REST API és az SMB használatával is megtekintheti. Lekérheti a címtár vagy a fájl verzióinak listáját, és közvetlenül meghajtóként is csatlakoztathat egy adott verziót (csak Windows – lásd: [korlátozások](#limits)). 

A megosztási pillanatkép létrehozása után a fájl olvasható, másolható vagy törölhető, de nem módosítható. Nem másolhat teljes megosztási pillanatképet egy másik Storage-fiókba. Ezt a fájlt fájl alapján kell végrehajtania a AzCopy vagy más másolási mechanizmusok használatával.

A megosztott Pillanatképek funkció a fájlmegosztás szintjén van megadva. A lekérést az egyes fájlok szintjén kell megadnia, hogy lehetővé váljon az egyes fájlok visszaállítása. A teljes fájlmegosztást az SMB, a REST API, a portál, az ügyféloldali kódtár vagy a PowerShell/CLI-eszközök használatával állíthatja vissza.

A fájlmegosztás megosztási pillanatképe megegyezik az alapfájl-megosztással. Az egyetlen különbség, hogy egy **datetime** érték van hozzáfűzve a megosztási URI-hoz, hogy jelezze a megosztási pillanatkép készítésének időpontját. Ha például a fájlmegosztás URI-ja http:\//storagesample.Core.file.Windows.net/MyShare, a megosztási pillanatkép URI-ja a következőhöz hasonló:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

A pillanatképek megosztása mindaddig megmarad, amíg a rendszer explicit módon nem törli őket. A megosztási pillanatkép nem tudja megélni az alapfájl megosztását. Az alapfájl-megosztáshoz társított Pillanatképek enumerálásával nyomon követheti az aktuális pillanatképeket. 

Amikor megosztási pillanatképet hoz létre egy fájlmegosztás számára, a megosztás rendszertulajdonságaiban lévő fájlokat a rendszer a megosztási pillanatképre másolja, és ugyanazokat az értékeket használja. A rendszer az alapfájlokat és a fájlmegosztás metaadatait is átmásolja a megosztási pillanatképbe, hacsak nem ad meg külön metaadatokat a megosztási pillanatképhez a létrehozásakor.

A megosztási pillanatképekkel rendelkező megosztásokat nem törölheti, ha először törli az összes megosztási pillanatképet.

## <a name="space-usage"></a>Lemezterület-használat 
A megosztási Pillanatképek növekményes jellegűek. Csak a legutóbbi megosztási pillanatkép mentése után módosult adatok lettek mentve. Ez minimálisra csökkenti a megosztási pillanatkép létrehozásához szükséges időt, és menti a tárolási költségeket. Az objektumra vagy tulajdonságra vagy metaadat-frissítési műveletre vonatkozó bármilyen írási művelet beleszámít a "megváltozott tartalom" felé, és a megosztási pillanatképben tárolódik. 

A tárterület megőrzése érdekében törölheti a megosztási pillanatképet arra az időszakra, amikor a változás a legmagasabb volt.

Annak ellenére, hogy a megosztási Pillanatképek Növekményesen vannak mentve, csak a legutóbbi megosztási pillanatképet kell megőriznie a megosztás visszaállításához. Ha töröl egy megosztási pillanatképet, a rendszer csak az adott megosztási pillanatképhez egyedi adatok törlődnek. Az aktív Pillanatképek tartalmazzák az összes olyan információt, amelyre szüksége van az adatok tallózásához és visszaállításához (a megosztási pillanatkép készítésének időpontjáról) az eredeti helyre vagy egy másik helyre. Az elemek szintjén állítható vissza.

A pillanatképek nem számítanak bele az 5 TB-os megosztási korlátba. A tárolóhelyek megosztási pillanatképei összesen nem megengedettek. A Storage-fiókra vonatkozó korlátozások továbbra is érvényesek.

## <a name="limits"></a>Korlátok
A Azure Files ma 200-es megosztási Pillanatképek maximális száma. A 200-es megosztási pillanatképek létrehozása után törölni kell a régebbi megosztási pillanatképeket, hogy újakat hozzon létre. 

A megosztási Pillanatképek létrehozásának egyidejű hívása nincs korlátozva. Az adott fájlmegosztás pillanatképeit használó tárterületnek nincs korlátja. 

A megosztási Pillanatképek jelenleg nem csatlakoztathatók Linux rendszeren. Ennek az az oka, hogy a Linux SMB-ügyfél nem támogatja az olyan csatlakoztatási pillanatképeket, mint a Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Adatok másolása egy megosztási pillanatképből
A fájlokat és megosztási pillanatképeket tartalmazó másolási műveletek a következő szabályokat követik:

A fájlmegosztás pillanatképében lévő egyes fájlokat átmásolhatja az alapszintű megosztásra vagy bármely más helyre. A fájlok egy korábbi verzióját visszaállíthatja, vagy visszaállíthatja a teljes fájlmegosztást a megosztási pillanatképből a fájl fájlba másolásával. A megosztási pillanatkép nincs előléptetve az alapszintű megosztásra. 

A megosztási pillanatkép a másolás után érintetlen marad, de az alap fájlmegosztás felül van írva a megosztási pillanatképben elérhető adatok másolatával. Az összes visszaállított fájl a "módosított tartalom" értékre változik.

A megosztási pillanatképekben lévő fájlokat más néven is átmásolhatja egy másik célhelyre. Az eredményül kapott célfájl egy írható fájl, nem megosztási pillanatkép. Ebben az esetben az alapfájl megosztása érintetlen marad.

Ha a célfájl egy másolattal van felülírva, az eredeti célhelyhez társított megosztási Pillanatképek érintetlenek maradnak.

## <a name="general-best-practices"></a>Általános ajánlott eljárások 
Amikor infrastruktúrát futtat az Azure-ban, automatizálja az Adathelyreállítás biztonsági mentését, amikor csak lehetséges. Az automatizált műveletek sokkal megbízhatóbbak, mint a manuális folyamatok, ami segít az adatvédelem és a helyreállítás javításában. Az automatizáláshoz használhatja a REST API, az ügyfél-SDK-t vagy a parancsfájlok futtatását.

A megosztási pillanatkép-ütemező üzembe helyezése előtt körültekintően vegye figyelembe a megosztás pillanatképének gyakoriságát és a megőrzési beállításokat, hogy elkerülje a szükségtelen költségek felmerülését.

A megosztási Pillanatképek csak a fájl szintű védelmet biztosítják. A megosztási Pillanatképek nem akadályozzák meg a FAT-Finger törlését egy fájlmegosztás vagy egy Storage-fiók esetében. A Storage-fiók véletlen törlések elleni védelmének biztosításához zárolhatja a Storage-fiókot vagy az erőforráscsoportot.

## <a name="next-steps"></a>További lépések
- A megosztási Pillanatképek használata a ben:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [parancssori felület](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Pillanatképek megosztása – gyakori kérdések](storage-files-faq.md#share-snapshots)
