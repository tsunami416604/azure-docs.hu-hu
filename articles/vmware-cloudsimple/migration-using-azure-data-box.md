---
title: Azure VMware-megoldás CloudSimple szerint – áttelepítés Azure Data Box használatával
description: Tömeges adatáttelepítés az Azure VMware-megoldásba a CloudSimple használatával Azure Data Box
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817461"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>Az Azure VMware-megoldásba való áttelepítés CloudSimple használatával Azure Data Box

A Microsoft Azure Data Box felhőalapú megoldással gyorsan, olcsón és megbízható módon küldhet több terabájtnyi adatot az Azure-ba. A biztonságos adatátvitelt egy saját fejlesztésű Data Box tárolóeszköz küldésével gyorsítjuk fel. Minden tárolóeszköz legfeljebb 80 TB használható tárolókapacitással rendelkezik, és egy regionális futár szállítja az Ön adatközpontjába. Az eszköz ütésálló tokkal rendelkezik, hogy az adatok az átvitel során védve legyenek.

A Azure Data Box használatával a VMware-adatai nagy részét áttelepítheti a saját felhőbe.  A helyszíni vSphere-környezetből származó adatok az NFS protokoll használatával másolódnak a Data Boxba.  A tömeges adatáttelepítés magában foglalja a virtuális gépek, a konfiguráció és a kapcsolódó adatok egy adott időpontra történő másolását a Data Box és az Azure-ba szállítjuk.

Ez a cikk a következőket ismerteti:

* Azure Data Box beállítása.
* Adatok másolása a helyszíni VMware-környezetből a Data Box NFS használatával.
* Azure Data Box visszaküldésének előkészítése.
* Készítse elő a blob-Adatmásolást az Azure VMware-megoldásba CloudSimple.
* Adatok másolása az Azure-ból a saját felhőbe.

## <a name="scenarios"></a>Forgatókönyvek

Azure Data Box a következő esetekben kell használni a tömeges adatáttelepítés során.

* Nagy mennyiségű adat migrálása a helyszínről az Azure VMware-megoldásba a CloudSimple használatával, amely alapkonfigurációként és szinkronizálási különbségekkel használható a hálózaton.
* Nagy mennyiségű kikapcsolt virtuális gép migrálása (hideg virtuális gépek).
* Telepítse át a virtuális gépeket a fejlesztési és tesztelési környezetek beállításához.
* Nagy mennyiségű virtuálisgép-sablon, ISOs és virtuálisgép-lemez áttelepíthető.

## <a name="before-you-begin"></a>Előkészületek

* Győződjön meg arról, hogy az előfeltételeket és a [sorrendet Data Box](../databox/data-box-deploy-ordered.md) a Azure Portal.  A rendelési folyamat során ki kell választania egy Storage-fiókot, amely lehetővé teszi a BLOB Storage használatát.  Miután megkapta a Data Box, kapcsolja össze a helyszíni hálózattal, és [állítsa be a Data Boxt](../databox/data-box-deploy-set-up.md) a vSphere felügyeleti hálózatról elérhető IP-címmel.

* Hozzon létre egy virtuális hálózatot és egy Storage-fiókot az Azure ugyanazon régiójában, ahol a CloudSimple által biztosított Azure VMware-megoldás van kiépítve.

* Hozzon létre Azure-beli [virtuális hálózati kapcsolatot](cloudsimple-azure-network-connection.md) a saját felhőből a virtuális hálózatba, ahol a Storage-fiók az [Azure-beli virtuális hálózat CloudSimple-hez való csatlakoztatása a ExpressRoute használatával](virtual-network-connection.md) című cikkben említett lépések alapján történik.

## <a name="set-up-azure-data-box-for-nfs"></a>NFS-Azure Data Box beállítása

Kapcsolódjon Azure Data Box helyi webes felhasználói felületéhez az **eszközhöz való kapcsolódás** részben említett lépéseket követve, a következő cikkben: [Tutorial: Kábelt, és kapcsolódjon a Azure Data Box @ no__t-0.  Konfigurálja Data Box az NFS-ügyfelek elérésének engedélyezéséhez.

1. A helyi webes felületen lépjen a **Connect and copy** (Kapcsolódás és másolás) lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre. 

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/nfs-client-access.png)

2. Adja meg a VMware ESXi gazdagépek IP-címét, és kattintson a **Hozzáadás**gombra. A vSphere-fürtben található összes gazdagép hozzáférését a következő lépés megismétlésével állíthatja be. Kattintson az **OK** gombra.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. Nem másolhat fájlokat közvetlenül a tárfiók *gyökér*mappájába.

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. Az Azure Files-megosztások alatti első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat a Data Boxon található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> A VMware-adatok másolásához használja az Azure Block blobokat.

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>Az NFS-megosztás csatlakoztatása adattárként a helyszíni vCenter-fürtön és az Adatmásolás

