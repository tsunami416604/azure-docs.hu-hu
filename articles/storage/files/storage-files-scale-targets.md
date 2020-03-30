---
title: Az Azure Files méretezhetőségi és teljesítménycéljai
description: Ismerje meg az Azure Files méretezhetőségi és teljesítménycéljait, beleértve a kapacitást, a kérelmek arányát, valamint a bejövő és kimenő sávszélesség-korlátokat.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1a74ec3610367193b5eee53ea0e0818901433e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255131"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Az Azure Files méretezhetőségi és teljesítménycéljai

[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztásokat kínál a felhőben, amelyek az iparági szabványnak megfelelő SMB protokollon keresztül érhetők el. Ez a cikk ismerteti az Azure Files és az Azure File Sync méretezhetőségi és teljesítménycélokat.

Az itt felsorolt méretezhetőségi és teljesítménycélok csúcskategóriás célok, de a központi telepítés más változói is hatással lehetnek rájuk. Például egy fájl átviteli mertéje is korlátozhatja a rendelkezésre álló hálózati sávszélességet, nem csak az Azure Files szolgáltatást üzemeltető kiszolgálók. Javasoljuk, hogy tesztelje a használati mintát annak megállapítására, hogy az Azure Files méretezhetősége és teljesítménye megfelel-e az Ön igényeinek. Elkötelezettek vagyunk amellett is, hogy idővel növeljük ezeket a korlátokat. Kérjük, ne habozzon visszajelzést adni nekünk, akár az alábbi megjegyzésekben, akár az [Azure Files UserVoice-on,](https://feedback.azure.com/forums/217298-storage/category/180670-files)amelyekkel kapcsolatban növelni szeretné a korlátozásokat.

## <a name="azure-storage-account-scale-targets"></a>Az Azure storage-fiók méretezési céljai

Az Azure-fájlmegosztás szülőerőforrása egy Azure-tárfiók. A tárfiók az Azure-ban egy tárkészletet jelöl, amelyet több tárolási szolgáltatás, köztük az Azure Files is használhat az adatok tárolására. A tárfiókokban tárolt egyéb szolgáltatások az Azure Blob storage, az Azure Queue storage és az Azure Table storage. A következő célok az összes tárfiókban tárolt összes társzolgáltatást alkalmazza:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Az egyéb társzolgáltatások általános célú tárfiók-kihasználtsága hatással van az Azure-fájlmegosztásokra a tárfiókban. Ha például eléri a maximális tárfiók kapacitását az Azure Blob storage segítségével, akkor nem hozhat létre új fájlokat az Azure-fájlmegosztáson, még akkor sem, ha az Azure-fájlmegosztás a maximális megosztási méret alatt van.

## <a name="azure-files-scale-targets"></a>Az Azure Files méretezési célok

Az Azure Files esetében három korlátozási kategóriát kell figyelembe venni: tárfiókok, megosztások és fájlok.

Például: Prémium fájlmegosztások esetén egyetlen megosztás 100 000 IOPS-t érhet el, és egyetlen fájl akár 5000 IOPS-t is felskálázhat. Tehát, ha három fájl egy megosztás, a maximális IOPS kaphat, hogy a részvény 15.000.

### <a name="standard-storage-account-limits"></a>Szokásos tárfiók-korlátok

Tekintse meg az [Azure storage-fiók méretezési célok](#azure-storage-account-scale-targets) szakasz ezeket a korlátokat.

### <a name="premium-filestorage-account-limits"></a>Prémium filestorage fiók korlátai

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> A tárfiók-korlátok az összes megosztásra vonatkoznak. A FileStorage-fiókok maximális felskálázása csak akkor érhető el, ha FileStorage-fiókonként csak egy megosztás van.

### <a name="file-share-and-file-scale-targets"></a>Fájlmegosztási és fájlméretezési célok

> [!NOTE]
> Standard fájl részvények nagyobb, mint 5 TiB bizonyos korlátozások és a regionális korlátozásokat.
> A korlátozások, a területi információk és a nagyobb fájlmegosztási méretek engedélyezésére vonatkozó utasításokat a tervezési útmutató [Beépített fájlmegosztási](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) szakaszában találja.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync skálázási céljai

Az Azure File Sync célja a korlátlan használat, de a korlátlan használat nem mindig lehetséges. Az alábbi táblázat a Microsoft tesztelésének határait mutatja be, és azt is, hogy mely célok a szigorú korlátok:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Az Azure File Sync teljesítménymetrikái

Mivel az Azure File Sync ügynök egy Olyan Windows Server-gépen fut, amely az Azure-fájlmegosztásokhoz csatlakozik, a tényleges szinkronizálási teljesítmény számos tényezőtől függ az infrastruktúrában: a Windows Server től és az alapul szolgáló lemezkonfigurációtól, a hálózati sávszélességtől a kiszolgáló és az Azure-tároló, a fájlméret, a teljes adatkészlet mérete és az adatkészlet tevékenysége között. Mivel az Azure File Sync fájlszinten működik, az Azure File Sync-alapú megoldás teljesítményjellemzőit jobban méri konkretizálják a másodpercenként feldolgozott objektumok (fájlok és könyvtárak) száma.

Az Azure File Sync esetében a teljesítmény két szakaszban kritikus:

1. **Kezdeti egyszeri üzembe helyezés:** A kezdeti kiépítés teljesítményének optimalizálása érdekében tekintse meg [az Üzembe helyezés az Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) az optimális üzembe helyezési részleteket.
2. **Folyamatos szinkronizálás:** Miután az adatok at az Azure fájlmegosztások, az Azure File Sync több végpontszinkronban tartja.

Az egyes szakaszok üzembe helyezésének megtervezéséhez az alábbiakban a rendszer belső tesztelése során megfigyelt eredményeket

| Rendszerkonfiguráció |  |
|-|-|
| CPU | 64 virtuális magok 64 MiB L3 cache |
| Memory (Memória) | 128 GiB |
| Lemez | SAS lemezek RAID 10 akkumulátorral ellátott gyorsítótárral |
| Network (Hálózat) | 1 Gbps hálózat |
| Számítási feladat | Általános célú fájlkiszolgáló|

| Kezdeti egyszeri kiépítés  |  |
|-|-|
| Objektumok száma | 25 millió tárgy |
| Adatkészlet mérete| ~4,7 TiB |
| Átlagos fájlméret | ~200 KiB (Legnagyobb fájl: 100 GiB) |
| Átviteli áttöltő | Szinkronizálási csoportonként másodpercenként 20 objektum |
| Névtér letöltési átviteli fóka* | 400 objektum másodpercenként |

*Új kiszolgálóvégpont létrehozásakor az Azure File Sync ügynök nem tölti le a fájltartalmát. Először szinkronizálja a teljes névteret, majd elindítja a háttér-visszahívást a fájlok teljes teljes letöltéséhez, vagy ha a felhőrétegezés engedélyezve van, a kiszolgáló végpontján beállított felhőrétegezési házirendre.

| Folyamatos szinkronizálás  |   |
|-|--|
| Szinkronizált objektumok száma| 125 000 objektum (~1% forgalom) |
| Adatkészlet mérete| 50 GiB |
| Átlagos fájlméret | ~500 KiB |
| Átviteli áttöltő | Szinkronizálási csoportonként másodpercenként 20 objektum |
| Teljes letöltési átviteli -átmenő* | 60 objektum másodpercenként |

*Ha a felhőrétegezés engedélyezve van, valószínűleg jobb teljesítményt fog megfigyelni, mivel csak a fájladatok egy része töltődik le. Az Azure File Sync csak akkor tölti le a gyorsítótárazott fájlok adatait, ha azok a végpontok bármelyikén módosulnak. A rétegzett vagy újonnan létrehozott fájlok esetében az ügynök nem tölti le a fájladatokat, hanem csak szinkronizálja a névteret az összes kiszolgálói végponthoz. Az ügynök is támogatja a rétegzett fájlok részleges letöltését, mivel azok a felhasználó által elért. 

> [!Note]  
> A fenti számok nem jelzik a teljesítményt, amit tapasztalni fog. A tényleges teljesítmény több tényezőtől függ, ahogy azt a szakasz elején ismertetett.

A telepítés általános útmutatójaként érdemes néhány dolgot szem előtt tartania:

- Az objektum átviteli átadója körülbelül a kiszolgálón lévő szinkronizálási csoportok számával arányosan méreteződik. Az adatok több szinkronizálási csoportra történő felosztása a kiszolgálón jobb átviteli forgalmat eredményez, amelyet a kiszolgáló és a hálózat is korlátoz.
- Az objektum átviteli áteresztője fordítottan arányos a MiB másodpercenkénti átviteli. Kisebb fájlok esetén nagyobb átviteli eresztést fog tapasztalni a másodpercenként feldolgozott objektumok száma, de alacsonyabb MiB másodpercenkénti átviteli igény tekintetében. Ezzel szemben a nagyobb fájlok esetén kevesebb objektumot dolgoz fel másodpercenként, de nagyobb MiB másodpercenkénti átviteli. A MiB másodpercenkénti átviteli lépték et az Azure Files méretezési célok korlátozzák.

## <a name="see-also"></a>Lásd még

- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
