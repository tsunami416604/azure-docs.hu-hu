---
title: Adatok áttelepítése az Azure File Sync szolgáltatásba az Azure Data Box segítségével
description: Tömeges adatok áttelepítése az Azure File Sync szolgáltatással kompatibilis módon.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9398aceeb7465392e82aeaa5760f6c0504f8e33d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159523"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Tömeges adatok áttelepítése az Azure DataBox segítségével az Azure File Sync szolgáltatásba
A tömeges adatokat kétféleképpen telepítheti át az Azure File Sync szolgáltatásba:

* **Töltse fel fájljait az Azure File Sync használatával.** Ez a legegyszerűbb módszer. Helyezze át a fájlokat helyileg a Windows Server 2012 R2 vagy újabb rendszerbe, és telepítse az Azure File Sync-ügynököt. A szinkronizálás beállítása után a rendszer feltölti a fájlokat a kiszolgálóról. (Ügyfeleink jelenleg kétnaponta átlagosan 1 TiB feltöltési sebességet tapasztalnak.) Annak érdekében, hogy a kiszolgáló ne használjon túl nagy sávszélességet az adatközpontszámára, érdemes beállítani a [sávszélesség-szabályozási ütemezést.](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)
* **Fájlok átvitele kapcsolat nélküli módban.** Ha nem rendelkezik elegendő sávszélességgel, előfordulhat, hogy ésszerű időn kívül nem tud fájlokat feltölteni az Azure-ba. A kihívás a fájlok teljes készletének kezdeti szinkronizálása. A kihívás megoldásához használjon offline tömeges áttelepítési eszközöket, például az [Azure Data Box családot.](https://azure.microsoft.com/services/storage/databox) 

Ez a cikk bemutatja, hogyan telepítheti át a fájlokat kapcsolat nélküli módban az Azure File Sync-tel kompatibilis módon. Kövesse az alábbi utasításokat a fájlütközések elkerülése érdekében, valamint a fájl- és mappahozzáférés-vezérlési listák (ACL-k) és az időbélyegek megőrzéséhez a szinkronizálás engedélyezése után.

## <a name="migration-tools"></a>Migrálási eszközök
Az ebben a cikkben leírt folyamat nem csak a Data Box esetében működik, hanem más offline áttelepítési eszközökesetében is. Ez is szerkezet részére szerszámok mint AzCopy, Robocopy, vagy társ szerszámok és szolgáltatás amit dolgozik egyenes felső a Internet. Azonban a kezdeti feltöltési kihívás leküzdéséhez kövesse a cikkben leírt lépéseket, hogy ezeket az eszközöket az Azure File Sync-el kompatibilis módon használhassa.

Bizonyos esetekben az Azure File Sync bevezetése előtt át kell helyeznie az egyik Windows-kiszolgálóról a másikra a Windows Servert. [A Storage Migration Service](https://aka.ms/storagemigrationservice) (SMS) segíthet ebben. Függetlenül attól, hogy az Azure File Sync (Windows Server 2012R2 és újabb) által támogatott kiszolgálóoperációs rendszerre (Windows Server 2012R2 és újabb verzió) van-e szüksége, vagy egyszerűen át kell telepítenie, mert új rendszert vásárol az Azure File Sync számára, az SMS számos olyan funkcióval és előnnyel rendelkezik, amelyek segítenek zökkenőmentesen történik.

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>Az eszközök segítségével történő adatátvitel előnyei
Az átviteli eszköz, például a Data Box offline áttelepítéshez való használatának fő előnyei:

- Nem kell az összes fájlt feltöltenie a hálózaton keresztül. Nagy névterek esetén ez az eszköz jelentős hálózati sávszélességet és időt takaríthat meg.
- Ha az Azure File Sync-et használja, függetlenül attól, hogy melyik átviteli eszközt használja (Data Box, Azure Import/Export szolgáltatás és így tovább), az élő kiszolgáló csak azokat a fájlokat tölti fel, amelyek az adatok Azure-ba való áthelyezése után változnak.
- Az Azure File Sync szinkronizálja a fájl- és mappaanciális adatokat, még akkor is, ha az offline tömeges áttelepítési eszköz nem szállítja az ACL-eket.
- A Data Box és az Azure File Sync nem igényel állásidőt. Ha a Data Box segítségével továbbítja az adatokat az Azure-ba, hatékonyan használja a hálózati sávszélességet, és megőrzi a fájlhűséget. A névteret is naprakészen tarthatja, ha csak azokat a fájlokat tölti fel, amelyek az adatok Azure-ba való áthelyezését követően változnak.

## <a name="prerequisites-for-the-offline-data-transfer"></a>Az offline adatátvitel előfeltételei
Az offline adatátvitel befejezése előtt ne engedélyezze a szinkronizálást azon a kiszolgálón, amelyet áttelepít. Más dolog, hogy fontolja meg, mielőtt elkezdené a következők:

- Ha a Data Box ot kívánja használni a tömeges áttelepítéshez: Tekintse át az [Adatmező telepítési előfeltételeit.](../../databox/data-box-deploy-ordered.md#prerequisites)
- Tervezze meg a végső Azure File Sync topológia: [Tervezze meg az Azure File Sync központi telepítését](storage-sync-files-planning.md)
- Válassza ki az Azure storage-fiók(ok), amely megtartja a fájlmegosztások szeretne szinkronizálni. Győződjön meg arról, hogy a tömeges áttelepítés történik ideiglenes átmeneti megosztások ugyanabban a tárfiók(oka)n. A tömeges áttelepítés csak egy ugyanabban a tárfiókban található végső és átmeneti megosztás tkihasználva engedélyezhető.
- A tömeges áttelepítés csak akkor használható, ha új szinkronizálási kapcsolatot hoz létre egy kiszolgálóhelyével. Meglévő szinkronizálási kapcsolattal nem engedélyezhető tömeges áttelepítés.


## <a name="process-for-offline-data-transfer"></a>Offline adatátvitel feldolgozása
Az Azure File Sync beállítása a tömeges áttelepítési eszközökkel, például az Azure Data Box-kal kompatibilis módon:

![Az Azure File Sync beállítását bemutató diagram](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| Lépés | Részletek |
|---|---------------------------------------------------------------------------------------|
| ![1. lépés](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [Rendelje meg adatdobozát.](../../databox/data-box-deploy-ordered.md) A Data Box család [számos terméket](https://azure.microsoft.com/services/storage/databox/data) kínál az Ön igényeinek megfelelően. Amikor megkapja a Data Box, kövesse a [dokumentációt, hogy másolja az adatokat,](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box) hogy ezt az UNC elérési utat a Data Box: * \\<\>\<DeviceIPAddres StorageAccountName_AzFile\>\<ShareName\>*. Itt a *ShareName* az átmeneti megosztás neve. Küldje vissza az adatdobozt az Azure-ba. |
| ![2. lépés](media/storage-sync-files-offline-data-transfer/bullet_2.png) | Várjon, amíg a fájlok megjelennek az ideiglenes átmeneti megosztásként kiválasztott Azure-fájlmegosztásokban. *Ne engedélyezze a szinkronizálást ezekkel a megosztásokkal.* |
| ![3. lépés](media/storage-sync-files-offline-data-transfer/bullet_3.png) | Hozzon létre egy új üres megosztást minden olyan fájlmegosztáshoz, amelyet a Data Box létrehozott. Ez az új megosztás nak ugyanabban a tárfiókban kell lennie, mint a Data Box megosztás. [Új Azure-fájlmegosztás létrehozása.](storage-how-to-create-file-share.md) |
| ![4. lépés](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [Hozzon létre egy szinkronizálási csoportot](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint) egy tárolószinkronizálási szolgáltatásban. Hivatkozzon az üres megosztásra felhővégpontként. Ismételje meg ezt a lépést minden Adatdoboz-fájlmegosztás esetén. [Állítsa be az Azure File Sync](storage-sync-files-deployment-guide.md)szolgáltatást. |
| ![5. lépés](media/storage-sync-files-offline-data-transfer/bullet_5.png) | [Adja hozzá az élő kiszolgálókönyvtárat kiszolgálóvégpontként](storage-sync-files-deployment-guide.md#create-a-server-endpoint). A folyamat során adja meg, hogy áthelyezte a fájlokat az Azure-ba, és hivatkozzon az átmeneti megosztások. Szükség szerint engedélyezheti vagy letilthatja a felhőrétegezést. Kiszolgálóvégpont létrehozása közben az élő kiszolgálón, hivatkozzon az átmeneti megosztásra. A **Kiszolgáló végponthozzáadása** panelen az **Offline adatátvitel**csoportban válassza **az Engedélyezve**lehetőséget, majd válassza ki azt az átmeneti megosztást, amelynek ugyanabban a tárfiókban kell lennie, mint a felhővégpont. Itt az elérhető megosztások listáját a tárfiók és a még nem szinkronizált megosztások szűrik. |

![Képernyőkép az Azure Portal felhasználói felületéről, amely bemutatja, hogyan engedélyezze az offline adatátvitelt új kiszolgálóvégpont létrehozása közben](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>A megosztás szinkronizálása
A kiszolgálóvégpont létrehozása után elindul a szinkronizálás. A szinkronizálási folyamat határozza meg, hogy a kiszolgálón lévő egyes fájlok is léteznek-e abban az átmeneti megosztásban, ahol a Data Box letétbe helyezte a fájlokat. Ha a fájl létezik, a szinkronizálási folyamat a fájlt az átmeneti megosztásról másolja, nem pedig feltölti a kiszolgálóról. Ha a fájl nem létezik az átmeneti megosztásban, vagy ha egy újabb verzió érhető el a helyi kiszolgálón, a szinkronizálási folyamat feltölti a fájlt a helyi kiszolgálóról.

> [!IMPORTANT]
> A tömeges áttelepítési módot csak kiszolgálóvégpont létrehozása közben engedélyezheti. A kiszolgálóvégpont létrehozása után nem integrálható a már szinkronizált kiszolgálóról a névtérbe a tömegesen áttelepített adatok.

## <a name="acls-and-timestamps-on-files-and-folders"></a>ACL-k és időbélyegek fájlokon és mappákon
Az Azure File Sync biztosítja, hogy a fájl és a mappa ACL-ok szinkronizálva az élő kiszolgálóakkor is, ha a tömeges áttelepítési eszköz, amely et használt, kezdetben nem átviteli ACLs. Emiatt az átmeneti megosztásnak nem kell a fájlok és mappák acl-jait tartalmaznia. Ha új kiszolgálóvégpont létrehozásakor engedélyezi a kapcsolat nélküli adatáttelepítési szolgáltatást, a rendszer az összes fájla-acl-t szinkronizálja a kiszolgálón. Az újonnan létrehozott és módosított időbélyegek is szinkronizálva vannak.

## <a name="shape-of-the-namespace"></a>A névtér alakja
A szinkronizálás engedélyezésekor a kiszolgáló tartalma határozza meg a névtér alakját. Ha a fájlokat az Adatdoboz pillanatképének és áttelepítésének befejezése után törli a helyi kiszolgálóról, ezek a fájlok nem lépnek át az élő, szinkronizáló névtérbe. Az átmeneti megosztáson maradnak, de nem másolják le őket. Erre azért van szükség, mert a szinkronizálás a névteret az élő kiszolgálónak megfelelően tartja. A Data Box *pillanatkép* csak egy átmeneti terep a hatékony fájlmásoláshoz. Ez nem az élő névtér alakjának hatásköre.

## <a name="cleaning-up-after-bulk-migration"></a>Tisztítás tömeges áttelepítés után 
Ahogy a kiszolgáló befejezi a névtér kezdeti szinkronizálását, a Data Box tömegesen áttelepített fájlok az átmeneti fájlmegosztást használják. Az Azure Portal **Kiszolgálóvégpont tulajdonságai** panelen az **Offline adatátvitel** szakaszban az állapot **folyamatban** lévőállapotról **Befejezett**állapotra változik. 

![Képernyőkép a Kiszolgálóvégpont tulajdonságai panelről, ahol az offline adatátvitel állapotát és letiltását helyezik el](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

Most már megtisztíthatja az átmeneti megosztást a költségek megtakarítása érdekében:

1. A **Kiszolgálóvégpont tulajdonságai** panelen, ha az állapot **befejeződött,** válassza **a Kapcsolat nélküli adatátvitel letiltása**lehetőséget.
2. Fontolja meg az átmeneti megosztás törlését a költségek csökkentése érdekében. Az átmeneti megosztás valószínűleg nem tartalmaz fájl- és mappaanciális adatokat, ezért nem valószínű, hogy hasznos. A biztonsági mentési pont-in-time célokra hozzon létre egy valós [pillanatképet a szinkronizálásazure-fájlmegosztás](storage-snapshots-files.md). [Beállíthatja, hogy az Azure Backup pillanatképeket készítsen]( ../../backup/backup-afs.md) ütemezés szerint.

A kapcsolat nélküli adatátviteli mód letiltása csak akkor, ha az állapot **befejeződött,** vagy ha helytelen konfiguráció miatt meg szeretné szakítani a műveletet. Ha a központi telepítés során letiltja a módot, a fájlok akkor is elindulnak a kiszolgálóról való feltöltésre, ha az átmeneti megosztás még elérhető.

> [!IMPORTANT]
> Miután letiltotta az offline adatátviteli módot, nem engedélyezheti újra, még akkor sem, ha a tömeges áttelepítés átmeneti része továbbra is elérhető.

## <a name="next-steps"></a>További lépések
- [Az Azure File Sync központi telepítésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
