---
title: Az Azure Redis Cache-ben az adatok importálása és exportálása |} A Microsoft Docs
description: Ismerje meg, hogyan importálhat és exportálhat adatokat, és a prémium szintű Azure Redis Cache-példány a blob storage-ból
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: wesmc
ms.openlocfilehash: 6733891213f15e9ceaf08ef7fb50380db47a695f
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259194"
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Adatok importálása és exportálása az Azure Redis Cache-ben
Importálás/exportálás szolgáltatás egy Azure Redis Cache-adatok felügyeleti művelet, amely lehetővé teszi, hogy az adatok importálása az Azure Redis Cache-be vagy exportál adatokat az Azure Redis Cache képes importálni, és a egy prémium szintű gyorsítótár egy Redis Cache-adatbázis (RDB) pillanatkép exportálása egy blobot az Azure-ban Storage-fiók. 

- **Exportálás** – Lapblob exportálhatók. az Azure Redis Cache RDB-pillanatképeket.
- **Importálása** – a Redis Cache RDB-pillanatképek importálhat egy Lapblob vagy Blokkblob.

Importálási/exportálási lehetővé teszi különböző Azure Redis Cache-példány közötti áttelepítéséhez, vagy töltse fel a gyorsítótárat az adatokat használat előtt.

Ez a cikk az Azure Redis Cache-adatok importálása és exportálása egy útmutatót biztosít, valamint megadja a gyakran feltett kérdésekre adott válaszokat.

> [!IMPORTANT]
> Importálási/exportálási előzetes verzióban érhető el, és csak [prémium szintű](cache-premium-tier-intro.md) gyorsítótárazza.
>
>

## <a name="import"></a>Importálás
Importálás a Redis-kompatibilis RDB-fájlok viszi, minden bármilyen felhőben vagy a környezetben, beleértve a Linux, Windows vagy bármely más szolgáltatónál, például az Amazon Web Services és a többi futó Redis futó Redis-kiszolgálóról is használható. A rendszer egyszerűen hozzon létre egy gyorsítótárat az adatokkal előre feltöltött adatok importálása. Az importálási folyamat során az Azure Redis Cache RDB-fájlba való fájlokat az Azure storage-ból betölti a memóriába, és majd szúrja be a kulcsokat a gyorsítótárba.

