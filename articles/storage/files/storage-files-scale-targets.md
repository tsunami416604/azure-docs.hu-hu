---
title: Méretezhetőségi és teljesítménybeli célok Azure Files
description: Ismerje meg a Azure Files méretezhetőségét és teljesítményét, beleértve a kapacitást, a kérelmek sebességét, valamint a bejövő és kimenő sávszélesség korlátozásait.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2e05f0cb46e1e54ced5911c0a78dd026dbb7f4fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905588"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Méretezhetőségi és teljesítménybeli célok Azure Files

A [Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely az iparági szabványnak megfelelő SMB protokollon keresztül érhető el. Ez a cikk a Azure Files és Azure File Sync méretezhetőségi és teljesítménybeli céljait ismerteti.

Az itt felsorolt skálázhatósági és teljesítményi célok magas végpontok, de a telepítés más változói is befolyásolhatják. Előfordulhat például, hogy a fájl átviteli sebességét a rendelkezésre álló hálózati sávszélesség korlátozza, nem csak a Azure Files szolgáltatást üzemeltető kiszolgálók. Nyomatékosan javasoljuk a használati minta tesztelését annak meghatározására, hogy a Azure Files méretezhetősége és teljesítménye megfelel-e a követelményeknek. Emellett elkötelezettek vagyunk a korlátok időbeli változásának növelésére. Kérjük, ne habozzon, hogy küldjön visszajelzést az alábbi megjegyzésekben vagy a [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), hogy mely korlátokat szeretné megtekinteni.

## <a name="azure-storage-account-scale-targets"></a>Azure Storage-fiók méretezési céljai

Az Azure-fájlmegosztás szülő erőforrása egy Azure Storage-fiók. A Storage-fiók az Azure-beli tárterületet jelöli, amelyet több tárolási szolgáltatás, többek között a Azure Files is használhat az adattároláshoz. A Storage-fiókokban tárolt egyéb szolgáltatások az Azure Blob Storage, az Azure üzenetsor Storage és az Azure Table Storage. Az alábbi célok az összes Storage-szolgáltatást a Storage-fiókba tároló összes tárolási szolgáltatásra érvényesek:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Az általános célú Storage-fiókok más tárolási szolgáltatásokból való kihasználtsága hatással van az Azure-fájlmegosztás használatára a Storage-fiókban. Ha például eléri a Storage-fiók kapacitását az Azure Blob Storage-ban, nem fog tudni új fájlokat létrehozni az Azure-fájlmegosztás számára, még akkor is, ha az Azure-fájlmegosztás a megosztások maximális mérete alá esik.

## <a name="azure-files-scale-targets"></a>Azure Files méretezési célok

Háromféle korlátozást kell figyelembe venni a Azure Files: Storage-fiókok,-megosztások és-fájlok esetében.

Például: prémium fájlmegosztás esetén egyetlen megosztás elérheti a 100 000 IOPS, és egyetlen fájl akár 5 000 IOPS is méretezhető. Ha tehát három fájl található egy megosztásban, az adott megosztásból lekérhető maximális IOPS 15 000.

### <a name="standard-storage-account-limits"></a>Standard Storage-fiók korlátai

Ezeket a korlátokat az [Azure Storage-fiók méretezési céljai](#azure-storage-account-scale-targets) című szakaszban találja.

### <a name="premium-filestorage-account-limits"></a>Prémium szintű FileStorage-fiók korlátai

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> A Storage-fiókra vonatkozó korlátozások minden megosztásra érvényesek. A FileStorage-fiókok maximálisra skálázása csak akkor érhető el, ha FileStorage-fiókban csak egy megosztás van.

### <a name="file-share-and-file-scale-targets"></a>Fájlmegosztás és méretezési célok

> [!NOTE]
> Az 5 TiB-nál nagyobb standard fájlmegosztás bizonyos korlátozásokkal rendelkezik.
> A korlátozások, a regionális információk és a nagyobb fájlmegosztás-méretek engedélyezésére vonatkozó utasítások listáját a tervezési útmutató a további [fájlmegosztás](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier) című szakasza tartalmazza.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync méretezési célok

Azure File Sync a korlátlan használat céljával lett tervezve, de a korlátlan használat nem mindig lehetséges. Az alábbi táblázat a Microsoft tesztelésének határait mutatja be, és azt is jelzi, hogy mely célok rögzített határértékek:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Teljesítmény-mérőszámok Azure File Sync

Mivel a Azure File Sync ügynök egy olyan Windows Server rendszerű gépen fut, amely az Azure-fájlmegosztást csatlakoztatja, a tényleges szinkronizálási teljesítmény számos tényezőtől függ az infrastruktúrában: a Windows Server és a mögöttes lemez konfigurációja, a hálózati sávszélesség a kiszolgáló és az Azure Storage, a fájlméret, a teljes adatkészlet mérete és az adatkészlet tevékenysége között. Mivel Azure File Sync a fájl szintjén működik, a Azure File Sync-alapú megoldás teljesítménybeli jellemzői jobban mérhetők a másodpercenként feldolgozott objektumok (fájlok és könyvtárak) száma alapján.

Azure File Sync esetében a teljesítmény kritikus fontosságú két szakaszban:

1. **Kezdeti egyszeri kiépítés**: a teljesítmény optimalizálásához a kezdeti kiépítés során tekintse meg a bevezetést [Azure file Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) az optimális üzembe helyezési részletekért.
2. **Folyamatos szinkronizálás**: miután az Azure-fájlmegosztás először befejeződik az adatgyűjtés során, Azure file Sync több végpontot is megtart a szinkronizálásban.

Az alábbi szakaszokban megtervezheti az üzembe helyezést, és a belső tesztelés során megfigyelheti a konfigurációt a rendszeren.

| Rendszerkonfiguráció |  |
|-|-|
| CPU | 64 virtuális magok a 64 MiB L3 gyorsítótárral |
| Memória | 128 GiB |
| Lemez | SAS-lemezek RAID 10 akkumulátorral rendelkező gyorsítótárral |
| Network (Hálózat) | 1 GB/s hálózat |
| Számítási feladat | általános célú fájlkiszolgáló|

| Egyszeri kiépítés kezdeti időpontja  |  |
|-|-|
| Objektumok száma | 25 000 000 objektum |
| Adatkészlet mérete| ~ 4,7 TiB |
| Fájlméret átlagos mérete | ~ 200 KiB (legnagyobb fájl: 100 GiB) |
| Feltöltési sebesség | 20 objektum másodpercenként |
| Névtér letöltési átviteli sebessége * | 400 objektum/másodperc |

\* Új kiszolgálói végpont létrehozásakor a Azure File Sync ügynök nem tölti le a fájl tartalmát. Először szinkronizálja a teljes névteret, majd elindítja a háttérben való visszahívást, hogy letöltse a fájlokat, akár teljes egészében, akár a felhőalapú rétegek engedélyezése esetén a kiszolgálói végponton beállított felhő-előállítási házirendhez.

| Folyamatban lévő szinkronizálás  |   |
|-|--|
| Szinkronizált objektumok száma| 125 000 objektum (~ 1%-os forgalom) |
| Adatkészlet mérete| 50 GiB |
| Fájlméret átlagos mérete | ~ 500 KiB |
| Feltöltési sebesség | 20 objektum másodpercenként |
| Teljes letöltési teljesítmény * | 60 objektum/másodperc |

\* Ha a felhő-rétegek engedélyezve vannak, a jobb teljesítmény várható, mivel csak néhány fájl töltődik le. Azure File Sync csak akkor tölti le a gyorsítótárazott fájlok mennyiségét, ha azok bármelyik végponton módosulnak. Bármely rétegű vagy újonnan létrehozott fájl esetében az ügynök nem tölti le a fájlokat, hanem csak az összes kiszolgálói végpontra szinkronizálja a névteret. Az ügynök támogatja a többplatformos fájlok részleges letöltését is, mivel azok a felhasználó számára érhetők el. 

> [!Note]  
> A fenti számok nem utalnak arra, hogy milyen teljesítményt fog tapasztalni. A tényleges teljesítmény több tényezőtől is függ, ahogy az a szakasz elején szerepel.

Az üzembe helyezésre vonatkozó általános útmutatóként vegye figyelembe a következőket:

- Az objektum átviteli sebessége körülbelül a kiszolgálón lévő szinkronizálási csoportok számával arányos. Az adatok több szinkronizálási csoportba való felosztása a kiszolgálókon jobb átviteli sebességet eredményez, amelyet a kiszolgáló és a hálózat is korlátoz.
- Az objektum átviteli sebessége fordítottan arányos a MiB másodpercenkénti átviteli sebességével. Kisebb fájlok esetében a másodpercenként feldolgozott objektumok száma, de a másodpercenkénti adatátviteli sebesség tekintetében nagyobb átviteli sebesség fog megjelenni. Ezzel szemben a nagyobb fájlok esetében kevesebb, másodpercenként feldolgozott objektum lesz feldolgozva, de másodpercenként nagyobb a MiB-átviteli sebesség. A MiB/másodperc átviteli sebességet a Azure Files méretezési célok korlátozzák.

## <a name="see-also"></a>Lásd még:

- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
