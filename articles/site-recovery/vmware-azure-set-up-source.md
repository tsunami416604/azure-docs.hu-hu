---
title: A forráskörnyezet beállítása VMware-ről az Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet VMware virtuális gépek replikálásához az Azure-bA az Azure Site Recovery a helyszíni környezet beállítása.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 1380c1bc820a815fae317a86fcd0ee4f46dd9aa5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952654"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>A forráskörnyezet beállítása VMware-ről az Azure-bA

Ez a cikk ismerteti, hogyan állítható be a forráskörnyezethez a helyszíni VMware virtuális gépek replikálásához az Azure-bA. A replikációs forgatókönyv, egy a helyszíni gépen a Site Recovery konfigurációs kiszolgálóként beállítása kiválasztására szolgáló lépéseket tartalmazza, és automatikusan felderítése a helyszíni virtuális gépeket. 

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már:
- [Erőforrások beállítása](tutorial-prepare-azure.md) a a [az Azure portal](http://portal.azure.com).
- [Állítsa be a helyszíni VMware](vmware-azure-tutorial-prepare-on-premises.md), beleértve az automatikus felderítéshez egy dedikált fiókot.



## <a name="choose-your-protection-goals"></a>Védelmi célok megválasztása

1. Az Azure Portalon nyissa meg a **Recovery Services** panel tárolóját, és válassza ki a tárolót.
2. A tár erőforrás menüben nyissa meg **bevezetés** > **Site Recovery** > **1. lépés: az infrastruktúra előkészítése**  >  **Védelmi cél**.

    ![Célok megválasztása](./media/vmware-azure-set-up-source/choose-goals.png)
3. A **védelmi cél**válassza **az Azure-bA**, és válassza a **Igen, a VMware vSphere Hipervizorral**. Ezután kattintson az **OK** gombra.

    ![Célok megválasztása](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>A konfigurációs kiszolgáló beállítása

Állítható be a konfigurációs kiszolgálót egy helyszíni VMware virtuális gép egy Open Virtualization alkalmazás (OVA) sablon segítségével. [További](concepts-vmware-to-azure-architecture.md) kapcsolatban az összetevőket, amelyek a VMware virtuális gép lesz telepítve.

1. További információ a [Előfeltételek](vmware-azure-deploy-configuration-server.md#prerequisites) server központi telepítésére vonatkozóan.
2. [Ellenőrizze a kapacitás számok](vmware-azure-deploy-configuration-server.md#capacity-planning) üzembe helyezéshez.
3. [Töltse le](vmware-azure-deploy-configuration-server.md#download-the-template) és [importálása](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) az OVA sablon egy helyszíni VMware virtuális gép, amely futtatja a konfigurációs kiszolgáló beállításához. Az engedélyt a sablonhoz megadott értékelési engedélyhez és érvényes 180 napig. POST ennek az időtartamnak kell határidődátumával engedéllyel rendelkező a windows aktiválása.
4. A VMware virtuális gép bekapcsolása és [regisztrálja](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) a Recovery Services-tároló.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Adja hozzá a VMware-fiók automatikus felderítéshez

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>A VMware-kiszolgálóhoz való csatlakozáshoz

Ahhoz, hogy az Azure Site Recovery a helyszíni környezetben futó virtuális gépek felderítése, a Site Recovery a VMware vCenter-kiszolgáló vagy vSphere ESXi-gazdagépek csatlakoznia kell.

Válassza ki **+ vCenter** VMware vCenter-kiszolgáló vagy a VMware vSphere ESXi-gazdagéphez csatlakozó elindításához.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>További lépések
[A célkörnyezet beállítása](./vmware-azure-set-up-target.md) az Azure-ban.