A Azure Data Box NFS-megosztást a helyszíni vCenter-fürtön lévő adattárként kell csatlakoztatni, vagy az adatok másolásához VMware ESXi gazdagépet.  A csatlakoztatás után az adatmásolható az NFS-adattárba.

1. Jelentkezzen be a helyszíni vCenter-kiszolgálóra.

2. Kattintson a jobb gombbal az **adatközpontra**, válassza a **tárterület** Hozzáadás kattintson az **új adattár** elemre, majd kattintson a **tovább** gombra.

   ![Új adattár hozzáadása](media/databox-migration-add-datastore.png)

3. Az adattár hozzáadása varázsló 1. lépésében válassza az **NFS**típust.

   ![Új adattár hozzáadása – típus](media/databox-migration-add-datastore-type.png)

4. A 2. lépésben válassza az **NFS 3** NFS-verzió lehetőséget, majd kattintson a **tovább**gombra.

   ![Új adattár hozzáadása – NFS-verzió](media/databox-migration-add-datastore-nfs-version.png)

5. A 3. lépésben adja meg az adattár nevét, az elérési utat és a kiszolgálót.  Használhatja a Data Box IP-címét a kiszolgálóhoz.  A mappa elérési útja `/<StorageAccountName_BlockBlob>/<ContainerName>/` formátumú lesz.

   ![Új adattár – NFS-konfiguráció hozzáadása](media/databox-migration-add-datastore-nfs-configuration.png)

