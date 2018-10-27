---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8f7eae06947a40117f3952a0a5624c22df750b34
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165859"
---
* A **vCenter hozzáadása** területen adjon meg egy rövid nevet a vSphere-gazdagép vagy a vCenter-kiszolgáló számára, majd adja meg a kiszolgáló IP-címét vagy teljes tartománynevét. A 443-as portot csak akkor módosítsa, ha a VMware-kiszolgálók úgy vannak konfigurálva, hogy más porton figyeljék a kéréseket. Válassza ki a VMware vCenter- vagy vSphere ESXi-kiszolgálóhoz csatlakoztatni kívánt fiókot. Kattintson az **OK** gombra.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Ha egy olyan fiókkal adja hozzá a VMware vCenter-kiszolgálót vagy VMware vSphere-gazdagépet, amely nem rendelkezik rendszergazdai jogosultsággal a vCenter- vagy a gazdagép-kiszolgálón, győződjön meg róla, hogy a fiókon engedélyezve vannak a következő jogosultságok: Adatközpont, Adattár, Mappa, Gazdagép, Hálózat, Erőforrás, Virtuális gép és vSphere elosztott kapcsoló. Emellett a VMware vCenter-kiszolgálón engedélyeznie kell a Storage-nézetek jogosultságot.
