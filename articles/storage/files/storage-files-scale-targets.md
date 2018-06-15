---
title: Az Azure fájlok méretezhetőségi és Teljesítménycélok |} Microsoft Docs
description: További tudnivalók a méretezhetőségi és Teljesítménycélok Azure-fájlok, beleértve a kapacitás, a lekérdezési gyakorisága és a bejövő és kimenő sávszélesség korlátja.
services: storage
documentationcenter: na
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: beb3e5caf8c8dce9b2ea06bbd0a2ea5a4e05a714
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738074"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Az Azure fájlok méretezhetőségi és Teljesítménycélok
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló a felhőben, amelyek elérhetők az iparági szabványos SMB protokollon keresztül. Ez a cikk ismerteti a méretezhetőségi és Teljesítménycélok Azure fájlok és az Azure fájl Sync (előzetes verzió).

Az itt felsorolt méretezhetőségi és Teljesítménycélok csúcskategóriás célozza, de előfordulhat, hogy a központi telepítésben lévő többi változó nem érinti. Például egy fájl átviteli is korlátozhatja a rendelkezésre álló hálózati sávszélességet, nem csak az Azure Fileshoz szolgáltatást futtató kiszolgálók. Erősen ajánlott a használati mintáját, és határozza meg, hogy a méretezhetőség és teljesítmény Azure fájlok igényeinek tesztelése. Azt is elkötelezettek adott idő alatt a korlátok növelését. Adjon ne habozzon, küldjön visszajelzést, vagy a Megjegyzések alatt vagy a a [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), mely korlátok kapcsolatos szeretne látni, növeli.

## <a name="azure-storage-account-scale-targets"></a>Az Azure storage-fiók méretezési célok
A szülő erőforrás egy Azure fájlmegosztás az Azure storage-fiók. A tárfiók a tárolókészletet, amely az adatok tárolásához több tárolószolgáltatásokra, köztük az Azure fájlok által használható az Azure-ban jelöli. Egyéb szolgáltatások tárolt adatok storage-fiókok Azure Blob Storage tárolóban, az Azure Queue storage és az Azure Table storage. A következő célokat alkalmazni az adattárolás a tárfiókban lévő összes tárolási szolgáltatások:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Tárhely fiók kihasználtságát a többitől tárolás az Azure fájlmegosztások a tárfiókban lévő hatással van. Például ha eléri a maximális tárfiókok kapacitásával az Azure Blob storage szolgáltatással, csak akkor hozhat létre az Azure-fájlmegosztáshoz, új fájlok akkor is, ha az Azure fájlmegosztás maximális megosztási méreténél kisebbre.

## <a name="azure-files-scale-targets"></a>Az Azure fájlok méretezési célok
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Az Azure fájlszinkronizálás méretezési célok
Azure-fájl szinkronizálással azt próbált korlátlan használatra tervezhet lehetőség szerint ez azonban nem mindig lehetséges. Az alábbi táblázat azt jelzi, a határokat a tesztelés során, és mely célok ténylegesen szigorú korlátozásokat:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Az Azure fájlszinkronizálás metrikák
Mivel az Azure fájlszinkronizálás ügynök, amely kapcsolódik az Azure fájlmegosztásokat Windows Server-számítógépen fut, a hatékony szinkronizálási teljesítmény attól függ, hogy a infrastruktúrában tényező: Windows Server és az alapjául szolgáló lemez konfigurációját a hálózati sávszélesség között a kiszolgáló és az Azure storage, fájlméret, teljes adatkészlet méretének és a DataSet adatkészlet a tevékenység. Azure fájlszinkronizálás a fájl szintjén működik, mert egy fájlszinkronizálás Azure-alapú megoldás teljesítményétől jobban mérik a másodpercenként feldolgozott objektumok (fájlok és könyvtárak) száma. 
 
Az Azure-szinkronizálás teljesítmény fontos két fázisból áll:
1. **Kezdeti egyszeri telepítése**: A kezdeti telepítése a teljesítmény optimalizálása érdekében tekintse meg [Azure fájl szinkronizálással bevezetési](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) optimális központi telepítés részleteinek.
2. **A folyamatban lévő szinkronizálás**: követően az adatok kezdetben rendezés az Azure fájlmegosztások, Azure fájlszinkronizálás tartja több végpontot szinkronban.

