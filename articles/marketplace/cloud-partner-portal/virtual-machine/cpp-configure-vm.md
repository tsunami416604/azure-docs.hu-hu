---
title: A Microsoft Azure-ban üzemeltetett virtuális gép konfigurálása az Azure Marketplace-en |} A Microsoft Docs
description: Méretezés, frissítése és az Azure-ban üzemeltetett virtuális gép általánosítása a módját ismerteti.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 9cf363bc5f4230306c2fec99eb6287b23e598a4c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57833501"
---
# <a name="configure-the-azure-hosted-vm"></a>Az Azure-ban üzemeltetett virtuális gép konfigurálása

Ez a cikk a méretezés, frissítése és általánosítani a virtuális gépet (VM) az Azure-ban üzemeltetett ismerteti.  Ezeket a lépéseket az Azure Marketplace-ről üzembe helyezni a virtuális gép előkészítése szükségesek.


## <a name="sizing-the-vhds"></a>A virtuális merevlemezeket méretezése

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Ha kiválasztotta a virtuális gépek előre konfigurált operációs rendszert (és szükség esetén további szolgáltatások), majd, már kivételezett egy standard Azure Virtuálisgép-méretet leírtak szerint [VM-termékváltozatok lapon](./cpp-skus-tab.md).  Az ajánlott módszer a megoldás kezdve egy előre konfigurált operációs rendszer.  Azonban ha manuálisan telepíti az operációs rendszer, majd kell méretezze az elsődleges virtuális Merevlemezt a VM-lemezképben található:

- A Windows, az operációs rendszer VHD-fájlként kell létrehoznia egy 127 – 128 GB-os rögzített formátumú VHD-t. 
- Linux rendszeren ez a VHD-fájlként kell létrehoznia egy 30 – 50 GB-os rögzített formátumú VHD-t.

Ha a fizikai méret kisebb, mint 127 – 128 GB-os, a virtuális merevlemez ritka fájlnak kell lennie. Biztosított alap Windows- és SQL Server rendszerképek megfelel ezeknek a követelményeknek, ezért ne módosítsa a formátumot vagy beszerzett virtuális merevlemez méretét. 

Az adatlemezek mérete akár 1 TB is lehet. Amikor eldönti, azok méretét, ne feledje, hogy ügyfelek nem tudják átméretezni a virtuális merevlemezek lemezképen belüli a telepítéshez. Az adatlemezek virtuális rögzített formátumú vhd-fájlként kell létrehoznia. Akkor is ritka fájlnak kell lennie. Adatlemezek kezdetben lehet üres, vagy adatokat tartalmaznak.


## <a name="install-the-most-current-updates"></a>A legújabb frissítéseinek telepítéséhez

Az operációs rendszer virtuális gépek tartalmazzák a legújabb frissítéseket, azok közzétett dátummal záródó részéből. Az operációs rendszer VHD létrehozott közzététele, győződjön meg arról, frissítse az operációs rendszer és az összes telepített szolgáltatásokat az összes a legújabb biztonsági és karbantartási javítások.

