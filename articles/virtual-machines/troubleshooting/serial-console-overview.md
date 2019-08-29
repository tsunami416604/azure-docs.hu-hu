---
title: Azure soros konzol Linux rendszerhez | Microsoft Docs
description: Kétirányú soros konzol az Azure Virtual Machines és Virtual Machine Scale Setshoz.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: cc964b1b11a68921c1d5d2b68a7413934565fdc8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129600"
---
# <a name="azure-serial-console"></a>Azure soroz konzol

A Azure Portal soros konzolja hozzáférést biztosít a virtuális gépek (VM-EK) és virtuálisgép-méretezési csoport példányaihoz, amelyek Linux vagy Windows rendszert futtatnak. Ez a soros kapcsolat a virtuális gép vagy a virtuálisgép-méretezési csoport példányának ttyS0 vagy COM1 soros portjához csatlakozik, és a hálózat vagy az operációs rendszer állapotának független hozzáférést biztosít. A soros konzol csak a Azure Portal használatával érhető el, és csak azon felhasználók számára engedélyezett, akik a virtuális gép vagy a virtuálisgép-méretezési csoport számára a közreműködő vagy annál magasabb hozzáférési szerepkörrel rendelkeznek.

A soros konzol a virtuális gépek és a virtuálisgép-méretezési csoport példányai esetében is ugyanúgy működik. Ebben a doc-ban a virtuális gépekre vonatkozó összes említés implicit módon tartalmazza a virtuálisgép-méretezési csoport példányait, hacsak másként nincs megadva.

> [!NOTE]
> A soros konzol általánosan elérhető a globális Azure-régiókban. Ez még nem érhető el az Azure government vagy Azure China felhőkben.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Az Azure soros konzol elérésének előfeltételei
A soros konzol virtuális gépen vagy virtuálisgép-méretezési csoport példányán való eléréséhez a következőkre lesz szüksége:

- A virtuális géphez engedélyezni kell a rendszerindítási diagnosztikát
- A jelszó-hitelesítést használó felhasználói fióknak léteznie kell a virtuális gépen. Létrehozhat egy jelszó-alapú felhasználót a virtuálisgép-hozzáférési bővítmény [jelszó](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) alaphelyzetbe állítása funkciójával. Válassza ki **jelszó alaphelyzetbe állítása** származó a **támogatás + hibaelhárítás** szakaszban.
- A soros konzolhoz hozzáférő Azure-fióknak rendelkeznie kell [virtuálisgép-közreműködő szerepkörrel](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) mind a virtuális gép, mind a rendszerindítási [diagnosztika](boot-diagnostics.md) Storage-fiókhoz.

> [!NOTE]
> - Klasszikus üzemi modellben nem támogatottak. A virtuális gép vagy virtuálisgép-méretezési csoport példányának a Azure Resource Manager telepítési modellt kell használnia.

## <a name="get-started-with-the-serial-console"></a>Ismerkedés a soros konzollal
A virtuális gépek és a virtuálisgép-méretezési csoport soros konzolja csak a Azure Portal érhető el:

### <a name="serial-console-for-virtual-machines"></a>Virtual Machines soros konzolja
A virtuális gépek soros konzolja olyan egyszerű, mint a Azure Portal **támogatás + hibaelhárítás** szakaszának **Serial consoleére** kattintva.
  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. Navigáljon az **összes erőforráshoz** , és válasszon ki egy virtuális gépet. Megnyílik a virtuális gép áttekintő lapja.

  1. Görgessen le a **támogatás + hibaelhárítás** szakaszt, és válassza **soros konzol**. A soros konzol segítségével egy új panel nyílik meg, és elindítja a kapcsolatot.

     ![Linux soros konzol ablak](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets soros konzolja
A soros konzol a méretezési csoport minden példányán elérhető a virtuálisgép-méretezési csoportokhoz. A **Serial Console** gomb meglátása előtt navigáljon a virtuálisgép-méretezési csoport egyes példányaira. Ha a virtuálisgép-méretezési csoport nem rendelkezik engedélyezve a rendszerindítási diagnosztika szolgáltatással, frissítse a virtuálisgép-méretezési csoport modelljét a rendszerindítási diagnosztika engedélyezéséhez, majd frissítse az összes példányt az új modellre a soros konzol eléréséhez.
  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. Navigáljon az **összes erőforráshoz** , és válasszon ki egy virtuálisgép-méretezési készletet. Megnyílik a virtuálisgép-méretezési csoport Áttekintés lapja.

  1. A **példányok** navigálása

  1. Virtuálisgép-méretezési csoport példányának kiválasztása

  1. A **támogatás + hibaelhárítás** szakaszban válassza a **Serial Console**lehetőséget. A soros konzol segítségével egy új panel nyílik meg, és elindítja a kapcsolatot.

     ![Linuxos virtuálisgép-méretezési csoport soros konzolja](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>A soros konzol speciális felhasználási módjai
A konzolhoz való hozzáféréstől eltekintve a következőhöz is használhatja az Azure soros konzolt:
* Rendszerkérési [parancs küldése a virtuális gépre](./serial-console-nmi-sysrq.md)
* [Nem maszkolt megszakítás küldése a virtuális géphez](./serial-console-nmi-sysrq.md)
* [A virtuális gép biztonságos újraindítása vagy kényszerített](./serial-console-power-options.md) bekapcsolása


## <a name="next-steps"></a>További lépések
A soros konzol további dokumentációja az oldalsávon érhető el.
- További információ a Linux rendszerű [virtuális gépek soros konzolján](./serial-console-linux.md)érhető el.
- További információ a Windows rendszerű [virtuális gépek soros konzolján](./serial-console-windows.md)érhető el.