Segítségével az egyes szakaszok az üzembe helyezés megtervezésében, az alábbiakban az eredmények figyelhetők a egy config rendszeren belső tesztelése során
| Rendszer-konfiguráció |  |
|-|-|
| CPU | 64 virtuális magos 64 MiB 3. gyorsítótárával |
| Memory (Memória) | 128 GiB |
| Lemez | SAS-lemezek RAID 10-es az akkumulátor biztonsági gyorsítótár |
| Network (Hálózat) | 1 GB/s hálózaton |
| Számítási feladat | Általános célú fájlkiszolgáló|

| Kezdeti egyszeri telepítése  |  |
|-|-|
| Objektumok száma | 10 millió objektumok | 
| A DataSet mérete| ~ 4 TiB |
| Átlagos mérete | ~ 500 KiB (legnagyobb fájl: 100 GiB) |
| Töltse fel az átviteli sebesség | 15 objektumok száma másodpercenként |
| Namespace letöltési átviteli * | 350 objektumok száma másodpercenként |
 
Ha a kiszolgáló új végpont jön létre, az Azure fájl Sync-ügynök nem töltse le a fájl tartalma. A szinkronizált először teljes névterét és majd eseményindítók háttérben visszaírási töltse le a fájlokat, vagy teljes egészében vagy felhőalapú rétegezéséhez engedélyezve van-e, a felhő beállítása a kiszolgáló végpont rétegzési házirendet.

| A folyamatban lévő szinkronizálás  |   |
|-|--|
| Szinkronizált objektumok száma| 125,000 objektumok (~ 1 %-os forgalom) | 
| A DataSet mérete| 50 giB |
| Átlagos mérete | ~ 500 KiB (legnagyobb fájl: 100 GiB) |
| Töltse fel az átviteli sebesség | 20 objektumok száma másodpercenként |
| Teljes átviteli sebesség * | 30 objektumok száma másodpercenként |
 
Ha felhőalapú rétegezéséhez engedélyezve van, akkor valószínű, hogy tekintse át az adatokat a rendszer letölti a fájlt csak néhány jobb teljesítményt. Azure fájlszinkronizálás csak az adatok gyorsítótárazott fájlok tölt le, abban az esetben, ha bármely, a végpontok módosultak. Rétegzett vagy újonnan létrehozott fájlok esetén az ügynök nem tölti le a fájladatok, és ehelyett szinkronizálásának csak a kiszolgáló végpontok a névteret. Az ügynök is támogatja a rétegzett fájlok részleges letölti azokat a felhasználó által érik el. 
 
> [!Note]  
> A fenti számok nincsenek utalhat, hogy a működés teljesítményét. Az aktuális teljesítménye a Ez a szakasz elején leírt módon történő több tényezőtől függ.

Az üzembe helyezéshez általános útmutatóként néhány dolgot szem előtt kell tartania:
- Az objektum átviteli körülbelül arányosan arányában szinkronizálási csoportok a kiszolgálón. Nagyobb átviteli sebességben, ami is korlátozza a kiszolgáló és a hálózat adatai azonos egy kiszolgálón több szinkronizálási csoportokba adja eredményül.
- Az objektum átviteli egy második átviteli MiB fordítottan arányos. A kisebb fájlok fog tapasztalni nagyobb átviteli sebesség szempontjából egy második, de kevésbé MiB egy második átviteli feldolgozott objektumok száma. Ezzel szemben a nagyobb méretű fájlok elérhetővé válik egy második, de magasabb MiB egy második átviteli feldolgozott kevesebb objektumot. Egy második átviteli MiB korlátozza az Azure-fájlok méretezési célokat. 

## <a name="see-also"></a>Lásd még
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Egy Azure fájlszinkronizálás központi telepítésének tervezése](storage-sync-files-planning.md)
- [Az egyéb tárolási szolgáltatások méretezhetőségi és Teljesítménycélok](../common/storage-scalability-targets.md)