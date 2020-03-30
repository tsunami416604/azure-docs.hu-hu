---
title: VMware/fizikai feladat-visszavétel folyamatkiszolgáló beállítása az Azure Site Recovery szolgáltatásban
description: Ez a cikk ismerteti, hogyan állíthat be egy folyamatkiszolgálót az Azure-ban, a feladat-visszavételi Azure virtuális gépek a VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083956"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Azure-beli folyamatkiszolgálók beállítása feladat-visszavételhez

Miután a Site Recovery használatával átadta a vmware virtuális gépeket vagy a fizikai kiszolgálókat az [Azure-ba,](site-recovery-overview.md)a rendszer visszaviheti őket a helyszíni helyre, amikor az újra működik. A visszavételhez be kell állítania egy ideiglenes folyamatkiszolgálót az Azure-ban, hogy kezelni tudja az Azure-ból a helyszíni replikációt. Törölheti ezt a virtuális gép után feladat-visszavétel befejeződött.

## <a name="before-you-start"></a>Előkészületek

További információ az [újravédelmi](vmware-azure-reprotect.md) és [feladat-visszavételi](vmware-azure-failback.md) folyamatról.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Folyamatkiszolgáló üzembe helyezése az Azure-ban

1. A tárolóban > **helyhelyreállítási infrastruktúra**> **Manage** > **konfigurációs kiszolgálóinak kezelése konfigurációs kiszolgálók,** válassza ki a konfigurációs kiszolgálót.
2. A kiszolgáló lapon kattintson a **+ Process server (Kiszolgáló feldolgozása) elemre.**
3. A **Folyamatkiszolgáló hozzáadása** lapon válassza ki a folyamatkiszolgáló azure-beli telepítését.
4. Adja meg az Azure-beállításokat, beleértve a feladatátvételhez használt előfizetést, egy erőforráscsoportot, a feladatátvételhez használt Azure-régiót és azt a virtuális hálózatot, amelyben az Azure virtuális gépek találhatók. Ha több Azure-hálózatot használt, mindegyikben szüksége van egy folyamatkiszolgálóra.

   ![Folyamatkiszolgáló-gyűjtemény elemének hozzáadása](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. A **Kiszolgálónév**, **Felhasználónév**és **Jelszó csoportban**adja meg a folyamatkiszolgáló nevét és a kiszolgálón rendszergazdai engedélyekkel rendelkező hitelesítő adatokat.
5. Adja meg a kiszolgáló virtuálisgép-lemezeihez használandó tárfiókot, azt az alhálózatot, amelyben a folyamatkiszolgáló virtuális gépe található, és a kiszolgáló IP-címét, amelyet a virtuális gép indításakor hozzárendel.
6. Kattintson **az OK** gombra a folyamatkiszolgáló virtuális gépének telepítéséhez. A folyamatkiszolgáló Standard_A8_v2 termékváltozatra lesz telepítve. Győződjön meg arról, hogy ez a virtuális gép termékváltozat érhető el az előfizetéshez.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>A folyamatkiszolgáló regisztrálása (az Azure-ban futó) egy konfigurációs kiszolgálóra (helyszíni futtatás)

Miután a folyamatkiszolgáló virtuális gépe már működik, regisztrálnia kell azt a helyszíni konfigurációs kiszolgálón, az alábbiak szerint:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


