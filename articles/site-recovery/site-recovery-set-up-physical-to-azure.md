---
title: "A forrás (fizikai kiszolgálók Azure-bA) környezet beállítása |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan állíthat be a helyszíni környezet az Azure Windows vagy Linux operációs rendszert futtató fizikai kiszolgálók replikálást indítani."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: d242954eb62a0d7325cc4222a54f2581967fdc19
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Állítsa be a forrás-környezetet (az Azure-bA a fizikai kiszolgáló)
> [!div class="op_single_selector"]
> * [VMware – Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Fizikai az Azure-bA](./site-recovery-set-up-physical-to-azure.md)

A cikkből megtudhatja, hogyan állíthat be a helyszíni környezet az Azure Windows vagy Linux operációs rendszert futtató fizikai kiszolgálók replikálást indítani.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már rendelkezik:
1. A tároló a Recovery Services a [Azure-portálon](http://portal.azure.com "Azure-portálon").
3. Egy fizikai számítógépen, amelyre a konfigurációs kiszolgáló telepítése.

### <a name="configuration-server-minimum-requirements"></a>Konfigurációs kiszolgáló minimális követelményeknek
A következő táblázat felsorolja a minimális hardver-, szoftver, és hálózati követelményei a konfigurációs kiszolgáló.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> A konfigurációs kiszolgáló által a HTTPS-alapú proxykiszolgálókat nem támogatottak.

## <a name="choose-your-protection-goals"></a>Védelmi célok megválasztása

1. Az Azure-portálon lépjen a **Recovery Services** -tárolók panelt, és válassza ki a tároló.
2. Az a **erőforrás** a tároló menüjében kattintson **bevezetés** > **Site Recovery** > **1. lépés: infrastruktúra előkészítése**   >  **Védelmi cél**.

    ![Célok megválasztása](./media/site-recovery-set-up-physical-to-azure/choose-goals.png)
3. A **védelmi cél**, jelölje be **az Azure-bA** és **nem virtualizált vagy egyéb**, és kattintson a **OK**.

    ![Célok megválasztása](./media/site-recovery-set-up-physical-to-azure/physical-protection-goal.PNG)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

1. A **forrás előkészítése**, ha nem rendelkezik a konfigurációs kiszolgáló, kattintson a **+ konfigurációs kiszolgáló** kattintva felvehet egyet.

  ![A forrás beállítása](./media/site-recovery-set-up-physical-to-azure/plus-config-srv.png)
2. Az a **kiszolgáló hozzáadása** panelen ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. Töltse le a Site Recovery az egységes telepítő telepítőfájlját.
5. Töltse le a tárolóregisztrációs kulcsot. Az egységes telepítő futtatásakor a regisztrációs kulcsot kell. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/site-recovery-set-up-physical-to-azure/set-source2.png)
6. A számítógépen, mint a konfigurációs kiszolgálót használ, futtassa **Azure Site Recovery az egységes telepítő** a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló telepítése.

#### <a name="run-azure-site-recovery-unified-setup"></a>Futtassa az Azure Site Recovery egyesített telepítő

> [!TIP]
> Konfigurációs kiszolgáló regisztrálása sikertelen lesz, ha a számítógép rendszer órája a ideje ki a helyi idő több mint öt perc. A rendszer szinkronizálást, és egy [kiszolgálót](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) a telepítés megkezdése előtt.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló telepíthető a parancssorból. További információkért lásd: [telepítése konfigurációs kiszolgáló parancssori eszközökkel](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Gyakori problémák

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>További lépések

Magában foglalja a következő lépés [a célkörnyezet beállítása](./site-recovery-prepare-target-physical-to-azure.md) az Azure-ban.
