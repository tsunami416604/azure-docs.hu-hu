---
title: Adatok importálása és exportálása az Azure Cache for Redis számára
description: Ismerje meg, hogyan importálhat és exportálhat adatokat blobtárolóba és onnan a prémium szintű Azure-gyorsítótárredis-példányokkal
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 29ad5ca6c9058b88a539c7a3bb8ace4d9a65083a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278089"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Adatok importálása és exportálása az Azure Cache for Redis számára
Az importálás/exportálás egy Azure-gyorsítótár a Redis-adatkezelési művelethez, amely lehetővé teszi az adatok importálását az Azure Cache for Redis számára, vagy adatok exportálása az Azure Cache for Redis-ből a Redis Database (RDB) pillanatképének importálásával és exportálásával egy prémium szintű gyorsítótárból egy Azure Storage-fiók blobjába.

- **Exportálás** – exportálhatja az Azure-gyorsítótárat a Redis RDB pillanatképeihez egy lapblobba.
- **Importálás** – importálhatja az Azure Cache redis RDB pillanatképek akár egy lapblob vagy egy blokk blob.

Az importálás/exportálás lehetővé teszi a redis-példányok különböző Azure-gyorsítótárai közötti áttelepítést, vagy a gyorsítótár feltöltése adatokkal használat előtt.

Ez a cikk útmutatót biztosít az adatok importálásához és exportálásához az Azure Cache for Redis segítségével, és választ ad a gyakran feltett kérdésekre.

> [!IMPORTANT]
> Az importálás/exportálás csak [a prémium szintű](cache-premium-tier-intro.md) gyorsítótárak esetében érhető el.
>
>

## <a name="import"></a>Importálás
Az importálás segítségével redis kompatibilis RDB fájlokat hozhat bármely Redis szerverről, amely bármilyen felhőben vagy környezetben fut, beleértve a Linuxon, Windowsrendszeren vagy bármely felhőszolgáltatón futó Redis-t, például az Amazon Web Services-t és másokat. Az adatok importálásával egyszerűen hozhat létre gyorsítótárat előre kitöltött adatokkal. Az importálási folyamat során az Azure Cache for Redis betölti az RDB-fájlokat az Azure storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

