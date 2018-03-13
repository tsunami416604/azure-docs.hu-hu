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
ms.openlocfilehash: 255e496881f6269d37d3b2d982ba31861458631c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="how-to-configure-cloud-services"></a>Felhőszolgáltatás konfigurálása

Az Azure portálon konfigurálhatja a leggyakrabban használt beállításait egy felhőalapú szolgáltatás. Azok az ügyfeleink, akik közvetlenül szeretnék frissíteni a konfigurációs fájlokat, letölthetik a frissítendő szolgáltatáskonfigurációs fájlt, amelyet módosítás után feltölthetnek, így frissül a felhőszolgáltatás konfigurációja. A rendszer mindkét esetben az összes szerepkörpéldányon elvégzi a konfiguráció módosítását.

Emellett kezelheti a példányait a felhőszolgáltatás szerepköreit, vagy a távoli asztal be őket.

Azure is csak 99,95 % szolgáltatás rendelkezésre állásának biztosításához a konfigurációfrissítések során ha legalább két szerepkör minden egyes szerepkörhöz. Amely lehetővé teszi, hogy egy virtuális gép ügyfél kérelmek feldolgozásához, a másik frissítése közben. További információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Egy felhőalapú szolgáltatás módosítása

Megnyitás után a [Azure-portálon](https://portal.azure.com/), keresse meg a felhőalapú szolgáltatás. Itt azt sok szempontból kezelhető.

![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)

A **beállítások** vagy **összes beállítás** hivatkozásokra kattintva megnyílik **beállítások** ahol módosíthatók a **tulajdonságok**, módosítsa a  **Konfigurációs**, kezelheti a **tanúsítványok**, állítsa be a következőt **riasztási szabályok**, és kezelheti a **felhasználók** ki van-e a felhőalapú szolgáltatás eléréséhez.

![Azure cloud service beállításai](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Kezelheti a vendég operációs rendszer verziója

Alapértelmezés szerint Azure rendszeres időközönként frissíti a vendég operációs rendszer a legújabb támogatott lemezképhez belül a szolgáltatás konfigurációs (.cscfg), a megadott operációsrendszer-termékcsalád például a Windows Server 2016.

Ha egy operációs rendszer adott verziójához van szüksége, beállíthatja **konfigurációs**.

![Az operációs rendszer verzió beállítása](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Egy adott operációs rendszer verziója letiltja az operációs rendszer automatikus kiválasztása frissíti, és lehetővé teszi a felhasználó felelőssége javítását. Gondoskodnia kell arról, hogy a szerepkörpéldányok frissítések kap, vagy az alkalmazás biztonsági rések tehetik közzé.

## <a name="monitoring"></a>Figyelés

A felhőalapú szolgáltatás riasztások adhat hozzá. Kattintson a **beállítások** > **riasztás szabályok** > **riasztás hozzáadása**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Itt állíthatja be a riasztást. Az a **metrika** legördülő mezőben, beállíthat egy riasztás a következő típusú adatokról.

* Lemez sebessége olvasott
* Lemez írási
* A hálózati
* Kimenő hálózati
* Processzorhasználat (%)

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Egy mérték csempe a figyelés konfigurálása

Helyett **beállítások** > **riasztási szabályok**, a metrika csempék egyik kattinthat a **figyelés** szakasz a felhőalapú szolgáltatás.

![Cloud Service Monitoring](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Innen testre a diagram a csempe használt, vagy vegye fel a riasztási szabályt.

## <a name="reboot-reimage-or-remote-desktop"></a>Rendszer újraindítása, a lemezkép-visszaállítási vagy a távoli asztal

A távoli asztal használatával állíthat be a [Azure-portálra (a távoli asztal beállítása)](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), vagy [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Az újraindításhoz lemezkép-visszaállítási vagy egy felhőalapú szolgáltatás, a távoli válassza ki a cloud service-példány.

![Cloud Service Instance](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Majd indítsa el a távoli asztali kapcsolatokhoz, távolról indítsa újra a példányt, vagy távolról újból lemezképet létrehozni (Indítás friss képének) a példányt.

![Cloud Service példány gombok](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Konfigurálja újra a .cscfg

Szükség lehet, hogy engedélyezzék a felhőalapú szolgáltatás keresztül a [szolgáltatás konfigurációs (szolgáltatáskonfigurációs séma)](cloud-services-model-and-package.md#cscfg) fájlt. Először meg kell töltse le a .cscfg fájlban, módosítsa, majd töltse fel azt.

1. Kattintson a a **beállítások** ikon vagy a **összes beállítás** hivatkozásra kattintva nyissa meg **beállítások**.

    ![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Kattintson a **konfigurációs** elemet.

    ![Configuration Blade](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Kattintson a **Download** (Letöltés) gombra.

    ![Letöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Miután frissítette a szolgáltatás konfigurációs fájlja, töltse fel, és a konfigurációs frissítések alkalmazásához:

    ![Feltöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Válassza ki a .cscfg fájlban, és kattintson a **OK**.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage-portal.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).
