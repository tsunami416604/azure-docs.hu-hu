---
title: Az Azure Files méretezhetőségi és teljesítménycéljai |} A Microsoft Docs
description: További információk a méretezhetőségi és teljesítménycéljai az Azure Files között, beleértve a kapacitás, a kérelmek arányának és a bejövő és kimenő sávszélesség korlátja.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 7/19/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 630ad1e0558fc089d79eee50175e497b771a0a8a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494969"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Az Azure Files méretezhetőségi és teljesítménycéljai

[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amely az iparági szabvány SMB protokollon keresztül érhető el a felhőben. Ez a cikk ismerteti a méretezhetőségi és teljesítménycéljai Azure Files és az Azure File Sync.

Az itt felsorolt méretezhetőségi és teljesítménycéljai csúcskategóriás célozza, de hatással lehet más változókat a központi telepítésben. Például egy fájl az átviteli sebességet is korlátozhatja a rendelkezésre álló hálózati sávszélességet, nem csak a az Azure Files szolgáltatást futtató kiszolgálók. Erősen ajánlott a tesztelés a használati mintáját, és határozza meg, a méretezhetőség és teljesítmény az Azure Files megfelel-e a követelményeknek. Idővel ezek a korlátok növelését is igyekszünk áthelyezését. Visszajelzését, vagy a Megjegyzések alatt vagy a véleménye a [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), mely korlátaival hozzánk növelni szeretné.

## <a name="azure-storage-account-scale-targets"></a>Az Azure storage-fiók méretezési célok

A szülő erőforrás Azure-fájlmegosztások az Azure storage-fiók. Storage-fiók az Azure-beli adatok tárolására használható több tárolószolgáltatások, beleértve az Azure Files között, amelyet egy tárolókészletet jelöli. Egyéb szolgáltatások, amelyek az adatok tárolása a storage-fiókok az Azure Blob storage, Azure Queue storage és Azure Table storage. A következő célokat a alkalmazni adatok tárolása a storage-fiókban az összes tárolási szolgáltatások:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Általános célú fiók tárhelyhasználat hatékonyságát a más tárolási szolgáltatásokra hatással van az Azure-fájlmegosztások a storage-fiókban. Például ha eléri a maximális tárfiókok kapacitásával az Azure Blob storage, nem tudja hozni az Azure-fájlmegosztás, az új fájlok akkor is, ha az Azure-fájlmegosztás maximális megosztási méreténél.

## <a name="azure-files-scale-targets"></a>Az Azure Files méretezési célok

### <a name="premium-files-scale-targets"></a>Prémium files tárolók skálázása

A prémium szintű fájlok megfontolandó korlátozások három kategóriába sorolhatók: storage-fiókok, a megosztások és a fájlokat.

Példa: Egyetlen megosztása 100 000 IOPS érhető el, és egyetlen fájl akár 5000 IOPS skálázhatja. Így például, ha már három egy megosztásban lévő fájlokat, a maximális iops-érték el azt a megosztást, 15 000.

### <a name="premium-filestorage-account-limits"></a>Prémium szintű filestorage korlátai

Prémium files használjon egy egyedi nevű tárfiókot **filestorage (előzetes verzió)**, ez a fiók rendelkezik, mint a standard szintű fájlok által használt tárfiók némileg eltérő méretezési célokat. A tárolási fiók méretezési célokat, tekintse meg a tábla a [az Azure storage-fiók méretezési célok](#azure-storage-account-scale-targets) szakaszban.

> [!IMPORTANT]
> Tárfiókok korlátai megosztások a alkalmazni. Méretezés akár a storage-fiókok maximális száma csak akkor megvalósítható, ha csak egy megosztás tárfiókonként.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync méretezési célok

Az Azure File Sync rendelkezik megpróbáltuk lehető legnagyobb mértékben tervezhet a korlátlan használat, ez azonban nem mindig lehetséges. Az alábbi táblázat azt jelzi, hogy tesztelés határain, és mely tárolók ténylegesen szigorú korlátozásokat:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Az Azure File Sync teljesítmény-mérőszámok

Az Azure File Sync ügynök fut, amely csatlakozik az Azure-fájlmegosztások Windows kiszolgáló gépen, mivel a hatékony szinkronizálási teljesítmény számos tényezőtől, az infrastruktúra függ: A Windows Server és az alapul szolgáló lemez konfigurációját, a kiszolgáló és az Azure storage között a hálózati sávszélesség a fájl mérete, a teljes adatkészlet méretét és a tevékenység az adatkészlethez. Mivel az Azure File Sync a fájlok szintjén működik, a teljesítményjellemzők az Azure File Sync-alapú megoldás jobban mérik a másodpercenként feldolgozott (fájlok és könyvtárak) objektumok száma.

Az Azure File Sync, a teljesítmény fontos két szakaszban végzik:

1. **Kezdeti egyszeri kiépítés**: A kiinduló telepítéstől a teljesítmény optimalizálása érdekében tekintse meg [bevezetése az Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) az optimális üzembe helyezés részleteiről.
2. **Folyamatban lévő szinkronizálás**: Miután az adatok kezdetben áttöltésekor, az Azure-fájlmegosztások, az Azure File Sync biztosítja, hogy több végpontot szinkronizálva.

Segítséget az egyes szakaszok üzembe helyezésének megtervezése, az alábbiakban az eredmények figyelhetők egy konfigurációs rendszereken belső tesztelése során

| Rendszer-konfiguráció |  |
|-|-|
| CPU | 64 virtuális maggal és 64 MiB L3-gyorsítótár |
| Memory (Memória) | 128 GiB |
| Lemez | Akkumulátor a RAID 10-zel SAS-lemezek biztonsági gyorsítótár |
| Network (Hálózat) | 1 GB/s hálózat |
| Számítási feladat | Általános célú fájlkiszolgáló|

| Kezdeti egyszeri kiépítése  |  |
|-|-|
| Objektumok száma | 25 millió objektumok |
| Adatkészlet mérete| ~4.7 Tib-ra |
| Átlagos mérete | Közel 200 KiB (legnagyobb fájlt: 100 GiB) |
| Töltse fel az átviteli sebesség | másodpercenként 20 objektumok |
| Namespace letöltési átviteli * | másodpercenként 400 objektumok |

Ha a kiszolgáló új végpont létrehozása az Azure File Sync ügynök nem töltse le a fájl tartalma. Először szinkronizálja a teljes névterét és a majd eseményindítók háttér-e letölteni a fájlokat, akár teljes egészében visszaírási, vagy ha, felhőbeli rétegezés engedélyezve van, a kiszolgálói végpont beállított felhőalapú rétegzési szabályzat.

| Folyamatban lévő szinkronizálás  |   |
|-|--|
| Szinkronizált objektumok száma| 125,000 objektumok (~ 1 %-os forgalom) |
| Adatkészlet mérete| 50 GB |
| Átlagos mérete | Körülbelül 500 KiB |
| Töltse fel az átviteli sebesség | másodpercenként 30 objektumok |
| Teljes átviteli sebesség * | másodpercenként 60 objektumok |

* Ha felhőbeli rétegezés engedélyezve van, akkor valószínű, hogy tekintse át az adatokat a rendszer letölti a fájlt csak néhány jobb teljesítményt. Az Azure File Sync csak letölti az adatokat a gyorsítótárban tárolt fájlok, a végpontok bármelyikét, módosításakor. Rétegzett vagy újonnan létrehozott fájlokhoz az ügynök nem tölti le a fájl adatait, és inkább csak szinkronizálja a névtér összes kiszolgálói végpontot. Az ügynök is támogatja a részleges letöltés rétegzett fájlok, a felhasználó által elért. 

> [!Note]  
> A fenti számok nem állnak arra utalhat, hogy a teljesítmény, amely fog tapasztalni. A tényleges teljesítmény több tényezőtől függ, ez a szakasz elején leírt módon.

Az üzemelő példány általános útmutatásként kell tartania néhány dolgot figyelembe:

- Az objektum átviteli körülbelül méretezi a kiszolgálón a szinkronizálási csoportok száma arányos. Jobb átviteli sebességet, amely is korlátozza a kiszolgáló és a hálózat egy kiszolgálón több szinkronizálási csoportokba felosztása a adatokat eredményez.
- Az objektum átviteli sebesség a MiB s sebességet fordítottan arányos. A kisebb fájlok esetében tapasztalható nagyobb átviteli sebességet egy második, de alacsonyabb MiB s sebességet feldolgozott objektumok számának tekintetében. Ezzel szemben nagyobb méretű fájlok, kap egy második, de magasabb MiB s sebességet feldolgozott kevesebb objektumot. Az Azure Files méretezési célokat a MiB s sebességet korlátozza.

## <a name="see-also"></a>Lásd még

- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Más tárolási szolgáltatásokra méretezhetőségi és teljesítménycéljai](../common/storage-scalability-targets.md)
