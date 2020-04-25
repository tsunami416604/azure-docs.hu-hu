---
title: A Microsoft Azure által üzemeltetett virtuális gép konfigurálása az Azure Marketplace-en
description: Ismerteti, hogyan lehet méretezni, frissíteni és általánosítani az Azure-ban üzemeltetett virtuális gépeket.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 4cb247a3e64f8d44cc64010dde40963f4e9a1993
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146095"
---
# <a name="configure-the-azure-hosted-vm"></a>Az Azure által üzemeltetett virtuális gép konfigurálása

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuális gépekre vonatkozó ajánlatoknak a partner Centerbe való áthelyezését. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az Azure-beli [virtuális gépek technikai eszközeinek létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) című témakör utasításait.

Ez a cikk az Azure-ban üzemeltetett virtuális gépek (VM-EK) méretének, frissítésének és általánosításának módját ismerteti.  Ezek a lépések szükségesek ahhoz, hogy előkészítse a virtuális gépet az Azure piactéren való üzembe helyezéshez.

## <a name="sizing-the-vhds"></a>A VHD-k méretezése

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Ha kiválasztotta az operációs rendszerhez előre konfigurált virtuális gépek valamelyikét (és opcionálisan további szolgáltatásokat is), akkor a [virtuális gép SKU](./cpp-skus-tab.md)-ban található szabványos Azure-beli virtuálisgép-méretet már leválasztotta.  Az ajánlott módszer a megoldás elindítása előre konfigurált operációs rendszerrel.  Ha azonban manuálisan telepít egy operációs rendszert, akkor az elsődleges virtuális merevlemezt a virtuálisgép-rendszerképbe kell méreteznie:

- Windows esetén az operációs rendszer virtuális merevlemezét 127-128 GB-os rögzített formátumú VHD-ként kell létrehozni. 
- Linux esetén ezt a virtuális merevlemezt 30-50 GB-os rögzített formátumú VHD-ként kell létrehozni.

Ha a fizikai méret kisebb, mint 127-128 GB, a VHD-nek ritkanak kell lennie. A megadott alapszintű Windows-és SQL Server-lemezképek már megfelelnek ezeknek a követelményeknek, ezért ne módosítsa a virtuális merevlemez formátumát vagy méretét. 

Az adatlemezek mérete akár 1 TB is lehet. A méretük meghatározásakor ne feledje, hogy az ügyfelek nem tudják átméretezni a rendszerképeken belüli virtuális merevlemezeket az üzembe helyezés időpontjában. Az adatlemezek virtuális merevlemezeit rögzített formátumú VHD-ként kell létrehozni. Emellett ritka is lehet. Az adatlemezek kezdetben üresek vagy tartalmazhatnak adatfájlokat.


## <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

Az operációs rendszer virtuális gépei alapképei a közzétételük napjáig tartalmazzák a legújabb frissítéseket. Mielőtt közzéteszi a létrehozott operációsrendszer-VHD-t, győződjön meg arról, hogy az operációs rendszer és az összes telepített szolgáltatás frissítése megtörtént a legújabb biztonsági és karbantartási javításokkal.

A Windows Server 2016 esetén futtassa a **frissítések keresése** parancsot.  Ellenkező esetben a Windows régebbi verzióiban tekintse [meg a frissítés beszerzése a Windows Update használatával](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update)című témakört.  A Windows Update automatikusan telepíti a legújabb kritikus és fontos biztonsági frissítéseket.

A Linux-disztribúciók esetében a frissítések általában egy parancssori eszközzel vagy egy grafikus segédprogrammal tölthetők le és telepíthetők.  Például Ubuntu Linux biztosítja az [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) parancsot és az [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) eszközt az operációs rendszer frissítéséhez.


## <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzések végrehajtása

