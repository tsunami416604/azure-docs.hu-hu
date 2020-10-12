---
title: Azure VMware-megoldás – áttelepítés Azure Data Box használatával
description: Az Azure VMware-megoldásba való tömeges Migrálás Azure Data Box használata.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507658"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Az Azure VMware-megoldásba való Migrálás Azure Data Box használatával

A Microsoft Azure Data Box Cloud megoldás lehetővé teszi, hogy gyorsan, olcsóan és megbízhatóan küldjön terabájtos (TBs) adatmennyiséget az Azure-ba. A biztonságos adatátvitelt egy saját fejlesztésű Data Box tárolóeszköz küldésével gyorsítjuk fel. Minden tárolóeszköz 80 TB-os maximális felhasználható tárolókapacitással rendelkezik, és az adatközpontba egy regionális Szolgáltató szállítja. Az eszköz robusztus burkolattal rendelkezik, amely védelmet nyújt az adatai számára az átvitel során.

A Data Box használatával tömegesen áttelepítheti a VMware-adatait a saját felhőbe. A helyszíni VMware vSphere környezetből származó adatok a hálózati fájlrendszer (NFS) protokollon keresztül Data Boxra kerülnek. A tömeges adatáttelepítés magában foglalja a virtuális gépek, a konfiguráció és a kapcsolódó adatData Box időponthoz tartozó másolatának mentését, majd az Azure-ba történő manuális szállítását.

Ez a cikk a következőket ismerteti:

* Data Box beállítása.
* Adatok másolása a helyszíni VMware-környezetből a Data Boxba az NFS-en keresztül.
* Data Box visszaküldésének előkészítése.
* BLOB-adatok előkészítése az Azure VMware-megoldásba való másoláshoz.
* Adatok másolása az Azure-ból a saját felhőbe.

## <a name="scenarios"></a>Forgatókönyvek

A tömeges adatáttelepítéshez a következő esetekben használjon Data Box:

* Nagy mennyiségű adatok migrálása a helyszínről az Azure VMware megoldásba. Ez a metódus alapkonfigurációt hoz létre, és szinkronizálja a hálózattal fennálló különbségeket.
* Nagy számú, kikapcsolt virtuális gép áttelepíthető (hideg virtuális gépek).
* A virtuális gépekre vonatkozó adatáttelepítés a fejlesztési és tesztelési környezetek beállításához.
* Nagy mennyiségű virtuálisgép-sablon, ISO-fájl és virtuálisgép-lemez migrálása.

## <a name="before-you-begin"></a>Előkészületek

* Győződjön meg arról, hogy az előfeltételeket és a [sorrendet Data Box](../databox/data-box-deploy-ordered.md) a Azure Portal. A rendelési folyamat során ki kell választania egy olyan Storage-fiókot, amely lehetővé teszi a blob Storage használatát. Miután megkapta a Data Box eszközt, kapcsolja össze a helyszíni hálózattal, és [állítsa be az eszközt](../databox/data-box-deploy-set-up.md) egy olyan IP-címmel, amely elérhető a vSphere felügyeleti hálózatáról.

* Hozzon létre egy virtuális hálózatot és egy Storage-fiókot ugyanabban a régióban, ahol az Azure VMware-megoldás van kiépítve.

* Hozzon létre egy Azure-beli [virtuális hálózati kapcsolatot](cloudsimple-azure-network-connection.md) a saját felhőből a virtuális hálózatba, ahol a Storage-fiókot az [Azure Virtual Network és a ExpressRoute CloudSimple használatával történő csatlakoztatásához](virtual-network-connection.md)szükséges lépéseket követve hozza létre.

## <a name="set-up-data-box-for-nfs"></a>NFS-Data Box beállítása

Kapcsolódjon a Data Box helyi webes felhasználói felületéhez a következő oktatóanyagban található "kapcsolódás az eszközhöz" szakaszának lépéseit követve [: kábel és kapcsolódás a Azure Data boxhoz](../databox/data-box-deploy-set-up.md).  Data Box konfigurálása az NFS-ügyfelek elérésének engedélyezéséhez:

1. A helyi webes KEZELŐFELÜLETen lépjen a **Kapcsolódás és másolás** lapra. Az **NFS-beállítások**területen válassza az **NFS-ügyfél-hozzáférés**lehetőséget. 

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/nfs-client-access.png)

