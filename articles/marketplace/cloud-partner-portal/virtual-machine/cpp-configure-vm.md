---
title: Konfigurálja a Microsoft Azure által üzemeltetett virtuális gép az Azure Piactérre
description: Bemutatja, hogyan méretezheti, frissítheti és általánosíthatja az Azure-ban üzemeltetett virtuális gépeket.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: b0ed430098203c5c1a0d00eb7bf17da1be0000cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278092"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurálja az Azure által üzemeltetett virtuális gép

Ez a cikk bemutatja, hogyan méretezheti, frissítheti és általánosíthatja az Azure-ban üzemeltetett virtuális gépeket (VM).This article explain how to size, update, and generalize a virtual machine (VM) hosted on Azure.  Ezek a lépések szükségesek a virtuális gép az Azure Marketplace-ről való üzembe helyezéséhez.


## <a name="sizing-the-vhds"></a>A VHD-k méretezése

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Ha kiválasztotta az operációs rendszerrel (és opcionálisan további szolgáltatásokkal) előre konfigurált virtuális gépek egyikét, akkor már kiválasztott egy szabványos Azure virtuális gépméretet, a [virtuális gépek skus lapján leírtak szerint.](./cpp-skus-tab.md)  A megoldás indítása egy előre konfigurált operációs rendszer az ajánlott megközelítés.  Ha azonban manuálisan telepít iszit, akkor az elsődleges virtuális merevlemezt a virtuális gép lemezképében kell méreteznie:

- A Windows, az operációs rendszer Virtuális merevlemez kell létrehozni, mint egy 127-128 GB-os fix formátumú Virtuális merevlemez. 
- Linux esetén ezt a virtuális merevlemezt 30–50 GB-os rögzített formátumú virtuális merevlemezként kell létrehozni.

Ha a fizikai méret kisebb, mint 127-128 GB, a virtuális merevlemez ritkák. A megadott alap Windows- és SQL Server-lemezképek már megfelelnek ezeknek a követelményeknek, ezért ne módosítsa a kapott virtuális merevlemez formátumát vagy méretét. 

Az adatlemezek akár 1 TB méretűek is lehetnek. A méretük meghatározásakor ne feledje, hogy az ügyfelek nem méretezhetik át a virtuális központi gépeket egy lemezképen belül a telepítés időpontjában. A VHD-k adatlemezét rögzített formátumú VHD-ként kell létrehozni. Azt is meg kell ritkák. Az adatlemezek kezdetben üresek vagy tartalmazhatnak adatokat.


## <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

Az operációs rendszer virtuális gépeinek alaplemezei tartalmazzák a közzétett dátumig a legújabb frissítéseket. A létrehozott operációs rendszer virtuális merevlemezének közzététele előtt győződjön meg arról, hogy frissíti az operációs rendszert és az összes telepített szolgáltatást a legújabb biztonsági és karbantartási javításokkal.

