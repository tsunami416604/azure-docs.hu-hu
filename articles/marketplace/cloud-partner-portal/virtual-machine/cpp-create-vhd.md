---
title: Azure-kompatibilis virtuális merevlemez létrehozása az Azure Marketplace-en
description: A cikk azt ismerteti, hogyan hozhat létre virtuális MEREVLEMEZt egy virtuálisgép-ajánlathoz az Azure piactéren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 642c6964aaad8d6e8750fca67efb11eb3feaf19d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147015"
---
# <a name="create-an-azure-compatible-vhd"></a>Azure-kompatibilis virtuális merevlemez létrehozása

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuális gépekre vonatkozó ajánlatoknak a partner Centerbe való áthelyezését. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az Azure-beli [virtuális gépek technikai eszközeinek létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) című témakör utasításait.

Ez a cikk részletesen ismerteti a virtuális merevlemezek (VHD-k) létrehozásához szükséges lépéseket az Azure piactéren.  Emellett bevált eljárásokat is tartalmaz a különböző szempontokkal kapcsolatban, például a RDP protokoll (RDP) használatát, a virtuális gép méretének kiválasztását, a legújabb Windows-frissítések telepítését és a VHD-lemezkép általánosítását.  A következő fejezetek elsősorban a Windows-alapú virtuális merevlemezekre összpontosítanak; a Linux-alapú virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: az [Azure által támogatott disztribúciók Linux](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Erősen ajánlott, hogy kövesse az ebben a témakörben ismertetett útmutatást, hogy az Azure-t használva hozzon létre egy virtuális gépet, amely egy előre konfigurált, támogatott operációs rendszert tartalmaz.  Ha ez nem kompatibilis a megoldással, akkor lehetséges, hogy a helyszíni virtuális gépet egy jóváhagyott operációs rendszer használatával lehet létrehozni és konfigurálni.  Ezután beállíthatja és felkészítheti a feltöltéshez a [Windows VHD vagy a VHDX előkészítése az Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)-ba való feltöltéshez című témakörben leírtak szerint.


## <a name="select-an-approved-base"></a>Jóváhagyott alap kiválasztása
A virtuálisgép-rendszerkép operációs rendszerének VHD-jét egy olyan, az Azure által jóváhagyott alaprendszerképen kell alapulnia, amely Windows Servert vagy SQL Server tartalmaz.
A kezdéshez hozzon létre egy virtuális gépet az alábbi rendszerképek egyikéről, amely a Microsoft Azure Portal található:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 r2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, web)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, web)

> [!TIP]
> Ha a jelenlegi Azure Portal vagy PowerShellt használja, akkor a 2014-es és újabb verziókon közzétett Windows Server-lemezképek jóváhagyása megtörténik.

Az Azure számos jóváhagyott Linux-disztribúciót kínál.  Az aktuális listán tekintse [meg az Azure által támogatott disztribúciókkal](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)foglalkozó Linux című témakört.


## <a name="create-vm-in-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portalban 

A Microsoft [Azure Portalban](https://ms.portal.azure.com/)hozza létre az alaprendszerképet a következő lépésekkel.

1. Jelentkezzen be a portálra annak az Azure-előfizetésnek a Microsoft-fiókával, amelyhez közzé szeretné tenni a virtuálisgép-ajánlatát.
2. Hozzon létre egy új erőforráscsoportot, és adja meg az **erőforráscsoport nevét**, az **előfizetést**és az **erőforráscsoport helyét**.  További útmutatást az [erőforráscsoportok kezelése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)című témakörben talál.
3. A virtuális gépek részletei lap megjelenítéséhez kattintson a bal oldali menüsorban található **virtuális gépek** elemre. 
4. Az új lapon kattintson a **+ Hozzáadás** elemre a **számítási** panel megjelenítéséhez.  Ha nem látja a virtuálisgép-típust a kezdeti képernyőn, megkeresheti az alapszintű virtuális gép nevét, például:

    ![Új virtuális gép számítási panelje](./media/publishvm_014.png)

5. A megfelelő virtuális rendszerkép kiválasztása után adja meg a következő értékeket:
   * Az **alapvető beállítások** panelen adja meg a virtuális gép **nevét** , amely 1-15 alfanumerikus karakterből áll. (Ez a példa `DemoVm009`a következőt használja:.)
   * Adjon meg egy **felhasználónevet** és egy erős **jelszót**, amelynek használatával helyi fiókot hozhat létre a virtuális gépen.  (Itt `adminUser` van használatban.)  A jelszónak 8-123 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi követelménynek: egy kisbetű, egy nagybetű, egy szám és egy speciális karakter. További információ: [username és Password követelmények](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Válassza ki a létrehozott erőforráscsoportot (itt `DemoResourceGroup`).
   * Válasszon ki egy Azure **Location** -adatközpont `West US`helyet (itt).
   * Az értékek mentéséhez kattintson **az OK** gombra. 

6. Válassza ki az alábbi javaslatok alapján telepítendő virtuális gép méretét:
   * Ha a helyszíni virtuális merevlemez fejlesztését tervezi, a méret nem számít. Érdemes lehet a kisebb virtuális gépek egyikét használni.
   * Ha azt tervezi, hogy az Azure-ban fejleszti a rendszerképet, érdemes lehet a kiválasztott rendszerképhez ajánlott virtuálisgép-méreteket használni.
   * A díjszabással kapcsolatos információkért tekintse meg a portálon megjelenő **ajánlott árképzési szintek** kiválasztása című témakört. Ekkor megjelenik a kiadó által megadott három ajánlott méret. (Itt a közzétevő a Microsoft.)

   ![Új virtuális gép méretének panelje](./media/publishvm_015.png)

7. A **Beállítások** panelen állítsa a **felügyelt lemez használata** lehetőséget a **nem**értékre.  Ez lehetővé teszi az új virtuális merevlemez manuális kezelését. (A **Settings (beállítások** ) panel lehetővé teszi a tárolási és hálózati beállítások egyéb módosításának módosítását, például a **prémium (SSD)** **lemez típusának**kiválasztását.)  A folytatáshoz kattintson **az OK** gombra.

    ![Új virtuális gép beállításainak panelje](./media/publishvm_016.png)

8. A választott beállítások áttekintéséhez kattintson az **Összefoglalás** elemre. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, kattintson az **OK** gombra.

    ![Új virtuális gép összefoglaló panelje](./media/publishvm_017.png)

Az Azure megkezdi a megadott virtuális gép üzembe helyezését.  Az előrehaladás nyomon követéséhez kattintson a bal oldalon található **Virtual Machines** fülre.  A létrehozást követően az állapot **fut**értékre változik.  Ezen a ponton [csatlakozhat a virtuális géphez](./cpp-connect-vm.md).


## <a name="next-steps"></a>További lépések

Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása során, tekintse meg a [gyakori problémák a VHD létrehozása során](./cpp-common-vhd-creation-issues.md)című témakört.  Ellenkező esetben a következő lépésekhez csatlakoznia kell az Azure-ban létrehozott [virtuális gépekhez](./cpp-connect-vm.md) . 
