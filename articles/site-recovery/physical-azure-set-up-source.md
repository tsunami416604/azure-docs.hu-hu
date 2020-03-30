---
title: A konfigurációs kiszolgáló beállítása az Azure-ba irányuló fizikai kiszolgálók vészhelyreállítási állapotának helyreállításához az Azure Site Recovery használatával | Microsoft Dokumentumok"
description: Ez a cikk ismerteti, hogyan állíthatja be a helyszíni konfigurációs kiszolgáló a helyszíni fizikai kiszolgálók azure-ba vész-helyreállítási.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257874"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>A konfigurációs kiszolgáló beállítása az Azure-ba irányuló fizikai kiszolgálók vészhelyreállítása érdekében

Ez a cikk bemutatja, hogyan állíthatja be a helyszíni környezetben a Windows vagy Linux operációs rendszert futtató fizikai kiszolgálók Azure-ba replikálásának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy már rendelkezik:
- A Recovery Services-tároló az [Azure Portalon.](https://portal.azure.com "Azure portál")
- Olyan fizikai számítógép, amelyre a konfigurációs kiszolgálót telepíteni szeretné.
- Ha letiltotta a TLS 1.0-s verzióját azon a számítógépen, amelyre a konfigurációs kiszolgálót telepíti, győződjön meg arról, hogy a TLs 1.2 engedélyezve van, és hogy a . [További információ](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>A konfigurációs kiszolgáló minimális követelményei
Az alábbi táblázat a konfigurációs kiszolgáló minimális hardver-, szoftver- és hálózati követelményeit sorolja fel.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> A konfigurációs kiszolgáló nem támogatja a HTTPS-alapú proxykiszolgálókat.

## <a name="choose-your-protection-goals"></a>Válaszd ki a védelmi célokat

1. Az Azure Portalon nyissa meg a **Recovery Services-tárolók** panelt, és válassza ki a trezort.
2. **Protection goal**A tároló **Erőforrás** menüjében kattintson az Első **lépések** > **hely-helyreállítási** > lépés**1.** > 

    ![Célok megválasztása](./media/physical-azure-set-up-source/choose-goals.png)
3. A **Védelem cél ban**válassza az Azure **és** a **Nem virtualizált/egyéb**lehetőséget, majd kattintson **az OK gombra.**

    ![Célok megválasztása](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

1. Ha **Prepare source**nincs konfigurációs kiszolgálója, a **+Configuration server** elemre kattintva adjon hozzá egyet.

   ![A forrás beállítása](./media/physical-azure-set-up-source/plus-config-srv.png)
2. A **Kiszolgáló hozzáadása** panelen ellenőrizze, hogy a **konfigurációs kiszolgáló** **kiszolgáló típusban jelenik-e**meg.
4. Töltse le a Site Recovery egyesített telepítési telepítőfájlját.
5. Töltse le a tároló regisztrációs kulcsát. Az Egyesített telepítés futtatásakor a regisztrációs kulcsra van szükség. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/physical-azure-set-up-source/set-source2.png)
6. A konfigurációs kiszolgálóként használt számítógépen futtassa az **Azure Site Recovery unified setup telepítőjét** a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló telepítéséhez.

#### <a name="run-azure-site-recovery-unified-setup"></a>Az Azure Site Recovery egységes telepítésének futtatása

> [!TIP]
> A konfigurációs kiszolgáló regisztrációja sikertelen, ha a számítógép rendszerórájának ideje több mint öt perccel eltér a helyi időórától. A telepítés megkezdése előtt szinkronizálja a rendszerórát egy [időkiszolgálóval.](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló parancssorból telepíthető. [További információ](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Gyakori problémák

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>További lépések

A következő lépés [a célkörnyezet beállítása az](physical-azure-set-up-target.md) Azure-ban.