> [!NOTE]
> Az importálási művelet megkezdése előtt győződjön meg arról, hogy a Redis Database (RDB) fájl vagy fájlok feltöltése az Azure storage-ban, ugyanabban a régióban és előfizetésben, mint az Azure Cache for Redis példány. További információ: [Az Azure Blob storage – első lépések.](../storage/blobs/storage-dotnet-how-to-use-blobs.md) Ha exportálta az RDB-fájlt az [Azure Cache for Redis Export](#export) szolgáltatás használatával, az RDB-fájl már egy lapblobban van tárolva, és készen áll az importálásra.
>
>

1. Egy vagy több exportált gyorsítótárblob importálásához [keresse meg a gyorsítótárat](cache-configure.md#configure-azure-cache-for-redis-settings) az Azure Portalon, és kattintson az **Adatok importálása parancsra** az **Erőforrás menüből.**

    ![Adatok importálása](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Kattintson **a Blob(ok kiválasztása)** elemre, és válassza ki az importálni kívánt adatokat tartalmazó tárfiókot.

    ![Tárfiók kiválasztása](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Kattintson az importálandó adatokat tartalmazó tárolóra.

    ![Tároló kiválasztása](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Jelöljön ki egy vagy több importálandó blobot a blob nevének bal oldalán található területre kattintva, majd kattintson a **Kijelölés gombra.**

    ![Blobok kiválasztása](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Az **importálási** folyamat megkezdéséhez kattintson az Importálás gombra.

   > [!IMPORTANT]
   > A gyorsítótár-gyorsítótárat az importálási folyamat során nem lehet elérhetővé a gyorsítótár-ügyfelek számára, és a gyorsítótárban lévő adatok törlődnek.
   >
   >

    ![Importálás](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Az importálási művelet előrehaladását az Azure Portalon küldött értesítések követésével vagy a [naplóban](../azure-resource-manager/management/view-activity-logs.md)lévő események megtekintésével figyelheti.

    ![Importálás folyamata](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportálás
Exportálás lehetővé teszi, hogy exportálja a tárolt adatokat az Azure Cache for Redis a Redis kompatibilis RDB fájl(ok). Ezzel a funkcióval áthelyezheti az adatokat az egyik Azure-gyorsítótárból a Redis-példányegy másik vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gép, amely az Azure Cache for Redis kiszolgálópéldány, és a fájl feltöltése a kijelölt tárfiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződik be, az ideiglenes fájl törlődik.

1. Ha a gyorsítótár aktuális tartalmát tárolóba szeretné exportálni, [keresse meg a gyorsítótárat](cache-configure.md#configure-azure-cache-for-redis-settings) az Azure Portalon, és kattintson az **Adatok exportálása** parancsra az **Erőforrás menüből**.

    ![Tárolótároló kiválasztása](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Kattintson **a Tárolótároló kiválasztása elemre,** és válassza ki a kívánt tárfiókot. A tárfióknak ugyanabban az előfizetésben és régióban kell lennie, mint a gyorsítótárnak.

   > [!IMPORTANT]
   > Az exportálás olyan lapblobokkal működik, amelyeket klasszikus és Erőforrás-kezelő i. További információkat az [Azure Storage-fiókok áttekintésében](../storage/common/storage-account-overview.md) találhat.
   >

    ![Tárfiók](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Válassza ki a kívánt blobtárolót, és kattintson a **Kijelölés gombra.** Új tároló használatához először a **Tároló hozzáadása** gombra kattintva adja hozzá, majd jelölje ki a listából.

    ![Tárolótároló kiválasztása](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Írjon be egy **Blob-névelőtagot,** és az **exportálási** folyamat elindításához kattintson az Exportálás gombra. A blob névelőtagja az exportálási művelet által létrehozott fájlok nevének előtagra szolgál.

    ![Exportálás](./media/cache-how-to-import-export-data/cache-export-data.png)

    Az exportálási művelet előrehaladását az Azure Portalon küldött értesítések követésével vagy a [naplóban](../azure-resource-manager/management/view-activity-logs.md)lévő események megtekintésével figyelheti.

    ![Adatok exportálása kész](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    A gyorsítótárak továbbra is használhatók az exportálási folyamat során.

## <a name="importexport-faq"></a>Gyakori kérdések importálása/exportálása
Ez a szakasz az Importálás/exportálás funkcióval kapcsolatos gyakori kérdéseket tartalmazza.

* [Milyen tarifacsomagok használhatják az importálást/exportálást?](#what-pricing-tiers-can-use-importexport)
* [Importálhatok adatokat bármely Redis-kiszolgálóról?](#can-i-import-data-from-any-redis-server)
* [Milyen RDB-verziókat importálhatok?](#what-rdb-versions-can-i-import)
* [Elérhető a gyorsítótár importálási/exportálási művelet során?](#is-my-cache-available-during-an-importexport-operation)
* [Használhatom az Importálás/exportálást a Redis fürttel?](#can-i-use-importexport-with-redis-cluster)
* [Hogyan működik az importálás/exportálás egyéni adatbázis-beállítással?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Miben különbözik az importálás/exportálás a Redis perzisztenciától?](#how-is-importexport-different-from-redis-persistence)
* [Automatizálható az importálás/exportálás PowerShell, CLI vagy más felügyeleti ügyfelek használatával?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Időelt- és megdöntési hiba történt az importálási/exportálási művelet során. Mit jelentsen ez?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Hiba történt az adatok Azure Blob Storage-ba való exportálásakor. mi történt?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Milyen tarifacsomagok használhatják az importálást/exportálást?
Az importálás/exportálás csak a prémium díjszabási csomagban érhető el.

### <a name="can-i-import-data-from-any-redis-server"></a>Importálhatok adatokat bármely Redis-kiszolgálóról?
Igen, az Azure Cache-ből Redis-példányokhoz exportált adatok importálása mellett rdb-fájlokat importálhat bármely Redis-kiszolgálóról, amely bármilyen felhőben vagy környezetben fut, például Linux, Windows vagy felhőszolgáltatók, például az Amazon Web Services. Ehhez töltse fel az RDB-fájlt a kívánt Redis-kiszolgálóról egy lapra vagy blokkblobba egy Azure Storage-fiókban, majd importálja azt a prémium szintű Azure-gyorsítótárba a Redis-példányhoz. Előfordulhat például, hogy szeretné exportálni az adatokat az éles gyorsítótárból, és importálni egy átmeneti környezet részeként tesztelésre vagy áttelepítésre használt gyorsítótárba.

> [!IMPORTANT]
> A Redis-gyorsítótártól eltérő Redis-kiszolgálókról exportált adatok sikeres importálásához a lapblobok használatakor a lapblob méretét egy 512 bájtos határhoz kell igazítani. A mintakód a szükséges bájtos kitöltés végrehajtásához [lásd: Mintalapblob-feltöltés.](https://github.com/JimRoberts-MS/SamplePageBlobUpload)
>
>

### <a name="what-rdb-versions-can-i-import"></a>Milyen RDB-verziókat importálhatok?

Az Azure Cache for Redis támogatja az RDB-importálást az RDB 7-es verzióján keresztül.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Elérhető a gyorsítótár importálási/exportálási művelet során?
* **Exportálás** – A gyorsítótárak továbbra is elérhetők maradnak, és az exportálási művelet során továbbra is használhatja a gyorsítótárat.
* **Importálás** – A gyorsítótárak elérhetetlenné válnak, amikor egy importálási művelet elindul, és az importálási művelet befejezésekor is használhatók lesznek.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Használhatom az Importálás/exportálást a Redis fürttel?
Igen, és importálhat/exportálhat fürtözött gyorsítótár és nem fürtözött gyorsítótár között. Mivel a Redis-fürt [csak a 0 adatbázist támogatja,](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)a 0-tól eltérő adatbázisokban lévő adatok importálása nem történik meg. Fürtözött gyorsítótár-adatok importálásakor a kulcsok újraelosztásra kerülnek a fürt szegmensei között.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hogyan működik az importálás/exportálás egyéni adatbázis-beállítással?
Egyes tarifacsomagok különböző [adatbázis-korlátokkal](cache-configure.md#databases)rendelkeznek, ezért az importálás során bizonyos `databases` szempontokat figyelembe kell venni, ha a gyorsítótár létrehozása során egyéni értéket állított be a beállításhoz.

* Ha olyan tarifacsomagra importál, amelyalacsonyabb korláttal van eltántikulálva, `databases` mint az a szint, amelyből exportált:
  * Ha az alapértelmezett számú `databases`, amely 16 az összes tarifacsomag, nem vesznek el adatokat.
  * Ha olyan egyéni számot `databases` használ, amely az importálandó réteg korlátai közé esik, nem vesznek el adatok.
  * Ha az exportált adatok olyan adatbázisban tárolt adatokat tartalmaztak, amelyek meghaladják az új réteg korlátait, a magasabb szintű adatbázisokból származó adatok nem lesznek importálva.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Miben különbözik az importálás/exportálás a Redis perzisztenciától?
A Redis-megőrzéshez az Azure Cache lehetővé teszi a Redisben tárolt adatok megőrzését az Azure Storage számára. Ha az adatmegőrzés konfigurálva van, az Azure Cache for Redis megőrzi az Azure Cache for Redis egy Redis bináris formátumban a lemez egy konfigurálható biztonsági mentési gyakoriság alapján egy pillanatképet. Ha olyan katasztrofális esemény következik be, amely letiltja mind az elsődleges, mind a replika-gyorsítótárat, a gyorsítótár-adatok automatikusan visszaállnak a legutóbbi pillanatkép használatával. További információ: [Az adatmegőrzés konfigurálása a Redis prémium szintű Azure-gyorsítótárához.](cache-how-to-premium-persistence.md)

Az Importálás/ exportálás lehetővé teszi, hogy adatokat hozzon be vagy exportáljon az Azure Cache for Redis számára. Nem konfigurálja a biztonsági mentést és visszaállítást a Redis adatmegőrzéshasználatával.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Automatizálható az importálás/exportálás PowerShell, CLI vagy más felügyeleti ügyfelek használatával?
Igen, a PowerShell-utasításokhoz [lásd: Az Azure-gyorsítótár importálása a Redis és](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) [az Azure-gyorsítótár exportálásához a Redis.](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis)

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Időelt- és megdöntési hiba történt az importálási/exportálási művelet során. Mit jelentsen ez?
Ha a művelet megkezdése előtt 15 percnél hosszabb ideig marad az **Adatok importálása** vagy **az Adatok exportálása** panelen, a következő példához hasonló hibaüzenet jelenik meg:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

A probléma megoldásához kezdeményezze az importálási vagy exportálási műveletet 15 perc eltelte előtt.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Hiba történt az adatok Azure Blob Storage-ba való exportálásakor. Mi történt?
Az exportálás csak a lapblobként tárolt RDB-fájlokkal működik. Más blobtípusok jelenleg nem támogatottak, beleértve a blob storage-fiókok gyakori és ritka elérésű szintekkel. További információkat az [Azure Storage-fiókok áttekintésében](../storage/common/storage-account-overview.md) találhat.

## <a name="next-steps"></a>További lépések
További információ a további prémium szintű gyorsítótár-funkciók használatáról.

* [Bevezetés az Azure Cache for Redis premium szintű rétegbe](cache-premium-tier-intro.md)