A Windows Server 2016-ra, futtassa az alábbi parancsot a **frissítései** parancsot.  Ellenkező esetben a Windows korábbi verziói esetén lásd [frissítés beszerzése a Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows update automatikusan telepíti a legújabb kritikus fontosságú és fontos biztonsági frissítések.

Linux-disztribúciókra vonatkozó frissítéseket gyakran letölthető és telepíthető egy parancssori eszköz vagy egy grafikus segédprogramot.  Például az Ubuntu Linux nyújt a [apt-get paranccsal](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) parancsot és a [kezelő](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) az operációs rendszer frissítésére szolgáló eszköz.


## <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzést

A megoldás rendszerképeket az Azure piactéren, a magas biztonsági szintű fenn kell tartania.  A következő cikkben biztonsági konfigurációk és eljárások segítséget nyújtanak e cél feladatlistát tartalmazza: [Biztonsági javaslatok az Azure Marketplace-rendszerképek](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Ezek a javaslatok némelyike Linux-alapú rendszerképekhez jellemző, de bármely Virtuálisgép-lemezkép legtöbb vonatkoznak. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfigurációs és ütemezett feladatok végrehajtása

Ha további konfigurációs van szükség, az ajánlott módszer az végső módosításokat a virtuális gép után üzembe helyezéskor elvégzik rendszerindításkor futó ütemezett feladatot használni.  Továbbá vegye figyelembe az alábbi javaslatokat:
- Ha egyszer futtatott feladat, javasoljuk, hogy a feladat törli magát sikeresen befejeződik.
- Konfigurációk nem igazolható meghajtóra C, D, mert csak ez a két meghajtó, amely mindig garantáltan létezik. A C meghajtó az operációsrendszer-lemez, és a D meghajtón, az átmeneti helyi lemezt.

Linux-testreszabások kapcsolatos további információkért lásd: [virtuális gépi bővítmények és szolgáltatások Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>A lemezkép általánossá tétele

Az Azure piactéren elérhető összes rendszerkép újrafelhasználható újrafelhasználhatónak kell lennie. Érhet el az újrahasznosíthatóság, az operációs rendszer VHD-t kell *általánosítva*, egy műveletet, amely az összes példány-specifikus azonosítók és szoftverillesztők távolít el egy virtuális Gépet.

### <a name="windows"></a>Windows

Windows operációsrendszer-lemezek a általánosítva vannak a [a sysprep eszköz](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Ha ezt követően frissítse vagy konfigurálja újra az operációs rendszer, újra futtatnia kell a sysprep. 

> [!WARNING]
>  Mert frissítések automatikusan, előfordulhat, hogy futtatja a sysprep futtatása után, akkor kapcsolja ki a virtuális gép, amíg nem telepítik.  A Leállítás elkerüli a virtuális merevlemez operációs rendszerének példány-specifikus módosítása az ezt követő frissítésekben vagy szolgáltatások telepítése.

A sysprep futtatásával kapcsolatos további információkért lásd: [lépéseket általánosítani a virtuális merevlemez](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd)

### <a name="linux"></a>Linux

A következő két lépésből álló folyamat Linux rendszerű virtuális gép általánosítása és ismételt egy különálló virtuális gép üzembe helyezése.  További információkért lásd: [hogyan hozhat létre virtuális gépet vagy virtuális merevlemez lemezképét](../../../virtual-machines/linux/capture-image.md). 

#### <a name="remove-the-azure-linux-agent"></a>Az Azure Linux-ügynök eltávolítása
1.  A Linux virtuális gép egy SSH-ügyfél csatlakozhat.
2.  Az SSH ablakban írja be a következő parancsot: <br/>
    `sudo waagent -deprovision+user`
3.  Típus `y` folytatásához. (Is hozzáadhat a `-force` paramétert az előző parancs elkerülése érdekében ebben a lépésben megerősítő.)
4.  A parancs befejeződése után írja be a `exit` gombra kattintva zárja be az SSH-ügyfél.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>A lemezkép rögzítése
1.  Nyissa meg az Azure Portalon, válassza ki az erőforráscsoportot (RG), és megszüntetéséhez foglal le a virtuális gép.
2.  A virtuális merevlemez már általánosítva van, és létrehozhat egy új virtuális Gépet a virtuális merevlemez használatával.


## <a name="create-one-or-more-copies"></a>Hozzon létre egy vagy több példányban

Másolatokat készít a virtuális gép hasznos gyakran a biztonsági mentés, tesztelés, testre szabott feladatátvétel vagy terheléselosztás, különböző konfigurációjú megoldást kínálnak, és így tovább. A duplikált, és töltse le az elsődleges virtuális Merevlemezt, hogy egy nem felügyelt Klónozás információkért lásd:

- Linux rendszerű virtuális Gépen: [Linux rendszerű VHD letöltése az Azure-ból](../../../virtual-machines/linux/download-vhd.md)
- Windows VM: [Töltse le a virtuális merevlemez Windows Azure-ból](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>További lépések

A virtuális gép konfigurálása után készen áll [virtuális gép üzembe helyezése egy virtuális merevlemezről](./cpp-deploy-vm-vhd.md).
