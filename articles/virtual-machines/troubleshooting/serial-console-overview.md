---
title: Azure soros konzol | Microsoft Docs
description: Az Azure soros konzol lehetővé teszi, hogy csatlakozzon a virtuális géphez, ha SSH vagy RDP nem érhető el.
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
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374065"
---
# <a name="azure-serial-console"></a>Azure soros konzol

A Azure Portal soros konzolja hozzáférést biztosít a virtuális gépek (VM-EK) és virtuálisgép-méretezési csoport példányaihoz, amelyek Linux vagy Windows rendszert futtatnak. Ez a soros kapcsolat a virtuális gép vagy a virtuálisgép-méretezési csoport példányának ttyS0 vagy COM1 soros portjához csatlakozik, és a hálózat vagy az operációs rendszer állapotának független hozzáférést biztosít. A soros konzol csak a Azure Portal használatával érhető el, és csak azon felhasználók számára engedélyezett, akik a virtuális gép vagy a virtuálisgép-méretezési csoport számára a közreműködő vagy annál magasabb hozzáférési szerepkörrel rendelkeznek.

A soros konzol a virtuális gépek és a virtuálisgép-méretezési csoport példányai esetében is ugyanúgy működik. Ebben a doc-ban a virtuális gépekre vonatkozó összes említés implicit módon tartalmazza a virtuálisgép-méretezési csoport példányait, hacsak másként nincs megadva.

A soros konzol általánosan elérhető a globális Azure-régiókban és a nyilvános előzetes verzióban Azure Government. Az Azure China Cloud-ban még nem érhető el.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Az Azure soros konzol elérésének előfeltételei
A soros konzol virtuális gépen vagy virtuálisgép-méretezési csoport példányán való eléréséhez a következőkre lesz szüksége:

- A virtuális géphez engedélyezni kell a rendszerindítási diagnosztikát
- A jelszó-hitelesítést használó felhasználói fióknak léteznie kell a virtuális gépen. Létrehozhat egy jelszó-alapú felhasználót a virtuálisgép-hozzáférési bővítmény [jelszó alaphelyzetbe állítása](../extensions/vmaccess.md#reset-password) funkciójával. Válassza a **jelszó alaphelyzetbe állítása** lehetőséget a **támogatás + hibaelhárítás** szakaszban.
- A soros konzolhoz hozzáférő Azure-fióknak rendelkeznie kell [virtuálisgép-közreműködő szerepkörrel](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) mind a virtuális gép, mind a [rendszerindítási diagnosztika](boot-diagnostics.md) Storage-fiókhoz.
- A klasszikus központi telepítések nem támogatottak. A virtuális gép vagy virtuálisgép-méretezési csoport példányának a Azure Resource Manager telepítési modellt kell használnia.

> [!NOTE]
> A soros konzol jelenleg nem kompatibilis egy felügyelt rendszerindítási diagnosztikai Storage-fiókkal. A soros konzol használatához győződjön meg arról, hogy egyéni Storage-fiókot használ.

## <a name="get-started-with-the-serial-console"></a>Ismerkedés a soros konzollal
A virtuális gépek és a virtuálisgép-méretezési csoport soros konzolja csak a Azure Portal érhető el:

### <a name="serial-console-for-virtual-machines"></a>Virtual Machines soros konzolja
A virtuális gépek soros konzolja olyan egyszerű, mint a Azure Portal **támogatás + hibaelhárítás** szakaszának **Serial consoleére** kattintva.
  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. Navigáljon az **összes erőforráshoz** , és válasszon ki egy virtuális gépet. Megnyílik a virtuális gép áttekintő lapja.

  1. Görgessen le a **támogatás + hibaelhárítás** szakaszhoz, és válassza a **Serial Console**lehetőséget. Megnyílik egy új ablaktábla a soros konzollal, és elindítja a kapcsolatát.

     ![Linux soros konzol ablak](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets soros konzolja
A soros konzol a méretezési csoport minden példányán elérhető a virtuálisgép-méretezési csoportokhoz. A **Serial Console** gomb meglátása előtt navigáljon a virtuálisgép-méretezési csoport egyes példányaira. Ha a virtuálisgép-méretezési csoport nem rendelkezik engedélyezve a rendszerindítási diagnosztika szolgáltatással, frissítse a virtuálisgép-méretezési csoport modelljét a rendszerindítási diagnosztika engedélyezéséhez, majd frissítse az összes példányt az új modellre a soros konzol eléréséhez.
  1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

  1. Navigáljon az **összes erőforráshoz** , és válasszon ki egy virtuálisgép-méretezési készletet. Megnyílik a virtuálisgép-méretezési csoport Áttekintés lapja.

  1. A **példányok** navigálása

  1. Virtuálisgép-méretezési csoport példányának kiválasztása

  1. A **támogatás + hibaelhárítás** szakaszban válassza a **Serial Console**lehetőséget. Megnyílik egy új ablaktábla a soros konzollal, és elindítja a kapcsolatát.

     ![Linuxos virtuálisgép-méretezési csoport soros konzolja](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>TLS 1,2 a soros konzolon
A soros konzol a TLS 1,2 végpontok közötti kapcsolatot használja a szolgáltatáson belüli összes kommunikáció biztonságossá tételéhez. A soros konzol egy felhasználó által felügyelt rendszerindítási diagnosztikai fióktól függ, és a TLS 1,2-et külön kell konfigurálni a Storage-fiókhoz. Erre [itt](../../storage/common/transport-layer-security-configure-minimum-version.md)talál útmutatást.

## <a name="advanced-uses-for-serial-console"></a>A soros konzol speciális felhasználási módjai
A konzolhoz való hozzáféréstől eltekintve a következőhöz is használhatja az Azure soros konzolt:
* [Rendszerkérési parancs küldése a virtuális gépre](./serial-console-nmi-sysrq.md)
* [Nem maszkolt megszakítás küldése a virtuális géphez](./serial-console-nmi-sysrq.md)
* [A virtuális gép biztonságos újraindítása vagy kényszerített bekapcsolása](./serial-console-power-options.md)


## <a name="next-steps"></a>További lépések
A soros konzol további dokumentációja az oldalsávon érhető el.
- További információ a Linux rendszerű [virtuális gépek soros konzolján](./serial-console-linux.md)érhető el.
- További információ a Windows rendszerű [virtuális gépek soros konzolján](./serial-console-windows.md)érhető el.
