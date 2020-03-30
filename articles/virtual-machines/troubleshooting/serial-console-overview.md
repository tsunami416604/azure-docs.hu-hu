---
title: Azure soros konzol | Microsoft dokumentumok
description: Az Azure serial console lehetővé teszi, hogy csatlakozzon a virtuális géphez, ha SSH vagy RDP nem érhetők el.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267013"
---
# <a name="azure-serial-console"></a>Azure soros konzol

Az Azure Portalon található soros konzol hozzáférést biztosít a virtuális gépek (VM-ek) és a Linux vagy Windows rendszertelen virtuálisgép-méretezési csoport példányai szöveges alapú konzoljához. Ez a soros kapcsolat a virtuális gép vagy a virtuálisgép-méretezési csoport példányának ttyS0 vagy COM1 soros portjához csatlakozik, és a hálózattól vagy az operációs rendszer állapotától független hozzáférést biztosít. A soros konzol csak az Azure Portalon keresztül érhető el, és csak azok a felhasználók számára engedélyezett, akik a virtuális gép vagy a virtuális gép méretezési készlethez tartozó közreműködői vagy magasabb szintű hozzáférési szerepkörrel rendelkeznek.

Soros konzol működik ugyanúgy a virtuális gépek és a virtuális gép méretezési készlet példányok. Ebben a dokumentumban a virtuális gépek minden említése implicit módon tartalmazza a virtuális gép méretezési példányait, kivéve, ha másként van feltüntetve.

> [!NOTE]
> A soros konzol általánosan elérhető a globális Azure-régiókban és az Azure Government nyilvános előzetes verzióban. Még nem érhető el az Azure China felhőben.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Az Azure soros konzol elérésének előfeltételei
A virtuális gép vagy a virtuálisgép-méretezési csoport példányának soros konzoljának eléréséhez a következőkre lesz szüksége:

- A rendszerindítási diagnosztikát engedélyezni kell a virtuális géphez
- A jelszó-hitelesítést használó felhasználói fióknak léteznie kell a virtuális gépen belül. Létrehozhat egy jelszóalapú felhasználót a virtuális gép hozzáférési bővítmény [jelszó-visszaállítási](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funkciójával. Válassza a **Jelszó alaphelyzetbe állítása** lehetőséget a **Támogatás + hibaelhárítás** szakaszban.
- A soros konzoleléréséhez hozzáférő Azure-fióknak [virtuálisgép-közreműködői szerepkört](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) kell használnia mind a virtuális géphez, mind a [rendszerindítási diagnosztikai](boot-diagnostics.md) tárfiókhoz.

> [!NOTE]
> Klasszikus központi telepítések nem támogatottak. A virtuális gép vagy a virtuálisgép méretezési csoport példányának az Azure Resource Manager telepítési modelljét kell használnia.

## <a name="get-started-with-the-serial-console"></a>Első lépések a soros konzollal
A virtuális gépek soros konzolja és a virtuálisgép-méretezési készlet csak az Azure Portalon keresztül érhető el:

### <a name="serial-console-for-virtual-machines"></a>Soros konzol virtuális gépekhez
A virtuális gépeksoros konzolja olyan egyszerű, mint a **Soros konzolra** kattintani az Azure Portal **Support + troubleshooting** szakaszában.
  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. Nyissa meg a **Minden erőforrást,** és válasszon ki egy virtuális gépet. Megnyílik a virtuális gép áttekintő lapja.

  1. Görgessen le a **Támogatás + hibaelhárítás** szakaszhoz, és válassza a **Soros konzol**lehetőséget. Megnyílik egy új ablaktábla a soros konzollal, és elindítja a kapcsolatot.

     ![Linux soros konzol ablak](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Soros konzol a virtuálisgép-méretezési készletekhez
Soros konzol érhető el a virtuális gép méretezési készletek, elérhető minden példány a méretezési csoporton belül. A **Soros konzol** gomb láttához a virtuálisgép-méretezési csoport egyedi példányát kell megnyitnunk. Ha a virtuálisgép-méretezési csoport nem rendelkezik rendszerindítási diagnosztika engedélyezve, győződjön meg arról, hogy frissítse a virtuálisgép méretezési csoport modell, hogy a rendszerindítási diagnosztika, majd frissítse az összes példányt az új modell elérése érdekében a soros konzol.
  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. Nyissa meg a **Minden erőforrást,** és válasszon egy virtuálisgép-méretezési készletet. Megnyílik a virtuálisgép-méretezési csoport áttekintő lapja.

  1. Navigálás **a példányokra**

  1. Virtuálisgép-méretezési csoport példányának kiválasztása

  1. A **Támogatás + hibaelhárítás** csoportban válassza a **Soros konzol**lehetőséget. Megnyílik egy új ablaktábla a soros konzollal, és elindítja a kapcsolatot.

     ![Linux virtuálisgép-méretezési készlet Soros konzol](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>A soros konzol speciális használatai
A virtuális gépkonzolhoz való hozzáférés mellett az Azure Serial Console-t is használhatja a következőkhöz:
* [Rendszerkérelem-parancs](./serial-console-nmi-sysrq.md) küldése a virtuális gépnek
* Nem [maszkelfedhető megszakítás](./serial-console-nmi-sysrq.md) küldése a virtuális gépnek
* A virtuális gép kecses [újraindítása vagy erőteljes teljesítmény-kerékpározása](./serial-console-power-options.md)


## <a name="next-steps"></a>További lépések
A soros konzol további dokumentációja az oldalsávon érhető el.
- További információ a [Soros konzol Linuxos virtuális gépekhez.](./serial-console-linux.md)
- További információ a [Soros konzol Windows virtuális gépekhez.](./serial-console-windows.md)
