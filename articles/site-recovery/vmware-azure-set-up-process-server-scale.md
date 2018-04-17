---
title: Állítsa be a folyamatkiszolgáló az Azure-ban a VMware virtuális és fizikai kiszolgálók feladat-visszavétel az Azure Site Recovery |} Microsoft Docs
description: A cikkből megtudhatja, hogyan állíthat be a folyamatkiszolgáló, Azure, a feladat-visszavételi Azure virtuális gépek VMware.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 7bbe690e749680edde08facadf6d5910d7896f7e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>A feladat-visszavételre az Azure-ban folyamat kiszolgáló beállítása

Miután a rendszer átadja a VMware virtuális gépek vagy fizikai kiszolgálók Azure használatával [Site Recovery](site-recovery-overview.md), akkor is feladatátvétel céljaként a helyszíni hely esetén újra működik. Ahhoz, hogy a feladat-visszavételt, akkor be kell állítania egy ideiglenes folyamatkiszolgáló az Azure az Azure-ból a helyszíni replikáció kezelésére. Ez a virtuális gép feladat-visszavétel befejezése után törölheti.

## <a name="before-you-start"></a>Előkészületek

További információ a [ismételt védelem](vmware-azure-reprotect.md) és [feladat-visszavétel](vmware-azure-failback.md) folyamat.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-in-azure"></a>Az Azure-ban folyamat-kiszolgáló központi telepítése

1. A tárolóban lévő > **Site Recovery-infrastruktúra**> **kezelése** > **konfigurációs kiszolgálók**, válassza ki a konfigurációs kiszolgálót.
2. A kiszolgálói oldalon kattintson **+ folyamatkiszolgáló**
3. A **Hozzáadás folyamatkiszolgáló** lap, és válassza a folyamatkiszolgáló az Azure-ban telepítéséhez.
4. Adja meg az Azure beállításai, többek között a feladatátvétel, egy erőforráscsoport, a feladatátvételi és a virtuális hálózatot, amelyben az Azure virtuális gépek találhatók használható az Azure-régió használt előfizetés. Ha több Azure-hálózatok, a folyamatkiszolgáló egyes szüksége.
5. A **kiszolgálónév**, **felhasználónév**, és **jelszó**, adjon meg egy nevet a folyamatkiszolgáló és a hitelesítő adatokat, amelyek a kiszolgálón rendszergazdai jogosultságokkal hozzá lesz rendelve.
6. Adjon meg egy tárfiókot, a kiszolgáló virtuális gépek lemezei, amelyben a folyamatkiszolgáló VM található az alhálózat és a kiszolgáló IP-címe, amely hozzá lesz rendelve a virtuális gép indításakor használandó.
7. Kattintson a **OK** gombra kattintva indítsa el a folyamatkiszolgáló VM telepítése.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>(Az Azure-ban futó) a folyamatkiszolgáló a konfigurációs kiszolgálóhoz (a helyszínen fut) regisztrálása

Miután a folyamatkiszolgáló virtuális gép fut, kell való regisztrálása a helyszíni konfigurációs kiszolgáló, az alábbiak szerint:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


