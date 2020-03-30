---
title: Azure VMware-megoldás – áttelepítés az Azure Data Box használatával
description: Az Azure Data Box használata az azure-beli VMware-megoldásba történő tömeges áttelepítéshez.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019757"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Adatok áttelepítése az Azure VMware-megoldásba az Azure Data Box használatával

A Microsoft Azure Data Box felhőalapú megoldáslehetővé teszi, hogy terabájtnyi (TB) adatot küldjön az Azure-ba gyors, olcsó és megbízható módon. A biztonságos adatátvitelt egy saját fejlesztésű Data Box tárolóeszköz küldésével gyorsítjuk fel. Minden tárolóeszköz maximálisan felhasználható, 80 TB-os tárolókapacitással rendelkezik, és egy regionális szolgáltató szállítja az adatközpontba. A készülék masszív burkolattal rendelkezik, amely védi és védi az adatokat az átvitel során.

A Data Box használatával tömegesen áttelepítheti a VMware-adatokat a magánfelhőbe. A helyszíni VMware vSphere környezetből származó adatok a Hálózati fájlrendszer (NFS) protokollon keresztül a Data Box ba kerülnek. A tömeges adatáttelepítés magában foglalja a virtuális gépek, a konfiguráció és a kapcsolódó adatok időponthoz való, időbeli másolatát a Data Boxba történő mentését, majd manuálisszállítást az Azure-ba.

Ebben a cikkben a következőket ismerheti meg:

* Az Adatdoboz beállítása.
* Adatok másolása a helyszíni VMware környezetből a Data Box-ba az NFS-en keresztül.
* Felkészülés a Data Box visszaküldésére.
* Blob-adatok előkészítése az Azure VMware-megoldásba való másoláshoz.
* Az azure-beli adatok másolása a magánfelhőbe.

## <a name="scenarios"></a>Forgatókönyvek

A tömeges adatáttelepítéshez a következő esetekben használja az Adatmezőt:

* Nagy mennyiségű adat áttelepítése a helyszíni Azure VMware Solution. Ez a módszer létrehoz egy alapvonalat, és szinkronizálja a különbségeket a hálózaton keresztül.
* Nagyszámú kikapcsolt virtuális gép (hideg virtuális gépek) áttelepítése.
* Virtuálisgép-adatok áttelepítése fejlesztési és tesztelési környezetek beállításához.
* Nagyszámú virtuálisgép-sablon, ISO-fájl és virtuálisgép-lemez áttelepítése.

## <a name="before-you-begin"></a>Előkészületek

* Ellenőrizze az előfeltételeket, és rendelje meg a [Data Box-ot](../databox/data-box-deploy-ordered.md) az Azure Portalon keresztül. A rendelési folyamat során ki kell választania egy tárfiókot, amely lehetővé teszi a Blob storage. Miután megkapta a Data Box eszközt, csatlakoztassa azt a helyszíni hálózathoz, és [állítsa be az eszközt](../databox/data-box-deploy-set-up.md) egy OLYAN IP-címmel, amely elérhető a vSphere felügyeleti hálózatról.

* Hozzon létre egy virtuális hálózatot és egy tárfiókot ugyanabban a régióban, ahol az Azure VMware-megoldás ki van építve.

* Hozzon létre egy [Azure virtuális hálózati kapcsolatot](cloudsimple-azure-network-connection.md) a magánfelhőből a virtuális hálózathoz, ahol a tárfiók jön létre az Azure virtuális hálózat [csatlakoztatása a CloudSimple szolgáltatáshoz az ExpressRoute használatával](virtual-network-connection.md)című lépések végrehajtásával.

## <a name="set-up-data-box-for-nfs"></a>Adatmező beállítása NFS-hez

Csatlakozzon a Data Box helyi webes felhasználói felületéhez az Oktatóanyag "Csatlakozás az eszközhöz" című részében leírt lépéseket [követve: Kábel és csatlakozzon az Azure Data Box-hoz.](../databox/data-box-deploy-set-up.md)  Az Adatmező konfigurálása az NFS-ügyfelek elérésének engedélyezéséhez:

1. A helyi webes felhasználói felületen lépjen a **Csatlakozás és másolás** lapra. Az **NFS-beállítások csoportban**válassza az **NFS-ügyfélhozzáférés**lehetőséget. 

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/nfs-client-access.png)

