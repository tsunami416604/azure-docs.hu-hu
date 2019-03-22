---
title: Egy Azure-kompatibilis virtuális Merevlemezének létrehozása az Azure Marketplace-en |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre egy virtuális Merevlemezt egy virtuálisgép-ajánlat az Azure piactéren.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 5643b7c80307cea36f60bcc116c82ea7b31171f9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096571"
---
# <a name="create-an-azure-compatible-vhd"></a>Hozzon létre egy Azure-kompatibilis virtuális Merevlemezt.

Ez a cikk ismerteti a lépéseket szükséges, hozzon létre egy virtuális merevlemezt (VHD) egy virtuális gép (VM) az ajánlat az Azure piactéren.  Ajánlott eljárások az üzemeltetés minőségének különböző szempontjait, például a távoli asztal protokoll (RDP) használatával, válassza ki a méretet a virtuális gép, a legújabb Windows-frissítések telepítése és általánosítása a VHD-lemezképet is tartalmaz.  A következő szakaszok középpontjában elsősorban a windows-alapú virtuális merevlemezek; Linux-alapú virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Linux-disztribúciók, Azure által támogatott](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Erősen ajánlott, hogy kövesse az útmutatást ebben a témakörben előre konfigurálva, a támogatott operációs rendszert tartalmazó virtuális gép létrehozása az Azure segítségével.  Ha ez nem kompatibilis a megoldását, majd akkor lehet létrehozni és konfigurálni egy jóváhagyott operációs rendszert használ, a helyszíni virtuális gép.  Ezután konfigurálja, és előkészítése feltöltéshez, leírtak szerint [készítse elő a Windows VHD vagy VHDX, az Azure-bA feltöltendő](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Válasszon egy jóváhagyott alapja
A Virtuálisgép-lemezkép az operációs rendszer virtuális Merevlemeze kell lennie egy Azure által jóváhagyott alaplemezkép alapján, amely tartalmazza a Windows Server vagy SQL Server.
A kezdéshez egy virtuális gép létrehozása az alábbi lemezképek egyikéből található a Microsoft Azure Portalon:

-   A Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/), [2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/), [2008 R2 SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Az aktuális Azure-portálon vagy a PowerShell használatakor közzétett 2014. szeptember 8 és újabb Windows Server-rendszerképeket hagyja jóvá.

Azt is megteheti Azure által nyújtott széles körű jóváhagyott Linux-disztribúció.  Aktuális listáját lásd: [Linux-disztribúciók, Azure által támogatott](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Virtuális gép létrehozása az Azure Portalon 

A Microsoft [az Azure portal](https://ms.portal.azure.com/), az alaprendszerképet az alábbi lépéseket követve hozzon létre.

1. Jelentkezzen be a portálra a Microsoft-fiókot az Azure-előfizetést a Virtuálisgép-ajánlat közzététele szeretné.
2. Hozzon létre egy új erőforráscsoportot, és adja meg a **erőforráscsoport-név**, **előfizetés**, és **erőforráscsoport helye**.  További útmutatásért lásd: [-erőforráscsoportok kezelése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Kattintson a **virtuális gépek** a a bal oldali menüsáv a virtuális gépek részletei lap megjelenítéséhez. 
4. Ez új oldalon, kattintson a **+ Hozzáadás** megjelenítéséhez a **számítási** panelen.  Ha a kezdeti képernyőn nem jelenik meg a virtuális gép típusát, például is keresése, az alap virtuális gép neve:

    ![Számítás panel új virtuális gép](./media/publishvm_014.png)

5. Miután kiválasztotta a megfelelő virtuális lemezképet, adja meg a következő értékeket:
   * Az a **alapjai** panelen adjon meg egy **neve** a virtuális gép, 1 – 15 alfanumerikus karaktert. (Ez a példa `DemoVm009`.)
   * Adjon meg egy **felhasználónév** és a egy erős **jelszó**, használt helyi fiók létrehozása a virtuális gépen.  (Itt `adminUser` használja.)  A jelszónak 8–123 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter. További információkért lásd: [felhasználónév és jelszó követelmények](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Válassza ki a létrehozott erőforráscsoportot (Itt `DemoResourceGroup`).
   * Válassza ki az Azure-adatközpont **hely** (Itt `West US`).
   * Kattintson a **OK** menteni ezeket az értékeket. 

6. Válassza ki a méretet a virtuális gép üzembe helyezés az alábbi javaslatokat:
   * Ha azt tervezi, a VHD-t a helyszíni fejlesztés, a méret nem számít. Érdemes egy kisebb méretű virtuális gépet használni.
   * Amennyiben az Azure-ban kívánja fejleszteni a lemezképet, érdemes a kiválasztott lemezképhez javasolt virtuálisgép-méretek valamelyikét használni.
   * Díjszabási információkért tekintse meg a **javasolt tarifacsomagok** választó jelenik meg a portálon. Ez megjeleníti a kiadó által megadott három ajánlott méretet. (A közzétevő itt, a Microsoft is.)

   ![Új virtuális gép méret panel](./media/publishvm_015.png)

7. Az a **beállítások** panelen adja meg a **használata felügyelt lemez** beállítást **nem**.  Ez lehetővé teszi, hogy az új virtuális merevlemez kézi kezelésére. (A **beállítások** panel lehetővé teszi, hogy módosítsa az egyéb olyan módosítást a tárolási és hálózati beállításokat, például kiválasztásával **prémium (SSD)** a **lemeztípus**.)  Kattintson a **OK** folytatásához.

    ![Új virtuális gép (beállítások) panelén](./media/publishvm_016.png)

8. A választott beállítások áttekintéséhez kattintson az **Összefoglalás** elemre. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, kattintson az **OK** gombra.

    ![Új virtuális gép összefoglalás panelje](./media/publishvm_017.png)

Az Azure elkezdi a megadott virtuális gép kiépítése.  Kattintson a követheti a folyamat előrehaladását **virtuális gépek** lapon bal oldalon.  Miután létrejött, az állapot változik **futó**.  Ezen a ponton lehetősége [csatlakozzon a virtuális géphez](./cpp-connect-vm.md).


## <a name="next-steps"></a>További lépések

Ha Ön problémát észlelt az új Azure-alapú VHD létrehozása, [gyakori hibák a virtuális merevlemez létrehozása közben](./cpp-common-vhd-creation-issues.md).  Ellenkező esetben mellett kell [csatlakozhat a virtuális gépekhez](./cpp-connect-vm.md) létrehozta az Azure-ban. 
