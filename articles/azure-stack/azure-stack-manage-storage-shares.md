---
title: "Azure-készletben a tárolási kapacitás kezelése |} Microsoft Docs"
description: "Megfigyelését és felügyeletét a rendelkezésre álló szabad hely Azure verem."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b0e694e4-3575-424c-afda-7d48c2025a62
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: dce4252846732ca5161018103438df1f9ff6146d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="manage-storage-capacity-for-azure-stack"></a>A tárolókapacitás Azure verem kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A cikkben szereplő információkat az Azure-verem felhő operátor monitor segítségével, és a tárolási kapacitást az Azure-verem központi telepítés kezelése. Az Azure-verem tároló-infrastruktúra foglal le, a teljes kapacitásának az Azure Alkalmazásveremben üzembe használandó részhalmazának **tárolószolgáltatások**. A tárolási szolgáltatások egy bérlő adatok tárolása, hogy a csomópontok a központi telepítés köteteken megosztások.

Felhő operátorként használható tárolási csak korlátozott mennyiségű rendelkezik. A megoldás megvalósítása tárolókapacitást határozza meg. A megoldás a OEM gyártójának többcsomópontos megoldás használatakor, vagy a hardver, amelyre telepíti az Azure verem szoftverfejlesztői készlet által biztosított.

