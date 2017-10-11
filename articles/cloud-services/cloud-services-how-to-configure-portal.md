---
title: "A felhőszolgáltatás (portál) konfigurálása |} Microsoft Docs"
description: "Útmutató az Azure felhőszolgáltatások konfigurálása. Ismerje meg, a felhőalapú szolgáltatás konfigurációja frissítése, és konfigurálja a távelérést a szerepkörpéldányok. Ezekben a példákban az Azure-portálon."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: adegeo
ms.openlocfilehash: a7e891d05ffe4cc2b4f68dce072a81499cc6de80
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-configure-cloud-services"></a>Felhőszolgáltatás konfigurálása
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-configure-portal.md)
> * [klasszikus Azure portál](cloud-services-how-to-configure.md)
>
>

Az Azure portálon konfigurálhatja a leggyakrabban használt beállításait egy felhőalapú szolgáltatás. Azok az ügyfeleink, akik közvetlenül szeretnék frissíteni a konfigurációs fájlokat, letölthetik a frissítendő szolgáltatáskonfigurációs fájlt, amelyet módosítás után feltölthetnek, így frissül a felhőszolgáltatás konfigurációja. A rendszer mindkét esetben az összes szerepkörpéldányon elvégzi a konfiguráció módosítását.

Emellett kezelheti a példányait a felhőszolgáltatás szerepköreit, vagy a távoli asztal be őket.

Azure is csak 99,95 % szolgáltatás rendelkezésre állásának biztosításához a konfigurációfrissítések során ha legalább két szerepkör minden egyes szerepkörhöz. Amely lehetővé teszi, hogy egy virtuális gép ügyfél kérelmek feldolgozásához, a másik frissítése közben. További információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Egy felhőalapú szolgáltatás módosítása
Megnyitás után a [Azure-portálon](https://portal.azure.com/), keresse meg a felhőalapú szolgáltatás. Itt azt sok szempontból kezelhető.

![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)

A **beállítások** vagy **összes beállítás** hivatkozásokra kattintva megnyílik a **beállítások** panel, amelyen módosíthatja a **tulajdonságok**, módosítsa a **konfigurációs**, kezelése a **tanúsítványok**, telepítő **riasztási szabályok**, és kezelése a **felhasználók** ki van-e a felhőalapú szolgáltatás eléréséhez.

![Azure cloud service beállítások panel](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Kezelheti a vendég operációs rendszer verziója

Alapértelmezés szerint Azure rendszeres időközönként frissíti a vendég operációs rendszer a legújabb támogatott lemezképhez belül a szolgáltatás konfigurációs (.cscfg), a megadott operációsrendszer-termékcsalád például a Windows Server 2016.

Ha egy operációs rendszer adott verziójához van szüksége, állíthatja be, a **konfigurációs** panelen.

![Az operációs rendszer verzió beállítása](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)


>[!IMPORTANT]
> Egy adott operációs rendszer verziója letiltja az operációs rendszer automatikus kiválasztása frissíti, és lehetővé teszi a felhasználó felelőssége javítását. Gondoskodnia kell arról, hogy a szerepkörpéldányok frissítések kap, vagy az alkalmazás biztonsági rések tehetik közzé.

## <a name="monitoring"></a>Figyelés
A felhőalapú szolgáltatás riasztások adhat hozzá. Kattintson a **beállítások** > **riasztás szabályok** > **riasztás hozzáadása**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Itt beállíthatja egy riasztást. Az a **metrika** legördülő listája, beállíthatja a következő típusú adatok egy riasztást.

* Lemez sebessége olvasott
* Lemez írási
* A hálózati
* Kimenő hálózati
* Processzorhasználat (%)

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Egy mérték csempe a figyelés konfigurálása
Helyett **beállítások** > **riasztási szabályok**, a metrika csempék egyik kattinthat a **figyelés** szakasza a **felhőalapú szolgáltatás** panelen.

![A felhőalapú szolgáltatás figyelése](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Innen testre a diagram a csempe használt, vagy vegye fel a riasztási szabályt.

## <a name="reboot-reimage-or-remote-desktop"></a>Rendszer újraindítása, a lemezkép-visszaállítási vagy a távoli asztal
Jelenleg nem lehet konfigurálni a távoli asztal használatával a **Azure-portálon**. Azonban beállíthatja azt keresztül a [a klasszikus Azure portálon](cloud-services-role-enable-remote-desktop.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), illetve [Visual Studio](../vs-azure-tools-remote-desktop-roles.md).

Első lépésként kattintson az a cloud service-példány.

![Cloud Service-példány](./media/cloud-services-how-to-configure-portal/cs-instance.png)

A panelen, megnyílik a távoli asztali kapcsolat létrehozására, távolról indítsa újra a példányt, vagy távolról újból lemezképet létrehozni (Indítás friss képének) a példányt.

![Cloud Service példány gombok](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Konfigurálja újra a .cscfg
Szükség lehet, hogy engedélyezzék a felhőalapú szolgáltatás keresztül a [szolgáltatás konfigurációs (szolgáltatáskonfigurációs séma)](cloud-services-model-and-package.md#cscfg) fájlt. Először meg kell töltse le a .cscfg fájlban, módosítsa, majd töltse fel azt.

1. Kattintson a a **beállítások** ikon vagy a **összes beállítás** hivatkozásra kattintva nyissa meg a **beállítások** panelen.

    ![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Kattintson a **konfigurációs** elemet.

    ![Konfigurációs panel](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Kattintson a **letöltése** gombra.

    ![Letöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Miután frissítette a szolgáltatás konfigurációs fájlja, töltse fel, és a konfigurációs frissítések alkalmazásához:

    ![Feltöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Válassza ki a .cscfg fájlban, és kattintson a **OK**.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage-portal.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).
