---
title: Adatok áttelepítése az Azure File Sync az Azure Data Box és más módszerek használatával
description: Adatok kötegelt úgy, hogy a kompatibilis az Azure File Sync áttelepíteni.
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 02/12/2019
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 04b13c1e511f54c1fcf7b632d3a368fde16bf319
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549017"
---
# <a name="migrate-bulk-data-to-azure-file-sync"></a>Adatok kötegelt migrálása az Azure File Sync
Adatok kötegelt is áttelepíthet Azure File Sync két módon:

* **A fájlok feltöltése az Azure File Sync használatával.** Ez a legegyszerűbb módszer. Helyezze át a fájlokat helyileg a Windows Server 2012 R2 vagy újabb verzióra, és az Azure File Sync ügynök telepítése. Miután beállította a szinkronizálás, a fájlok a kiszolgálóról lesz feltöltve. (Ügyfeleink jelenleg kiváló egy átlagos feltöltési sebessége 1 Tib-ra vonatkozó kétnaponta.) Győződjön meg arról, hogy a kiszolgáló nem használja a sávszélesség túl sok adatközpontja számára, hogy állítsa be a érdemes egy [a sávszélesség-szabályozási ütemezés](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter).
* **Kapcsolat nélküli módban a fájlok átvitelét.** Ha nem rendelkezik elegendő sávszélesség, nem fogja tudni az Azure-bA fájlok feltöltése egy ésszerű időn belül. A kihívás abban a kezdeti szinkronizálás, a fájlok teljes készlete áll. A nehézség használjon offline tömeges áttelepítési eszközök például a [Azure Data Box-család](https://azure.microsoft.com/services/storage/databox). 

Ez a cikk bemutatja, hogyan telepíthet át úgy, hogy a kompatibilis az Azure File Sync offline fájlokat. Kövesse az alábbi utasításokat a fájl ütközések elkerülése érdekében, és a szinkronizálás engedélyezése után a fájl és mappa hozzáférés-vezérlési listák (ACL-ek) és időbélyegek megőrzése érdekében.

## <a name="online-migration-tools"></a>Online áttelepítési eszközök
A folyamat azt írják le ez a cikk működik nem csak a Data Box ki, hanem más offline migrálás eszközökkel. Azt online eszközök, például az AzCopy, a Robocopy, vagy a partnerek eszközei és a szolgáltatások esetében is működik. Azonban elhárítja a kezdeti feltöltése kihívás, kövesse az ebben a cikkben használja ezeket az eszközöket úgy, hogy az Azure File Sync kompatibilis.


## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Egy eszköz offline adatátvitelhez előnyei
Az alábbiakban egy fájlátviteli eszköz használatával például a Data Box offline áttelepítéshez a fő előnyök:

- Nem kell feltölteni a fájlokat a hálózaton keresztül. A nagy névterekhez Ez az eszköz sikerült menteni a jelentős hálózati sávszélességet és időt.
- Az Azure File Sync használatakor függetlenül attól, hogy mely fájlátviteli eszköz (Data Box, az Azure Import/Export szolgáltatás, és így tovább) használja, az élő kiszolgáló csak azokat a fájlokat, amelyek módosítása után az Azure-bA helyezze át az adatokat tölt fel.
- Az Azure File Sync szinkronizálja a fájl- és hozzáférés-vezérlési listák, még akkor is, ha a kapcsolat nélküli tömeges áttelepítési eszköz hozzáférés-vezérlési listák nem átviteli.
- Data Box és az Azure File Sync szükséges állásidő nélkül. Data Box adatok átviteléhez az Azure-ba való használatakor, hatékonyan használhatják a hálózati sávszélességet, és őrizze meg a fájl forrásanyagot. Akkor is naprakészen a névtér csak a módosítása után helyezze át az adatokat az Azure-bA fájlok feltöltésével.

## <a name="prerequisites-for-the-offline-data-transfer"></a>A kapcsolat nélküli adatátvitel előfeltételei
Ne engedélyezze a kiszolgálón, a kapcsolat nélküli adatátvitel végrehajtása előtt áttelepítésekor szinkronizálási. További tevékenység következik, érdemes figyelembe venni, mielőtt megkezdené a következők:

- Ha azt tervezi, használja a Data Boxot a tömeges az áttelepítéshez: Tekintse át a [üzembe helyezési Előfeltételek Data Box](../../databox/data-box-deploy-ordered.md#prerequisites).
- Az Azure File Sync végső topológiájának megtervezése: [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- Válassza ki az Azure storage (ok), amely tartalmazza majd a szinkronizálni kívánt fájlmegosztások. Győződjön meg arról, hogy a kötegelt migrálás történik, az azonos tárfiók(ok) az ideiglenes előkészítési megosztásokhoz. Tömeges áttelepítése csak akkor engedélyezhető, a végső - és a egy átmeneti-share ugyanabban a tárfiókban található.
- A tömeges áttelepítése csak ha kiszolgálóról egy új szinkronizálási kapcsolat létrehozásához használhatók fel. A tömeges áttelepítése a meglévő szinkronizálási kapcsolat nem engedélyezhető.


## <a name="process-for-offline-data-transfer"></a>Offline adatok átvitele folyamatban
Az Azure File Sync beállítása úgy, hogy a kompatibilis tömeges áttelepítési eszközök például az Azure Data Box az itt látható:

![Hogyan állítható be az Azure File Sync bemutató ábra.](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Lépés | Részlet |
|---|---------------------------------------------------------------------------------------|
| ![1. lépés](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [A Data Box ORDER](../../databox/data-box-deploy-ordered.md). A Data Box-családba tartozó ajánlatok [több termék](https://azure.microsoft.com/services/storage/databox/data) az igényeinek. Amikor megjelenik a Data Box, kövesse a [dokumentáció adatok másolása az](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) az UNC elérési útra a a Data Box:  *\\< DeviceIPAddres\>\<StorageAccountName_AzFile\> \<Megosztásnév\>*. Itt *megosztásnév* az átmeneti tárolási fájlmegosztás neve. A Data Box küld vissza az Azure-bA. |
| ![2. lépés](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Várjon, amíg az Azure-fájlmegosztások választott ideiglenes előkészítési megosztások, megjelennek a fájlokat. *Ne engedélyezze ezeket a megosztásokat történő szinkronizálásának engedélyezése.* |
| ![3. lépés](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Hozzon létre egy új üres megosztás minden az Ön számára létrehozott Data Box fájlmegosztáshoz. Erre az új megosztásra ugyanazt a tárfiókot, a Data Box-megosztáson kell lennie. [Egy új Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md). |
| ![4. lépés](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Szinkronizálási csoport létrehozása a](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) a társzinkronizálási szolgáltatás. A felhőbeli végpont üres megosztás hivatkozás. Ismételje meg ezt a lépést minden Data Box-fájlmegosztást. [Állítsa be az Azure File Sync](storage-sync-files-deployment-guide.md). |
| ![5. lépés](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Az élő kiszolgáló könyvtár hozzáadása a kiszolgálói végpont,](storage-sync-files-deployment-guide.md#create-a-server-endpoint). A folyamat során adja meg, hogy az Azure-bA áthelyezni a fájlokat, és az átmeneti megosztások hivatkozhat. Engedélyezheti vagy letilthatja a felhőbeli rétegezés igény szerint. Kiszolgálói végpont létrehozásakor az élő kiszolgálón, az átmeneti megosztás hivatkozhat. A a **kiszolgálói végpont felvétele** panel alatt **Offline adatátviteli**, jelölje be **engedélyezve**, és válassza ki az átmeneti megosztás kell ugyanazt a tárfiókot, a felhőben a végpont. Itt a elérhető megosztások storage-fiók és a megosztásokat, amely már nem szinkronizálása alapján szűri. |

![Képernyőfelvétel az Azure portal felhasználói felület nélküli adatátvitel engedélyezése az új kiszolgálói végpont létrehozása közben:](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>A megosztás szinkronizálása
Miután létrehozta a kiszolgálói végpontot, a szinkronizálás indul el. A szinkronizálási folyamat határozza meg, hogy minden fájl a kiszolgálón is létezik a Data Box letétbe, ahol a fájlok átmeneti megosztást. Ha a fájl nem létezik, a szinkronizálási folyamat során másolja át a fájlt feltölteni a kiszolgáló helyett az átmeneti tárolási fájlmegosztás. Ha a fájl az átmeneti megosztás nem létezik, vagy egy újabb verzió érhető el a helyi kiszolgálón, a szinkronizálási folyamat feltölti a fájlt a helyi kiszolgálóról.

> [!IMPORTANT]
> A tömeges áttelepítési mód engedélyezheti csak, miközben egy kiszolgálói végpontot hoz létre. Miután létrehozta a kiszolgálói végpont, nem integrálja adatok tömeges áttelepítése már szinkronizálja kiszolgálóról a névteret.

## <a name="acls-and-timestamps-on-files-and-folders"></a>Hozzáférés-vezérlési listák és a fájlok és mappák időbélyegek
Az Azure File Sync biztosítja, hogy fájl- és hozzáférés-vezérlési listák szinkronizálva lesznek az élő kiszolgálóról akkor is, ha a használt tömeges áttelepítési eszköz kezdetben nem átviteli ACL-ek. Emiatt az átmeneti tárolási fájlmegosztás nem kell minden olyan fájlok és mappák ACL-ek tartalmaznia. Új kiszolgálói végpont létrehozása a kapcsolat nélküli áttelepítés funkció engedélyezésével, hozzáférés-vezérlési listák az összes fájl szinkronizálva lesznek a kiszolgálón. Újonnan létrehozott és módosított időbélyegek is szinkronizálva lesznek.

## <a name="shape-of-the-namespace"></a>A névtér alakzat
Ha engedélyezi a szinkronizálás, a kiszolgáló tartalmát a névtér az alakzat határozza meg. Ha a rendszer törli a fájlokat a helyi kiszolgálóról a Data Box-pillanatkép és a migrálás befejezése után, nem a valós idejű, szinkronizálja a névtér helyezze át ezeket a fájlokat. Az átmeneti megosztás maradnak, de nem másolja őket. Erre akkor szükség, mert a szinkronizálás tartja a névteret, az élő kiszolgáló megfelelően. A Data Box *pillanatkép* csak átmeneti platform számára a hatékony fájlmásolás van. Már nem a szervezettől az alakzat élő névtér.

## <a name="cleaning-up-after-bulk-migration"></a>Tömeges az áttelepítés után ürítése 
A kiszolgáló befejezte a kezdeti szinkronizálás, a névtér, mivel a Data Box tömeges áttelepített fájlokat használja az átmeneti fájlmegosztást. Az a **kiszolgálói-végpont tulajdonságai** panel az Azure Portalon, az a **Offline adatátviteli** szakaszban, az állapot változik a **folyamatban lévő** való **befejezve** . 

![Hol találhatók a kapcsolat nélküli adatátvitel állapotát, és tiltsa le a vezérlők kiszolgálói-végpont tulajdonságai panelen – képernyőfelvétel](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Most törölheti a költségek csökkentése érdekében átmeneti megosztás is:

1. Az a **kiszolgálói-végpont tulajdonságai** panelen, ha az állapot értéke **befejezve**válassza **offline adatátvitel letiltása**.
2. Vegye figyelembe, hogy a költségek csökkentése érdekében átmeneti megosztás törlése. Az átmeneti tárolási fájlmegosztás valószínűleg nem tartalmazzák a fájl- és hozzáférés-vezérlési listák, így nem nagyon hasznos. Biztonsági mentési pont kötött célból hozzon létre egy valós [a szinkronizálja az Azure-fájlmegosztás pillanatképét](storage-snapshots-files.md). Is [állítsa be az Azure Backup pillanatképek]( ../../backup/backup-azure-files.md) ütemezés szerint.

Az offline adatok átviteli mód letiltása csak akkor, ha az állapot **befejezve** vagy ha azt szeretné, melyek a helytelen konfiguráció miatt megszakítja. Ha letiltja a módot a központi telepítés során, a fájlok feltöltése a kiszolgálóról, akkor is, ha az átmeneti megosztás továbbra is elérhető indul.

> [!IMPORTANT]
> A kapcsolat nélküli átviteli üzemmódot letiltásakor, nem engedélyezhető, újra, akkor is, ha az átmeneti megosztás a tömeges migrálással továbbra is elérhető.

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
