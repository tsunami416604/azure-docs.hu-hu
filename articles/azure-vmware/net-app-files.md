---
title: NetApp-fájlok az Azure VMware-megoldáshoz
description: Az Azure VMware virtuális gépekkel való Azure NetApp Files használatával áttelepítheti és szinkronizálhatja a helyszíni kiszolgálókon, az Azure VMware-alapú virtuális gépeken és a felhőalapú infrastruktúrán keresztül.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 993a67f82d5af971a4c4a0010bd9d19e2a3113b2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573922"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>NetApp-fájlok az Azure VMware-megoldáshoz

Ebben a cikkben végigvezeti a Azure NetApp Files integrálásának lépésein az Azure VMware megoldás-alapú számítási feladatokkal. A [Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) egy Azure-szolgáltatás, amellyel a felhőben nem módosítható a vállalati fájlok számítási feladatainak áttelepítése és futtatása. Lehetővé teszi az adatáttelepítést a helyszíni és a Felhőbeli infrastruktúrák között. A gyors, fokozott biztonságú adatszinkronizálás egyszerűbbé teszi a migrációs és DevOps forgatókönyveket olyan képességekkel, mint a pillanatnyi pillanatkép, a visszaállítás és a Active Directory integráció.

## <a name="topology"></a>Topológia

Ebben a forgatókönyvben a Azure NetApp Files készlet megosztásának teszteléséhez és ellenőrzéséhez Azure NetApp Files az Azure-ban, a kapacitás-készlettel, a mennyiségi készlettel és az alhálózattal van konfigurálva. Az NFS protokollt használtuk. A Azure NetApp Files és az Azure VMware megoldás is ugyanabban az Azure-régióban jön létre, mint az USA keleti régiója. Az Azure VMware-megoldáshoz való kapcsolódás az Azure ExpressRoute keresztül történik.

