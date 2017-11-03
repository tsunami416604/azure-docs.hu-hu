---
title: "Hozzon létre és freebsd rendszerű Virtuálisgép-lemezkép feltöltése |} Microsoft Docs"
description: "Megismerheti, hogyan kell létrehozni, majd töltse fel a virtuális merevlemez (VHD), amely tartalmazza a freebsd rendszerű operációs rendszer egy Azure virtuális gép létrehozása"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 0010e01d4333b96696680ec6fbbeee74b17f46a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Hozzon létre és freebsd rendszerű virtuális merevlemez feltöltése az Azure-bA
Ez a cikk bemutatja, hogyan hozhat létre, és töltse fel a virtuális merevlemez (VHD), amely tartalmazza a freebsd rendszerű operációs rendszer. Miután a feltöltés, segítségével azt saját képként (VM) virtuális gép létrehozása az Azure-ban.

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. További információ a Resource Manager modellt használó virtuális merevlemez feltöltése: [Itt](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik-e a következő elemek:

* **Azure-előfizetés**– Ha nincs fiókja, létrehozhat egyet, néhány perc alatt. Ha MSDN-előfizetéssel rendelkezik, tekintse meg [havi Azure-kredit a Visual Studio-előfizetők](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ellenkező esetben megtudhatja, hogyan [hozzon létre egy próbafiókot](https://azure.microsoft.com/pricing/free-trial/).  
* **Az Azure PowerShell eszközök**--az Azure PowerShell modul telepítve legyen, és az Azure-előfizetés használatára konfigurálva. A modul letöltése: [Azure letölti](https://azure.microsoft.com/downloads/). Itt érhető el egy oktatóanyag, amely ismerteti, hogyan telepítse és konfigurálja a modult. Használja a [Azure letölti](https://azure.microsoft.com/downloads/) parancsmag a virtuális merevlemez feltöltéséhez.
* **Freebsd rendszerű operációs rendszer van telepítve, a .vhd-fájllá**--egy támogatott freebsd rendszerű operációs rendszer telepítenie kell egy virtuális merevlemezt. Több különféle eszköz található .vhd fájlok létrehozásához. Például a Hyper-V hálózatvirtualizálási megoldás segítségével például a .vhd fájlt, és az operációs rendszer telepítése. Leírja, hogyan kell telepíteni és használni a Hyper-V, lásd: [telepítése Hyper-V virtuális gép létrehozása és](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. VHD-formátumban a lemezen a Hyper-V kezelője vagy a parancsmag használatával konvertálhatja [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Emellett nincs egy [freebsd rendszerű Hyper-v használatával kapcsolatos útmutató az MSDN-en](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Ez a feladat a következő öt szükséges lépéseket tartalmazza:

## <a name="step-1-prepare-the-image-for-upload"></a>1. lépés: A lemezkép előkészítése feltöltése
A virtuális gépen, amelyre telepítette a freebsd rendszerű operációs rendszer az alábbi eljárások végrehajtása:

1. Engedélyezze a DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. SSH engedélyezése.

    Győződjön meg arról, hogy az SSH-kiszolgálót telepítse és konfigurálja a rendszerindítás elindításához. Alapértelmezés szerint engedélyezve van a freebsd rendszerű lemezről telepítése után. 
3. A soros konzol beállítása.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Telepítse a sudo.

    A legfelső szintű fiók le van tiltva, az Azure-ban. Ez azt jelenti, hogy a magas jogosultságszinttel nem rendelkező parancsok futtatásához emelt szintű jogosultságokkal rendelkező felhasználó sudo használatára kell.

        # pkg install sudo
   
5. Előfeltételek az Azure-ügynököt.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Telepítse az Azure Agent ügynököt.

    Az Azure Agent ügynököt a legújabb kiadásának mindig található [github](https://github.com/Azure/WALinuxAgent/releases). A verzió 2.0.10 hivatalosan freebsd rendszerű 10 támogatja & 10.1, és a verzió 2.1.4 + (beleértve a 2.2.x) hivatalosan támogatja freebsd rendszerű 10.2 és a későbbi kibocsátásokban megtörténik.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0 most használja 2.0.16 példa:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1 most használja 2.1.4 példa:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Azure-ügynök telepítése után célszerű ellenőrizni, hogy fut-e:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. A rendszer kiosztásának megszüntetése.

    A rendszer megtisztítsa tőle, és lehetővé teszi a megfelelő reprovisioning kiosztásának megszüntetése. A következő parancs is törli, az utolsó kiépített felhasználói fiókot és a kapcsolódó adatokat:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Most, állítsa le a virtuális Gépet.

## <a name="step-2-create-a-storage-account-in-azure"></a>2. lépés: Az Azure storage-fiók létrehozása
A .vhd fájl feltöltéséhez, így a virtuális gépek létrehozásához használhatók az Azure-ban storage-fiók szükséges. A klasszikus Azure portál segítségével hozzon létre egy tárfiókot.

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. A parancssávon válassza **új**.
3. Válassza ki **adatszolgáltatások** > **tárolási** > **Gyorslétrehozás**.

    ![Storage-fiók gyors létrehozása](./media/freebsd-create-upload-vhd/Storage-quick-create.png)
4. Töltse ki a mezőket az alábbiak szerint:

   * Az a **URL-cím** mezőbe írja be egy altartomány nevet a tárolási fiók URL-címét. A bejegyzés a 3-24 számokat és kisbetűket tartalmazhat. Ez a név lesz az állomásnév, az Azure Blob Storage tárolóban, Azure Queue storage vagy az előfizetéshez tartozó Azure Table storage-erőforrások megoldására használt URL-cím belül.
   * Az a **hely/Affinitáscsoport** legördülő menüben válassza ki a **helye vagy affinitáscsoportja** a tárfiók. Az affinitáscsoportokban lehetővé teszi a felhőalapú szolgáltatások és a tárolási be ugyanabban az adatközpontban.
   * Az a **replikációs** mezőbe döntsön a **Georedundáns** a tárfiók replikációs. A georeplikáció alapértelmezés szerint be van kapcsolva. Ez a beállítás, hogy a tároló átadja a feladatokat, hogy a hely az elsődleges helyen jelentős hiba esetén replikálja a másodlagos helyre, hogy az adatok. A másodlagos hely automatikusan hozzá van rendelve, és nem módosítható. Ha hatékonyabban szeretné vezérelni jogi követelmények vagy a szervezeti házirend miatt a felhőalapú tároló helyét, kikapcsolhatja a georeplikáció. Azonban vegye figyelembe, hogy később bekapcsolása georeplikáció, fizetnie kell replikálni a másodlagos hely a meglévő adatokat egyszeri adatok adatátviteli díjat. Tárolási szolgáltatások nélkül georeplikáció kedvezményes áron érhető el. A tárfiókok georeplikáció kezelésével kapcsolatos további részletekért itt található: [Azure Storage replikációs](../../../storage/common/storage-redundancy.md).

     ![Írja be a tárolási fiók adatait](./media/freebsd-create-upload-vhd/Storage-create-account.png)
5. Válassza ki **Storage-fiók létrehozása**. A fiók ekkor már megjelenik a **tárolási**.

    ![A tárfiók sikeresen létrehozva](./media/freebsd-create-upload-vhd/Storagenewaccount.png)
6. Ezután hozzon létre egy a feltöltött .vhd fájlokat tároló. A tárfiók nevét, majd válassza ki és **tárolók**.

    ![Tárolási fiók részletei](./media/freebsd-create-upload-vhd/storageaccount_detail.png)
7. Válassza ki **létrehozni egy tárolót**.

    ![Tárolási fiók részletei](./media/freebsd-create-upload-vhd/storageaccount_container.png)
8. Az a **neve** mezőbe írja be a tároló nevét. Ezt követően a a **hozzáférés** legördülő menüben válassza kívánt hozzáférési házirendet milyen típusú.

    ![Tárolónév](./media/freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > Alapértelmezés szerint a tároló privát, és csak a fiók tulajdonosának hozzáférhet. A tárolóban lévő blobok, de nem a tároló tulajdonságainak és a metaadatok nyilvános olvasási hozzáférés engedélyezéséhez használja a **nyilvános Blob** lehetőséget. A tároló és a blobok teljes nyilvános olvasási hozzáférés engedélyezéséhez használja a **nyilvános tárolókban** lehetőséget.
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>3. lépés: Felkészülés a kapcsolat az Azure-bA
A .vhd fájlt tölthet, mielőtt kell a számítógép és az Azure-előfizetés között biztonságos kapcsolatot létrehozni. Ehhez használhatja az Azure Active Directory (Azure AD) vagy a tanúsítvány metódust.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Az Azure AD a módszerhez a .vhd fájl feltöltéséhez
1. Nyissa meg az Azure PowerShell-konzolt.
2. Írja be a következő parancsot:  
    `Add-AzureAccount`

    Ez a parancs megnyit egy bejelentkezési ablakot, ahol jelentkezhet be a munkahelyi vagy iskolai fiókjával.

    ![PowerShell-ablakot](./media/freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure hitelesíti, és menti a hitelesítő adatokat. Majd bezárja az ablakot.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>A tanúsítvány a módszerhez a .vhd fájl feltöltéséhez
1. Nyissa meg az Azure PowerShell-konzolt.
2. Típus: `Get-AzurePublishSettingsFile`.
3. Egy böngészőablakban megnyitja, és megkéri, hogy töltse le a .publishsettings fájlt. Ez a fájl tartalmazza az adatokat és az Azure-előfizetés tanúsítványát.

    ![Böngésző letöltési oldala](./media/freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. Mentse a .publishsettings fájlt.
5. Típus: `Import-AzurePublishSettingsFile <PathToFile>`, ahol `<PathToFile>` .publishsettings fájl teljes elérési útja.

   További információkért lásd: [Ismerkedés az Azure-parancsmagokkal](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   PowerShell konfigurálásával kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

## <a name="step-4-upload-the-vhd-file"></a>4. lépés: A .vhd fájl feltöltése
A .vhd fájlt tölt fel, ha azt bárhol elhelyezheti a Blob-tároló belül. Az alábbiakban néhány fogja használni, amikor a fájl feltöltése feltételek:

* **BlobStorageURL** az URL-cím, a 2. lépésben létrehozott tárfiók.
* **YourImagesFolder** a Blob storage tárolóban van, hol szeretné tárolni a képeket.
* **VHDName** a címke sem, hogy a virtuális merevlemez azonosítására a klasszikus Azure portálon megjelenik.
* **PathToVHDFile** a teljes elérési útja és neve a .vhd fájlt.

Az előző lépésben használt Azure PowerShell-ablakot írja be:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>5. lépés: Virtuális gép létrehozása a feltöltött .vhd fájl
A .vhd fájl feltöltése után hozzáadhatja képként az egyéni lemezképek, hogy az előfizetéshez, majd hozzon létre egy virtuális gépet egyéni lemezképpel listájához.

1. Az előző lépésben használt Azure PowerShell-ablakot írja be:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Linux operációsrendszer-típus használható. A jelenlegi Azure PowerShell-verzió csak a "Linux" vagy "Windows" fogad paraméterként.
   >
   >
2. Miután elvégezte az előző lépést, az új lemezkép szerepel-e, ha úgy dönt, a **képek** lapját, amelyen a klasszikus Azure portálon.  

    ![Kép kiválasztása](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Virtuális gép létrehozása a gyűjteményből. Az új lemezképet már elérhető a **saját lemezképek**.
4. Válassza ki az új lemezképet. A következő halad át a megjelenő utasításokat állítson be egy állomásnevet, a jelszót, az SSH-kulcs és a stb.

    ![Kép: egyéni](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. Miután elvégezte a kiépítés, látni fogja az Azure-beli freebsd rendszerű virtuális gép.

    ![Az azure-ban freebsd rendszerű kép](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
