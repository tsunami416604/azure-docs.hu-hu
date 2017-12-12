---
title: "Az Azure Redis Cache adatok importálása és exportálása |} Microsoft Docs"
description: "Ismerje meg, hogyan importálhatja és exportálhatja az adatokat a blob storage a premium Azure Redis Cache osztályt érkező vagy oda irányuló"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: sdanie
ms.openlocfilehash: 761c0d808eb787517473036e53992ff5e49b17fe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Az Azure Redis Cache adatok importálása és exportálása
Importálási/exportálási egy Azure Redis Cache adatok felügyeleti művelet, amely lehetővé teszi, hogy adatokat importáljon belőlük az Azure Redis Cache vagy exportál adatokat az Azure Redis Cache importálni és Redis gyorsítótár-adatbázis (Rekordadatbázis) pillanatkép exportálása egy prémium szintű gyorsítótár az Azure-Tárfiók a blob. 

- **Exportálás** -oldalakra vonatkozó Blob az Azure Redis Cache Rekordadatbázis pillanatképek exportálhatja.
- **Importálás** -oldalakra vonatkozó Blob vagy egy Blokkblob importálhatja a Redis gyorsítótár Rekordadatbázis pillanatképeket.

Importálási/exportálási lehetővé teszi különböző Azure Redis Cache-példányok közötti áttelepítése vagy a gyorsítótár adatokkal használat előtt.

Ez a cikk egy útmutatót biztosít az Azure Redis Cache-adatok importálása és exportálása, és gyakran feltett kérdésekre adott válaszokat biztosít.

> [!IMPORTANT]
> Importálási/exportálási jelenleg előzetes verzióban érhető, és csak [prémium csomagban](cache-premium-tier-intro.md) gyorsítótárazza.
>
>

## <a name="import"></a>Importálás
Importálás a Redis-kiszolgáló futtatja a felhő vagy a környezet, beleértve a futó Linux, Windows vagy bármely felhőalapú szolgáltató, például az Amazon Web Services, míg mások Redis vinnie a Redis-kompatibilis Rekordadatbázis fájlok használható. Adatok importálása egyszerű módja a gyorsítótár létrehozásához előre megadott adatokkal. Az importálási folyamat során az Azure Redis Cache Rekordadatbázis fájlokat az Azure storage betölti a memóriába, és majd szúrja be a kulcsokat a gyorsítótárba.