Mivel Azure verem nem támogatja a tárolási kapacitás bővítése, fontos, hogy [figyelő](#monitor-shares) hatékony műveletek biztosításához a rendelkezésre álló tár karbantartása.  

Amikor egy megosztás szabad kapacitása korlátozott, tervezi [címtér kezelésére](#manage-available-space) megakadályozhatja, hogy a megosztások a relatív azonosítók elfogyását kapacitás.

Kezelheti a kapacitás lehetőségek a következők:
- Kapacitás visszaigényléséhez
- A tároló áttelepítése

Ha egy nem 100 %-os funkcióját, a tárolás szolgáltatás már nem ennek a megosztásnak a funkciók. Segítségkérés a visszaállítási műveleteket a megosztáshoz, a Microsoft támogatási szolgálatához.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Kötetek és megosztások, tárolók és a lemezek ismertetése
### <a name="volumes-and-shares"></a>Kötetek és megosztások
A *társzolgáltatás* particionálja a rendelkezésre álló tár be különálló és egyenlő bérlői adatok tárolásához kiosztott köteteket. A kötetek száma megegyezik a csomópontok számát a verem Azure környezetben:

- Négy csomópontos üzemelő példányon négy kötetek vannak. Minden kötet egyetlen megosztása rendelkezik. Több csomópontos üzemelő példányon megosztások száma nem csökken, ha egy csomópont eltávolított vagy hibásan.
- Ha az Azure verem szoftverfejlesztői készlet használ, nincs egyik kötetéről, egyetlen megosztása.

Mivel a tárolás szolgáltatás megosztások tárolószolgáltatások kizárólagos használatára, kell közvetlenül nem módosítható, adja hozzá, vagy távolít el fájlokat, ha a megosztásokkal. Csak a tárolási szolgáltatások az ezeken a köteteken tárolt fájlok kell működnie.

Megosztások köteteken bérlői adatok tárolásához. Bérlői adatok magában foglalja a lapblobokat, blokkblobokat, hozzáfűző blobokat, táblák, üzenetsorok, adatbázisok és kapcsolódó metaadatok tárolja. Belül egyetlen megosztása külön-külön tartalmazza a tárolási objektum (BLOB, stb.), mert az egyes objektumok maximális mérete nem haladhatja meg a fájlmegosztás méretét. Új objektumok maximális mérete attól függ, hogy a kapacitás, amely az új objektum létrehozásakor, a nem használt terület olyan megosztáson marad.

Ha egy nem alacsony lemezterülettel és műveletek [VISSZAIGÉNYLÉSE](#reclaim-capacity) terület nem sikeres vagy elérhető, az Azure-verem felhő üzemeltetője lehet [áttelepítése](#migrate-a-container-between) a blob tárolók egy megosztást a másikra.

- Tárolók és blobok kapcsolatos további információkért lásd: [Blob-tároló](azure-stack-key-features.md#blob-storage) kulcs szolgáltatásainak és fogalmak Azure-készletben.
- Bérlő felhasználók miként az Azure-készletben a blob storage szolgáltatással kapcsolatos további információkért lásd: [Azure verem tárolószolgáltatások](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Tárolók
Bérlő felhasználók létrehozása, majd Blobadatok tárolására szolgáló tárolókat. Amíg a felhasználó úgy dönt, hogy mely tárolóban helyezhető el a blobokat, a tárolás szolgáltatás algoritmust használ annak meghatározásához, amelyre a tároló melyik köteten. Az algoritmus általában úgy dönt, a legtöbb szabad területtel rendelkező kötet.  

Egy blob a tárolóban lévő helyezkedik el, miután a, hogy a blob nagyobb területet növelhető. Amikor új blobok és a meglévő blobok növekszik, a rendelkezésre álló területet a köteten, amely az adott tároló zsugorítja tárolja.  

Tárolók egyetlen megosztása nem korlátozódnak. A kombinált Blobadatok tároló használata 80 %-ának a rendelkezésre álló terület növekszik, ha beírja-e a tároló *túlcsordulás* mód. Túlcsordulás módban bármely új BLOB a tárolóban létrehozott egy másik kötetre elegendő szabad hellyel rendelkező foglal le. Idővel túlcsordulás módban a tároló több kötet elosztott blobok is rendelkezhet.

80 %-át, és 90 %-a rendelkezésre álló területet a köteten a használata esetén, a rendszer riasztást küld a verem Azure felügyeleti portálon. A felhő üzemeltetői kell tekintse át a rendelkezésre álló lemezterület, és tervezze meg a tartalom egyensúlyba. A tároló szolgáltatás nem működik, ha egy lemez 100 %-át használja, és nincs további riasztásokról értesítő.

### <a name="disks"></a>Lemezek
Virtuális gépek lemezei bérlők tárolók adnak, és operációsrendszer-lemez tartalmazza. Virtuális gépek legalább egy adatlemezt is lehet. Mindkét típusú lemezek lapblobokat tárolódnak. Az útmutató a bérlők számára az, hogy minden lemez a virtuális Gépet a teljesítmény javítása érdekében egy külön tárolóba.
- Minden egyes tároló, amely egy virtuális géptől (oldalakra vonatkozó blob) lemez egy csatolt tárolót, hogy a virtuális Gépet, a lemez birtokló minősül.
- Olyan tároló, amely azonban nem tartalmaz olyan lemezek VM tekinthető szabad tárolója.

Szabadítson fel lemezterületet a egy csatlakoztatott tároló beállításokat [korlátozott](#move-vm-disks).
> [!TIP]  
> A felhő üzemeltetői kezel közvetlenül a virtuális gépek (VM), amely a bérlők előfordulhat, hogy adja hozzá a tárolóhoz csatlakoztatott lemezek. Azonban a storage-megosztásokat belüli kezelésére szolgáló tervezésekor lehet megérteni, hogyan tárolók és -megosztásoknak kapcsolódnak-e a lemezek használati.

## <a name="monitor-shares"></a>A figyelő megosztások
Használja a Powershellt vagy a felügyeleti portál megosztások figyelésére, így megismerheti, ha a szabad terület korlátozva. Ha a portál, megosztások, amelyek kevés a lemezterület kapcsolatos riasztásokat kapni.    

### <a name="use-powershell"></a>A PowerShell használata
Felhő operátorként, figyelheti a tárolási kapacitás, a PowerShell használatával megosztás **Get-AzsStorageShare** parancsmag. A Get-AzsStorageShare parancsmag adja vissza az összes lefoglalt és szabad terület bájt egyes a megosztásokat.   
![Példa: Vissza megosztások szabad terület](media/azure-stack-manage-storage-shares/free-space.png)

- **Teljes kapacitás** a teljes lemezterület, amely a megosztás elérhető. Ez a terület az adatok és a tárolási szolgáltatások által kezelt metaadatok szolgál.
- **Használható kapacitás** az adatmennyiség bájtban az összes tartományát a bérlői adatforgalom és kapcsolódó metaadatok tároló fájlok által használt van.

### <a name="use-the-administrator-portal"></a>A felügyeleti portálon
A felhő üzemeltetője használhatja is a felügyeleti portál minden megosztás tárolókapacitását megtekintéséhez. **Nyissa meg a tárolási** > **fájlmegosztások** nyissa meg a fájl megosztás a listát, ahol megtekintheti a használatra vonatkozó információ.
![Példa: Tárolófájl-megosztások](media/azure-stack-manage-storage-shares/storage-file-shares.png)
- **TELJES** a teljes lemezterület, amely a megosztás elérhető. Ez a terület az adatok és a tárolási szolgáltatások által kezelt metaadatok szolgál.
- **HASZNÁLT** az adatmennyiség bájtban az összes tartományát a bérlői adatforgalom és kapcsolódó metaadatok tároló fájlok által használt van.

### <a name="storage-space-alerts"></a>Tárolási terület riasztások
Ha a felügyeleti portál, megosztások, amelyek kevés a lemezterület kapcsolatos riasztásokat kapni.

> [!IMPORTANT]
> A felhő üzemeltetője tartson megosztások elérése teljes kihasználtsága. Ha egy nem 100 %-os funkcióját, a tárolás szolgáltatás már nem ennek a megosztásnak a funkciók. Szabad terület és visszaállítási műveletek 100 %-os be egy megosztott érdekében forduljon a Microsoft támogatási szolgálatához.

**Figyelmeztetés**: amikor fájlmegosztást több mint 80 % funkcióját, akkor megjelenik egy *figyelmeztetés* riasztás a felügyeleti portál: ![példa: figyelmeztető riasztás](media/azure-stack-manage-storage-shares/alert-warning.png)


**Kritikus**: amikor fájlmegosztást több mint 90 % funkcióját, akkor megjelenik egy *kritikus* riasztás a felügyeleti portál: ![példa: kritikus riasztás](media/azure-stack-manage-storage-shares/alert-critical.png)

**A részletek megtekintéséhez**: a felügyeleti portálon nyissa meg a riasztás részletei között egy megoldás megtekintéséhez: ![példa: a riasztás részleteinek megtekintése](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Rendelkezésre álló terület kezelése
Ha a szabad terület-megosztáson található, először használja a legkevésbé zavarja a munkában módszerek. Például megpróbálja jusson, úgy dönt, hogy a tároló áttelepítése előtt.  

### <a name="reclaim-capacity"></a>Kapacitás visszaigényléséhez
*Ez a beállítás a több csomópontos rendszerekhez és az Azure verem szoftverfejlesztői készlet egyaránt vonatkoznak.*

A bérlői fiókokat törölt által használt kapacitás is visszaigényléséhez. Ezeket a feladatokat a rendszer automatikusan visszaigényelt, ha az adatok [megőrzési időszak](azure-stack-manage-storage-accounts.md#set-the-retention-period) elérésekor, vagy a visszaszerezni azonnal működhet.

További információkért lásd: [kapacitás visszaigényléséhez](azure-stack-manage-storage-accounts.md#reclaim) a tárolási erőforrások kezelése.

### <a name="migrate-a-container-between-volumes"></a>Telepítse át a tároló kötetek között
*Ez a beállítás csak több csomópontos rendszerekhez vonatkozik.*

Bérlő használati szokásokról, mert bérlői megosztások használja a többinél nagyobb területet. Az eredmény lehet egy olyan megosztás, futó alacsony előtt más megosztások, viszonylag nem használt terület.

Próbálja meg manuálisan át kell telepítenie néhány blob tárolók különböző megosztásra szabadítson fel helyet a színvonalát megosztáson. Több kisebb tároló, amely rendelkezik a kapacitás ahhoz, azok összes egyetlen megosztása áttelepítheti. Áttelepítés használatával helyezze át *szabad* tárolók. Szabad tárolók olyan tárolók, amelyek nem tartalmaznak egy lemezt a virtuális gépek.   

Áttelepítés az új megosztás összes tárolók blob összesíti.

- Ha a blobok helyezte el a további kötetek tárolója túlcsordulás módra váltott, az új megosztás összes a tároló áttelepítése a blobok tárolására elegendő kapacitással kell rendelkeznie. Ez magában foglalja a blobok további megosztások található.

- A PowerShell-parancsmag *Get-AzsStorageContainer* csak a kezdeti köteten használja a tároló terület azonosítja. A parancsmag nem határozza meg a blobok további kötetek elhelyezése által használt lemezterület. A tároló teljes méretét, ezért nem feltétlenül nyilvánvaló. Akkor lehet, hogy egy tárolót az új megosztás összevonása küldhet, hogy új megosztás túlcsordult állapotot az hol helyezi további megosztások adatokat. Ennek eredményeképpen előfordulhat, hogy kell megosztások újra egyensúlyba.

- Ha nem rendelkezik engedélyekkel az erőforráscsoporthoz, és nem használható a PowerShell a további kötetek túlcsordulás adatok lekérdezésére, használható tulajdonosának el az adatok áttelepítése előtt át ezeket a erőforráscsoportok és tárolók megérteni az adatok teljes mérete.  

> [!IMPORTANT]
> Blobok a tároló áttelepítése során offline állapotban, amely PowerShell használatát igényli. Amíg az áttelepítés befejezése végzi az áttelepítést a tároló összes BLOB offline állapotban marad, és nem használható.

#### <a name="to-migrate-containers-using-powershell"></a>PowerShell-lel tárolók áttelepítése
1. Győződjön meg arról, hogy [Azure PowerShell telepítése és konfigurálása](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](http://go.microsoft.com/fwlink/?LinkId=394767).
2.  Vizsgálja meg, hogy milyen adatok áttelepítését tervezi, a megosztott tároló. A legjobb jelölt tárolók egy kötetet az áttelepítéshez azonosításához használja a **Get-AzsStorageContainer** parancsmagot:

    ```
    $shares = Get-AzsStorageShare
    $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -Intent Migration
    ```
    Vizsgálja meg $containers:
    ```
    $containers
    ```
    ![Példa: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3.  Azonosítsa a legjobb cél megosztások ahhoz, hogy a tároló áttelepítése:
    ```
    $destinationshares = Get-AzsStorageShare -SourceShareName
    $shares[0].ShareName -Intent ContainerMigration
    ```
    Vizsgálja meg $destinationshares:
    ```
    $destinationshares
    ```    
    ![Példa: $destination megosztások](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Indítsa el a tároló áttelepítése. Az áttelepítés akkor aszinkron. Ha további tárolókat az áttelepítés megkezdése az első áttelepítés befejezése előtt, a feladat azonosítója segítségével minden állapotának nyomon követését.
  ```
  $jobId = Start-AzsStorageContainerMigration -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares[0].UncPath
  ```
  Vizsgálja meg $jobId. Az alábbi példában cserélje le *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* meg szeretne vizsgálni feladatazonosító:
  ```
  $jobId
  d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
  ```
5. A feladat az azonosítót használva az áttelepítési feladat állapotát. Ha a tároló áttelepítése be nem fejeződött, **MigrationStatus** értéke **Complete**.
  ```
  Get-AzsStorageContainerMigrationStatus -JobId $jobId
  ```
  ![Példa: Áttelepítés állapota](media/azure-stack-manage-storage-shares/migration-status1.png)

6.  Egy folyamatban lévő áttelepítési feladat megszakítása Áttelepítési feladatok feldolgozása aszinkron módon megszakítva. Megszakítási $jobid segítségével követheti nyomon:

  ```
  Stop-AzsStorageContainerMigration -JobId $jobId
  ```
  ![Példa: Visszaállítási állapota](media/azure-stack-manage-storage-shares/rollback.png)

7. A parancs a 6. lépés ismét futtathatja, amíg az állapot megerősíti, hogy az áttelepítési feladat **visszavonva**:  
    ![Példa: Megszakítva állapot](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Helyezze át a virtuális gépek lemezei
*Ez a beállítás csak több csomópontos rendszerekhez vonatkozik.*

A rendkívüli belüli kezelésére szolgáló módszernél a virtuális gép lemezeinek áthelyezésekor. Mivel egy csatlakoztatott tároló (egy virtuális gép lemezt tartalmazó) áthelyezése összetett, forduljon a Microsoft Support Ez a művelet elvégzéséhez.

## <a name="next-steps"></a>További lépések
További információ [a felhasználók számára virtuális gépek ajánlat](azure-stack-tutorial-tenant-vm.md).
