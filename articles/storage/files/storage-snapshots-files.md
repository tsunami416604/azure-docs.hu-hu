---
title: Azure-fájlok megosztási pillanatképek áttekintése |} Microsoft Docs
description: Megosztás pillanatképet egy olyan Azure fájlok megosztási konzolán kell elvégezni egy ideje, készítsen biztonsági másolatot a megosztást is csak olvasható verziója telepítve.
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: cfb1e6810285886da13aa0bf69faee9c65e2b222
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738227"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Azure-fájlok megosztási pillanatképek áttekintése 
Az Azure Files lehetővé teszi a megosztás pillanatképek készítése fájlmegosztások. A fájlmegosztási állapot pillanatképek rögzítési ezen a ponton idő megosztás. Ez a cikk azt írják le, milyen lehetőségek megosztás pillanatképek adja meg, és hogyan fordíthatja előnyére azokat az egyéni használati eset a.

## <a name="when-to-use-share-snapshots"></a>Megosztás pillanatképek használata

### <a name="protection-against-application-error-and-data-corruption"></a>Alkalmazás hiba és adatok sérülése elleni védelem
Fájlmegosztások használó alkalmazások például az írást, a olvasási, a tárolás, az átvitel és a feldolgozási műveleteket. Ha egy alkalmazás helytelenül van konfigurálva, vagy egy véletlen hiba megjelent, természeti katasztrófa vagy véletlen felülírása néhány címblokkokhoz fordulhat elő. Ezek a forgatókönyvek elleni védelme érdekében is pillanatképet készít megosztás új alkalmazáskód központi telepítése előtt. Egy hiba vagy az alkalmazás hiba az új üzemelő példányhoz jelenik meg, ha később is visszatérhet egy korábbi verzióját az adatok adott fájlmegosztáson. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Nem kívánt módosítások vagy véletlen törlés elleni védelem
Tegyük fel, hogy a fájlt egy fájlmegosztás dolgozik. A szövegfájl lezárása után nem tud a továbbiakban visszavonja a módosításokat. Ezekben az esetekben majd kell helyreállítania a fájl egy korábbi verzióját. Megosztás-pillanatképek használatával a fájlok korábbi verzióinak helyreállítását, ha véletlenül átnevezték vagy törölték.

### <a name="general-backup-purposes"></a>Általános biztonsági másolat létrehozása céljából
Fájlmegosztás létrehozása után a fájlmegosztáshoz való használatát az adatok biztonsági mentésének megosztás pillanatkép rendszeresen hozhat létre. A pillanatkép, rendszeres időközönként időpontjában megosztás segít használható jövőbeli naplózási követelményeinek, vagy vész-helyreállítási adatok korábbi verzióinak.

## <a name="capabilities"></a>Funkciók
Egy megosztás pillanatkép az adatok időpontban, csak olvasható másolatát. Létrehozása, törlése és a pillanatképek kezelése a REST API használatával. Ugyanazokat a képességeket is rendelkezésre állnak az ügyféloldali kódtár, az Azure parancssori felület és Azure-portálon. 

A pillanatképek megosztás a REST API-t és az SMB használatával tekintheti meg. A könyvtár vagy fájl verzióinak listája le, és egy adott verziójához meghajtóként közvetlenül lehet csatlakoztatni. 

Megosztás pillanatképének létrehozása után azt is kell olvasni, másolja, vagy törölni, de nem módosított. Nem másolhat egy teljes megosztási pillanatkép másik tárolási fiókot. AzCopy vagy más másolása mechanizmusok használatával teheti, hogy által fájl, akkor.

A fájl fájlmegosztási szinten megosztás pillanatkép-készítés valósul meg. Lekérés egyedi fájlszintű, amelyek engedélyezik az egyes fájlok visszaállítása során valósul meg. Egy teljes fájlmegosztást SMB, a REST API, a portál, az ügyféloldali kódtár vagy a PowerShell vagy parancssori felület tooling segítségével állíthatja vissza.

Fájlmegosztás megosztás pillanatképe megegyezik az alap fájlmegosztást. Az egyetlen különbség, hogy egy **DateTime** értéket fűzi hozzá a megosztás URI-t, ahol a megosztás pillanatkép készült időpontját jelzi. Például ha egy fájlmegosztás URI van http://storagesample.core.file.windows.net/myshare, a megosztás pillanatkép URI hasonlít:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Megosztás pillanatképek továbbra is fennáll, addig, amíg explicit módon törlődnek. Egy megosztás pillanatkép nem outlive az alap fájlmegosztást. A pillanatképek a base-megosztás és nyomon követheti a jelenlegi pillanatképek társított enumerálása. 

Amikor egy fájlmegosztás megosztás pillanatképet hoz létre, vannak másolja a fájlokat a megosztás tulajdonságainál ugyanazokat az értékeket a megosztás pillanatkép. A bázisfájlokhoz és a fájlmegosztás metaadatok is kerülnek a megosztás pillanatképet, ha nem ad meg, hogy a pillanatkép-létrehozás során megosztás külön metaadatok.

