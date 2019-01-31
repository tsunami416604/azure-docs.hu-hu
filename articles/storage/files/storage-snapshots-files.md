---
title: Az Azure Files megosztási pillanatképeinek áttekintése |} A Microsoft Docs
description: Megosztási pillanatkép egy Azure-fájlmegosztási egy időben, arra, hogy készítsen biztonsági másolatot a megosztáshoz készített egy csak olvasható verzióját.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.subservice: files
ms.openlocfilehash: b39b6cf5936820d6e26ad5a17638a00c86fbf68a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457139"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Az Azure Files megosztási pillanatképeinek áttekintése 
Az Azure Files lehetővé teszi, hogy a megosztási pillanatképek a fájlmegosztások. Pillanatképek rögzítése a fájlmegosztási állapot ezen a ponton idő megosztani. Ez a cikk ismertetünk, adja meg a megosztási pillanatképek funkciókról, és hogyan igénybe veheti, az az egyedi használati esetekhez.

## <a name="when-to-use-share-snapshots"></a>Mikor érdemes használni a megosztási pillanatképek

### <a name="protection-against-application-error-and-data-corruption"></a>Alkalmazás hiba és az adatok sérülését elleni védelem
Fájlmegosztások használó alkalmazások például az írás, Olvasás, storage, átviteli és feldolgozási műveletet hajt végre. Ha egy alkalmazás helytelenül van konfigurálva, vagy egy véletlen bug bemutatott, véletlen felülírása vagy sérülés néhány címblokkokhoz fordulhat elő. Ezek a forgatókönyvek elleni elvégezhető egy megosztási pillanatképre új alkalmazáskód üzembe helyezése előtt. Ha egy hiba vagy az alkalmazás hiba történt az új központi telepítés a rendszerekben jelent meg, később is visszatérhet az adatok korábbi verzióinak a fájlmegosztáson. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Nem kívánt módosítások vagy véletlen törlés elleni védelem
Tegyük fel, hogy egy fájlmegosztást a fájlt a dolgozik. A szövegfájlban lezárása után, nem fog tudni visszavonja a módosításokat. Ezekben az esetekben kell helyreállítani a fájl egy korábbi verzióját. Megosztási pillanatképek használatával helyreállíthatja a fájlok korábbi verzióit, ha rendelkezik véletlenül átnevezték vagy törölték.

### <a name="general-backup-purposes"></a>Általános biztonsági mentési célból
Miután létrehozott egy fájlmegosztást, az adatok biztonsági mentés használandó fájlmegosztás megosztási pillanatképek rendszeres időközönként hozhat létre. Egy megosztási pillanatképet, amikor venni rendszeres időközönként, segít a jövőbeli naplózási követelmények vagy vész-helyreállítási használható adatok korábbi verzióinak karbantartásához.

## <a name="capabilities"></a>Funkciók
Megosztási pillanatkép az adatok időponthoz, a csak olvasható példányát. Létrehozása, törlése és a pillanatképek kezelése a REST API-val. Ugyanazokat a képességeket is érhetők el az ügyféloldali kódtár, az Azure CLI-vel és az Azure Portalon. 