Windows Server 2016 esetén futtassa a **Frissítések keresése** parancsot.  Ellenkező esetben a Windows régebbi verzióiról a [Frissítés beszereznie a Windows Update szolgáltatásban](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  A Windows update automatikusan telepíti a legújabb kritikus és fontos biztonsági frissítéseket.

Linux disztribúciók esetén a frissítéseket általában parancssori eszközzel vagy grafikus segédprogrammal töltik le és telepítik.  Például az Ubuntu Linux biztosítja az [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) parancsot és az [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) eszközt az operációs rendszer frissítéséhez.


## <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzések végrehajtása

Magas szintű biztonságot kell fenntartania a megoldáslemezei számára az Azure Marketplace-en.  A következő cikk a biztonsági konfigurációk és eljárások ellenőrző listáját tartalmazza, amelyek segítséget nyújtanak ebben a célkitűzésben: [Biztonsági javaslatok az Azure Marketplace-rendszerképekhez.](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)  Ezek a javaslatok közül néhány a Linux-alapú rendszerképek, de a legtöbb vonatkozik a virtuális gép rendszerkép. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfigurációs és ütemezett feladatok végrehajtása

Ha további konfigurációra van szükség, az ajánlott megközelítés egy ütemezett feladat, amely fut indításkor, hogy a virtuális gép telepítése után a végső módosításokat.  Is figyelembe kell venni a következő ajánlásokat:
- Ha egyszeri feladatról van szó, ajánlott, hogy a feladat a sikeres befejezése után törölje magát.
- A konfigurációk nem támaszkodhatnak a C vagy D meghajtókon kívül más meghajtókra, mert csak ez a két meghajtó garantáltan létezik. A C meghajtó az operációs rendszer lemeze, a D meghajtó pedig az ideiglenes helyi lemez.

A Linux-testreszabásokról a [Virtual machine extensions and features for Linux című](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)témakörben talál további információt.


## <a name="generalize-the-image"></a>A kép általánosítása

Az Azure Piactéren az összes rendszerképnek újrafelhasználhatónak kell lennie általános módon. Az újrafelhasználhatóság elérése érdekében az operációs rendszer virtuális merevlemezét *általánossá*kell tenni, amely művelet eltávolítja az összes példányspecifikus azonosítót és szoftver-illesztőprogramot a virtuális gépről.

### <a name="windows"></a>Windows

A Windows operációs rendszer lemezei általánosak a [sysprep eszközzel](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Ha ezt követően frissíti vagy újrakonfigurálja az operációs rendszert, újra kell futtatnia a sysprep programot. 

> [!WARNING]
>  Mivel a frissítések automatikusan futhatnak, a sysprep futtatása után ki kell kapcsolnia a virtuális gép telepítését.  Ez a leállítás megakadályozza, hogy a későbbi frissítések példányspecifikus módosításokat hajtsanak végre a virtuális merevlemez operációs rendszerén vagy a telepített szolgáltatásokon.

A sysprep futtatásáról a [Virtuális merevlemez általánosításának lépései című témakörben](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) talál további információt.

### <a name="linux"></a>Linux

A következő kétlépéses folyamat általánosítja a Linux virtuális gép, és újratelepíti azt egy külön virtuális gép. Ez a két lépés csak a folyamat lényegét képezi. A két lépésről és azok elvégzésének okáról további információt [a Virtuális gép vagy virtuális merevlemez lemezképének létrehozása című témakörben talál.](../../../virtual-machines/linux/capture-image.md) Az Azure Marketplace-ajánlat virtuális merevlemezének létrehozása céljából leállíthatja, amikor eléri a "Virtuális gép létrehozása a rögzített lemezképből" szakaszt.

#### <a name="remove-the-azure-linux-agent"></a>Az Azure Linux-ügynök eltávolítása
1.  Csatlakozzon a Linux virtuális géphez egy SSH-ügyfél használatával.
2.  Az SSH ablakban írja be a következő parancsot: <br/>
    `sudo waagent -deprovision+user`
3.  Írja `y` be a folytatáshoz. (A paramétert `-force` hozzáadhatja az előző parancshoz, hogy elkerülje ezt a megerősítési lépést.)
4.  A parancs befejezése után `exit` írja be az SSH-ügyfél bezárását.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>A kép rögzítése
1.  Nyissa meg az Azure Portalon, válassza ki az erőforráscsoportot (RG) és törölje a virtuális gép lefoglalását.
2.  A virtuális merevlemez általánossá most, és létrehozhat egy új virtuális gép ezzel a virtuális merevlemez használatával.


## <a name="create-one-or-more-copies"></a>Egy vagy több példány létrehozása

A virtuális gép példányainak létrehozása gyakran hasznos a biztonsági mentéshez, teszteléshez, a testreszabott feladatátvételhez vagy a terheléselosztáshoz, a megoldás különböző konfigurációinak kínálásához és így tovább. Az elsődleges virtuális merevlemez másolásáról és letöltésével kapcsolatos további tudnivalókért itt található:

- Linux virtuális gép: [Linux virtuális merevlemez letöltése az Azure-ból](../../../virtual-machines/linux/download-vhd.md)
- Windows virtuális gép: [Windows virtuális merevlemez letöltése az Azure-ból](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>További lépések

Miután a virtuális gép általánossá vált, felszabadította, és létrehozott egy lemezképet a virtuális gép, készen áll a [virtuális gép üzembe helyezésére egy virtuális merevlemezről.](./cpp-deploy-vm-vhd.md)
