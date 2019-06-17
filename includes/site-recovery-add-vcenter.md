---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171762"
---
A **vCenter hozzáadása** területen adjon meg egy rövid nevet a vSphere-gazdagép vagy a vCenter-kiszolgáló számára, majd adja meg a kiszolgáló IP-címét vagy teljes tartománynevét. A 443-as portot csak akkor módosítsa, ha a VMware-kiszolgálók úgy vannak konfigurálva, hogy más porton figyeljék a kéréseket. Válassza ki a VMware vCenter- vagy vSphere ESXi-kiszolgálóhoz csatlakoztatni kívánt fiókot. Kattintson az **OK** gombra.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Ha a VMware vCenter-kiszolgáló vagy a VMware vSphere-gazdagép egy olyan fiókkal, amely nem rendelkezik rendszergazdai jogokkal a vCenter- vagy gazdagép-kiszolgálón, győződjön meg arról, hogy a fiók rendelkezik ezeket a jogosultságokat engedélyezve: Adatközpont, adattár, mappa, gazdagép, hálózati, erőforrás, virtuális gép és vSphere elosztott kapcsoló. Emellett a VMware vCenter-kiszolgálón engedélyeznie kell a Storage-nézetek jogosultságot.
