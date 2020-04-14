---
title: Azure-kompatibilis virtuális merevlemez létrehozása az Azure Piactérhez
description: Bemutatja, hogyan hozhat létre virtuális merevlemezt egy virtuálisgép-ajánlathoz az Azure Piactéren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 99d2bc95c1dd837bfc3bcabcead28777b7e6f746
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273936"
---
# <a name="create-an-azure-compatible-vhd"></a>Azure-kompatibilis virtuális merevlemez létrehozása

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure virtuálisgép-ajánlatok partnerközpontba való áthelyezését. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure Virtuálisgép technikai eszközeinek létrehozása](https://aka.ms/AzureVMTechAsset) az áttelepített ajánlatok kezeléséhez című, az Utasításokat.

Ez a cikk ismerteti a virtuális merevlemez (VHD) létrehozásához szükséges lépéseket egy virtuális gép (VM) ajánlat az Azure Marketplace-en.  Emellett gyakorlati tanácsokat is tartalmaz különböző szempontokra vonatkozóan, például a Távoli asztali protokoll (RDP) használatával, a virtuális gép méretének kiválasztásával, a legújabb Windows-frissítések telepítésével és a Virtuális merevlemez-lemezkép általánosításával.  A következő szakaszok elsősorban a windows-alapú virtuális gépekre összpontosítanak; A Linux-alapú Virtuális hálózatok létrehozásáról további információt az [Azure által jóváhagyott Linux-disztribúciókcímű](../../../virtual-machines/linux/endorsed-distros.md)témakörben talál. 

> [!WARNING]
> Erősen ajánlott, hogy kövesse az ebben a témakörben található útmutatást az Azure-t egy előre konfigurált, jóváhagyott operációs rendszert tartalmazó virtuális gép létrehozásához.  Ha ez nem kompatibilis a megoldással, akkor lehetőség van egy helyszíni virtuális gép létrehozására és konfigurálására egy jóváhagyott operációs rendszer használatával.  Ezután konfigurálhatja és előkészítheti a feltöltésre a [Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)című részben leírtak szerint.


## <a name="select-an-approved-base"></a>Jóváhagyott alap kiválasztása
A virtuális gép lemezképének operációs rendszer virtuális merevlemezének egy Azure által jóváhagyott alaplemezképen kell alapulnia, amely Windows Server vagy SQL Server rendszert tartalmaz.
Először hozzon létre egy virtuális gép az alábbi képek egyike, a Microsoft Azure portalon található:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (nagyvállalati, normál, webes)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (nagyvállalati, normál, webes)

> [!TIP]
> Ha a jelenlegi Azure Portalon vagy a PowerShellen használja, a 2014.

Az Azure a jóváhagyott Linux-disztribúciók széles skáláját kínálja.  Az aktuális listát az [Azure által jóváhagyott Linux-disztribúciókról](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)szóló témakörben található.


## <a name="create-vm-in-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portalon 

A Microsoft [Azure portalon](https://ms.portal.azure.com/)hozza létre az alaplemezképet a következő lépésekkel.

1. Jelentkezzen be a portálra a Microsoft-fiókkal a virtuális gép ajánlat közzétenni kívánt Azure-előfizetés.
2. Hozzon létre egy új erőforráscsoportot, és adja meg az **Erőforráscsoport nevét**, **Az Előfizetés**és **az Erőforráscsoport helyét.**  További útmutatásért [lásd: Erőforráscsoportok kezelése.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)
3. Kattintson a **virtuális gépek** a bal menüsorban, hogy megjelenjen a virtuális gépek részleteit oldalon. 
4. Ezen az új oldalon kattintson a **+Hozzáadás** **gombra** a Számítási panel megjelenítéséhez.  Ha nem látja a virtuális gép típusát a kezdeti képernyőn, megkeresheti az alap virtuális gép nevét, például:

    ![Az új virtuális gép számítási panelje](./media/publishvm_014.png)

5. A megfelelő virtuális lemezkép kiválasztása után adja meg a következő értékeket:
   * Az **Alapok** panelen adjon meg egy **nevet** a virtuális gépnek 1-15 alfanumerikus karakter között. (Ez a `DemoVm009`példa a .)
   * Adjon meg egy **felhasználónevet** és egy erős **jelszót,** amely a helyi fiók létrehozásához használatos a virtuális számítógépen.  (Itt `adminUser` használatban van.)  A jelszónak 8-123 karakter hosszúnak kell lennie, és a következő négy összetettségi követelmény közül háromnak kell megfelelnie: egy kisbetűs, egy nagybetűs karakter, egy szám és egy speciális karakter. További információt a [Felhasználónév és jelszó követelmények című](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm)témakörben talál.
   * Válassza ki a létrehozott `DemoResourceGroup`erőforráscsoportot (itt).
   * Válasszon ki egy Azure `West US`Datacenter-helyet (itt). **Location**
   * Az értékek mentéséhez kattintson az **OK** gombra. 

6. Válassza ki a virtuális gép méretét az alábbi javaslatok használatával:
   * Ha azt tervezi, hogy a virtuális merevlemez helyszíni fejlesztése, a méret nem számít. Fontolja meg a kisebb virtuális gépek használatát.
   * Ha azt tervezi, hogy a rendszerkép az Azure-ban, fontolja meg a kiválasztott rendszerkép ajánlott virtuálisgép-méretek egyikének használata.
   * A díjszabási információkért tekintse meg a portálon megjelenő **ajánlott tarifacsomagok** választóját. Megjeleníti a kiadó által biztosított három ajánlott méretet. (Itt a kiadó a Microsoft.)

   ![Az új virtuális gép mérete](./media/publishvm_015.png)

7. A **Beállítások** panelen állítsa a **Felügyelt lemez használata** beállítást Nem **(Nem)** beállításra.  Ez lehetővé teszi az új virtuális merevlemez manuális kezelését. (A **Beállítások** panel lehetővé teszi a tárolási és hálózati beállítások egyéb módosítását is, például a **Prémium (SSD)** lehetőséget **a Lemez típusban.)**  A folytatáshoz kattintson az **OK** gombra.

    ![Az új virtuális gép beállítási panelje](./media/publishvm_016.png)

8. A választott beállítások áttekintéséhez kattintson az **Összefoglalás** elemre. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, kattintson az **OK** gombra.

    ![Az új virtuális gép összefoglaló panelje](./media/publishvm_017.png)

Az Azure megkezdi a megadott virtuális gép kiépítését.  Nyomon követheti a haladást, ha a bal oldalon a **Virtuális gépek** fülre kattint.  Létrehozása után az állapot **Futás**állapotra változik.  Ezen a ponton [csatlakozhat a virtuális géphez.](./cpp-connect-vm.md)


## <a name="next-steps"></a>További lépések

Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása, lásd: [Gyakori problémák a virtuális merevlemez létrehozása során.](./cpp-common-vhd-creation-issues.md)  Ellenkező esetben tovább kell csatlakoznia az Azure-ban létrehozott [virtuális gépekhez.](./cpp-connect-vm.md) 