Az Azure Marketplace-en magas szintű biztonságot kell biztosítania a megoldási lemezképek számára.  A következő cikk az ebben a célkitűzésben segítséget nyújtó biztonsági konfigurációkról és eljárásokról tartalmaz feladatlistát: [biztonsági javaslatok az Azure Marketplace-lemezképekhez](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  A javaslatok némelyike a Linux-alapú rendszerképekre jellemző, de a legtöbb esetben a virtuálisgép-lemezképekre is érvényes. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfiguráció és ütemezett feladatok végrehajtása

Ha további konfigurálásra van szükség, az ajánlott módszer egy olyan ütemezett feladat használata, amely indításkor fut, hogy az üzembe helyezést követően végső módosításokat végezzen a virtuális gépen.  Vegye figyelembe az alábbi ajánlásokat is:
- Ha ez egy egyszeri futtatású feladat, azt javasoljuk, hogy a feladat sikeres befejeződése után törölje magát.
- A konfigurációk nem hivatkozhatnak a C vagy a D-től eltérő meghajtókra, mert csak ez a két meghajtó létezik, amely mindig garantált. A C meghajtó az operációs rendszer lemeze, a D meghajtó pedig az ideiglenes helyi lemez.

A Linux testreszabásával kapcsolatos további információkért lásd: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>A rendszerkép általánosítása

Az Azure Marketplace-en lévő összes lemezképet általános módon újrafelhasználhatónak kell lennie. Ennek a felhasználhatóságnak az elvégzéséhez *általánosítva*kell lennie az operációs rendszer virtuális merevlemezének, egy művelettel, amely eltávolítja az összes példány-specifikus azonosítót és a szoftver ILLESZTŐPROGRAMJAIT egy virtuális gépről.

### <a name="windows"></a>Windows

A Windows operációsrendszer-lemezek általánosítva vannak a [Sysprep eszközzel](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Ha később frissíti vagy újrakonfigurálja az operációs rendszert, újra kell futtatnia a Sysprep programot. 

> [!WARNING]
>  Mivel a frissítések automatikusan is futtathatók, a Sysprep futtatása után ki kell kapcsolni a virtuális gépet, amíg üzembe nem helyezi őket.  Ez a Leállítás megakadályozza, hogy a következő frissítések példány-specifikus módosításokat hozzanak a VHD operációs rendszer vagy a telepített szolgáltatások számára.

A Sysprep futtatásával kapcsolatos további információkért lásd: [virtuális merevlemez általánosításának lépései](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

A következő kétlépéses folyamat általánosít egy Linux rendszerű virtuális gépet, és újratelepíti külön virtuális gépre. Ez a két lépés csak a folyamat lényegét képezi. További információ erről a két lépésről, valamint arról, hogy miért kell őket elvégezni. a [virtuális gép vagy VHD rendszerképének létrehozása](../../../virtual-machines/linux/capture-image.md)című témakörben talál további információt. Az Azure Marketplace-ajánlat virtuális merevlemezének létrehozása céljából leállíthatja a virtuális gép létrehozása a rögzített lemezképből című szakaszt.

#### <a name="remove-the-azure-linux-agent"></a>Az Azure Linux-ügynök eltávolítása
1.  Csatlakozhat a linuxos virtuális géphez egy SSH-ügyfél használatával.
2.  Az SSH ablakban írja be a következő parancsot: <br/>
    `sudo waagent -deprovision+user`
3.  A `y` folytatáshoz írja be a következőt:. (A `-force` paramétert hozzáadhatja az előző parancshoz, így elkerülheti ezt a megerősítő lépést.)
4.  A parancs befejezése után írja be `exit` a parancsot az SSH-ügyfél bezárásához.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>A rendszerkép rögzítése
1.  Nyissa meg a Azure Portalt, válassza ki az erőforráscsoportot (RG) és a virtuális gép lefoglalását.
2.  A VHD-fájl már általánosítva van, és létrehozhat egy új virtuális gépet a virtuális merevlemez használatával.


## <a name="create-one-or-more-copies"></a>Egy vagy több másolat létrehozása

A virtuális gép példányainak létrehozása gyakran hasznos a biztonsági mentés, a tesztelés, a testreszabott feladatátvétel vagy a terheléselosztás számára, hogy különböző konfigurációkat nyújtson a megoldásokhoz, és így tovább. A nem felügyelt klónok megkettőzéséről és letöltéséről további információt a következő témakörben talál:

- Linuxos virtuális gép: [linuxos virtuális merevlemez letöltése az Azure-ból](../../../virtual-machines/linux/download-vhd.md)
- Windows rendszerű virtuális gép: [Windows virtuális merevlemez letöltése az Azure-ból](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>További lépések

Miután a virtuális gép általánosítva lett, fel lett távolítva, és létrehozta a virtuális gép lemezképét, készen áll a [virtuális gépek virtuális merevlemezről történő üzembe helyezésére](./cpp-deploy-vm-vhd.md).