Egy megosztást, amelyet a megosztás pillanatképekkel rendelkezik, kivéve, ha előbb törölje a megosztást pillanatképek nem törölhető.

## <a name="space-usage"></a>Lemezterület-használat 
Megosztás pillanatképei növekményes jellegűek. Csak a módosított adatokat a legutóbbi megosztás Pillanatkép mentése után. Ez a megosztás pillanatkép létrehozásához szükséges idő minimálisra csökkenti, és menti a tárolási költségeket. Bármely írási művelet az objektum vagy tulajdonság vagy metaadat-frissítési művelet felé "módosított tartalmat" számít, és a megosztás pillanatkép tárolja. 

Az időszak, amikor a forgalom volt legmagasabb megtakarítás érdekében törölheti a megosztás pillanatkép.

Annak ellenére, hogy a megosztás pillanatképek Növekményesen menti, meg kell ahhoz, hogy a megosztás visszaállítása csak a legutóbbi megosztás pillanatkép tartani. Ha törli a megosztást pillanatképet, csak az adott megosztás pillanatkép egyedi adatokat törlődik. Aktív pillanatképek keresse meg és állítsa vissza az adatokat (az az idő során létrehozott pillanatképen megosztás) az eredeti helyére vagy máshová szükséges összes adatot tartalmaznak. Az elemek szintjén is helyreállíthatja.

A pillanatképek nem száma 5-TB megosztás korlát felé. Mekkora terület megosztás pillanatképek elfoglalt összesen nincs korlátozva van. Tárfiókok korlátai továbbra is érvényesek lesznek.

## <a name="limits"></a>Korlátok
Az Azure-fájlok ma lehetővé teszi a megosztás pillanatképek maximális száma: 200. 200 megosztás pillanatképek után először törölnie kell régebbi megosztás pillanatképek ahhoz, hogy hozzon létre újakat. 

Az egyidejű hívások a megosztás pillanatképeinek korlátozva van. Nincs korlát terület adott megosztás egy adott fájlmegosztás pillanatképek felhasználhat. 

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Az adatok másolásának vissza egy megosztást a megosztás pillanatképből
Másolási műveletek tartalmaz, amely fájlok megosztása pillanatképek kövesse ezeket a szabályokat:

Egyes fájlok fájl megosztási pillanatképbe keresztül az alap megosztásra vagy bármely más helyre másolja. Állítsa vissza egy fájl egy korábbi verzióját, vagy állítsa vissza a teljes fájlmegosztás fájl által a megosztás pillanatképből másolásával. A megosztás pillanatkép nem lépteti alap megosztáshoz. 

A megosztás pillanatkép változatlanul másolását követően, de az alap fájlmegosztás felülírja a rendelkezésre álló fájlmegosztás pillanatképben lévő adatok másolatát. Felé visszaállított fájlok száma "megváltozott tartalmat."

A megosztás pillanatképet egy célra, más néven másolhat egy fájlt. Az eredményül kapott célfájl egy írható fájlt, és nem megosztás pillanatképet.

A célfájl másolatot felülírja, esetleg az eredeti célfájl társított megosztás pillanatképeket változatlanok maradnak.

## <a name="general-best-practices"></a>Általános gyakorlati tanácsok 
Infrastruktúra az Azure-on futtatja, amikor automatizálhatja az adat-helyreállítás számára, amikor csak lehetséges biztonsági mentéseit. Automatizált műveletek olyan sokkal megbízhatóbb, mint manuálisan végrehajtott folyamatokat, segítve az adatvédelem és helyreállíthatósága. A REST API-t, az ügyfél SDK-t vagy az automatizálási parancsfájlokat is használhatja.

A megosztás pillanatkép-ütemező telepítése előtt alaposan gondolja át, a megosztás pillanatfelvételeinek gyakoriságát és a felesleges költségek megcélzásával elkerülheti az adatmegőrzési beállítások.

Megosztás pillanatképek csak a fájl szintű védelmet nyújt. Megosztás pillanatképek nem akadályozzák meg a fájl megosztásra vagy a tárolási fiók fat-ujját törlések. A tárfiók véletlen törlések védelme érdekében zárolhatja a tárfiók vagy az erőforráscsoportot.

## <a name="next-steps"></a>További lépések
- A megosztás pillanatképek használata:
    - [Portal](storage-how-to-use-files-portal.md#create-and-modify-share-snapshots)
    - [PowerShell](storage-how-to-use-files-powershell.md#create-and-modify-share-snapshots)
    - [Parancssori felület](storage-how-to-use-files-cli.md#create-and-modify-share-snapshots)
- [Megosztás pillanatkép – gyakori kérdések](storage-files-faq.md#share-snapshots)