![NetApp-fájlok az Azure VMware megoldás-topológiához.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Előfeltételek 

> [!div class="checklist"]
> * Azure-előfizetés Azure NetApp Files
> * Az Azure NetApp-fájlhoz tartozó alhálózat
> * Linux virtuális gép Azure VMware-megoldással
> * Windowsos virtuális gép Azure VMware-megoldással

## <a name="verify-configuration-of-azure-netapp-files"></a>Azure NetApp Files konfigurációjának ellenőrzése 

Először ellenőrizze az Azure-ban a prémium szintű lemezen létrehozott Azure NetApp Files konfigurációját.

1. A Azure Portal **tároló**területén válassza a **Azure NetApp Files**lehetőséget. A konfigurált Azure NetApp Files listája jelenik meg.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Azure NetApp Files listája."::: 

2. Ha a NetApp-fiókot választja, különböző beállításokat tekinthet meg. Ha például a **contoso-anf2**lehetőséget választja, megjelenik a beállításai. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Azure NetApp Files listája."::: 

3. Válassza a **Kapacitási készletek** elemet a konfigurált készlet ellenőrzéséhez. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Azure NetApp Files listája.":::

    Láthatja, hogy a Storage-készlet 4 TiB-ként van konfigurálva prémium lemezzel.

4. Válassza a **kötetek** lehetőséget (lásd a 2. lépés képernyőképét) a kapacitási készletben létrehozott kötetek megtekintéséhez.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Azure NetApp Files listája.":::

5. Válasszon ki egy kötetet a konfiguráció megtekintéséhez.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Azure NetApp Files listája.":::

    Láthatja, hogy a anfpool mennyiségi készlet a 200 GiB használatával lett létrehozva NetApp-fájlmegosztásként. Egy magánhálózati IP virtuális hálózat lett létrehozva a Azure NetApp Files számára, amely lehetővé teszi az NFS-útvonal csatlakoztatását az Azure VMware megoldás virtuális gépei számára. 

## <a name="protocols-supported-by-azure-netapp-files"></a>A Azure NetApp Files által támogatott protokollok  

A Azure NetApp Files támogatja a Server Message Block (SMB) és a hálózati fájlrendszer (NFS) megosztásának leképezését. 

- **SMB-megosztás**: az SMB-kötetek telepítéséhez először létre kell hoznia egy Active Directory-kapcsolatokat. A sikeres kapcsolódáshoz a megadott tartományvezérlők számára a Azure NetApp Files delegált alhálózatának elérhetőnek kell lennie. Ha Active Directory a Azure NetApp Files-fiókban van konfigurálva, akkor az SMB-kötetek létrehozásakor választható elemként jelenik meg. 

- **NFS-megosztás**: Azure NetApp Files hozzájárul a kötetek NFS-vel való létrehozásához (NFSv3 és nfsv 4.1), SMBv3 vagy kettős protokoll (NFSV3 és SMB) használatával. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Az NFS parancssori használatával csatlakoztatható Linux-kiszolgálóhoz.

## <a name="create-azure-netapp-files"></a>Azure NetApp Files létrehozása 

1. Jelentkezzen be az [Azure portálra](https://rc.portal.azure.com/#home). Az Azure-szolgáltatások területen válassza a **Azure NetApp Files**lehetőséget. 

2. Új Azure NetApp Files kötet létrehozásához válassza a **+ Hozzáadás** lehetőséget. 

3. Adja meg a kötelező mezőket (név, előfizetés, erőforráscsoport és hely), majd válassza a **Létrehozás**lehetőséget. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Kapacitás-készletek hozzáadása Azure NetApp Files 

1. A Azure Portal válassza a **Azure NetApp Files**lehetőséget, majd válassza a **Kapacitási készletek** és a **+ készlet hozzáadása**lehetőséget. 

2. Adja meg a kötet-készlet neve, a szolgáltatási szint és a lemez mérete (FQDN) vagy az IP-cím és a súlyozás kötelező részleteit. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Azure NetApp Files listája.":::

3. Kötetek létrehozásához a kapacitás készletben válassza a **kötetek** elemet a keresés ablaktáblán, és válassza a **+ kötet hozzáadása**elemet. 
 
4. Töltse ki a kötelező mezőket az alábbi képernyőképen látható módon.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Azure NetApp Files listája.":::

5. A következő lapon válassza ki a protokoll típusú megosztást. Válassza ki a verziókat, ha az NFS-megosztást használja, és válassza ki a Active Directory tartományt, ha az SMB-megosztás.  

6. Ha NFS-megosztást használ, adja hozzá azt a forrás IP-címet, ahol a protokoll típusú megosztás elérhető lesz. Válassza az **Áttekintés + létrehozás** lehetőséget. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Azure NetApp Files listája.":::
 
    A Azure Portal Azure NetApp Files alatt az NFS-megosztás készen áll a használatra.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Azure NetApp Files listája.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>NFS-fájlmegosztás csatlakoztatása Azure VMware-Megoldásbeli virtuális gépekhez

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>NFS-fájlmegosztás csatlakoztatása Azure VMware-megoldás Windows-alapú virtuális gépen

- Nyisson meg egy parancssort, és futtassa a parancsot az NFS-fájlmegosztás leképezéséhez. A következő Képernyőképek a Windows rendszerű virtuális gépen leképezett megosztási meghajtót mutatják be az Azure VMware megoldásban.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Azure NetApp Files listája.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Azure NetApp Files listája.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>NFS-fájlmegosztás csatlakoztatása Azure VMware megoldás Linux rendszerű virtuális gépen

#### <a name="setting-up-your-azure-instance"></a>Az Azure-példány beállítása

1. A Azure Portal társítsa az Azure-példányt egy olyan alhálózattal, amely a kötethez tartozó azonos virtuális hálózaton van meghatározva.

    > [!NOTE]
    > Az alhálózatnak szüksége van egy hálózati biztonsági csoportra vonatkozó szabályra, amely engedélyezi a forgalmat az NFS-portokon (2049, 111), UDP és TCP.

2. Nyisson meg egy SSH-ügyfelet, és csatlakozzon az Azure-példányához. További információ: az [ssh-kulcsok használata az Azure](../virtual-machines/linux/ssh-from-windows.md)-ban a Windowsban.

3. Telepítse az NFS-ügyfelet az Azure-példányon:
   - Red Hat Enterprise Linux vagy SUSE Linux-példányon: `sudo yum install -y nfs-utils`
   - Ubuntu vagy Debian-példányon: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>A fájlrendszer csatlakoztatása

1. Hozzon létre egy új könyvtárat az Azure-példányon: `sudo mkdir ANFPOOL`

2. Válassza ki a csatlakoztatási cél IP-címét.

3. A fájlrendszer csatlakoztatása: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Azure NetApp Files listája.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Active Directory-kapcsolatok létrehozása SMB-megosztáshoz

1. Válasszon egy NetApp-fiókot a Azure Portal.

2. A Azure NetApp Files területen válassza a **Active Directory kapcsolatok**elemet.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Azure NetApp Files listája."::: 

3. Válassza a **Csatlakozás** lehetőséget az SMB-megosztás Active Directoryhoz való csatlakoztatásához. Az alábbi képernyőképen az SMB-megosztás tartományi adatai láthatók.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Azure NetApp Files listája."::: 

    Az Azure SMB-fájlmegosztás használatra kész.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Azure NetApp Files listája."::: 

4. Képezze le az SMB-megosztást a Windows Azure VMware megoldás virtuális gépén. Használja az SMB-csatlakoztatási útvonalat az előző képernyőképen látható módon. További információ: [hálózati meghajtó leképezése a Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive) rendszerben

## <a name="next-steps"></a>További lépések
- További információ a [Azure NetApp Files tárolási hierarchiáján](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- Lásd: [Az Azure VMware megoldás virtuális gépek életciklus-kezelése](lifecycle-management-of-azure-vmware-solution-vms.md)
- Lásd: [Az Azure VMware-megoldás integrálása egy központba és küllős architektúrába](concepts-hub-and-spoke.md)