> [!NOTE]
> Az importálási művelet megkezdése előtt győződjön meg arról, hogy a Redis adatbázis (Rekordadatbázis) fájl vagy fájlokat vannak töltve az Azure-tárfiókba, a ugyanabban a régióban, és az Azure Redis Cache példányt előfizetés lapot vagy blokk blobokat. További információkért lásd: [Ismerkedés az Azure Blob storage szolgáltatással](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Ha a Rekordadatbázis fájlkiszolgáló az exportált a [Azure Redis Cache exportálása](#export) funkció, a Rekordadatbázis fájl oldalakra vonatkozó blob már tárolja, és készen áll a importálása.
>
>

1. Egy vagy több exportált gyorsítótár blobot, importálandó [keresse meg a gyorsítótár](cache-configure.md#configure-redis-cache-settings) a Azure-portálon, majd kattintson a **adatimportálás** a a **erőforrás menü**.

    ![Adatok importálása][cache-import-data]
2. Kattintson a **válasszon Blob(s)** , és válassza ki a tárfiókot, amely tartalmazza az importálandó adatok megadásához.

    ![Tárfiók kiválasztása][cache-import-choose-storage-account]
3. Kattintson a tároló, amely tartalmazza az importálandó adatok megadásához.

    ![Válassza ki a tárolót][cache-import-choose-container]
4. Válassza ki egy vagy több blobot importálása a blob nevének balra arra a területre, és kattintson a **válasszon**.

    ![Válassza ki a blobok][cache-import-choose-blobs]
5. Kattintson a **importálása** az importálási folyamat megkezdéséhez.

   > [!IMPORTANT]
   > A gyorsítótár nem gyorsítótár-ügyfelek által elérhető az importálási folyamat során, és a gyorsítótárban adat törlődik.
   >
   >

    ![Importálás][cache-import-blobs]

    Az értesítések következő Azure-portálról, vagy az események megtekintésével figyelheti az importálási művelet előrehaladását a [napló](../azure-resource-manager/resource-group-audit.md).

    ![Importálás folyamatban][cache-import-data-import-complete]

## <a name="export"></a>Exportálás
Exportálás lehetővé teszi az Azure Redis Cache Redis kompatibilis Rekordadatbázis (oka) t a tárolt adatok exportálását. Ez a szolgáltatás segítségével tárolt adatok mozgatása egy Azure Redis Cache példányt, vagy egy másik Redis-kiszolgálóhoz. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális Gépen, amelyen az Azure Redis Cache server-példányt, és a fájl feltöltése a kijelölt tárfiókkal. Az exportálási művelet befejezése után a vagy állapota sikeres végrehajtásával vagy hibajelzéssel az ideiglenes fájl törlődik.

1. Exportálja a gyorsítótár tartalmát tároló, [keresse meg a gyorsítótár](cache-configure.md#configure-redis-cache-settings) a Azure-portálon, majd kattintson a **adatok exportálása** a a **erőforrás menü**.

    ![A tároló kiválasztása][cache-export-data-choose-storage-container]
2. Kattintson a **válassza ki a tároló** , és válassza ki a kívánt tárfiókot. A tárfiók, a gyorsítótár az előfizetés és a régióban kell lennie.

   > [!IMPORTANT]
   > Exportálja a lapblobokat, amelyek klasszikus és Resource Manager tárfiókok által támogatott, de nem támogatja a együttműködik [Blob storage-fiókok](../storage/blobs/storage-account-options.md#blob-storage-accounts) most.
   >
   >

    ![Tárfiók][cache-export-data-choose-account]
3. Válassza ki a kívánt blob tároló, és kattintson a **válasszon**. Új tároló használata, kattintson a **tároló hozzáadása** hozzáadásához először, és állítsa be azt a listából.

    ![A tároló kiválasztása][cache-export-data-container]
4. Adjon meg egy **Blob előtagja** kattintson **exportálása** az exportálási folyamat elindítása. A blob nevének előtagja szolgál az exportálási művelet által létrehozott fájlok nevének előtagja.

    ![Exportálás][cache-export-data]

    Az értesítések következő Azure-portálról, vagy az események megtekintésével figyelheti az exportálási művelet előrehaladását a [napló](../azure-resource-manager/resource-group-audit.md).

    ![Az adatok exportálása befejeződött][cache-export-data-export-complete]

    Gyorsítótárak az exportálási folyamat során elérhetők maradnak.

## <a name="importexport-faq"></a>Importálási/exportálási – gyakori kérdések
Ez a szakasz az Import/Export szolgáltatás kapcsolatos gyakori kérdésekre.

* [Milyen árképzési tiers használhatja az Import/Export?](#what-pricing-tiers-can-use-importexport)
* [Szeretnék adatokat importálhat a Redis-kiszolgáló?](#can-i-import-data-from-any-redis-server)
* [Milyen Rekordadatbázis verziók lehet importálni?](#what-rdb-versions-can-i-import)
* [A gyorsítótár érhető el az importálási/exportálási művelet közben?](#is-my-cache-available-during-an-importexport-operation)
* [Használhatok Import/Export Redis-fürt?](#can-i-use-importexport-with-redis-cluster)
* [Hogyan működik a Import/Export beállítása egy egyéni adatbázisok?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Miben különbözik Import/Export Redis-adatmegőrzés?](#how-is-importexport-different-from-redis-persistence)
* [Automatizálható a PowerShell, a parancssori felületen vagy a más felügyeleti ügyfelek Import/Export?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Időtúllépési hiba érkezett a importálási/exportálási művelet közben. Mit jelent?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Hiba történt az adatok Azure Blob Storage exportálásakor jelent. mi történt?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Milyen árképzési tiers használhatja az Import/Export?
Importálási/exportálási csak a prémium tarifacsomag érhető el.

### <a name="can-i-import-data-from-any-redis-server"></a>Szeretnék adatokat importálhat a Redis-kiszolgáló?
Igen, mellett az Azure Redis Cache példány exportált adatok importálása, importálhatja Rekordadatbázis fájlok bármely Redis-t futtató kiszolgáló összes felhő vagy a környezetben, például a Linux, Windows, a és felhőalapú szolgáltatók például az Amazon Web Services. Ehhez a kívánt Redis-kiszolgáló Rekordadatbázis a fájl feltöltése be egy Azure Storage-fiókban lévő lapot vagy blokk blob, és importálja azt a premium Azure Redis Cache példány. Érdemes lehet például az adatok exportálása az éles gyorsítótárból, és importálja a fájlt egy tesztelési, illetve áttelepítési egy átmeneti környezet részeként használt gyorsítótár.

> [!IMPORTANT]
> Oldalakra vonatkozó blob használatakor eltérő Azure Redis Cache Redis-kiszolgálóról exportált adatokat importálni, a blob mérete 512 bájtos határra kell beállítani. Mintakód bármely szükséges bájt kitöltési végrehajtására, lásd: [minta lap blog feltöltéséhez](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Milyen Rekordadatbázis verziók lehet importálni?

Azure Redis Cache Rekordadatbázis importálási legfeljebb 7-es verzió Rekordadatbázis keresztül támogat.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>A gyorsítótár érhető el az importálási/exportálási művelet közben?
* **Exportálás** - gyorsítótárak elérhetők maradnak, és továbbra is a gyorsítótár használata az exportálási művelet során.
* **Importálás** - gyorsítótárak elérhetetlenné válik, az importálási művelet indításakor, és az importálási művelet befejeződésekor elérhető lesz.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Használhatok Import/Export Redis-fürt?
Igen, és akkor is importálási/exportálási egy fürtözött gyorsítótár és egy nem fürtözött gyorsítótár között. A Redis-fürt óta [csak által támogatott adatbázis-0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), egyetlen megadott adattal sem adatbázisok 0 kivételével nincs importálva. Fürtözött gyorsítótár-adatok importálása, a kulcsok terjeszti a szilánkok a fürt között.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Hogyan működik a Import/Export beállítása egy egyéni adatbázisok?
Különböző tartalmaznak az egyes tarifacsomagok [korlátok adatbázisok](cache-configure.md#databases), úgy, hogy nincs szempontokat importálásakor, ha konfigurálta az egyéni értékét a `databases` beállítása a gyorsítótár létrehozása közben.

* A tarifacsomag alsó importálásakor `databases` a réteget, ahol az exportált határa:
  * Alapértelmezett számának használata `databases`, amely az összes tarifacsomagok 16, adatok nem vesztek el.
  * Ha egyéni számos használ `databases` adott esik a határokon belül a réteget, amelyhez importál, az adatok nem vesztek el.
  * Ha az exportált adatok egy adatbázist, amely meghaladja a új réteg adatokat, a rendszer nem importálja ezeket magasabb adatbázisból származó adatok.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Miben különbözik Import/Export Redis-adatmegőrzés?
Azure Redis Cache adatmegőrzési lehetővé teszi a Redis Azure Storage-ban tárolt adatok megőrzéséhez. Adatmegőrzési konfigurálásakor az Azure Redis Cache Redis bináris formában lemezre egy konfigurálható biztonsági mentési gyakoriság alapján a Redis gyorsítótár pillanatkép továbbra is fennáll. Ha egy katasztrofális esemény történik, akkor az elsődleges és a replika-gyorsítótár, a gyorsítótárazott adatokat állítja automatikusan a legújabb pillanatkép. További információkért lásd: [konfigurálása prémium szintű Azure Redis Cache adatok megőrzését](cache-how-to-premium-persistence.md).

Importálás / exportálás lehetővé teszi az adatok, vagy az Azure Redis Cache exportálja. Biztonsági mentés konfigurálása, és nem visszaállítása a Redis-adatmegőrzés használatával.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Automatizálható a PowerShell, a parancssori felületen vagy a más felügyeleti ügyfelek Import/Export?
Igen, a PowerShell útmutatásért lásd: [importálása a Redis gyorsítótár](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) és [exportálása a Redis gyorsítótár](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Időtúllépési hiba érkezett a importálási/exportálási művelet közben. Mit jelent?
Ha a számítógép a a **adatimportálás** vagy **exportálhatja az adatokat** panelen a művelet kezdeményezése előtt legfeljebb 15 perc, a következőhöz hasonló hibaüzenettel hibaüzenetet kap:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Probléma megoldása, kezdeményezni az importálási vagy exportálási művelet előtt 15 perc telt el.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Hiba történt az adatok Azure Blob Storage exportálásakor jelent. mi történt?
Exportálás csak Rekordadatbázis fájlokat tárolja a lapblobokat működik. Egyéb blob típusú jelenleg nem támogatottak, beleértve a blob storage-fiókok gyakran és ritkán használt rétegekkel. További információkért lásd: [Blob storage-fiókok](../storage/blobs/storage-account-options.md#blob-storage-accounts).

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan további premium gyorsítótár-funkciók használatára.

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
