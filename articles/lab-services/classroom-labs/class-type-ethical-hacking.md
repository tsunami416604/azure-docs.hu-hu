---
title: Etikai hackelési tesztkörnyezet beállítása Azure Lab Servicesokkal | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a labort a Azure Lab Services használatával az etikai hackerek tanításához.
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
ms.openlocfilehash: c92dd48e81512b79fb72cbb751303504d1036594
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71982033"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Tesztkörnyezet beállítása az etikai hackelési osztály tanításához 
Ez a cikk bemutatja, hogyan állíthat be egy olyan osztályt, amely az etikai feltörések kriminalisztikai oldalára koncentrál. A behatolási teszt, amelyet az etikai hackelési Közösség használ, akkor következik be, amikor valaki megpróbál hozzáférni a rendszerhez vagy a hálózathoz a rosszindulatú támadó által kihasználható biztonsági rések bemutatására. 

Egy etikai hackelési osztályban a tanulók modern technikákat tanulnak a biztonsági rések elleni védelemhez. Minden tanuló egy olyan Windows Server Host virtuális gépet kap, amely két beágyazott virtuális géppel rendelkezik – egy **Metaspoiltable** -lemezképpel rendelkező virtuális géppel és egy másik, [Kali Linux](https://www.kali.org/) -lemezképpel rendelkező géppel. A Metasploitable virtuális gép felhasználási célokra szolgál, és a Kali virtuális gép hozzáférést biztosít a kriminalisztikai feladatok végrehajtásához szükséges eszközökhöz.

Ez a cikk két fő szakaszt tartalmaz. Az első szakasz ismerteti, hogyan hozhatja létre az osztályterem labort. A második szakasz ismerteti, hogyan hozhatja létre a sablon gépet, amelyen engedélyezve van a beágyazott virtualizálás, valamint a szükséges eszközök és lemezképek. Ebben az esetben a Metasploitable-lemezkép és a Kali Linux-lemezkép egy olyan gépen, amelyen engedélyezve van a Hyper-V a lemezképek üzemeltetéséhez.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja
A tesztkörnyezet beállításához Azure-előfizetésre van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services, vagy használhat meglévő fiókot is. Új Labor-fiók létrehozásához tekintse meg a következő oktatóanyagot: [Oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).

[Ezt az oktatóanyagot](tutorial-setup-classroom-lab.md) követve hozzon létre egy új labort, majd alkalmazza a következő beállításokat:

| Virtuális gép mérete | Image |
| -------------------- | ----- | 
| Közepes (beágyazott virtualizálás) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Sablon számítógép 

A sablon létrehozása után indítsa el a gépet, és kapcsolódjon ahhoz, hogy elvégezze a következő három fő feladatot. 
 
1. Állítsa be a gépet a beágyazott virtualizálás számára. Lehetővé teszi az összes megfelelő Windows-funkció (például a Hyper-V) használatát, és beállítja a Hyper-V-lemezképek hálózatkezelését, hogy képes legyen kommunikálni egymással és az internettel.
2. A [Kali](https://www.kali.org/) Linux-rendszerkép beállítása. A Kali egy Linux-disztribúció, amely a behatolás tesztelésére és a biztonsági naplózásra szolgáló eszközöket tartalmaz.
3. Állítsa be a Metasploitable-rendszerképet. Ebben a példában a rendszer a [Metasploitable3](https://github.com/rapid7/metasploitable3) rendszerképet fogja használni. Ez a rendszerkép szándékosan biztonsági réseket hoz létre.

### <a name="prepare-template-machine-for-nested-virtualization"></a>Sablon számítógépének előkészítése beágyazott virtualizálás esetén
A [cikk](how-to-enable-nested-virtualization-template-vm.md) utasításait követve készítse elő a sablon virtuális gépet a beágyazott virtualizálás számára. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Beágyazott virtuális gép beállítása a Kali Linux-lemezképpel
A Kali egy Linux-disztribúció, amely a behatolás tesztelésére és a biztonsági naplózásra szolgáló eszközöket tartalmaz.

1. Rendszerkép letöltése [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)fájlból.  
    1. Töltse le a következőt: **Kali Linux Hyper-v 64 bit** a Hyper-v-hez.
    1. Bontsa ki a. 7z fájlt.  Ha még nem rendelkezik 7 zip-vel, töltse le a [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)címről. Jegyezze meg a kibontott mappa helyét, mert később szüksége lesz rá.
2. Nyissa meg a **Hyper-V kezelőjét** a felügyeleti eszközök közül.
1. Válassza a **művelet**, majd a **virtuális gép importálása**lehetőséget. 
1. A **virtuális gép importálása** varázsló **mappa megkeresése** lapján válassza ki a Kali Linux-rendszerképet tartalmazó kibontott mappa helyét.

    ![Mappa megkeresése párbeszédpanel](../media/class-type-ethical-hacking/locate-folder.png)
1. A **virtuális gép kiválasztása** lapon válassza ki a Kali Linux-rendszerképet.  Ebben az esetben a rendszerkép a **Kali-Linux-2019,3-HyperV**.

    ![Kali-rendszerkép kiválasztása](../media/class-type-ethical-hacking/select-kali-image.png)
1.  Az **importálási típus kiválasztása** lapon válassza **a virtuális gép másolása (új egyedi azonosító létrehozása)** lehetőséget.

    ![Importálási típus kiválasztása](../media/class-type-ethical-hacking/choose-import-type.png)
1. Fogadja el az alapértelmezett beállításokat a **virtuális gépek fájljainak kiválasztásához** , majd válassza a mappák lehetőséget a **virtuális merevlemezek lapjainak tárolásához** , majd kattintson a **tovább**gombra.
1. A **hálózat összekapcsolása** lapon válassza a korábban létrehozott **LabServicesSwitch** a **beágyazott virtualizáció előkészítése a sablonban** című szakaszt, majd kattintson a **tovább**gombra.

    ![Hálózati kapcsolat lap](../media/class-type-ethical-hacking/connect-network.png)
1. Válassza a **Befejezés** lehetőséget az **Összefoglalás** lapon. Várjon, amíg a másolás és az importálás művelet be nem fejeződik. A Kali Linux rendszerű virtuális gép mostantól elérhető lesz a Hyper-V-ben.
1. A **Hyper-V kezelőjében**válassza a **művelet**@no__t – 2**Indítás**lehetőséget, majd válassza a **művelet** -> **Kapcsolódás** lehetőséget a virtuális géphez való kapcsolódáshoz.  
12. Az alapértelmezett Felhasználónév `root`, a jelszó pedig `toor`. 

    > [!NOTE]
    > Ha fel kell oldania a rendszerkép zárolását, nyomja le a CTRL billentyűt, és húzza felfelé az egeret.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Beágyazott virtuális gép beállítása Metasploitable-lemezképpel  
A Rapid7 Metasploitable-rendszerkép a biztonsági rések mellett konfigurált rendszerkép. Ezt a rendszerképet fogja használni a problémák teszteléséhez és kereséséhez. Az alábbi utasítások bemutatják, hogyan használható egy előre létrehozott Metasploitable-rendszerkép. Ha azonban a Metasploitable rendszerkép újabb verziójára van szükség, tekintse meg a következőt: [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. Navigáljon a [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)értékre. Töltse ki az űrlapot a rendszerkép letöltéséhez, és kattintson a **Submit (Küldés** ) gombra.
1. Válassza a **Letöltés Metasploitable most** gombot.
1. A zip-fájl letöltésekor bontsa ki a zip-fájlt, és jegyezze fel a helyet.
1. Alakítsa át a kinyert VMDK-fájlt egy vhdx-fájlba, hogy a a Hyper-V használatával is használható legyen. Ehhez nyissa meg a PowerShellt rendszergazdai jogosultságokkal, és navigáljon arra a mappára, ahol a VMDK fájl található, és kövesse az alábbi utasításokat:
    1. Töltse le a [Microsoft Virtual Machine Convertert](https://www.microsoft.com/download/details.aspx?id=42497), és amikor a rendszer kéri, futtassa a mvmc_setup. msi fájlt.
    1. Importálja a PowerShell modult.  A modul telepítési alapértelmezett helye: C:\Program Files\Microsoft Virtual Machine Converter \

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. Alakítsa át a VMDK egy olyan VHD-fájlba, amelyet a Hyper-V használhat. A művelet több percet is igénybe vehet.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. Másolja az újonnan létrehozott metasploitable. vhdx C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\. 
1. Hozzon létre egy új Hyper-V virtuális gépet.
    1. Nyissa meg a **Hyper-V kezelőjét**.
    1. Válassza a **művelet** -> **új** -> **virtuális gép**lehetőséget.
    1. Az **új virtuális gép varázsló**alapismeretek **lapján kattintson a** **tovább**gombra.
    1. A **név és hely megadása** lapon adja meg a **Metasploitable** nevet a **név**mezőben, majd kattintson a **Tovább gombra**.

        ![Új virtuálisgép-rendszerkép varázsló](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. A **generáció megadása** lapon fogadja el az alapértelmezett értékeket, majd kattintson a **tovább**gombra.
    1. A **memória kiosztása** lapon adja meg a **512 MB** értéket az **indítási memória**számára, majd kattintson a **Tovább gombra**. 

        ![Memória kiosztása lap](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. A **hálózat konfigurálása** lapon hagyja a kapcsolatot **nem csatlakoztatottként**. A hálózati adaptert később kell beállítania.
    1. A **virtuális merevlemez összekapcsolása** lapon válassza a **meglévő virtuális merevlemez használata**lehetőséget. Keresse meg az előző lépésben létrehozott **metasploitable. vhdx** fájl helyét, és kattintson a **tovább**gombra. 

        ![Virtuális hálózati lemez összekapcsolásának lapja](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Az **új virtuális gép varázsló befejezése** lapon kattintson a **Befejezés gombra**.
    1. A virtuális gép létrehozása után válassza ki azt a Hyper-V kezelőjében. Ne kapcsolja be még a gépet.  
    1. Válassza a **művelet** -> **Beállítások**lehetőséget.
    1. A **Metasploitable beállításai** párbeszédpanelen válassza a **hardver hozzáadása**elemet. 
    1. Válassza az **örökölt hálózati adapter**lehetőséget, majd kattintson a **Hozzáadás**gombra.

        ![Hálózati adapter lap](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. Az **örökölt hálózati adapter** lapon válassza a **LabServicesSwitch** lehetőséget a **virtuális kapcsoló** beállításnál, majd kattintson az **OK gombra**. A rendszer létrehozta a LabServicesSwitch a Hyper-V sablon-számítógépének előkészítésekor a **beágyazott virtualizációs sablon előkészítése sablonjában** .

        ![Örökölt hálózati adapter lap](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. A Metasploitable-rendszerkép most már használatra kész. A **Hyper-V kezelőjében**válassza a **művelet**@no__t – 2**Indítás**lehetőséget, majd válassza a **művelet** -> **Kapcsolódás** lehetőséget a virtuális géphez való kapcsolódáshoz.  Az alapértelmezett Felhasználónév a **msfadmin** , a jelszó pedig **msfadmin**. 


A sablon frissítve lett, és a rendszerképek szükségesek egy etikai hackelési bevezetési tesztelési osztályhoz, amely egy olyan eszközzel rendelkezik, amely a behatolási teszteket és egy másik, biztonsági réseket felderítő képet tartalmaz. A sablon képe mostantól közzétehető az osztályban. Kattintson a **Közzététel** gombra a sablon lapon a sablonnak a laborba való közzétételéhez.
  

## <a name="cost"></a>Költség  
Ha a labor költségeit szeretné megbecsülni, a következő példát használhatja: 
 
Egy 25 tanulós osztály esetében, amely 20 órányi ütemezett időpontot és 10 órányi munkafeladatot vagy hozzárendelést használ, a labor ára a következő lesz: 25 tanuló * (20 + 10) óra * 55 labor egység * 0,01 USD/óra = 412,50. A díjszabással kapcsolatos további információkért tekintse meg a [Azure Lab Services díjszabását](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés
Ez a cikk végigvezeti a tesztkörnyezet etikai feltörési osztályhoz való létrehozásának lépésein. Ez magában foglalja a beágyazott virtualizálás beállításának lépéseit két virtuális gép létrehozásához a gazda virtuális gépen az áthatoló tesztelés érdekében.

## <a name="next-steps"></a>További lépések
A következő lépések közösek a laborok beállításához:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](tutorial-setup-classroom-lab.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail-regisztráció a tanulók számára](tutorial-setup-classroom-lab.md#send-an-email-with-the-registration-link). 