A REST API-t és az SMB használatával megtekintheti a megosztás pillanatképeit. Kérheti le a fájl vagy könyvtár verziók listáját, és a egy adott verziót közvetlenül meghajtóként csatlakoztathatja (csak Windows - érhető el lásd [korlátok](#limits)). 

Megosztási pillanatkép létrehozása után azt is olvassa el, másolja, vagy törölni, de nem módosulnak. Teljes megosztási pillanatképek nem másolható egy másik tárfiókba. Meg kell tennie, hogy a fájl, AzCopy vagy más másolása mechanizmusok használatával.

Megosztási pillanatkép-készítés a fájlok megosztási szintjén van megadva. Lekérés biztosítunk az egyes fájlok szintjén, az egyes fájlok visszaállításának engedélyezése. Visszaállíthatja a teljes fájlmegosztás SMB, a REST API, a portálon, az ügyféloldali kódtár vagy a PowerShell vagy parancssori eszközök használatával.

Megosztási pillanatképek egy fájlmegosztás megegyezik a kiindulási fájlmegosztás. Az egyetlen különbség, hogy egy **DateTime** értéket fűzi hozzá a megosztás URI-t, amellyel a megosztási pillanatkép időpontját jelzi. Például ha egy fájlmegosztás URI-t, http://storagesample.core.file.windows.net/myshare, a megosztási pillanatkép URI hasonlít:
```
http://storagesample.file.core.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Megosztási pillanatképek továbbra is fennáll, addig, amíg explicit módon törlődnek. Egy megosztási pillanatképre annak alap fájlmegosztás nem outlive. A pillanatképek a jelenlegi pillanatképek nyomon követéséhez az alap fájlmegosztáshoz társított enumerálása. 

Megosztási pillanatképek egy fájlmegosztás létrehozásakor a fájlokat a a megosztás tulajdonságainál ugyanazokat az értékeket a megosztási pillanatkép másolódnak. Az alap fájljait és metaadatait a fájlmegosztás is másolja a megosztási pillanatkép, ha nem ad meg, hogy a megosztás létrehozásakor a pillanatkép külön metaadatait.

Egy megosztás rendelkező megosztási pillanatképek, kivéve, ha először törölje az összes megosztási pillanatképek nem törölhető.

## <a name="space-usage"></a>Lemezterület-használat 
Megosztási pillanatképek olyan növekményes jellegűek. Csak a módosított adatokat a legutóbbi megosztási Pillanatkép mentése után. Ez a minimálisra csökkenti a megosztási pillanatkép létrehozásához szükséges időt, és menti a tárolási költségekre. Az objektum írási művelet, vagy tulajdonságot vagy metaadat-frissítési művelet akkor számít "módosított tartalom" felé, és a megosztási pillanatkép tárolja. 

Az időszak, amikor a forgalom volt legmagasabb lemezterületet takarítson meg, hogy törölheti a megosztási pillanatkép.

Annak ellenére, hogy a megosztási pillanatképek Növekményesen lesznek mentve, szeretné megőrizni a legutóbbi megosztási pillanatképre annak érdekében, hogy a megosztás visszaállítása. Ha töröl egy megosztási pillanatképre, csak az adott megosztási pillanatkép egyedi adatokat a rendszer eltávolítja. Aktív pillanatképek szükséges információkat, keresse meg és állítsa vissza az adatok az eredeti helyére vagy máshová (az az idő a megosztási pillanatkép) kell tartalmaznia. Az elemek szintjén állíthatja vissza.

A pillanatképek nem beleszámítanak a megosztás 5 TB-os korlátot. Mekkora terület megosztási pillanatképek foglalhat el összesen nincs korlátozva van. Tárfiókok korlátai továbbra is érvényesek.

## <a name="limits"></a>Korlátok
Az Azure Files lehetővé teszi, hogy még ma megosztási pillanatképek maximális száma 200. Után 200 megosztási pillanatképek hogy régebbi megosztási pillanatképek törlése érdekében hozzon létre újakat. 

A párhuzamos hívások a megosztási pillanatképek létrehozása nincs korlátozva van. Terület mennyisége nincs korlátozva van nem használt megosztást egy adott fájlmegosztás pillanatképeinek használhatnak fel. 

Jelenleg nincs lehetőség a megosztási pillanatképek Linux rendszeren való csatlakoztatásához. Ennek az oka az Linuxos SMB-ügyfél nem támogatja a Windows does például csatlakoztatási pillanatképek.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Adatok másolása vissza a megosztás megosztási pillanatképből
Másolási műveletek, amely magában foglalja a fájlokat, és a fájlmegosztások pillanatképeinek kövesse ezeket a szabályokat:

Egy fájlmegosztási pillanatképet az egyes fájlok keresztül az alapszintű megosztásra vagy bármely más helyre másolja. A fájlok korábbi verziójának visszaállítása, vagy a teljes fájlmegosztás visszaállítása a megosztás pillanatképéből fájl által fájl másolásával. A megosztási pillanatkép nem lett előléptetve, alapszintű megosztás. 

A megosztási pillanatkép változatlanul másolása után, de az alapszintű fájlmegosztás felülírja az adatokat a megosztási pillanatkép elérhető. Felé visszaállított fájlok száma "módosított tartalmat."

A megosztási pillanatképek egy célhelyre, más néven másolhat egy fájlt. Az eredményül kapott célfájl egy írható fájlt, és nem egy megosztási pillanatképre.

Amikor másolatot felülírja a célfájlt, bármely az eredeti cél fájlhoz tartozó megosztási pillanatképek változatlanok maradnak.

## <a name="general-best-practices"></a>Általános ajánlott eljárások 
Amikor az infrastruktúra az Azure-ban futtatja, automatizálhatja a biztonsági másolatok adatait, amikor csak lehetséges. Automatikus műveletek: megbízhatóbb, mint a manuális folyamatokat, elősegítve a munkavállalók az adatok védelmét és helyreállíthatóságát. A REST API-t, az ügyfél-SDK vagy automation-szkriptek is használhatja.

A megosztási pillanatkép-ütemező, üzembe helyezése előtt alaposan gondolja át, a megosztási pillanatkép készítésének gyakorisága és a szükségtelen díjak elkerüléséhez megőrzési beállítások.

Megosztási pillanatképek csak a fájl szintű védelmet biztosítani. Megosztási pillanatképek nem akadályozzák meg a fájl megosztása vagy a storage-fiók fat-ujjlenyomat-törlések. Véletlen törlések storage-fiók védelme érdekében zárolhatja a storage-fiók vagy az erőforráscsoportot.

## <a name="next-steps"></a>További lépések
- A megosztási pillanatképek használata:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [Parancssori felület](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
- [Pillanatkép – GYIK](storage-files-faq.md#share-snapshots)