2. Adja meg a VMware ESXi állomások IP-címét, és válassza **a Hozzáadás**lehetőséget. A vSphere-fürt összes állomásának hozzáférését konfigurálhatja ezzel a lépéssel. Válassza **az OK gombot.**

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. A blokkblob- és lapblobmegosztások alatt létrehozott mappa azt a tárolót jelöli, amelybe a rendszer feltölti az adatokat blobokként. A fájlok nem másolhatók *root* közvetlenül a tárfiók gyökérmappájába.

A blokkblob- és lapblobmegosztások alatti első szintű entitások tárolók, a második szintű entitások pedig blobok. Az Azure Files megosztások alatt az első szintű entitások megosztások, a második szintű entitások pedig fájlok.

Az alábbi táblázat a Data Boxon található megosztások UNC elérési útját és az adatok feltöltéséhez használt Azure Storage elérési útjának URL-címét mutatja. Az Azure Storage elérési útjának végső URL-címe a megosztás UNC elérési útjából származik.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blokkblobok | <li>A megosztások UNC elérési útja: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>A megosztások UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Használja az Azure Block blobok vmware-adatok másolásához.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Az NFS-megosztás csatlakoztatása adattárként a helyszíni vCenter-fürtön, és az adatok másolása

Az Adatok dobozból származó NFS-megosztást adattárként kell csatlakoztatni a helyszíni vCenter-fürtön vagy a VMware ESXi gazdagépen ahhoz, hogy az adatokat az NFS-adattárba lehessen másolni:

1. Jelentkezzen be a helyszíni vCenter-kiszolgálóra.

2. Kattintson a jobb gombbal **az Adatközpont**elemre, válassza a **Tárolás**parancsot, válassza az **Új adattár**lehetőséget, majd kattintson a **Tovább**gombra.

   ![Új adattár hozzáadása](media/databox-migration-add-datastore.png)

3. Az Adattár hozzáadása varázsló **1.** **NFS**

   ![Új adattár hozzáadása - típus](media/databox-migration-add-datastore-type.png)

4. A varázsló **2.** **NFS 3**

   ![Új adattár hozzáadása - NFS-verzió](media/databox-migration-add-datastore-nfs-version.png)

5. A varázsló 3. Használhatja az IP-címét a Data Box a szerver. A mappa elérési útja `/<StorageAccountName_BlockBlob>/<ContainerName>/` formátumban lesz.

   ![Új adattár hozzáadása - NFS-konfiguráció](media/databox-migration-add-datastore-nfs-configuration.png)

