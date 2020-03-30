---
title: Etikus feltörési labor beállítása az Azure Lab Services szolgáltatással | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthat be egy labort az Azure Lab Services használatával az etikus hackelés tanításához.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133182"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Hozzon létre egy labort, hogy etikus hackelési osztályt tanítson 
Ez a cikk bemutatja, hogyan kell létrehozni egy osztályt, amely elsősorban a törvényszéki oldalán etikai hacker. A behatolási tesztelés, amelyet az etikus feltörési közösség alkalmaz, akkor történik, amikor valaki megpróbál hozzáférni a rendszerhez vagy a hálózathoz, hogy bemutassa a rosszindulatú támadó által kihasználható biztonsági réseket. 

Egy etikus hackelési osztályban a diákok modern technikákat tanulhatnak a sebezhetőségek elleni védekezésre. Minden diák kap egy Windows Server-gazdavirtuális gépet, amely két beágyazott virtuális géppel rendelkezik – egy [metasploitable3](https://github.com/rapid7/metasploitable3) lemezképpel rendelkező virtuális gép, egy másik pedig [Kali Linux-lemezképpel](https://www.kali.org/) rendelkező gép. A metasploitable virtuális gép használható kihasználó célokra, és Kali virtuális gép hozzáférést biztosít a szükséges eszközöket a törvényszéki feladatok végrehajtásához.

Ez a cikk két fő részből rendelkezik. Az első rész ismerteti, hogyan lehet létrehozni az osztályteremben laborban. A második szakasz ismerteti, hogyan lehet létrehozni a sablongép beágyazott virtualizáció engedélyezve van, és a szükséges eszközökkel és képekkel. Ebben az esetben egy metaploitable lemezkép és egy Kali Linux-lemezkép egy olyan gépen, amelyen a Hyper-V engedélyezve van a képek üzemeltetéséhez.

## <a name="lab-configuration"></a>Labor konfigurációja
A labor beállításához azure-előfizetésre van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. Miután megkapja az Azure-előfizetést, létrehozhat egy új laborfiókot az Azure Lab Servicesben, vagy használhat egy meglévő fiókot. Tekintse meg a következő oktatóanyagot az új laborfiók [létrehozásáról: Bemutató a laborfiók beállításához](tutorial-setup-lab-account.md).

Az [oktatóanyag követéséhez](tutorial-setup-classroom-lab.md) hozzon létre egy új tesztkörnyezetet, majd alkalmazza a következő beállításokat:

| Virtuális gép mérete | Kép |
| -------------------- | ----- | 
| Közepes (beágyazott virtualizáció) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Sablongép 

A sablongép létrehozása után indítsa el a gépet, és csatlakozzon hozzá a következő három fő feladat elvégzéséhez. 
 
1. Állítsa be a gépet beágyazott virtualizációhoz. Lehetővé teszi az összes megfelelő Windows-funkciót, például a Hyper-V-t, és beállítja a Hyper-V-lemezképek hálózatát, hogy képesek legyenek kommunikálni egymással és az internettel.
2. Állítsa be a [Kali](https://www.kali.org/) Linux-lemezképet. A Kali egy Linux disztribúció, amely a penetráció teszteléséhez és a biztonsági auditáláshoz szükséges eszközöket tartalmaz.
3. Állítsa be a metaszploitable képet. Ebben a példában a [Metasploitable3](https://github.com/rapid7/metasploitable3) kép lesz használva. Ez a lemezkép szándékosan biztonsági rések et szeretne használni.

A szkript, amely automatizálja a fent vázolt feladatok elérhető [Lab Services Etikai Hacking Scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Sablongép előkészítése beágyazott virtualizációhoz
Ebben a [cikkben](how-to-enable-nested-virtualization-template-vm.md) található utasításokat követve készítse elő a sablon virtuális gép beágyazott virtualizáció. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Beágyazott virtuális gép beállítása Kali Linux-lemezképpel
A Kali egy Linux disztribúció, amely a penetráció teszteléséhez és a biztonsági auditáláshoz szükséges eszközöket tartalmaz.

1. Kép letöltése a alkalmazásból. [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)  
    1. Töltse le a Kali Linux Hyper-V 64 Bit a Hyper-V.Download the **Kali Linux Hyper-V 64 Bit** for Hyper-V.
    1. Bontsa ki a .7z fájlt.  Ha még nem rendelkezik 7 zip, [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)töltse le a . Ne feledje a kibontott mappa helyét, mivel később szüksége lesz rá.
2. Nyissa meg a **Hyper-V managert** a felügyeleti eszközökből.
1. Válassza **a Művelet**lehetőséget, majd a Virtuális gép **importálása**lehetőséget. 
1. A **Virtuális gép importálása** varázsló **Mappa felkutatása** lapján válassza ki a Kali Linux-lemezképet tartalmazó kibontott mappa helyét.

    ![Mappa megjelenítése párbeszédpanel](../media/class-type-ethical-hacking/locate-folder.png)
1. A **Virtuális gép kiválasztása** lapon válassza ki a Kali Linux-lemezképet.  Ebben az esetben a kép **kali-linux-2019.3-hyperv**.

    ![Kali kép kijelölése](../media/class-type-ethical-hacking/select-kali-image.png)
1.  A **Típus importálása lehetőséget lapon** válassza **a Virtuális gép másolása (új egyedi azonosító létrehozása)** lehetőséget.

    ![Importálástípus kiválasztása](../media/class-type-ethical-hacking/choose-import-type.png)
1. Fogadja el a **Virtuálisgép-fájlok mappák kiválasztása** és a Virtuális merevlemezek tárolására szolgáló mappák kiválasztása alapértelmezett **beállításait,** majd válassza a **Tovább**gombot.
1. A **Hálózat csatlakoztatása** lapon válassza a cikk **Beágyazott virtualizációhoz** sablon előkészítése című szakaszában korábban létrehozott **LabServicesSwitch** lehetőséget, majd kattintson a **Tovább**gombra.

    ![Hálózati lap csatlakoztatása](../media/class-type-ethical-hacking/connect-network.png)
1. Az **Összegzés** lapon válassza a **Befejezés** gombot. Várjon, amíg a másolási és importálási műveletek befejeződnek. A Kali Linux virtuális gép mostantól elérhető lesz a Hyper-V-ben.
1. A **Hyper-V Manager programban**válassza a **Művelet** -> **indítása**lehetőséget, majd a **Művelet** -> **csatlakozni** lehetőséget választva csatlakozzon a virtuális géphez.  
12. Az alapértelmezett felhasználónév, `root` és `toor`a jelszó . 

    > [!NOTE]
    > Ha fel kell oldania a kép zárolását, nyomja le a CTRL billentyűt, és húzza felfelé az egeret.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Beágyazott virtuális gép beállítása metaploitable image-el  
A Rapid7 metaszploitképes lemezkép egy szándékosan biztonsági résekkel konfigurált lemezkép. Ezt a képet fogja használni a problémák tesztelésére és keresésére. Az alábbi utasítások bemutatják, hogyan kell használni egy előre létrehozott metasploitable képet. Ha azonban a metasploitable kép újabb verziójára [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)van szükség, lásd: .

1. Keresse [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)meg a it. Töltse ki az űrlapot a kép letöltéséhez, és válassza a **Küldés** gombot.
1. Válassza a **Metasploitable Now letöltése** gombot.
1. A zip-fájl letöltésekor bontsa ki a zip fájlt, és jegyezze meg a helyet.
1. Konvertálja a kibontott vmdk fájlt vhdx fájllá, hogy a Hyper-V-vel is használhassa. Ehhez nyissa meg a Rendszergazdai jogosultságokkal rendelkező PowerShellt, és keresse meg azt a mappát, amelyben a vmdk-fájl található, és kövesse az alábbi utasításokat:
    1. Töltse le a [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497)programot , és amikor a rendszer kéri, futtassa a mvmc_setup.msi fájlt.
    1. Importálja a PowerShell modult.  A modul telepítésének alapértelmezett helye a C:\Program Files\Microsoft Virtual Machine Converter\

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Konvertálja a vmdk egy vhd fájlt, amely a Hyper-V által használható. Ez a művelet több percet is igénybe vehet.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Másolja az újonnan létrehozott metasploitable.vhdx fájlba a C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\.c.-be. 
1. Hozzon létre egy új Hyper-V virtuális gépet.
    1. Nyissa meg **a Hyper-V Manager t.**
    1. Válassza **a Művelet** -> **új** -> **virtuális gép lehetőséget.**
    1. Az Új virtuális gép **varázsló** **Kezdés előtti** lapján kattintson a **Tovább**gombra.
    1. A **Név és hely megadása** lapon írja be a **Metasploitable parancsot** a **névhez,** és válassza a **Tovább**gombot.

        ![Új virtuálisgép-lemezkép varázsló](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. A **Létrehozás megadása** lapon fogadja el az alapértelmezett értékeket, és válassza a **Tovább**gombot.
    1. A **Memória hozzárendelése** lapon írja be az **512 MB** értéket az **indítási memóriához,** és válassza a **Tovább**gombot. 

        ![Memórialap hozzárendelése](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. A **Hálózat konfigurálása** lapon hagyja a kapcsolatot **nem csatlakoztatottként.** A hálózati adaptert később kell beállítani.
    1. A **Virtuális merevlemez csatlakoztatása** lapon válassza **a Meglévő virtuális merevlemez használata**lehetőséget. Tallózással keresse meg az előző lépésben létrehozott **metasploitable.vhdx** fájl helyét, és válassza a **Tovább**gombot. 

        ![Virtuális hálózati lemez csatlakoztatása lap](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Az **Új virtuális gép varázsló befejezése** lapon válassza a **Befejezés**gombot.
    1. A virtuális gép létrehozása után válassza ki azt a Hyper-V Managerben. Még ne kapcsold be a gépet.  
    1. Válassza **a Műveletbeállítások** -> **lehetőséget.**
    1. A **Metasploitable beállításai** párbeszédpanelen válassza a **Hardver hozzáadása lehetőséget.** 
    1. Válassza **az Örökölt hálózati adapter**lehetőséget, majd a **Hozzáadás**lehetőséget.

        ![Hálózati adapter lap](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Az **Örökölt hálózati adapter** lapon válassza a **LabServicesSwitch** lehetőséget a **Virtuális kapcsoló** beállításhoz, majd az **OK**gombra. A LabServicesSwitch a Hyper-V sablongépének előkészítésekor jött létre a **Beágyazott virtualizáláselőkészítése** szakaszban.

        ![Örökölt hálózati adapter lap](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. A metasploitable kép most már használatra kész. A **Hyper-V Manager programban**válassza a **Művelet** -> **indítása**lehetőséget, majd a **Művelet** -> **csatlakozni** lehetőséget választva csatlakozzon a virtuális géphez.  Az alapértelmezett felhasználónév **msfadmin,** a jelszó pedig **msfadmin**. 


A sablon most frissül, és a képek szükségesek egy etikai hacker penetráció tesztelés imázs, egy kép eszközökkel, hogy ezt a penetrációs vizsgálat és egy másik kép biztonsági réseket felfedezni. A sablonkép most már közzétehető az osztályban. A sablon közzétételéhez kattintson **a** közzététel gombra a sablon tesztkörnyezetben való közzétételéhez.
  

## <a name="cost"></a>Költségek  
Ha meg szeretné becsülni a labor költségét, a következő példát használhatja: 
 
Egy 25 fős osztály esetében, akik 20 óra ütemezett óra időt és 10 óra kvótát kapnak a házi feladatra vagy a feladatokra, a labor ára a következő lenne: 

25 diák * (20 + 10) óra * 55 Labor egység * 0.01 USD / óra = 412.50 USD. 

A díjszabásról az [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/)című témakörben talál további információt.

## <a name="conclusion"></a>Összegzés
Ez a cikk végigment a lépéseket, hogy hozzon létre egy laboretikai hacker osztály. Ez magában foglalja a beágyazott virtualizáció beállításának lépéseit két virtuális gép létrehozásához a gazdagép virtuális gépén belül a behatoló teszteléshez.

## <a name="next-steps"></a>További lépések
A következő lépések gyakoriak a tesztkörnyezet beállításában:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail regisztrációs linkek a diákok](how-to-configure-student-usage.md#send-invitations-to-users). 

