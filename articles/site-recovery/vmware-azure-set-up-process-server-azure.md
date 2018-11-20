---
title: Az Azure-ban folyamatkiszolgáló beállítása VMware virtuális gépek és fizikai kiszolgálók feladat-visszavétel az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állítható be a folyamatkiszolgáló az Azure-ban feladat-visszavétel VMware virtuális gépek Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/19/2018
ms.author: ramamill
ms.openlocfilehash: 6bdbb60da2a135b5f53f6119377c703d88df6ed9
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975345"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Állítsa be a folyamatkiszolgáló, az Azure-ban feladat-visszavételhez

Miután átadja a feladatokat a VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA [Site Recovery](site-recovery-overview.md), visszaadhatja a azokat a helyszíni hely és megint válik. Annak érdekében, hogy a feladat-visszavételt, akkor be kell állítania egy ideiglenes folyamatkiszolgáló az Azure-ban, kezelni a replikáció az Azure-ból a helyszíni. Ez a virtuális gép feladat-visszavétel befejezése után törölheti.

## <a name="before-you-start"></a>Előkészületek

Tudjon meg többet a [ismételt védelem](vmware-azure-reprotect.md) és [feladat-visszavétel](vmware-azure-failback.md) folyamat.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Az Azure-ban folyamatkiszolgáló üzembe helyezése

1. A tárolóban > **Site Recovery-infrastruktúra**> **kezelés** > **konfigurációs kiszolgálók**, válassza ki a konfigurációs kiszolgálót.
2. A kiszolgáló lapján kattintson a **+ folyamatkiszolgáló**
3. A **Hozzáadás folyamatkiszolgáló** lapot, és válassza a folyamatkiszolgáló az Azure-beli üzembe helyezéséhez.
4. Adja meg az Azure beállításait, például feladatátvételi, egy erőforráscsoportot és az Azure-régiót használ a feladatátvétel és a virtuális hálózatot, amelyben az Azure virtuális gépek találhatók használt előfizetés. Ha több Azure-hálózatok használta, a folyamatkiszolgáló egyes kell.

  ![Folyamat kiszolgáló a gyűjteményelem hozzáadása](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. A **kiszolgálónév**, **felhasználónév**, és **jelszó**, adjon meg egy nevet a folyamatkiszolgáló és a hitelesítő adatokat, amelyek a kiszolgálón rendszergazdai engedélyekkel hozzá lesz rendelve.
5. A kiszolgáló Virtuálisgép-lemezek az alhálózatot, amelyben kerülnek a folyamatkiszolgáló virtuális gép és a kiszolgáló IP-címe, amely hozzá lesz rendelve a virtuális gép indításakor használandó tárfiókot adjon meg.
6. Kattintson a **OK** gombra kattintva indítsa el a folyamatkiszolgáló virtuális gép üzembe helyezése.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>A folyamatkiszolgáló (az Azure-ban futó) (a helyszínen futó) konfigurációs kiszolgáló regisztrálása

A folyamatkiszolgáló virtuális gép működik-e, miután kell való regisztrálása a helyszíni konfigurációs kiszolgálót, a következő:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