6. A varázsló **4.**  Egy fürtben jelölje ki az összes állomást a virtuális gépek áttelepítésének biztosításához.

   ![Új adattár hozzáadása – Állomások kiválasztása](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. A varázsló **5.**

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Adatok másolása a Data Box NFS adattárba

A virtuális gépek áttelepíthetők vagy klónozhatók az új adattárba.  Az áttelepíteni kívánt, nem használt virtuális gépek a **storage vMotion** beállítással áttelepíthetők a Data Box NFS-adattárba. Az aktív virtuális gépek klónozhatók a Data Box NFS adattárba.

* Azonosítsa és sorolja fel az **áthelyezhető**virtuális gépeket.
* Azonosítsa és sorolja fel a klónozni kívánt virtuális **gépeket.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Virtuális gép áthelyezése adatdoboz-adattárba

1. Kattintson a jobb gombbal arra a virtuális gépre, amelyet át szeretne helyezni az adatdoboz adattárába, majd válassza az **Áttelepítés parancsot.**

    ![Virtuális gép áttelepítése](media/databox-migration-vm-migrate.png)

2. Válassza a Csak az áttelepítési típushoz tartozó **Tároló módosítása lehetőséget,** majd a **Tovább**gombot.

    ![Virtuális gép áttelepítése – csak tároló](media/databox-migration-vm-migrate-change-storage.png)

3. Válassza a **Databox-Datastore lehetőséget** célként, majd válassza a **Tovább**gombot.

    ![Virtuális gép áttelepítése – adattár kiválasztása](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Tekintse át az információkat, és válassza a **Befejezés**lehetőséget.

5. Ismételje meg az 1–4.

> [!TIP]
> Több olyan virtuális gépet is kijelölhet, amelyek ugyanabban az energiaállapotban vannak (be vagy ki vannak kapcsolva), és tömegesen telepítheti őket.

A virtuális gép átlesz telepítve az NFS-adattárba a Data Box ból. Az összes virtuális gép áttelepítése után kikapcsolhatja (leállíthatja) az aktív virtuális gépeket az adatok Azure VMware-megoldásba való áttelepítésének előkészítéseként.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Virtuális gép vagy virtuálisgép-sablon klónozása a Data Box adattárba

1. Kattintson a jobb gombbal a klónozni kívánt virtuális gépre vagy virtuálisgép-sablonra. Válassza **a Klónozás** > **a virtuális gép hez**lehetőséget.

    ![Virtuális gép klónja](media/databox-migration-vm-clone.png)

2. Válassza ki a klónozott virtuális gép vagy a virtuálisgép-sablon nevét.

3. Jelölje ki azt a mappát, ahová a klónozott objektumot el szeretné helyezni, majd válassza a **Tovább**gombot.

4. Jelölje ki azt a fürtöt vagy erőforráskészletet, ahová a klónozott objektumot el szeretné helyezni, majd válassza a **Tovább**gombot.

5. Válassza a **Databox-Datastore lehetőséget** tárolóhelyként, majd válassza a **Tovább**gombot.

    ![Virtuálisgép-klón - adattár kiválasztása](media/databox-migration-vm-clone-select-datastore.png)

6. Ha testre szeretné szabni a klónozott objektum beállításait, adja meg a testreszabási beállításokat, majd kattintson a **Tovább**gombra.

7. Tekintse át a konfigurációkat, és válassza a **Befejezés lehetőséget.**

8. Ismételje meg az 1–7.

A virtuális gépek klónozása és tárolása a Data Box nFS-adattárában lesz. A virtuális gépek klónozása után győződjön meg arról, hogy le állnak az Adatok Azure VMware-megoldásba való áttelepítésének előkészítéseként.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO-fájlok másolása a Data Box adattárba

1. A helyszíni vCenter webes felhasználói felületéről nyissa meg a **Storage (Tárhely) webhelyet.**  Válassza a **Databox-Datastore ( Adatmező) lehetőséget,** majd a **Fájlok**lehetőséget. Hozzon létre egy új mappát az ISO-fájlok tárolására.

    ![ISO másolása - új mappa létrehozása](media/databox-migration-create-folder.png)

2. Adja meg annak a mappának a nevét, amelyben az ISO-fájlok at tárolni fogja.

3. Dupla kattintással nyissa meg az újonnan létrehozott mappát.

4. Válassza **a Fájlok feltöltése lehetőséget,** majd jelölje ki a feltölteni kívánt ISO-fájlokat.
    
    ![ISO másolása - fájlok feltöltése](media/databox-migration-upload-iso.png)

> [!TIP]
> Ha már rendelkezik ISO-fájlokkal a helyszíni adattárban, kiválaszthatja a fájlokat, és **a Másolás lehetőséget** a fájlok adatdobozn belüli NFS-adattárba való másolásához.


## <a name="prepare-data-box-for-return"></a>Adatmező előkészítése a visszaküldésre

Miután az összes virtuális gép adatok, virtuálisgép sablon adatok, és az ISO fájlokat másolja a Data Box NFS adattár, akkor bontsa le az adattár a vCenter. Az adattár leválasztása előtt minden virtuális gépet és virtuálisgép-sablont el kell távolítani a készletből.

### <a name="remove-objects-from-inventory"></a>Objektumok eltávolítása a készletből

1. A helyszíni vCenter webes felhasználói felületéről nyissa meg a **Storage (Tárhely) webhelyet.** Válassza a **Databox-Datastore** , majd **a Virtuális gépek**lehetőséget.

    ![Virtuális gépek eltávolítása a készletből - ki van kapcsolva](media/databox-migration-select-databox-vm.png)

2. Győződjön meg arról, hogy az összes virtuális gép leállt.

3. Jelölje ki az összes virtuális gépet, kattintson a jobb gombbal, majd válassza **az Eltávolítás a készletből parancsot.**

    ![Virtuális gépek eltávolítása a készletből](media/databox-migration-remove-vm-from-inventory.png)

4. Válassza a **Virtuálisgép-sablonok lehetőséget a mappákban,** majd ismételje meg a 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>A Data Box NFS-adattár eltávolítása a vCenterből

A Data Box NFS adattárait le kell választani a VMware ESXi gazdagépeiről, mielőtt visszatérne.

1. A helyszíni vCenter webes felhasználói felületéről nyissa meg a **Storage (Tárhely) webhelyet.**

2. Kattintson a jobb gombbal **a Databox-Datastore elemre,** és válassza **az Adattár leválasztása parancsot.**

    ![Adatdoboz-adattár leválasztása](media/databox-migration-unmount-datastore.png)

3. Jelölje ki az összes olyan ESXi-állomást, ahová az adattár csatlakoztatva van, és válassza az **OK gombot.**

    ![Adatdoboz-adattár leválasztása – állomások kiválasztása](media/databox-migration-unmount-datastore-select-hosts.png)

4. Tekintse át és fogadja el a figyelmeztetéseket, és válassza **az OK gombot.**

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Az Adatdoboz előkészítése a visszaküldésre, majd visszaadása

Kövesse az Azure Data Box visszatérése című cikkben ismertetett lépéseket, és ellenőrizze az [Azure-ba való adatfeltöltést](../databox/data-box-deploy-picked-up.md) az adatmező visszaküldéséhez. Ellenőrizze az Azure storage-fiókjába történő adatmásolás állapotát. Miután az állapot befejeződött, ellenőrizheti az azure-beli tárfiókban lévő adatokat.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Adatok másolása az Azure Storage-ból az Azure VMware-megoldásba

A Data Box-eszközre másolt adatok az Azure tárfiókjában lesznek elérhetők, miután a Data Box rendelési állapota befejeződött. Az adatok most már átmásolhatók az Azure VMware-megoldásba. A tárfiókban lévő adatokat az NFS protokoll használatával a magánfelhő vSAN-adattárába kell másolni. 

Először másolja a Blob storage-adatokat egy linuxos virtuális gép felügyelt lemezére az Azure-ban az **AzCopy**használatával. Tegye elérhetővé a felügyelt lemezt az NFS-en keresztül, csatlakoztassa az NFS-megosztást adattárként a magánfelhőbe, majd másolja az adatokat. Ez a módszer lehetővé teszi az adatok gyorsabb másolását a magánfelhőbe.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Adatok másolása a magánfelhőbe Linux-alapú virtuális gép és felügyelt lemezek használatával, majd exportálás NFS-megosztásként

1. Hozzon létre egy [Linux virtuális gépet](../virtual-machines/linux/quick-create-portal.md) az Azure-ban ugyanabban a régióban, ahol a tárfiók jön létre, és egy Azure virtuális hálózati kapcsolat a magánfelhő.

2. Hozzon létre egy felügyelt lemezt, amelynek tárolókapacitása nagyobb, mint a blobadatok mennyisége, és [csatolja a Linux virtuális géphez.](../virtual-machines/linux/attach-disk-portal.md)  Ha a blobadatok mennyisége nagyobb, mint a rendelkezésre álló legnagyobb felügyelt lemez kapacitása, az adatokat több lépésben vagy több felügyelt lemez használatával kell másolni.

3. Csatlakozzon a Linux virtuális géphez, és csatlakoztassa a felügyelt lemezt.

4. Telepítse [az AzCopy-t a Linux virtuális gépére.](../storage/common/storage-use-azcopy-v10.md)

5. Töltse le az adatokat az Azure Blob storage-ból a felügyelt lemezre az AzCopy használatával.  Parancs szintaxisa: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Cserélje `<storage-account-name>` le az Azure `<container-name>` tárfiók nevét, és a tároló, amely tárolja a Data Box-on keresztül másolt adatokat.

6. Telepítse az NFS-kiszolgálót linuxos virtuális gépére:

    - Ubuntu/Debian disztribúción: `sudo apt install nfs-kernel-server`.
    - Vállalati Linux disztribúcióesetén: `sudo yum install nfs-utils`.

7. Módosítsa a kezelt lemezen lévő azon mappának az engedélyét, amelybe az Azure Blob storage-ból származó adatokat másolta.  Módosítsa az NFS-megosztásként exportálni kívánt mappák engedélyeit.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Rendeljen engedélyeket az ügyfél IP-címekhez az `/etc/exports` NFS-megosztás eléréséhez a fájl szerkesztésével.

    ```bash
    sudo vi /etc/exports
    ```
    
    Írja be a következő sorokat a fájlban a privát felhő minden ESXi állomás IP-címéhez.  Ha több mappához hoz létre megosztásokat, adja hozzá az összes mappát.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportálja az NFS-megosztásokat a `sudo exportfs -a` paranccsal.

10. Indítsa újra az NFS `sudo systemctl restart nfs-kernel-server` kernelkiszolgálót a paranccsal.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>A Linux virtuális gép NFS-megosztásának csatlakoztatása adattárként egy privát felhőbeli vCenter-fürtön, majd adatok másolása

Az NFS-megosztást a Linux virtuális gépről adattárként kell csatlakoztatni a privát felhőbeli vCenter-fürtön. Csatlakoztatása után az adatok másolhatók az NFS-adattárból a magánfelhővSAN-adattárba.

1. Jelentkezzen be a privát felhővCenter-kiszolgálóra.

2. Kattintson a jobb gombbal **az Adatközpont**elemre, válassza a **Tárolás**parancsot, válassza az **Új adattár**lehetőséget, majd kattintson a **Tovább**gombra.

   ![Új adattár hozzáadása](media/databox-migration-add-datastore.png)

3. Az Adattár hozzáadása varázsló első lépésében válassza az **NFS-típust.**

   ![Új adattár hozzáadása - típus](media/databox-migration-add-datastore-type.png)

4. A varázsló **2.** **NFS 3**

   ![Új adattár hozzáadása - NFS-verzió](media/databox-migration-add-datastore-nfs-version.png)

5. A varázsló 3.  Használhatja a Linux virtuális gép IP-címét a kiszolgálóhoz.  A mappa elérési útja `/<folder>/<subfolder>/` formátumban lesz.

   ![Új adattár hozzáadása - NFS-konfiguráció](media/databox-migration-add-datastore-nfs-configuration.png)

6. A varázsló **4.**  Egy fürtben jelölje ki az összes állomást a virtuális gépek áttelepítésének biztosításához.

   ![Új adattár hozzáadása – Állomások kiválasztása](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. A varázsló **5.**

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Virtuális gépek és virtuálisgép-sablonok hozzáadása NFS-adattárból a készlethez

1. A privát felhőbeli vCenter-web felhasználói felületéről nyissa meg a **Storage című területet.**  Válasszon egy Linux virtuális gép NFS adattára, majd válassza **a Fájlok**lehetőséget.

    ![Fájlok kijelölése az NFS-adattárból](media/databox-migration-datastore-select-files.png)

2. Jelöljön ki egy olyan mappát, amely virtuális gépet vagy virtuálisgép-sablont tartalmaz.  A részleteket tartalmazó ablaktáblán jelöljön ki egy .vmx fájlt egy virtuális géphez, vagy egy .vmtx fájlt egy virtuálisgép-sablonhoz.

3. Válassza **a Virtuálisgép regisztrálása** lehetőséget a virtuális gép regisztrálásához a privát felhőbeli vCenteren.

    ![Virtuális gép regisztrálása](media/databox-migration-datastore-register-vm.png)

4. Jelölje ki azt az adatközpontot, mappát és fürt-/erőforráskészletet, ahol regisztrálni szeretné a virtuális gépet.

4. Ismételje meg a 3.

5. Nyissa meg az ISO-fájlokat tartalmazó mappát.  Jelölje ki az ISO-fájlokat, majd a Másolás lehetőséget, **ha** a fájlokat a vSAN-adattár egyik mappájába szeretné másolni.

A virtuális gépek és a virtuális gép sablonok már elérhetőak a privát felhő vCenter. Ezeket a virtuális gépeket át kell helyezni az NFS-adattárból a vSAN-adattárba, mielőtt bekapcsolna. Használhatja a **storage vMotion** beállítást, és válassza ki a vSAN adattár, mint a virtuális gépek cél.

A virtuális gép sablonokat kell klónozni a Linux virtuális gép NFS-adattára a vSAN adattárba.

### <a name="clean-up-your-linux-virtual-machine"></a>A Linux virtuális gép karbantartása

Miután az összes adatot a magánfelhőbe másolta, eltávolíthatja az NFS-adattaboltot a magánfelhőből:

1. Győződjön meg arról, hogy az összes virtuális gépek és sablonok áthelyezése és klónozása a vSAN adattárba.

2. Távolítsa el a készletből az összes virtuálisgép-sablont az NFS-adattárból.

3. Válassza le a Linux virtuálisgép-adattár a privát felhő vCenter.

4. Törölje a virtuális gépet és a felügyelt lemezt az Azure-ból.

5. Ha nem szeretné megtartani a Data Box által átvitt adatokat a tárfiókban, törölje az Azure storage-fiókot.  
    


## <a name="next-steps"></a>További lépések

* További információ a [Data Box ról](../databox/data-box-overview.md).
* További információ a [számítási feladatok magánfelhőbe való áttelepítésének](migrate-workloads.md)különböző lehetőségeiről.