2. Adja meg a VMware ESXi gazdagépek IP-címét, majd válassza a **Hozzáadás**lehetőséget. A vSphere-fürtben lévő összes gazdagép hozzáférését a következő lépés megismétlésével állíthatja be. Kattintson az **OK** gombra.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. A fájlok nem másolhatók közvetlenül a Storage-fiók *gyökérkönyvtárára* .

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. A Azure Files megosztások területen az első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat a Data Boxon található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
| Blobok és fájlok | Elérési út és URL-cím |
|---------------- | ------------ |
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> A VMware-adatok másolásához használja az Azure Block blobokat.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Csatlakoztassa az NFS-megosztást adattárként a helyszíni vCenter-fürtön, és másolja az adatait

Az adatok NFS-adattárba való másolásához a Data Box NFS-megosztását a helyszíni vCenter-fürtön vagy VMware ESXi-gazdagépen adattárként kell csatlakoztatni:

1. Jelentkezzen be a helyszíni vCenter-kiszolgálóra.

2. Kattintson a jobb gombbal az **adatközpont**elemre, válassza a **tárolás**lehetőséget, válassza az **új adattár**lehetőséget, majd válassza a **tovább**lehetőséget.

   ![Új adattár hozzáadása](media/databox-migration-add-datastore.png)

3. Az adattár hozzáadása varázsló 1. lépésében válassza az **NFS** elemet a **típus**területen.

   ![Új adattár hozzáadása – típus](media/databox-migration-add-datastore-type.png)

4. A varázsló 2. lépésében válassza az **NFS 3** NFS-verzió lehetőséget, majd kattintson a **tovább**gombra.

   ![Új adattár hozzáadása – NFS-verzió](media/databox-migration-add-datastore-nfs-version.png)

5. A varázsló 3. lépésében adja meg az adattár nevét, az elérési utat és a kiszolgálót. Használhatja a Data Box IP-címét a kiszolgálóhoz. A mappa elérési útja a megadott `/<StorageAccountName_BlockBlob>/<ContainerName>/` formátumban jelenik meg.

   ![Új adattár – NFS-konfiguráció hozzáadása](media/databox-migration-add-datastore-nfs-configuration.png)

