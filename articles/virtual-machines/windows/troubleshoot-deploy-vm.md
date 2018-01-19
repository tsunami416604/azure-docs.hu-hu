---
title: "Problémamegoldás telepítését Windows virtuális gép az Azure-ban |} Microsoft Docs"
description: "Problémamegoldás telepítését Windows virtuális gép Azurethe Resource Manager üzembe helyezési modellben."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 6a1697061d20d26b4263c02487180fee81e87947
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Problémamegoldás telepítését Windows virtuális gép az Azure-ban

Az Azure virtuális gép (VM) telepítési problémák megoldásához tekintse át a [leggyakoribb problémák](#top-issues) a gyakori hibák és megoldására.

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.

## <a name="top-issues"></a>Problémák
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>A fürt nem támogatja a kért Virtuálisgép-méret
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Próbálja megismételni a kérelmet kisebb Virtuálisgép-méretet.
- Ha a kért virtuális gép mérete nem lehet módosítani:
    - Állítsa le a rendelkezésre állási csoport összes virtuális gépet. Kattintson a **erőforráscsoportok** > az erőforráscsoport > **erőforrások** > a rendelkezésre állási csoport > **virtuális gépek** > a virtuális gép > **leállítása**.
    - Állítsa le a virtuális gépeket, létre kell hoznia a virtuális gép a kívánt méretet.
    - Először indítsa el a új virtuális Gépet, majd válassza ki a leállított virtuális gépek mindegyikének, és válassza az Indítás parancsot.


## <a name="the-cluster-does-not-have-free-resources"></a>A fürt nem rendelkezik szabad erőforrást
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Később próbálkozzon újra a kéréssel.
- Ha az új virtuális Gépet egy másik rendelkezésre állási készlet része lehet.
    - Hozzon létre egy virtuális Gépet egy másik rendelkezésre állási csoport (ugyanabban a régióban).
    - Adja hozzá az új virtuális gép ugyanahhoz a virtuális hálózathoz.

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hogyan használja és az Azure windows ügyfél lemezkép telepítéséhez?

Használhatja a Windows 7, Windows 8 vagy Windows 10 az Azure-ban fejlesztési/Tesztelési forgatókönyvek ha megfelelő (korábbi nevén MSDN) Visual Studio-előfizetéssel rendelkezik. Ez [cikk](client-images.md) ismerteti az Azure-ban futó Windows-ügyfél a jogosultsági követelményeit, és használja az Azure-katalógus képek.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hogyan telepítheti a virtuális gépek a hibrid használata juttatás (HUB)?

Van több különböző módon használható előnyökkel Azure hibrid Windows virtuális gépek telepítéséhez.

Egy nagyvállalati szerződés előfizetéshez:

• Az adott piactéren elérhető rendszerkép, amelyek az Azure hibrid használata juttatás előre konfigurált virtuális gépek telepítéséhez.

A nagyvállalati szerződés:

• Feltöltése egy egyéni virtuális Gépet, és telepítése a Resource Manager-sablon vagy Azure PowerShell segítségével.

További információkért lásd a következőket:

 - [Az Azure hibrid használata juttatás áttekintése](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Letölthető – gyakori kérdések](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Windows Server és a Windows ügyfél Azure hibrid használata juttatás](hybrid-use-benefit-licensing.md).

 - [Hogyan használhatók a hibrid használja előnyt az Azure-ban](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hogyan aktiválhatja a Visual Studio Enterprise (BizSpark) a havi keretet

A havi kreditek aktiválásához megjelenik ez [cikk](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Hogyan adhat hozzá a vállalati fejlesztési és tesztelési célú számára a nagyvállalati szerződés (EA) ablakban ügyfél képek eléréséhez?

Képes létrehozni a vállalati fejlesztési és tesztelési célú ajánlat alapján előfizetések kapott engedélyt ehhez a vállalati rendszergazda fiók tulajdonosok korlátozódik. A fiók tulajdonosának előfizetések az Azure-fiók portálon hoz létre, és kell adja aktív Visual Studio-előfizetők társadminisztrátorként. Így azok kezelése, és használja a fejlesztéshez és teszteléshez szükséges erőforrásokat. További információkért lásd: [vállalati fejlesztési és tesztelési célú](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Az illesztőprogramok hiányoznak a Windows-N sorozatú virtuális Gépemhez

Illesztőprogramok Windows-alapú virtuális gépek találhatók [Itt](n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>A GPU példánya nem található a N sorozatú virtuális Gépen belül

Windows Server 2016 vagy a Windows Server 2012 R2 rendszert futtató Azure N sorozatú virtuális gépek GPU lehetőségeinek előnyeit, telepítenie kell NVIDIA video-illesztőprogramok minden egyes virtuális gépen a telepítés utáni. Illesztőprogram-telepítő információ [Windows virtuális gépek](n-series-driver-setup.md) és [Linux virtuális gépek](../linux/n-series-driver-setup.md).

## <a name="are-client-images-supported-for-n-series"></a>Ügyfél képek N-adatsorozathoz támogatottak?

Jelenleg Azure csak támogatja az N-sorozat a Windows Server és Linux operációs rendszert futtató virtuális gépeken.

## <a name="is-n-series-vms-available-in-my-region"></a>Érhető el N sorozatú virtuális gépek régiómban?

Ellenőrizheti a szinten rendelkezésre áll a [régió tábla által elérhető termékek](https://azure.microsoft.com/regions/services), és az árképzés terén [Itt](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Milyen ügyfél képek is I használata és telepítése az Azure-ban, és hogyan való elérni őket?

Használhatja a Windows 7, Windows 8 vagy Windows 10 fejlesztési és tesztelési célú forgatókönyvek az Azure-ban biztosított megfelelő (korábbi nevén MSDN) Visual Studio-előfizetéssel rendelkezik. 

- Windows 10-lemezképek elérhetők az Azure katalógusából belül [jogosult fejlesztési és tesztelési célú kínál](client-images.md#eligible-offers). 
- A Visual Studio-előfizetők ajánlat bármilyen típusú belül is [megfelelően készítse elő és hozzon létre](prepare-for-upload-vhd-image.md) egy 64 bites Windows 7, Windows 8 vagy Windows 10-lemezképet, majd [feltöltése az Azure-bA](upload-generalized-managed.md). Használatát marad által aktív Visual Studio-előfizetők fejlesztési és tesztelési célú korlátozódik.

Ez [cikk](client-images.md) a jogosultsági követelmények futó Windows-ügyfél Azure-ban és az Azure-katalógus képek használatát ismerteti.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nem tudom, hogy a virtuális gép átméretezésekor kívánt Virtuálisgép-méretet termékcsalád talál.

Ha egy virtuális gép fut, a fizikai kiszolgáló rendszer. A fizikai kiszolgálók Azure-régiók közös fizikai hardver fürtök vannak csoportosítva. A, amelyhez a virtuális gépek áthelyezése a másik hardverre fürtök a virtuális gépek átméretezésével eltér attól függően, hogy melyik telepítési modell használatával telepítse a virtuális Gépet.

- Klasszikus üzembe helyezési modellel, a felhő szolgáltatástelepítés telepített virtuális gépek kell eltávolítása és újratelepítése módosításához a virtuális gépeket egy másik mérete termékcsalád méretre.

- Erőforrás-kezelő üzembe helyezési modellben telepített virtuális gépek, le kell állítania minden virtuális gép rendelkezésre állási csoportban, a rendelkezésre állási csoport minden virtuális gép méretének módosítása előtt.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>A listán szereplő virtuális gép mérete nem támogatott rendelkezésre állási csoportban való telepítése közben.

A rendelkezésre állási csoport fürtön támogatott méret kiválasztása. Ha válassza ki a legnagyobb virtuális gép méretét úgy gondolja, hogy kell, és rendelkezik, amelyek az első telepített a rendelkezésre állási csoport létrehozása a rendelkezésre állási beállítása ajánlott.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Hozzáadható az létező klasszikus virtuális gép egy rendelkezésre állási csoportot?

Igen. Meglévő klasszikus virtuális adhat hozzá egy új vagy meglévő rendelkezésre állási csoportban. További információ: [hozzáadása egy meglévő virtuális gép rendelkezésre állási csoportok](classic/configure-availability.md#addmachine).


## <a name="next-steps"></a>További lépések
Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/).

Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az beszerzése**.
