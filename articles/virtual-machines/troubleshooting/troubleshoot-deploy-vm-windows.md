---
title: A Windows virtuális gépekkel kapcsolatos problémáinak az Azure-ban történő telepítésének elhárítása – problémamegoldás | Microsoft dokumentumok
description: A Windows virtuális gépekkel kapcsolatos problémák központi telepítése az Azure Resource Manager telepítési modelljében.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: cdbaeb5a97beba342bc471e75d1b07be0d0141ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921411"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Windows rendszerű virtuális gépek Azure-beli üzembe helyezése során előforduló problémák elhárítása

A virtuális gép (VM) üzembe helyezési problémáinak elhárításához az Azure-ban tekintse át a [leggyakoribb hibákat](#top-issues) és megoldásokat.

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**

## <a name="top-issues"></a>Legfontosabb problémák
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>A fürt nem támogatja a kért virtuális gép méretét
\<tulajdonságok supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Próbálkozzon újra a kérelem egy kisebb virtuális gép mérete használatával.
- Ha a kért virtuális gép mérete nem módosítható:
    - Állítsa le az összes virtuális gépet a rendelkezésre állási csoportban. Kattintson **az Erőforráscsoportok** > erőforráscsoport > **erőforrások** > a virtuális gépek > **a virtuális gépek** > a virtuális gép > **a Stop**elemre.
    - Miután az összes virtuális gép leáll, hozza létre a virtuális gépet a kívánt méretben.
    - Indítsa el először az új virtuális gépet, majd jelölje ki az egyes leállított virtuális gépeket, és kattintson a Start gombra.


## <a name="the-cluster-does-not-have-free-resources"></a>A fürt nem rendelkezik szabad erőforrásokkal
\<tulajdonságok supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Próbálkozzon később a kéréssel.
- Ha az új virtuális gép egy másik rendelkezésre állási készlet része lehet
    - Hozzon létre egy virtuális gép egy másik rendelkezésre állási csoport (ugyanabban a régióban).
    - Adja hozzá az új virtuális gépet ugyanahhoz a virtuális hálózathoz.

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hogyan használhatom és telepíthetem a Windows-ügyfélrendszerképet az Azure-ba?

A Windows 7, a Windows 8 vagy a Windows 10 az Azure-ban fejlesztési/tesztelési forgatókönyvekhez is használható, ha rendelkezik megfelelő Visual Studio (korábbi MSDN) előfizetéssel. Ez a [cikk](../windows/client-images.md) ismerteti a windowsos ügyfél Azure-beli futtatásának jogosultsági követelményeit, és az Azure Gallery-lemezképek használatával.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hogyan telepíthetek egy virtuális gépet a hibrid használati juttatás (HUB) használatával?

Az Azure Hybrid Use Benefit használatával többféleképpen is üzembe helyezheti a Windows virtuális gépeket.

Nagyvállalati szerződéssel kapcsolatos előfizetés esetén:

• Virtuális gépek telepítése adott Piactér-lemezképekről, amelyek előre konfigurálva vannak az Azure Hybrid Use Benefit használatával.

Nagyvállalati szerződés esetén:

• Töltsön fel egy egyéni virtuális gép, és üzembe helyezett egy Resource Manager sablon vagy az Azure PowerShell használatával.

További információkért lásd a következőket:

 - [Az Azure hybrid use benefit – áttekintés](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Letölthető gyIK](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Az Azure Hybrid use benefit for Windows Server és Windows Ügyfél .](../windows/hybrid-use-benefit-licensing.md)

 - [Hogyan használhatom a hibrid használati előnyt az Azure-ban?](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hogyan aktiválhatom a havi kreditemet a Visual Studio Enterprise (BizSpark) számára?

A havi kredit aktiválásához olvassa el ezt a [cikket.](https://azure.microsoft.com/offers/ms-azr-0064p/)

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Hogyan adhat hozzá nagyvállalati fejlesztési/tesztelési adatokat a nagyvállalati szerződésemhez (EA), hogy hozzáférjen a Windows-ügyfélrendszerképekhez?

A vállalati fejlesztési és tesztelési ajánlat alapján előfizetések létrehozásának lehetősége azokra a fióktulajdonosokra korlátozódik, akik erre egy vállalati rendszergazda engedélyt kaptak. A fióktulajdonos az Azure-fiókportálon keresztül hoz létre előfizetéseket, majd hozzá kell adnia az aktív Visual Studio-előfizetőket társadminisztrátorként. Annak biztosítása, hogy a fejlesztéshez és teszteléshez szükséges erőforrásokat kezelni és használni tudják. Bővebb információ: [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Hiányoznak az illesztőprogramok a Windows N sorozatú virtuális gépemből

A Windows-alapú virtuális gépek illesztőprogramjai [itt](../windows/n-series-driver-setup.md)találhatók.

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nem találom a GPU-példányt az N sorozatú virtuális gépemben

A Windows Server 2016 vagy Windows Server 2012 R2 rendszert futtató Azure N sorozatú virtuális gépek GPU-funkcióinak kihasználásához a telepítés után telepítenie kell az NVIDIA grafikus illesztőprogramokat minden virtuális gépre. Az illesztőprogram-beállítási információk [a Windows virtuális gépekhez](../windows/n-series-driver-setup.md) és [a Linuxos virtuális gépekhez](../linux/n-series-driver-setup.md)érhetők el.

## <a name="is-n-series-vms-available-in-my-region"></a>Elérhetők az N sorozatú virtuális gépek a régiómban?

A rendelkezésre állást a [Termékek régiónként táblázatból](https://azure.microsoft.com/regions/services)és [az](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)árakból itt ellenőrizheti.

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Milyen ügyféllemezképeket használhatok és helyezhetek üzembe az Azure-ban, és hogyan szerezhetem be őket?

Használhatja a Windows 7, Windows 8 vagy Windows 10 az Azure-ban a fejlesztési és tesztelési forgatókönyvek, feltéve, hogy rendelkezik a megfelelő Visual Studio (korábbi MSDN) előfizetéssel. 

- A Windows 10-es képek az Azure Gallery-ből érhetők el a [jogosult fejlesztési és tesztelési ajánlatokon](../windows/client-images.md#eligible-offers)belül. 
- A Visual Studio bármely ajánlatán belül [megfelelően előkészítheti és létrehozhatja](../windows/prepare-for-upload-vhd-image.md) a 64 bites Windows 7, Windows 8 vagy Windows 10 lemezképet, majd [feltöltheti az Azure-ba.](../windows/upload-generalized-managed.md) A használat továbbra is az aktív Visual Studio-előfizetők általi fejlesztésre/tesztelésre korlátozódik.

Ez a [cikk](../windows/client-images.md) ismerteti a windowsos ügyfél Azure-beli futtatásának és az Azure Gallery-lemezképek használatának jogosultsági követelményeit.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nem látom a virtuális gép mérete család, amely szeretnék átméretezésekor a virtuális gép.

Amikor egy virtuális gép fut, akkor telepítve van egy fizikai kiszolgálón. Az Azure-régiók fizikai kiszolgálói közös fizikai hardverfürtökbe vannak csoportosítva. A virtuális gép átméretezése, amely megköveteli, hogy a virtuális gép kell mozgatni a különböző hardverfürtök eltérő attól függően, hogy melyik üzembe helyezési modellt használták a virtuális gép üzembe helyezéséhez.

- A klasszikus üzembe helyezési modellben üzembe helyezett virtuális gépeket el kell távolítani, és újra kell telepíteni a virtuális gépek egy másik méretcsaládban lévő méretre való módosításához.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Az Erőforrás-kezelő központi telepítési modelljében üzembe helyezett virtuális gépeknek le kell állítania a rendelkezésre állási csoportban lévő összes virtuális gépet, mielőtt módosítaná a rendelkezésre állási csoport bármely virtuális gépének méretét.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>A felsorolt virtuális gép mérete nem támogatott a rendelkezésre állási készlet üzembe helyezésekor.

Válasszon olyan méretet, amely et a rendelkezésre állási csoport fürtje támogatja. Ajánlott, ha egy rendelkezésre állási csoport, válassza ki a legnagyobb virtuális gép mérete úgy gondolja, hogy szükség van, és hogy az első üzembe helyezés a rendelkezésre állási készlet.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Hozzáadhatok egy meglévő klasszikus virtuális gép egy rendelkezésre állási csoport?

Igen. Hozzáadhat egy meglévő klasszikus virtuális gép egy új vagy meglévő rendelkezésre állási csoport. További információt a [Meglévő virtuális gép hozzáadása egy rendelkezésre állási csoporthoz című](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)témakörben talál.


## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/)

Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