6. A varázsló 4. lépésében válassza ki azokat az ESXi-gazdagépeket, amelyekhez csatlakoztatni szeretné az adattárat, majd kattintson a **tovább**gombra.  A fürtben válassza a minden gazdagép lehetőséget a virtuális gépek áttelepítésének biztosításához.

   ![Új adattár hozzáadása – gazdagépek kiválasztása](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. A varázsló 5. lépésében tekintse át az összegzést, és válassza a **Befejezés**lehetőséget.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Adatmásolás a Data Box NFS-adattárba

A virtuális gépek áttelepíthetők vagy klónozottak az új adattárba.  Az áttelepíteni kívánt használaton kívüli virtuális gépek áttelepíthetők a Data Box NFS-adattárba a **Storage vMotion** kapcsoló használatával. Az aktív virtuális gépek klónozása a Data Box NFS-adattárba történhet.

* Azonosítsa és sorolja fel az **áthelyezhető**virtuális gépeket.
* Azonosítsa és sorolja fel azokat a virtuális gépeket, amelyeknek **klónozottnak**kell lennie.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Virtuális gép áthelyezése egy Data Box adattárba

1. Kattintson a jobb gombbal arra a virtuális gépre, amelyet át szeretne helyezni a Data Box adattárba, majd válassza az **áttelepítés**lehetőséget.

    ![Virtuális gép migrálása](media/databox-migration-vm-migrate.png)

2. Válassza a **tárterület módosítása csak** az áttelepítési típushoz lehetőséget, majd kattintson a **tovább**gombra.

    ![Csak a virtuális gép tárterületének áttelepíteni](media/databox-migration-vm-migrate-change-storage.png)

3. Válassza a **Databox-adattár** lehetőséget célként, majd válassza a **tovább**lehetőséget.

    ![Virtuális gép áttelepíteni – adattár kiválasztása](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Tekintse át az adatokat, és válassza a **Befejezés**lehetőséget.

5. További virtuális gépek esetén ismételje meg az 1 – 4. lépést.

> [!TIP]
> Egyszerre több virtuális gépet is kiválaszthat, amelyek ugyanabban az állapotban vannak (be van kapcsolva vagy ki van kapcsolva), és nagy mennyiségű áttelepíthetők.

A rendszer áttelepíti a virtuális gépet a Data Box NFS-adattárára. Az összes virtuális gép migrálása után kikapcsolhatja (leállíthatja) az aktív virtuális gépeket az Azure VMware-megoldásba való áttelepítésre való felkészülés során.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Virtuális gép vagy virtuálisgép-sablon klónozása a Data Box adattárba

1. Kattintson a jobb gombbal a klónozáshoz használni kívánt virtuális gépre vagy virtuálisgép-sablonra. Válassza a **klón**klónozása  >  **virtuális gépre**lehetőséget.

    ![Virtuális gép klónozása](media/databox-migration-vm-clone.png)

2. Válassza ki a klónozott virtuális gép vagy a virtuálisgép-sablon nevét.

3. Válassza ki azt a mappát, ahová el szeretné helyezni a klónozott objektumot, majd kattintson a **tovább**gombra.

4. Válassza ki azt a fürtöt vagy erőforráskészletet, amelyben el szeretné helyezni a klónozott objektumot, majd kattintson a **tovább**gombra.

5. Válassza a **Databox-adattár** lehetőséget a tárolási helyként, majd kattintson a **tovább**gombra.

    ![Virtuális gép klónozása – adattár kiválasztása](media/databox-migration-vm-clone-select-datastore.png)

6. Ha testre szeretné szabni a klónozott objektum beállításait, válassza ki a testreszabási beállításokat, majd kattintson a **tovább**gombra.

7. Tekintse át a konfigurációkat, és válassza a **Befejezés**lehetőséget.

8. További virtuális gépek vagy virtuálisgép-sablonok esetén ismételje meg az 1 – 7. lépést.

A virtuális gépek klónozása és tárolása a Data Box NFS-adattárán történik. A virtuális gépek klónozása után győződjön meg róla, hogy az Azure VMware-megoldásba való áttelepítésre való felkészülés során le van állítva.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO-fájlok másolása a Data Box adattárba

1. A helyszíni vCenter webes felhasználói felületén nyissa meg a **Storage**lapot.  Válassza ki a **Databox-adattár** elemet, majd válassza a **fájlok**lehetőséget. Hozzon létre egy új mappát az ISO-fájlok tárolásához.

    ![ISO másolása – új mappa létrehozása](media/databox-migration-create-folder.png)

2. Adja meg annak a mappának a nevét, ahová az ISO-fájlokat tárolni fogja.

3. Kattintson duplán az újonnan létrehozott mappára a megnyitásához.

4. Válassza a **fájlok feltöltése** lehetőséget, majd válassza ki a feltölteni kívánt ISO-fájlokat.
    
    ![ISO-fájlok feltöltésének másolása](media/databox-migration-upload-iso.png)

> [!TIP]
> Ha már rendelkezik ISO-fájlokkal a helyszíni adattárolóban, kiválaszthatja a fájlokat, és **másolja** a fájlokat a Data Box NFS-adattárba.


## <a name="prepare-data-box-for-return"></a>Data Box előkészítése a visszatéréshez

Miután az összes virtuális gép adatait, a virtuálisgép-sablon adatait és az összes ISO-fájlt átmásolja a Data Box NFS-adattárba, leválaszthatja az adattárt a vCenter. Az adattár leválasztása előtt az összes virtuális gépet és virtuálisgép-sablont el kell távolítani a leltárból.

### <a name="remove-objects-from-inventory"></a>Objektumok eltávolítása a leltárból

1. A helyszíni vCenter webes felhasználói felületén nyissa meg a **Storage**lapot. Válassza ki a **Databox-adattár** elemet, majd válassza a **virtuális gépek**lehetőséget.

    ![Virtuális gépek eltávolítása a leltárból – kikapcsolva](media/databox-migration-select-databox-vm.png)

2. Győződjön meg arról, hogy az összes virtuális gép le van állítva.

3. Válassza az összes virtuális gép lehetőséget, kattintson a jobb gombbal, majd válassza **az Eltávolítás a leltárból**lehetőséget.

    ![Virtuális gépek eltávolítása a leltárból](media/databox-migration-remove-vm-from-inventory.png)

4. Válassza **a mappákban található** virtuálisgép-sablonok lehetőséget, majd ismételje meg a 3. lépést.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>A Data Box NFS-adattár eltávolítása a vCenter-ből

Az Data Box NFS-adattárat a visszatérés előkészítése előtt le kell kapcsolni VMware ESXi gazdagépekről.

1. A helyszíni vCenter webes felhasználói felületén nyissa meg a **Storage**lapot.

2. Kattintson a jobb gombbal a **Databox-adattár** elemre, és válassza az **adattár leválasztása**lehetőséget.

    ![Data Box adattár leválasztása](media/databox-migration-unmount-datastore.png)

3. Válassza ki az összes ESXi-gazdagépet, ahol az adattár csatlakoztatva van, és kattintson **az OK gombra**.

    ![Data Box adattár leválasztása – gazdagépek kiválasztása](media/databox-migration-unmount-datastore-select-hosts.png)

4. Tekintse át és fogadja el a figyelmeztetéseket, és kattintson **az OK gombra**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Data Box előkészítése visszalépéshez, majd visszaküldése

Hajtsa végre a következő cikkben ismertetett lépéseket: [Azure Data Box visszaküldése és az adatfeltöltés ellenőrzése az Azure](../databox/data-box-deploy-picked-up.md) -ba a Data Box visszaküldéséhez. Az Adatmásolás állapotát az Azure Storage-fiókjába kell ellenőriznie. Miután az állapot befejezettként jelenik meg, ellenőrizheti az Azure Storage-fiókban tárolt adatait.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Adatok másolása az Azure Storage-ból az Azure VMware-megoldásba

A Data Box eszközre másolt adatmennyiség elérhető lesz az Azure Storage-fiókban, miután a Data Box a befejezett állapotot jeleníti meg. Az adatai mostantól átmásolhatók az Azure VMware-megoldásba. A Storage-fiókban lévő adatait az NFS protokoll használatával át kell másolni a privát felhő vSAN adattárba. 

Először másolja a blob Storage-beli adataikat egy felügyelt lemezre egy Azure-beli linuxos virtuális gépen a **AzCopy**használatával. Tegye elérhetővé a felügyelt lemezt az NFS-en keresztül, csatlakoztassa az NFS-megosztást adattárként a saját felhőben, majd másolja az adatait. Ez a módszer lehetővé teszi az adatmennyiség gyors másolását a privát felhőbe.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Másolja az adatait a saját felhőbe linuxos virtuális géppel és felügyelt lemezekkel, majd exportálja NFS-megosztásként

1. Hozzon létre egy [Linux virtuális gépet](../virtual-machines/linux/quick-create-portal.md) az Azure-ban ugyanabban a régióban, ahol a Storage-fiókot létrehozta, és rendelkezik egy Azure-beli virtuális hálózati kapcsolatban a saját felhővel.

2. Hozzon létre egy felügyelt lemezt, amelynek tárolókapacitása nagyobb a blob-adatmennyiségnél, és [csatolja a linuxos virtuális géphez](../virtual-machines/linux/attach-disk-portal.md).  Ha a blob-adatmennyiség nagyobb, mint az elérhető legnagyobb felügyelt lemez kapacitása, az adatátvitelt több lépésben vagy több felügyelt lemez használatával kell átmásolni.

3. Kapcsolódjon a Linux rendszerű virtuális géphez, és csatlakoztassa a felügyelt lemezt.

4. Telepítse a AzCopy-t a [linuxos virtuális gépre](../storage/common/storage-use-azcopy-v10.md).

5. Töltse le az Azure Blob Storage-ból származó adatait a felügyelt lemezre a AzCopy használatával.  Parancs szintaxisa: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Cserélje le az `<storage-account-name>` -t az Azure Storage-fiók nevére és `<container-name>` a Data Box használatával átmásolt adattárolóra.

6. Telepítse az NFS-kiszolgálót a linuxos virtuális gépre:

    - Ubuntu/Debian disztribúcióban: `sudo apt install nfs-kernel-server` .
    - Vállalati Linux-disztribúción: `sudo yum install nfs-utils` .

7. Módosítsa a felügyelt lemezen lévő azon mappa engedélyét, amelyen az Azure Blob Storage-ból másolt adatok másolása megtörtént.  Módosítsa az NFS-megosztásként exportálni kívánt összes mappa engedélyeit.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Rendeljen engedélyeket az ügyfél IP-címeihez az NFS-megosztás eléréséhez a fájl szerkesztésével `/etc/exports` .

    ```bash
    sudo vi /etc/exports
    ```
    
    Adja meg a következő sorokat a fájlban a privát felhő összes ESXi-gazdagépének IP-címéhez.  Ha több mappára is hoz létre megosztásokat, adja hozzá az összes mappát.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportálja az NFS-megosztásokat a `sudo exportfs -a` parancs használatával.

10. Indítsa újra az NFS kernel-kiszolgálót a `sudo systemctl restart nfs-kernel-server` parancs használatával.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Csatlakoztassa a linuxos virtuális gép NFS-megosztását adattárként egy privát felhőalapú vCenter-fürtön, majd másolja az Adatmásolás

A linuxos virtuális gépről származó NFS-megosztást a saját felhőalapú vCenter-fürtön lévő adattárként kell csatlakoztatni. A csatlakoztatása után az adatok átmásolhatók az NFS-adattárból a Private Cloud vSAN adattárba.

1. Jelentkezzen be a saját felhőalapú vCenter-kiszolgálójára.

2. Kattintson a jobb gombbal az **adatközpont**elemre, válassza a **tárolás**lehetőséget, válassza az **új adattár**lehetőséget, majd válassza a **tovább**lehetőséget.

   ![Új adattár hozzáadása](media/databox-migration-add-datastore.png)

3. Az adattár hozzáadása varázsló 1. lépésében válassza ki az **NFS** -típust.

   ![Új adattár hozzáadása – típus](media/databox-migration-add-datastore-type.png)

4. A varázsló 2. lépésében válassza az **NFS 3** NFS-verzió lehetőséget, majd kattintson a **tovább**gombra.

   ![Új adattár hozzáadása – NFS-verzió](media/databox-migration-add-datastore-nfs-version.png)

5. A varázsló 3. lépésében adja meg az adattár nevét, az elérési utat és a kiszolgálót.  Használhatja a linuxos virtuális gép IP-címét a-kiszolgálóhoz.  A mappa elérési útja a megadott `/<folder>/<subfolder>/` formátumban jelenik meg.

   ![Új adattár – NFS-konfiguráció hozzáadása](media/databox-migration-add-datastore-nfs-configuration.png)

6. A varázsló 4. lépésében válassza ki azokat az ESXi-gazdagépeket, amelyekhez csatlakoztatni szeretné az adattárat, majd kattintson a **tovább**gombra.  A fürtben válassza a minden gazdagép lehetőséget a virtuális gépek áttelepítésének biztosításához.

   ![Új adattár hozzáadása – gazdagépek kiválasztása](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. A varázsló 5. lépésében tekintse át az összegzést, majd kattintson a **Befejezés gombra**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Virtuális gépek és virtuálisgép-sablonok hozzáadása egy NFS-adattárból a leltárba

1. A saját felhőalapú vCenter webes felhasználói felületén nyissa meg a **Storage**lapot.  Válassza ki a Linux rendszerű virtuális gép NFS-adattárát, majd válassza a **fájlok**lehetőséget.

    ![NFS-adattárból származó fájlok kiválasztása](media/databox-migration-datastore-select-files.png)

2. Válasszon olyan mappát, amely tartalmaz egy virtuális gépet vagy egy virtuálisgép-sablont.  A részleteket tartalmazó ablaktáblán válassza ki a virtuális gép vagy a. vmtx fájl. VMX fájlját egy virtuálisgép-sablonhoz.

3. Válassza a **VM regisztrálása** lehetőséget a virtuális gép saját Felhőbeli vCenter való regisztrálásához.

    ![Virtuális gép regisztrálása](media/databox-migration-datastore-register-vm.png)

4. Válassza ki azt az adatközpontot, mappát és fürtöt vagy erőforráskészletet, ahová regisztrálni szeretné a virtuális gépet.

4. Ismételje meg a 3. és a 4. lépést az összes virtuális gép és virtuálisgép-sablon esetében.

5. Nyissa meg az ISO-fájlokat tartalmazó mappát.  Válassza ki az ISO-fájlokat, majd a **Másolás gombra** kattintva másolja a fájlokat egy mappába a vSAN adattárba.

A virtuális gépek és a virtuálisgép-sablonok mostantól elérhetők a saját felhőalapú vCenter. Ezeket a virtuális gépeket a bekapcsolása előtt át kell helyezni az NFS-adattárból a vSAN adattárba. Használhatja a **Storage vMotion** kapcsolót, és kiválaszthatja a virtuális gépek céljaként szolgáló vSAN adattárt.

A virtuálisgép-sablonokat a linuxos virtuális gép NFS-adattáráról a vSAN adattárba kell klónozott.

### <a name="clean-up-your-linux-virtual-machine"></a>A linuxos virtuális gép karbantartása

Miután az összes fájlt átmásolta a saját felhőbe, eltávolíthatja az NFS-adattárt a saját felhőből:

1. Győződjön meg arról, hogy az összes virtuális gép és sablon át lett helyezve és klónozott a vSAN adattárba.

2. Az NFS-adattárból származó összes virtuálisgép-sablon eltávolítása a készletből.

3. Válassza le a linuxos virtuális gép adattárát a saját felhőalapú vCenter.

4. Törölje a virtuális gépet és a felügyelt lemezt az Azure-ból.

5. Ha nem szeretné megőrizni a Storage-fiókban Data Box által átvitt adatait, törölje az Azure Storage-fiókot.  
    


## <a name="next-steps"></a>További lépések

* További információ a [Data Boxról](../databox/data-box-overview.md).
* További információ a [számítási feladatok saját felhőbe való áttelepítésének](migrate-workloads.md)különböző lehetőségeiről.
