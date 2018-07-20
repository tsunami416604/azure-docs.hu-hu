---
title: Az Azure Files méretezhetőségi és teljesítménycéljai |} A Microsoft Docs
description: További információk a méretezhetőségi és teljesítménycéljai az Azure Files között, beleértve a kapacitás, a kérelmek arányának és a bejövő és kimenő sávszélesség korlátja.
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
ms.openlocfilehash: df777e6ebb8ed58fb820b13fb029ddc67775afa7
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39146193"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Az Azure Files méretezhetőségi és teljesítménycéljai
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amely az iparági szabvány SMB protokollon keresztül érhető el a felhőben. Ez a cikk ismerteti a méretezhetőségi és teljesítménycéljai Azure Files és az Azure File Sync.

Az itt felsorolt méretezhetőségi és teljesítménycéljai csúcskategóriás célozza, de hatással lehet más változókat a központi telepítésben. Például egy fájl az átviteli sebességet is korlátozhatja a rendelkezésre álló hálózati sávszélességet, nem csak a az Azure Files szolgáltatást futtató kiszolgálók. Erősen ajánlott a tesztelés a használati mintáját, és határozza meg, a méretezhetőség és teljesítmény az Azure Files megfelel-e a követelményeknek. Idővel ezek a korlátok növelését is igyekszünk áthelyezését. Visszajelzését, vagy a Megjegyzések alatt vagy a véleménye a [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), mely korlátaival hozzánk növelni szeretné.

## <a name="azure-storage-account-scale-targets"></a>Az Azure storage-fiók méretezési célok
A szülő erőforrás Azure-fájlmegosztások az Azure storage-fiók. Storage-fiók az Azure-beli adatok tárolására használható több tárolószolgáltatások, beleértve az Azure Files között, amelyet egy tárolókészletet jelöli. Egyéb szolgáltatások, amelyek az adatok tárolása a storage-fiókok az Azure Blob storage, Azure Queue storage és Azure Table storage. A következő célokat a alkalmazni adatok tárolása a storage-fiókban az összes tárolási szolgáltatások:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Fiók tárhelyhasználata más tárolási szolgáltatások hatással van az Azure-fájlmegosztások a storage-fiókban. Például ha eléri a maximális tárfiókok kapacitásával az Azure Blob storage, nem tudja hozni az Azure-fájlmegosztás, az új fájlok akkor is, ha az Azure-fájlmegosztás maximális megosztási méreténél.

## <a name="azure-files-scale-targets"></a>Az Azure Files méretezési célok
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Az Azure File Sync méretezési célok
Az Azure File Sync rendelkezik megpróbáltuk lehető legnagyobb mértékben tervezhet a korlátlan használat, ez azonban nem mindig lehetséges. Az alábbi táblázat azt jelzi, hogy tesztelés határain, és mely tárolók ténylegesen szigorú korlátozásokat:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Az Azure File Sync teljesítmény-mérőszámok
Az Azure File Sync ügynök fut, amely csatlakozik az Azure-fájlmegosztások Windows kiszolgáló gépen, mivel a hatékony teljesítményt attól függ, hogy az infrastruktúra tényező: Windows Server és az alapul szolgáló lemez konfigurációját a hálózati sávszélesség a kiszolgáló és az Azure storage között a fájl méretével, a teljes adatkészlet méretét, és a tevékenység az adatkészlethez. Mivel az Azure File Sync a fájlok szintjén működik, a teljesítményjellemzők az Azure File Sync-alapú megoldás jobban mérik a másodpercenként feldolgozott (fájlok és könyvtárak) objektumok száma. 
 
Az Azure File Sync, a teljesítmény fontos két szakaszban végzik:
1. **Kezdeti egyszeri kiépítés**: kezdeti kiépítéstől a teljesítmény optimalizálása érdekében tekintse meg [bevezetése az Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) az optimális üzembe helyezés részleteiről.
2. **Folyamatban lévő szinkronizálás**: után az adatok kezdetben áttöltésekor, az Azure-fájlmegosztások, az Azure File Sync tartja több végpontot szinkronizálva.

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
| Objektumok száma | 10 millió objektumok | 
| Adatkészlet mérete| ~ 4 Tib-ra |
| Átlagos mérete | Körülbelül 500 KiB (legnagyobb fájlt: 100 GiB) |
| Töltse fel az átviteli sebesség | másodpercenként 15 objektumok |
| Namespace letöltési átviteli * | 350 objektumokat, másodpercenként |
 
Ha a kiszolgáló új végpont létrehozása az Azure File Sync ügynök nem töltse le a fájl tartalma. Először szinkronizálja a teljes névterét és a majd eseményindítók háttér-e letölteni a fájlokat, akár teljes egészében visszaírási, vagy ha, felhőbeli rétegezés engedélyezve van, a kiszolgálói végpont beállított felhőalapú rétegzési szabályzat.

| Folyamatban lévő szinkronizálás  |   |
|-|--|
| Szinkronizált objektumok száma| 125,000 objektumok (~ 1 %-os forgalom) | 
| Adatkészlet mérete| 50 GB |
| Átlagos mérete | Körülbelül 500 KiB |
| Töltse fel az átviteli sebesség | másodpercenként 20 objektumok |
| Teljes átviteli sebesség * | másodpercenként 30 objektumok |
 
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