> [!NOTE]
> Mielőtt megkezdené az importálási művelet, győződjön meg arról, hogy a Redis adatbázis (RDB) fájlt vagy fájlokat a rendszer feltölti az Azure storage, az ugyanabban a régióban és az Azure Redis Cache-példány előfizetésen stránka nebo Blok blobok. További információkért lásd: [Azure Blob storage használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ha az RDB-fájlba való fájl használatával exportált a [Azure Redis Cache exportálási](#export) funkció, a RDB-fájlba egy lapblob már tárolja, és készen áll a importálása.
>
>

1. Egy vagy több exportált gyorsítótár blobok importálása [tallózással keresse meg a gyorsítótár](cache-configure.md#configure-redis-cache-settings) az Azure Portalon, és kattintson **adatok importálása** származó a **erőforrás menüben**.

    ![Adatok importálása][cache-import-data]
2. Kattintson a **BLOB(ok) kiválasztása** , és válassza ki a tárfiókot, amely tartalmazza az adatok importálásához.

    ![Tárfiók kiválasztása][cache-import-choose-storage-account]
3. Kattintson a tároló, amely tartalmazza az adatok importálásához.

    ![Válasszon tárolót][cache-import-choose-container]
4. Válassza ki az importálandó bal oldalán a blob neveként a részre kattintva egy vagy több blobot, és kattintson a **kiválasztása**.

    ![Válassza ki a blobok][cache-import-choose-blobs]
5. Kattintson a **importálása** az importálási folyamat elindításához.

   > [!IMPORTANT]
   > A gyorsítótár nem érhető el a gyorsítótár-ügyfelek az importálási folyamat során, és a gyorsítótárban lévő összes adat törlődik.
   >
   >

    ![Importálás][cache-import-blobs]

    Az importálási művelet állapotát az alábbi, az értesítéseket az Azure Portalról, vagy az események megtekintésével figyelheti a [auditnapló](../azure-resource-manager/resource-group-audit.md).

    ![Importálás folyamatban van][cache-import-data-import-complete]

## <a name="export"></a>Exportálás
Exportálás lehetővé teszi, hogy exportálja az Azure Redis Cache redis-kompatibilis RDB-fájl(ok) tárolt adatokat. Ez a funkció segítségével az adatok áthelyezése egy Azure Redis Cache-példányról egy másikra, vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gépen, amelyen az Azure Redis Cache-server-példányt, és a fájlt a kijelölt tárfiók töltenek fel. Az exportálási művelet befejeződik, vagy sikeres állapotú vagy sikertelen, amikor az ideiglenes fájl törlődik.

1. A tárolóhoz, a gyorsítótár tartalmának exportálása [tallózással keresse meg a gyorsítótár](cache-configure.md#configure-redis-cache-settings) az Azure Portalon, és kattintson **adatok exportálása** származó a **erőforrás menüben**.

    ![Válasszon tárolót][cache-export-data-choose-storage-container]
2. Kattintson a **válassza ki a tároló** , és válassza ki a kívánt tárfiókot. A tárfiók a gyorsítótár azonos előfizetésben és régióban kell lennie.

   > [!IMPORTANT]
   > Exportálja a lapblobokat, melyek a klasszikus és Resource Manager-tárfiókokra is támogatottak, de nem támogatja a együttműködik [Blob storage-fiókok](../storage/common/storage-account-options.md#blob-storage-accounts) jelenleg.
   >
   >

    ![Tárfiók][cache-export-data-choose-account]
3. Válassza ki a kívánt blob-tárolóba, és kattintson a **kiválasztása**. Új tároló használatához kattintson **tároló hozzáadása** először hozzá, és válasszon a listából.

    ![Válasszon tárolót][cache-export-data-container]
4. Adjon meg egy **Blobnév előtagja** kattintson **exportálása** az exportálási folyamat elindításához. A blob nevének előtagja szolgál az exportálási művelet által generált fájlok nevének előtagja.

    ![Exportálás][cache-export-data]

    Az exportálási művelet állapotát az alábbi, az értesítéseket az Azure Portalról, vagy az események megtekintésével figyelheti a [auditnapló](../azure-resource-manager/resource-group-audit.md).

    ![Az adatok exportálása befejeződött][cache-export-data-export-complete]

    Gyorsítótárak az exportálási folyamat során elérhető marad.

## <a name="importexport-faq"></a>Importálási/exportálási – gyakori kérdések
Ez a szakasz tartalmazza az Import/Export szolgáltatás – gyakori kérdések.

* [Milyen díjszabási szint esetében használhatja az importálási/exportálási?](#what-pricing-tiers-can-use-importexport)
* [E importálhat adatokat bármely Redis-kiszolgáló?](#can-i-import-data-from-any-redis-server)
* [RDB-fájlba való verziók is importálhat?](#what-rdb-versions-can-i-import)
* [A gyorsítótár érhető el az importálási/exportálási művelet során?](#is-my-cache-available-during-an-importexport-operation)
* [Használható Redis-fürttel az importálási/exportálási?](#can-i-use-importexport-with-redis-cluster)
* [Hogyan működik a importálási/exportálási beállítása egy egyéni adatbázisok?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Miben különbözik importálási/exportálási Redis megőrzési funkciója?](#how-is-importexport-different-from-redis-persistence)
* [Automatizálhatja a PowerShell, a parancssori felület vagy a más felügyeleti ügyfelek importálási/exportálási?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Az importálási/exportálási művelet során időtúllépési hiba kapott. Mit jelent?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Az Azure Blob Storage-adatok exportálásakor hibaüzenetet kapok. mi történt?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Milyen díjszabási szint esetében használhatja az importálási/exportálási?
Importálási/exportálási csak a prémium tarifacsomagban érhető el.

### <a name="can-i-import-data-from-any-redis-server"></a>E importálhat adatokat bármely Redis-kiszolgáló?
Igen, mellett az Azure Redis Cache-példány-ból exportált adatok importálása is RDB-fájlok importálása a minden futó bármilyen felhőben vagy a környezetben, például a Linux, Windows, a Redis-kiszolgálóról, vagy a felhőbeli szolgáltatók, például az Amazon Web Services. Ehhez a kívánt kiszolgálóról Redis RDB-fájlba feltöltése stránka nebo Blok a blobok Azure Storage-fiókban, és importálja azt a prémium szintű Azure Redis Cache-példányhoz. Például előfordulhat, hogy szeretné az adatok exportálása az éles gyorsítótárból, és importálja azt a tesztelési vagy az áttelepítés során egy átmeneti környezetben használt gyorsítótár.

> [!IMPORTANT]
> Sikeresen importálni a Redis-kiszolgálók eltérő Azure Redis Cache egy lapblob használatakor exportált adatok, lapblob méretét egy 512 bájtos határhoz meg kell igazítani. A mintakódot, bármilyen szükséges bájt kitöltés végrehajtásához, lásd: [minta lap blob feltöltése](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>RDB-fájlba való verziók is importálhat?

Az Azure Redis Cache RDB-fájlból való importálása be keresztül RDB 7-es verzió támogatja.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>A gyorsítótár érhető el az importálási/exportálási művelet során?
* **Exportálás** - gyorsítótárak is elérhető marad, és továbbra is használja a gyorsítótár-exportálási művelet során.
* **Importálása** - gyorsítótárak elérhetetlenné válnak, amikor az importálási művelet elindulása és az importálási művelet befejeződésekor elérhető lesz.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Használható Redis-fürttel az importálási/exportálási?
Igen, és meg is importálási-exportálási fürtözött gyorsítótár és a egy nem fürtözött gyorsítótár között. Redis-fürttel óta [csak támogatja az adatbázis-0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), a 0 kivételével adatbázisokban adatokat nem importálja. Fürtözött gyorsítótárazott adatok importálásakor a kulcsokat a fürt a szegmensek között terjeszti.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hogyan működik a importálási/exportálási beállítása egy egyéni adatbázisok?
Egyes tarifacsomagok rendelkezik másik [adatbázisok korlátai](cache-configure.md#databases), így nincsenek néhány szempontot, ha konfigurálta az egyéni értéket importálásakor a `databases` beállítása gyorsítótár létrehozása során.

* Az ennél alacsonyabb tarifacsomagra importálásakor `databases` az szinttel, amelyet exportált korlát:
  * Ha használja az alapértelmezett száma `databases`, amely minden árképzési szint esetében 16, nem történik adatvesztés.
  * Ha egyéni számos használ `databases` , hogy esik a határokon belül, amelyhez importál, a szint nem áll adat elveszett.
  * Ha az exportált adatok egy adott adatbázisban, amely túllépi a korlátot, az új szinten, a rendszer nem importálja azokat magasabb adatbázisokból származó adatok.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Miben különbözik importálási/exportálási Redis megőrzési funkciója?
Az Azure Redis Cache megőrzése segítségével megőrizheti a Redis az Azure Storage-ban tárolt adatokkal. Adatmegőrzés be van állítva, ha az Azure Redis Cache a Redis cache a Redis bináris formátumot egy konfigurálható biztonsági mentési gyakoriság alapján lemezre pillanatképét továbbra is fennáll. Ha egy katasztrofális esemény történik, amely letiltja az elsődleges és replika gyorsítótár, a gyorsítótár adatainak visszaállítása használatával automatikusan a legutóbbi pillanatképet. További információkért lásd: [konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz adatmegőrzés](cache-how-to-premium-persistence.md).

Importálás / exportálás lehetővé teszi, hogy adatokat visz vagy exportálása az Azure Redis Cache. Biztonsági mentés konfigurálása, és nem visszaállítás Redis megőrzési funkciója segítségével.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Automatizálhatja a PowerShell, a parancssori felület vagy a más felügyeleti ügyfelek importálási/exportálási?
Igen, PowerShell-Útmutató: [importálása a Redis Cache-gyorsítótárhoz](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) és [exportálása egy Redis gyorsítótárhoz](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Az importálási/exportálási művelet során időtúllépési hiba kapott. Mit jelent?
Ha, ha továbbra is a **adatimportálás** vagy **adatok exportálása** paneljén, mielőtt elindítaná a művelet hosszabb 15 perc, akkor megjelenik egy hibaüzenet a következő példához hasonló hibaüzenettel:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

A probléma megoldásához, kezdeményezni az importálási vagy exportálási művelet előtt 15 perccel eltelt.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Az Azure Blob Storage-adatok exportálásakor hibaüzenetet kapok. Mi történt?
Exportálás csak lapblobként tárolt RDB-fájlok működik. Más blobtípusok jelenleg nem támogatottak, beleértve a blob storage-fiókok a gyakori és ritka elérésű szint használata mellett. További információkért lásd: [Blob storage-fiókok](../storage/common/storage-account-options.md#blob-storage-accounts).

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan használja a további prémiumszintű gyorsítótár funkcióival.

* [Az Azure Redis Cache prémium szintjének bemutatása](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png