6. A 4. lépésben válassza ki azokat az ESXi-gazdagépeket, amelyekhez csatlakoztatni szeretné az adattárat, majd kattintson a **tovább**gombra.  A fürtben válassza a minden gazdagép lehetőséget a virtuális gépek áttelepítésének biztosításához.

   ![Új adattár hozzáadása – gazdagépek kiválasztása](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Az 5. lépésben tekintse át az összegzést, és kattintson a **Befejezés** gombra.

## <a name="copy-data-to-data-box-nfs-datastore"></a>Az Adatmásolás Data Box NFS-adattárba

A virtuális gépek áttelepíthetők vagy klónozottak az új adattárba.  Az áttelepíteni kívánt fel nem használt virtuális gépek áttelepíthetők Data Box NFS-adattárba a **Storage vMotion**használatával.  Az aktív virtuális gépek **klónozása** a Data Box NFS-adattárba történhet.

* Azonosítsa az **áthelyezhető**virtuális gépek listáját.
* Azonosítsa azon virtuális gépek listáját, amelyeknek **klónozottnak**kell lennie.

### <a name="move-virtual-machine-to-data-box-datastore"></a>Virtuális gép áthelyezése Data Box adattárba

1. Kattintson a jobb gombbal a virtuális gépre, amelyet át szeretne helyezni Data Box adattárba, és válassza az **áttelepítés**lehetőséget.

    ![Virtuális gép migrálása](media/databox-migration-vm-migrate.png)

2. Válassza a **tárterület módosítása csak** áttelepítési típushoz lehetőséget, majd kattintson a **tovább**gombra.

    ![Csak a virtuális gép tárterületének áttelepíteni](media/databox-migration-vm-migrate-change-storage.png)

3. Válassza ki Data Box adattárt célként, és kattintson a **tovább**gombra.

    ![Virtuális gép áttelepíteni – adattár kiválasztása](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Tekintse át az adatokat, és kattintson a **Befejezés**gombra.

5. Ismételje meg az 1 – 4. lépést más virtuális gépek esetében.

> [!TIP]
> Egyszerre több virtuális gépet is kijelölhet (bekapcsolva vagy kikapcsolva), és tömegesen áttelepítheti őket.

A virtuális gép tárterületét a rendszer áttelepíti a Azure Data Boxról az NFS-adattárba.  Az összes virtuális gép migrálása után leállíthatja a virtuális gépeket az Azure VMware megoldásba való áttelepítésre való felkészülés során.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>Virtuális gép vagy virtuálisgép-sablon klónozása Data Box adattárba

1. Kattintson a jobb gombbal a klónozott virtuális gépre vagy virtuálisgép-sablonra.  **Klónozás**és **a virtuális gép klónozása**

    ![Virtuális gép klónozása](media/databox-migration-vm-clone.png)

2. Válassza ki a klónozott virtuális gép vagy a virtuálisgép-sablon nevét.

3. Válassza ki azt a mappát, ahová el szeretné helyezni a klónozott objektumot, és kattintson a **tovább**gombra.

4. Válassza ki azt a fürtöt vagy erőforráskészletet, ahol a klónozott objektumot szeretné, majd kattintson a **tovább**gombra.

5. Válassza ki a Azure Data Box NFS-adattárt tárolási helyként, majd kattintson a **tovább**gombra.

    ![Virtuális gép klónozása – adattár kiválasztása](media/databox-migration-vm-clone-select-datastore.png)

6. Válassza a Testreszabás lehetőséget, ha testre szeretné szabni a klónozott objektum beállításait, majd kattintson a **tovább**gombra.

7. Tekintse át a konfigurációkat, és kattintson a **Befejezés**gombra.

8. További virtuális gépek vagy virtuálisgép-sablonok esetén ismételje meg az 1 – 7. lépést.

A virtuális gépek klónozása és tárolása a Azure Data Box NFS-adattárán történik.  Ha a virtuális gépek klónozása megtörtént, győződjön meg arról, hogy a klónozott virtuális gépek ki vannak kapcsolva az Azure VMware-megoldásba való áttelepítésre való felkészülés során.

### <a name="copy-iso-files-to-data-box-datastore"></a>ISO-fájlok másolása Data Box adattárba

1. A helyszíni vCenter webes felhasználói felületén navigáljon a **tárolóhoz**.  Válassza Data Box NFS-adattár lehetőséget, majd kattintson a **fájlok**elemre.  Hozzon létre egy **új mappát** az ISO-fájlok tárolásához.

    ![ISO másolása – új mappa létrehozása](media/databox-migration-create-folder.png)

2. Adja meg annak a mappának a nevét, ahová az ISO-fájlokat tárolni fogja.

3. Kattintson duplán az újonnan létrehozott mappára a tartalom eléréséhez.

4. Kattintson a **fájlok feltöltése** elemre, és válassza ki a feltölteni kívánt ISO-ket.
    
    ![ISO-fájlok feltöltésének másolása](media/databox-migration-upload-iso.png)

> [!TIP]
> Ha már rendelkezik ISO-fájlokkal a helyszíni adattárolón, kiválaszthatja a fájlokat, és **másolhatja azokat** a Data Box NFS-adattárba.


## <a name="prepare-azure-data-box-for-return"></a>Azure Data Box előkészítése a visszatéréshez

Miután az összes virtuális gép adatait, a virtuálisgép-sablon adatait és az összes ISO-fájlt átmásolja a Data Box NFS-adattárba, az adattár leválasztható a vCenter.  Az adattár leválasztásához az összes virtuális gépet és virtuálisgép-sablont el kell távolítani a leltárból.

### <a name="remove-objects-from-inventory"></a>Objektumok eltávolítása a leltárból

1. A helyszíni vCenter webes felhasználói felületén navigáljon a **tárolóhoz**.  Válassza Data Box NFS-adattár lehetőséget, majd kattintson a **virtuális gépek**elemre.

2. Győződjön meg arról, hogy az összes virtuális gép ki van kapcsolva.

    ![Virtuális gépek eltávolítása a leltárból-kikapcsolva](media/databox-migration-select-databox-vm.png)

3. Válassza az összes virtuális gép lehetőséget, kattintson a jobb gombbal, majd válassza **a törlés a leltárból**lehetőséget.

    ![Virtuális gépek eltávolítása a leltárból](media/databox-migration-remove-vm-from-inventory.png)

4. Válassza a felső gombok **mappák területén található** virtuálisgép-sablonok lehetőséget, majd ismételje meg a 3. lépést. 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>Azure Data Box NFS-adattár eltávolítása a vCenter-ből

Data Box NFS-adattárat a visszatérés előkészítése előtt le kell kapcsolni VMware ESXi gazdagépekről.

1. A helyszíni vCenter webes felhasználói felületén navigáljon a **tárolóhoz**.

2. Kattintson a jobb gombbal a Data Box NFS-adattárra, és válassza az **adattár leválasztása**lehetőséget.

    ![Data Box adattár leválasztása](media/databox-migration-unmount-datastore.png)

3. Válassza ki az összes ESXi-gazdagépet, ahol az adattár csatlakoztatva van, és kattintson **az OK**gombra.

    ![Data Box adattár leválasztása – gazdagépek kiválasztása](media/databox-migration-unmount-datastore-select-hosts.png)

4. Tekintse át és fogadja el a figyelmeztetéseket, és kattintson **az OK**gombra.

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>Data Box előkészítése a visszatéréshez és a Data Box visszaküldése

Hajtsa végre a következő cikkben ismertetett lépéseket: [Azure Data Box visszaküldése és az adatfeltöltés ellenőrzése az Azure](../databox/data-box-deploy-picked-up.md) -ba a Data Box visszaküldéséhez.  Ellenőrizze az Azure Storage-fiókban tárolt adatmásolási állapotot, és ha az állapot megjelenik, ellenőrizheti az Azure Storage-fiókban tárolt adatait.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>Adatok másolása az Azure Storage-ból az Azure VMware-megoldásba CloudSimple

A Azure Data Boxra másolt adatmennyiség elérhető lesz az Azure Storage-fiókjában, ha a Data Box sorrendje befejezettként jelenik meg.  Az adatai mostantól a CloudSimple segítségével másolhatók az Azure VMware-megoldásba.  A Storage-fiókban lévő adatait az NFS protokoll használatával kell átmásolni a vSAN adattárba.  Először másolja a blob Store-ba az Azure-beli linuxos virtuális gépek felügyelt lemezére a **AzCopy**használatával.  Tegye elérhetővé a felügyelt lemezt az NFS protokollon keresztül, és csatlakoztassa az NFS-megosztást adattárként a saját felhőben, és másolja az adatait.  Ez a módszer lehetővé teszi az adatmennyiség gyors másolását a saját felhőbe. 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>Az Adatmásolás a saját felhőbe linuxos virtuális géppel és felügyelt lemezekkel, valamint az NFS-megosztásként való exportálással

1. Hozzon létre egy [Linux virtuális gépet](../virtual-machines/linux/quick-create-portal.md) az Azure-ban ugyanabban a régióban, ahol a Storage-fiókot létrehozta, és rendelkezik egy Azure-beli virtuális hálózati kapcsolatban a saját felhővel.

2. Hozzon létre egy felügyelt lemezt, amely nagyobb a blob-adatmennyiségnél, és [csatolja azt a linuxos virtuális géphez](../virtual-machines/linux/attach-disk-portal.md).  Ha a blob-adatmennyiség nagyobb, mint az elérhető legnagyobb felügyelt lemez, az adatátvitelt több lépésben vagy több felügyelt lemez használatával kell átmásolni.

3. Kapcsolódjon a Linux rendszerű virtuális géphez, és csatlakoztassa a felügyelt lemezt.

4. Telepítse a AzCopy-t a [linuxos virtuális gépre](../storage/common/storage-use-azcopy-v10.md).

5. Töltse le az adatait az Azure Blob Storage-ból a felügyelt lemezre a AzCopy használatával.  Parancs szintaxisa: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Cserélje le a `<storage-account-name>` értéket az Azure Storage-fiókja nevére, és `<container-name>` értéket a tárolóval, amely a Azure Data Box használatával másolt adatait tartalmazza.

6. Telepítse az NFS-kiszolgálót a linuxos virtuális gépre.

    1. Ubuntu/Debian disztribúcióban: `sudo apt install nfs-kernel-server`.
    2. Vállalati Linux-disztribúcióban: `sudo yum install nfs-utils`.

7. Módosítsa a felügyelt lemezen lévő azon mappa engedélyét, ahová az Azure Blob Store-ból másolt adatok másolása megtörtént.  Módosítsa az összes olyan mappa engedélyeit, amelyeket NFS-megosztásként szeretne exportálni.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. A `/etc/exports` fájl szerkesztésével rendeljen engedélyeket az ügyfél IP-címeihez az NFS-megosztás eléréséhez.

    ```bash
    sudo vi /etc/exports
    ```
    
    Adja meg a következő sorokat a fájlban a privát felhő összes ESXi-gazdagépének IP-címéhez.  Ha több mappához hoz létre megosztásokat, adja hozzá az összes mappát.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportálja az NFS-megosztásokat az `sudo exportfs -a` parancs használatával.

10. Indítsa újra az NFS kernel-kiszolgálót a `sudo systemctl restart nfs-kernel-server` parancs használatával.


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>A Linux rendszerű virtuális gépek NFS-megosztásának csatlakoztatása adattárként a Private Cloud vCenter-fürtön és az Adatmásolás

Az NFS-megosztást a linuxos virtuális gépről adattárként kell csatlakoztatni a saját felhőalapú vCenter-fürtön az adatok másolásához.  A csatlakoztatás után az adatok átmásolhatók az NFS-adattárból a saját felhőalapú vSAN adattárba.

1. Jelentkezzen be a saját felhőalapú vCenter-kiszolgálójára.

2. Kattintson a jobb gombbal az **adatközpontra**, válassza a **tárterület** Hozzáadás kattintson az **új adattár** elemre, majd kattintson a **tovább** gombra.

   ![Új adattár hozzáadása](media/databox-migration-add-datastore.png)

3. Az adattár hozzáadása varázsló 1. lépésében válassza az **NFS**típust.

   ![Új adattár hozzáadása – típus](media/databox-migration-add-datastore-type.png)

4. A 2. lépésben válassza az **NFS 3** NFS-verzió lehetőséget, majd kattintson a **tovább**gombra.

   ![Új adattár hozzáadása – NFS-verzió](media/databox-migration-add-datastore-nfs-version.png)

5. A 3. lépésben adja meg az adattár nevét, az elérési utat és a kiszolgálót.  Használhatja a Linux rendszerű virtuális gép IP-címét a kiszolgálóhoz.  A mappa elérési útja `/<folder>/<subfolder>/` formátumú lesz.

   ![Új adattár – NFS-konfiguráció hozzáadása](media/databox-migration-add-datastore-nfs-configuration.png)

6. A 4. lépésben válassza ki azokat az ESXi-gazdagépeket, amelyekhez csatlakoztatni szeretné az adattárat, majd kattintson a **tovább**gombra.  A fürtben válassza a minden gazdagép lehetőséget a virtuális gépek áttelepítésének biztosításához.

   ![Új adattár hozzáadása – gazdagépek kiválasztása](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Az 5. lépésben tekintse át az összegzést, és kattintson a **Befejezés** gombra.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>Virtuális gépek és virtuálisgép-sablonok hozzáadása az NFS-adattárból a leltárba

1. A saját felhőalapú vCenter webes felhasználói felületén navigáljon a **Storage**szolgáltatáshoz.  Válassza a Linux rendszerű virtuális gép NFS-adattár elemet, és kattintson a **fájlok**elemre.

    ![NFS-adattárból származó fájlok kiválasztása](media/databox-migration-datastore-select-files.png)

2. Válasszon egy mappát, amely tartalmaz egy virtuális gépet vagy egy virtuálisgép-sablont.  A részleteket tartalmazó ablaktáblán válassza ki `.vmx` fájlt egy virtuális géphez, vagy `.vmtx` fájlt egy virtuálisgép-sablonhoz.

3. Kattintson a virtuális gép **regisztrálása** lehetőségre, hogy regisztrálja a virtuális gépet a saját Felhőbeli vCenter.

    ![Virtuális gép regisztrálása](media/databox-migration-datastore-register-vm.png)

4. Válassza ki azt az adatközpontot, mappát és fürtöt vagy erőforráskészletet, ahová regisztrálni szeretné a virtuális gépet.

4. Ismételje meg a 3. és a 4. lépést az összes virtuális gép és virtuálisgép-sablon esetében.

5. Navigáljon ahhoz a mappához, amely az ISO-fájlokat tartalmazza.  Válassza az ISO-fájlok elemet, és **másolja** a vSAN adattárában található mappába.

A virtuális gépek és a virtuálisgép-sablonok mostantól elérhetők a saját felhőalapú vCenter.  Ezeket a virtuális gépeket át kell helyezni az NFS-adattárból a vSAN adattárba, mielőtt bekapcsolja őket.  Megteheti a virtuális gépek tárolási vMotion, és kiválaszthatja a vSAN adattárt a virtuális gép céljának megfelelően.

A virtuálisgép-sablonokat a linuxos virtuális gép NFS-adattáráról a vSAN adattárba kell klónozott.

### <a name="clean-up-of-your-linux-virtual-machine"></a>A linuxos virtuális gép tisztítása

Miután az összes fájlt átmásolta a saját felhőbe, eltávolíthatja az NFS-adattárt a saját felhőből.

1. Győződjön meg arról, hogy az összes virtuális gép és sablon át van helyezve és klónozott a vSAN adattárba.

2. Távolítsa el az összes virtuálisgép-sablont az NFS-adattárból a leltárból.

3. A Linux rendszerű virtuális gépek adattárának leválasztása a saját Felhőbeli vCenter.

4. Törölje a virtuális gépet és a felügyelt lemezt az Azure-ból.

5. Ha nem szeretné megtartani az átvitt adatátvitelt Azure Data Box használatával a Storage-fiókjában, törölje az Azure Storage-fiókot.  
    


## <a name="next-steps"></a>További lépések

* További információ a [Azure Data Box](../databox/data-box-overview.md)
* További információ a [számítási feladatok saját felhőbe való áttelepítésének](migrate-workloads.md) különböző lehetőségeiről
