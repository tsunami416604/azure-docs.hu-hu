---
title: A kapcsolat nélküli feltöltése az Azure File Sync Data Box és más módszereket használja.
description: Folyamat és az ajánlott eljárásokat, és engedélyezze az áttelepítést a szinkronizálási kompatibilis tömeges támogatja.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 6781ce4a3cf5f6f883678ac848162790d45a5a0f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669805"
---
# <a name="migrate-to-azure-file-sync"></a>Migrálás az Azure File Syncre
Ugrás az Azure File Sync számos lehetőség áll rendelkezésre:

### <a name="uploading-files-via-azure-file-sync"></a>Fájlokat tölthet fel az Azure File Sync-n keresztül
A legegyszerűbb lehetőség, hogy helyezze át a fájlokat helyileg a Windows Server 2012 R2 vagy újabb verzióra, és az Azure File Sync ügynök telepítése. Miután sync be van állítva, a fájlok feltölti a kiszolgálóról. A Microsoft minden ügyfél 1 TB-os kapcsolatos minden 2 nap között jelenleg észlelt problémát egy átlagos feltöltési sebességét.
Fontolja meg egy [a sávszélesség-szabályozási ütemezés](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter) annak biztosításához, a kiszolgáló egy jó citizen az adatközpontban.

### <a name="offline-bulk-transfer"></a>A kapcsolat nélküli tömeges adatátviteli
Feltöltése, természetesen a legegyszerűbb lehetőség, amíg azt nem működik az Ön számára esetén a fájlok az Azure-bA szinkronizálásához ésszerű időn belül nem teszi lehetővé a rendelkezésére álló sávszélességet. A kihívás, hogy itt a kezdeti szinkronizálás, a fájlok teljes készletét. Ezt követően az Azure File Sync csak át módosításokat kell, a névtér, amely általában sokkal kisebb sávszélességet.
Ezt a kezdeti feltöltése kihívás, offline tömeges áttelepítési eszközök például az Azure-t, hogy [Data Box-család](https://azure.microsoft.com/services/storage/databox) is használható. Az alábbi cikket a folyamatot, akkor kövesse az offline áttelepítés kihasználhatják az Azure File Sync kompatibilis módon összpontosít. Azt ismerteti, akkor az ajánlott eljárásokat, amelyek segítenek ütköző fájlokat és a őrzi meg a fájl- és hozzáférés-vezérlési listák és időbélyegek szinkronizálás engedélyezése után.

### <a name="online-migration-tools"></a>Online áttelepítési eszközök
Az alábbi eljárást csak nem működik a Data Box. Bármely offline migrálás eszközt (például a Data Box) vagy online eszközök, például az AzCopy, Robocopy vagy harmadik féltől származó eszközökkel és szolgáltatásokkal működik. Így módszertől függetlenül, hogy a kezdeti feltöltése kihívás, továbbra is fontos, hogy kövesse ezeket az eszközöket a szinkronizálás használatához az alábbi lépéseket követve kompatibilis-módja.


## <a name="offline-data-transfer-benefits"></a>A kapcsolat nélküli adatátvitel előnyeit
A Data Box használatakor offline migrálás fő előnyei a következők:

- Az Azure-bA fájlok egy offline tömeges átviteli folyamat, például a Data Box-n keresztül való migrálás során nem kell a kiszolgálóról a fájlok feltöltése a hálózaton keresztül. Nagy névterekhez Ez azt is jelentheti a hálózati sávszélességet és időt jelentős megtakarítást.
- Azure File Sync használata, majd az utazás módja függetlenül a használt (Data Box, Azure Import stb.), az élő kiszolgáló csak feltölti a fájlokat, amely tartalmazza a szükséges az adatok az Azure-bA óta.
- Az Azure File Sync biztosítja, hogy a fájl- és hozzáférés-vezérlési listák szinkronizált is – akkor is, ha a kapcsolat nélküli tömeges áttelepítése termék nem átviteli ACL-ek.
- Azure Data Box és az Azure File Sync használata esetén nincs állásidő nélkül. Az adatok átviteléhez az Azure Data Box használatával lehetővé teszi a fájl pontosságú megőrzése a hálózati sávszélesség hatékony felhasználása. Azt is tartja a névtér naprakész csak a Data Box elküldése óta megváltozott a fájlok feltöltésével.

## <a name="plan-your-offline-data-transfer"></a>A kapcsolat nélküli adatátvitel megtervezése
Mielőtt elkezdené, áttekintheti a következőket:

- A tömeges áttelepítése egy vagy több Azure-fájlmegosztások az Azure File Sync szinkronizálási engedélyezése előtt.
- Ha azt tervezi, használja a Data Boxot a tömeges az áttelepítéshez: Tekintse át a [üzembe helyezési Előfeltételek Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Az Azure File Sync végső topológiájának megtervezése: [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- Válassza ki az Azure storage (ok), amely tartalmazza majd a szinkronizálni kívánt fájlmegosztások. Győződjön meg arról, hogy a kötegelt migrálás történik, az azonos tárfiók(ok) az ideiglenes előkészítési megosztásokhoz. Tömeges áttelepítése csak akkor engedélyezhető, a végső - és a egy átmeneti-share ugyanabban a tárfiókban található.
- A tömeges áttelepítése csak ha kiszolgálóról egy új szinkronizálási kapcsolat létrehozásához használhatók fel. A tömeges áttelepítése a meglévő szinkronizálási kapcsolat nem engedélyezhető.

## <a name="offline-data-transfer-process"></a>Offline adatok átviteli folyamat
Ez a szakasz ismerteti a folyamat beállítása után az Azure File Sync módon tömeges áttelepítési eszközök például az Azure Data Box-kompatibilis.

![Folyamat lépéseit, amely szintén a következő bekezdés részletesen taglalja megjelenítése](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Lépés | Részlet |
|---|---------------------------------------------------------------------------------------|
| ![Folyamat 1. lépés.](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [A Data Box ORDER](../../databox/data-box-deploy-ordered.md). Nincsenek [több ajánlatokat a Data Box-családon belül](https://azure.microsoft.com/services/storage/databox/data) a igényekhez. A Data Box kap, és kövesse a Data Box [dokumentáció adatok másolása az](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box). Győződjön meg arról, hogy az adatokat másolja ki az UNC elérési útra a a Data Box: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>` ahol a `ShareName` az átmeneti tárolási fájlmegosztás neve. A Data Box küld vissza az Azure-bA. |
| ![Folyamat 2. lépés.](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Várjon, amíg az Azure-fájlmegosztások ideiglenes előkészítési megosztások jelölt megjelennek a fájlokat. **Ne engedélyezze ezeket a megosztásokat a szinkronizálási!** |
| ![Folyamat 3. lépés.](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Hozzon létre egy új megosztást, amely minden fájlmegosztáshoz Data Box az Ön számára létrehozott üres. Győződjön meg róla, hogy az új megosztás ugyanazt a tárfiókot, a Data Box-megosztást. [Egy új Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md). |
| ![Folyamat 4. lépés.](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Szinkronizálási csoport létrehozása a](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) a társzinkronizálási szolgáltatás és a felhőbeli végpont üres megosztás hivatkozás. Ismételje meg ezt a lépést minden Data Box-fájlmegosztást. Tekintse át a [Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md) útmutatót, és kövesse az Azure File Sync beállításához szükséges lépéseket. |
| ![Folyamat 5. lépés.](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Az élő kiszolgáló könyvtár hozzáadása a kiszolgálói végpont,](storage-sync-files-deployment-guide.md#create-a-server-endpoint). A folyamat során adja meg, hogy Ön már áthelyezte, a fájlokat az Azure-ba, és az átmeneti megosztások hivatkoznak. A választott engedélyezheti vagy tilthatja le a felhőbeli rétegezés igény szerint. Kiszolgálói végpont létrehozásakor az élő kiszolgálón, az átmeneti megosztás hivatkoznia kell. Engedélyezze a "Kapcsolat nélküli adatátvitel" (az alábbi képen) az új kiszolgáló végpont panelen, és hivatkoznia kell lennie, ugyanazt a tárfiókot, a felhőbeli végpont átmeneti megosztás. A rendelkezésre álló megosztások storage-fiók és a megosztásokat, amely már nem szinkronizálja alapján szűri. |

![A kapcsolat nélküli adatátvitel engedélyezése egy új kiszolgálói végpont létrehozása során az Azure portal felhasználói felület megjelenítése.](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>A megosztás szinkronizálása
Miután létrehozta a kiszolgálói végpontot, a szinkronizálási kezdődik. Minden fájl, amely létezik a kiszolgálón Ha ezt a fájlt a Data Box letétbe, ahol a fájlok átmeneti megosztást is létezik, és hogy szinkronizálási, ezért másolja a fájlt a feltöltés, a kiszolgáló helyett az átmeneti tárolási fájlmegosztás szinkronizálási határozza meg. Ha a fájl az átmeneti megosztás nem létezik, vagy egy újabb verzió érhető el a helyi kiszolgálón, szinkronizálási feltölteni a fájlt a helyi kiszolgálóról.

> [!IMPORTANT]
> Engedélyezheti a tömeges áttelepítési mód csak a kiszolgálói végpont létrehozása során. Miután létrejött a kiszolgálói végpont, jelenleg nem semmilyen módon nem lehet tömegesen integrálása át az adatokat egy már szinkronizálja kiszolgálóról az a névtér.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Hozzáférés-vezérlési listák és a fájlok és mappák időbélyegek
Az Azure File Sync biztosítja, hogy fájl- és hozzáférés-vezérlési listák szinkronizálva lesznek az élő kiszolgálóról akkor is, ha a használt tömeges áttelepítési eszköz fejeződött nem átviteli ACL-ek kezdetben. Ez azt jelenti, hogy az átmeneti megosztás léphetnek tartalmaz minden olyan fájlok és mappák ACL-ek. Új kiszolgálói végpont létrehozása a kapcsolat nélküli áttelepítés funkció engedélyezésével, hozzáférés-vezérlési listák a kiszolgálón lévő összes fájl jelenleg lesznek szinkronizálva. Ugyanez vonatkozik a létrehozása - és a módosított-időbélyegzőnél.

## <a name="shape-of-the-namespace"></a>A névtér alakzat
A névtér az alakzat határozza meg, mi van a kiszolgálón, ha a szinkronizálás engedélyezve van. Ha a Data Box után a helyi kiszolgálón a rendszer törli a fájlokat "-snapshot" és - migrálás, akkor ezeket a fájlokat nem beemelte a a valós idejű, szinkronizálja a névteret. Ezek még mindig az átmeneti megosztás, de soha nem másolja. Ez a kívánt viselkedés, a szinkronizálás tartja a névteret, az élő kiszolgáló megfelelően. A Data Box "pillanatkép" csak átmeneti platform számára a hatékony fájl másolása és az élő névtér az alakzat nem a szervezettől.

## <a name="finishing-bulk-migration-and-clean-up"></a>Tömeges áttelepítése és a karbantartás befejezése
Az alábbi képernyőfelvételen a kiszolgálói végpont tulajdonságpaneljén az Azure Portalon. A kapcsolat nélküli adatátvitel területen figyelheti, hogy a folyamat állapotát. "Folyamatban" vagy "Completed" vagy megjelenik.

A kiszolgáló a teljes névtér a kezdeti szinkronizálás befejezése után azt fogja végzett kihasználva az előkészítési fájlt a Data Box tömeges rendelkező megosztási áttelepített fájlokat. Figyelje meg a kapcsolat nélküli adatátvitelt, amely az állapot módosul, a "Teljes" kiszolgálói végpont tulajdonságai között. Jelenleg távolíthatja el az átmeneti megosztás csökkenthetők a költségek:

1. Kattintson a "Kapcsolat nélküli adatátvitel letiltása" a kiszolgálói végpont tulajdonságai között, ha a "Befejezve".
2. Fontolja meg az átmeneti megosztás csökkenthetők a költségek törlését. Az átmeneti megosztás nem valószínű, hogy tartalmazza a fájl- és hozzáférés-vezérlési listák és ezért csak korlátozottan használhatók. "Adott időpontú" biztonsági mentés céljából, inkább hozzon létre egy valós [a szinkronizálja az Azure-fájlmegosztás pillanatképét](storage-snapshots-files.md). Is [engedélyezése az Azure Backup pillanatképek]( ../../backup/backup-azure-files.md) ütemezés szerint.

![Hol találhatók az állapotát, és tiltsa le a vezérlők, a kapcsolat nélküli adatátvitelt kiszolgálói-végpont tulajdonságai az Azure portál felhasználói felületének megjelenítése.](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Ebben az üzemmódban csak le kell tiltania, amikor az állapot "kész" valóban a kívánt vagy megszakíthatja a helytelen konfiguráció miatt. A mód közepes módon jogos központi telepítés rendszer letiltása, ha fájlok megkezdjük töltse fel a kiszolgáló, akkor is, ha az átmeneti megosztás továbbra is elérhető.

> [!IMPORTANT]
> Kapcsolat nélküli adatátvitel letiltása után nincs lehetőség az engedélyezéshez, akkor is, ha az átmeneti megosztás a tömeges migrálással továbbra is elérhető.

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
