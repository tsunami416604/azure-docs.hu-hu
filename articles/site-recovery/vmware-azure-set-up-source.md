---
title: A forrás környezet beállítása a VMware Azure replikáció az Azure Site Recovery szolgáltatással |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan állíthat be a helyszíni környezetben VMware virtuális gépek replikálása az Azure-bA az Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 01b0717660265b28d4ea7d804a761e7e425c997c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319600"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>A forrás környezet beállítása a VMware Azure replikáció

Ez a cikk ismerteti, hogyan állíthat be a forráskörnyezettel helyszíni VMware virtuális gépek replikálása az Azure-bA. A replikációs forgatókönyvben egy a helyi számítógépen, mint a Site Recovery konfigurációs kiszolgáló beállítása kiválasztására szolgáló lépéseket tartalmazza, és automatikusan felderítéséhez a helyszíni virtuális gépek. 

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már rendelkezik:
- [Erőforrások beállítása](tutorial-prepare-azure.md) a a [Azure-portálon](http://portal.azure.com).
- [Állítsa be a helyszíni VMware](vmware-azure-tutorial-prepare-on-premises.md), beleértve egy külön fiókot a automatikus felderítése.



## <a name="choose-your-protection-goals"></a>Védelmi célok megválasztása

1. Az Azure-portálon lépjen a **Recovery Services** tároló panelt, és válassza ki a tároló.
2. A tároló erőforrás menüben Ugrás **bevezetés** > **Site Recovery** > **1. lépés: infrastruktúra előkészítése**  >  **Védelmi cél**.

    ![Célok megválasztása](./media/vmware-azure-set-up-source/choose-goals.png)
3. A **védelmi cél**, jelölje be **az Azure-bA**, és válassza a **Igen, amelyen a VMware vSphere Hipervizorra**. Ezután kattintson az **OK** gombra.

    ![Célok megválasztása](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>A konfigurációs kiszolgáló beállítása

Állíthat be a konfigurációs kiszolgáló, egy helyszíni VMware virtuális gép egy megnyitott virtualizálási alkalmazás (petesejtek) sablon segítségével. [További](concepts-vmware-to-azure-architecture.md) azokról az az összetevőkről, amelyek a VMware virtuális gép lesz telepítve.

1. További tudnivalók a [Előfeltételek](vmware-azure-deploy-configuration-server.md#prerequisites) a konfigurációs kiszolgáló környezethez.
2. [Ellenőrizze a kapacitás számok](vmware-azure-deploy-configuration-server.md#capacity-planning) központi telepítéshez.
3. [Töltse le](vmware-azure-deploy-configuration-server.md#download-the-template) és [importálása](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) petesejtek sablon állítson be egy helyszíni VMware virtuális gép, amelyen a konfigurációs kiszolgáló. Az engedélyt a sablonhoz megadott értékelési engedélyhez és érvényes 180 napig tart. POST ezen időszak alatt ügyféligények határidődátumával engedéllyel rendelkező a windows aktiválása.
4. A VMware virtuális gép bekapcsolása és [bejegyzéséhez](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) a helyreállítási szolgáltatások tároló.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Adja hozzá a VMware-fiókot a automatikus felderítése

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Csatlakozás a VMware-kiszolgálóhoz

Ahhoz, hogy az Azure Site Recovery számára a helyszíni környezetben futó virtuális gépek felderítése, meg kell kapcsolni a VMware vCenter-kiszolgáló vagy vSphere ESXi-gazdagépek a Site Recovery.

Válassza ki **+ vCenter** elindítani a VMware vCenter-kiszolgáló vagy egy VMware vSphere ESXi-állomáson.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Gyakori problémák
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>További lépések
[A célkörnyezet beállítása](./vmware-azure-set-up-target.md) az Azure-ban.
