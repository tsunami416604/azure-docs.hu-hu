---
title: Állítsa be a forrás-környezetet (VMware az Azure-bA) |} Microsoft Docs
description: A cikkből megtudhatja, hogyan állíthat be a helyszíni környezetben elindítani a VMware virtuális gépek replikálása Azure-bA.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768437"
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Állítsa be a forrás környezetet (az Azure-bA VMware)
> [!div class="op_single_selector"]
> * [VMware – Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Fizikai az Azure-bA](./site-recovery-set-up-physical-to-azure.md)

A cikkből megtudhatja, hogyan állíthat be a forrás, helyszíni környezetben, az Azure-bA a VMware rendszerben futó virtuális gépek replikálásához. A replikációs forgatókönyvben egy a helyi számítógépen, mint a Site Recovery konfigurációs kiszolgáló beállítása kiválasztására szolgáló lépéseket tartalmazza, és automatikusan felderítéséhez a helyszíni virtuális gépek. 

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már rendelkezik:
- [Erőforrások beállítása](tutorial-prepare-azure.md) a a [Azure-portálon](http://portal.azure.com).
- [Állítsa be a helyszíni VMware](tutorial-prepare-on-premises-vmware.md), beleértve egy külön fiókot a automatikus felderítése.



## <a name="choose-your-protection-goals"></a>Védelmi célok megválasztása

1. Az Azure-portálon lépjen a **Recovery Services** tároló panelt, és válassza ki a tároló.
2. A tároló erőforrás menüben Ugrás **bevezetés** > **Site Recovery** > **1. lépés: infrastruktúra előkészítése**  >  **Védelmi cél**.

    ![Célok megválasztása](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. A **védelmi cél**, jelölje be **az Azure-bA**, és válassza a **Igen, amelyen a VMware vSphere Hipervizorra**. Ezután kattintson az **OK** gombra.

    ![Célok megválasztása](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>A konfigurációs kiszolgáló beállítása

Állítsa be a konfigurációs kiszolgáló, egy helyszíni VMware virtuális gép, nyissa meg virtualizációs formátum (OVF) sablonnal. [További](concepts-vmware-to-azure-architecture.md) azokról az az összetevőkről, amelyek a VMware virtuális gép lesz telepítve. 

1. További tudnivalók a [Előfeltételek](how-to-deploy-configuration-server.md#prerequisites) a konfigurációs kiszolgáló környezethez. [Ellenőrizze a kapacitás számok](how-to-deploy-configuration-server.md#capacity-planning) központi telepítéshez.
2. [Töltse le](how-to-deploy-configuration-server.md#download-the-template) és [importálása](how-to-deploy-configuration-server.md#import-the-template-in-vmware) OVF sablon (how-to-telepíteni-konfiguráció-server.md) állítson be egy helyszíni VMware virtuális gép, amelyen a konfigurációs kiszolgáló.
3. A VMware virtuális gép bekapcsolása és [bejegyzéséhez](how-to-deploy-configuration-server.md#register-the-configuration-server) a helyreállítási szolgáltatások tároló.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Adja hozzá a VMware-fiókot a automatikus felderítése

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Csatlakozás a VMware-kiszolgálóhoz

Ahhoz, hogy az Azure Site Recovery számára a helyszíni környezetben futó virtuális gépek felderítése, meg kell kapcsolni a VMware vCenter-kiszolgáló vagy vSphere ESXi-gazdagépek a Site Recovery.

Válassza ki **+ vCenter** elindítani a VMware vCenter-kiszolgáló vagy egy VMware vSphere ESXi-állomáson.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>További lépések
[A célkörnyezet beállítása](./site-recovery-prepare-target-vmware-to-azure.md) az Azure-ban.
