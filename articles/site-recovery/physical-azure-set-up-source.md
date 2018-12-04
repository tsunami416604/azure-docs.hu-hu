---
title: Állítsa be a konfigurációs kiszolgálót a fizikai kiszolgálók az Azure-bA az Azure Site Recovery használata vész-helyreállítási |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan állítható be a helyszíni konfigurációs kiszolgáló, valamint a helyszíni fizikai kiszolgálóknak az Azure-bA.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 04d080d9acd74bea128c0e101165031119900dd1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848408"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Állítsa be a konfigurációs kiszolgáló, valamint fizikai kiszolgálók Azure-bA

Ez a cikk ismerteti, hogyan állítható be a helyszíni környezetben való fizikai kiszolgálók, Windows vagy Linux rendszerű Azure-ba való replikálásának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már rendelkezik:
- A Recovery Services-tároló az a [az Azure portal](http://portal.azure.com "az Azure portal").
- Fizikai számítógép, amelyen a konfigurációs kiszolgáló telepítése.
- Ha, le lett tiltva a TLS 1.0-t a gépen, amelyen a konfigurációs kiszolgálót telepít, ellenőrizze, hogy engedélyezve van-e a TLs 1.2-es és, hogy a .NET-keretrendszer 4.6-os vagy újabb verziója telepítve van a számítógépen (az erős titkosítás le van tiltva). [További információk](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Konfigurációs kiszolgáló minimális követelményei
A következő táblázat felsorolja a minimális hardver-, szoftver, és a konfigurációs kiszolgáló hálózati követelményei.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> A konfigurációs kiszolgáló által a HTTPS-alapú proxykiszolgálók nem támogatottak.

## <a name="choose-your-protection-goals"></a>Védelmi célok megválasztása

1. Az Azure Portalon nyissa meg a **Recovery Services** blade-tárolók, és válassza ki a tárolót.
2. Az a **erőforrás** a tároló menüjében kattintson **első lépések** > **Site Recovery** > **1. lépés: az infrastruktúra előkészítése**   >  **Védelmi cél**.

    ![Célok megválasztása](./media/physical-azure-set-up-source/choose-goals.png)
3. A **védelmi cél**válassza **az Azure-bA** és **nem virtualizált/egyéb**, és kattintson a **OK**.

    ![Célok megválasztása](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

1. A **forrás előkészítése**, ha nem rendelkezik a konfigurációs kiszolgáló, kattintson a **+ konfigurációs kiszolgáló** kattintva felvehet egyet.

  ![A forrás beállítása](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Az a **-kiszolgáló hozzáadása** panelen ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. A Site Recovery egyesített telepítőjének telepítőfájl letöltéséhez.
5. Töltse le a tároló regisztrációs kulcsát. A regisztrációs kulcs egyesített telepítő futtatásakor kell. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/physical-azure-set-up-source/set-source2.png)
6. A számítógépen, mint a konfigurációs kiszolgálót használ, futtassa **Azure Site Recovery egyesített telepítőjének** a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló telepítéséhez.

#### <a name="run-azure-site-recovery-unified-setup"></a>Futtassa az Azure Site Recovery egyesített telepítővel

> [!TIP]
> Konfigurációs kiszolgáló regisztrálása sikertelen lesz, ha a rendszeróra az Ön számítógépének idő engedményt helyi idő több mint öt perc alatt. A rendszeróra szinkronizálása egy [időkiszolgálóval](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) telepítésének megkezdése előtt.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgálót a parancssorból is telepíthető. [További információk](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Gyakori problémák

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>További lépések

Magában foglalja a következő lépésben [a célkörnyezet beállítása](physical-azure-set-up-target.md) az Azure-ban.
