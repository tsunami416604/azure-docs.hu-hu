---
title: Felhőalapú szolgáltatás konfigurálása (portál) | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Cloud Servicest az Azure-ban. Ismerje meg, hogyan frissítheti a Cloud Service-konfigurációt, és hogyan konfigurálhat távoli hozzáférést a szerepkör-példányokhoz. Ezek a példák a Azure Portal használják.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: 554d3e465b42ca889ba03565e87193f80e89ed1d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75361007"
---
# <a name="how-to-configure-cloud-services"></a>Cloud Services konfigurálása

A felhőalapú szolgáltatás leggyakrabban használt beállításait a Azure Portal is konfigurálhatja. Azok az ügyfeleink, akik közvetlenül szeretnék frissíteni a konfigurációs fájlokat, letölthetik a frissítendő szolgáltatáskonfigurációs fájlt, amelyet módosítás után feltölthetnek, így frissül a felhőszolgáltatás konfigurációja. A rendszer mindkét esetben az összes szerepkörpéldányon elvégzi a konfiguráció módosítását.

A felhőalapú szolgáltatás szerepköreinek példányait vagy a Távoli asztalt is kezelheti.

Az Azure a konfigurációs frissítések során csak a 99,95%-os rendelkezésre állást biztosíthatja, ha minden szerepkörhöz legalább két szerepkör-példány tartozik. Ez lehetővé teszi, hogy az egyik virtuális gép feldolgozza az ügyfelek kérelmeit, miközben a másik frissítése folyamatban van. További információ: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Felhőalapú szolgáltatás módosítása

A [Azure Portal](https://portal.azure.com/)megnyitása után navigáljon a felhőalapú szolgáltatáshoz. Itt számos aspektusát kezelheti.

![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)

A **Beállítások** vagy **az összes beállítás** hivatkozás olyan **beállításokat** nyit meg, ahol módosíthatja a **tulajdonságokat**, módosíthatja a **konfigurációt**, kezelheti a **tanúsítványokat**, beállíthatja a **riasztási szabályokat**, és kezelheti azokat a **felhasználókat** , akik hozzáféréssel rendelkeznek ehhez a felhőalapú szolgáltatáshoz.

![Azure Cloud Service-beállítások](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Vendég operációs rendszer verziójának kezelése

Alapértelmezés szerint az Azure rendszeresen frissíti a vendég operációs rendszert a szolgáltatás konfigurációjában (. cscfg) megadott operációsrendszer-családon belül a legújabb támogatott lemezképre, például a Windows Server 2016-re.

Ha egy adott operációsrendszer-verziót kell megcéloznia, beállíthatja a **konfigurációban**.

![Operációs rendszer verziójának beállítása](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Egy adott operációsrendszer-verzió kiválasztásával letilthatja az operációs rendszer frissítéseinek automatikus telepítését, és javíthatja a felelősségét. Győződjön meg arról, hogy a szerepkör-példányok frissítéseket kapnak, vagy az alkalmazást biztonsági rések számára teheti elérhetővé.

## <a name="monitoring"></a>Monitoring

Riasztásokat adhat hozzá a felhőalapú szolgáltatáshoz. Kattintson a **beállítások** > **riasztási szabályok** > **riasztás hozzáadása**lehetőségre.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Itt beállíthatja a riasztást. A **metrika** legördülő lista használatával riasztást állíthat be a következő típusú adatokhoz.

* Lemez olvasása
* Lemez írása
* bejövő hálózati forgalom
* kimenő hálózati forgalom
* Processzorhasználat (%)

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Figyelés beállítása metrikai csempéről

A **beállítások** > a **riasztási szabályok**használata helyett a Cloud Service **figyelés** szakaszának egyik mérőszám csempére kattinthat.

![Cloud Service-figyelés](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Itt testreszabhatja a csempéhez használt diagramot, vagy hozzáadhat egy riasztási szabályt.

## <a name="reboot-reimage-or-remote-desktop"></a>Újraindítás, rendszerkép vagy távoli asztal

A Távoli asztalt beállíthatja a [Azure Portal (távoli asztal beállítása)](cloud-services-role-enable-remote-desktop-new-portal.md), a [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)vagy a [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)használatával.

A Cloud Service-példány újraindításához, rendszerképének vagy távoli üzembe helyezéséhez válassza ki a Cloud Service-példányt.

![Cloud Service-példány](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Ezután kezdeményezheti a távoli asztali kapcsolatokat, távolról újraindíthatja a példányt, vagy távolról is áthelyezheti a rendszerképet (kezdjen egy friss képpel) a példányt.

![Cloud Service-példány gombjai](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Konfigurálja újra a. cscfg

Előfordulhat, hogy újra kell konfigurálnia a felhőalapú szolgáltatást a [Service config (cscfg)](cloud-services-model-and-package.md#cscfg) fájlon keresztül. Először le kell töltenie a. cscfg fájlt, módosítania kell, majd fel kell töltenie.

1. Kattintson a **Beállítások** ikonra, vagy a **minden beállítás** hivatkozásra a **Beállítások**megnyitásához.

    ![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Kattintson a **konfigurációs** elemre.

    ![Konfiguráció panel](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Kattintson a **Download** (Letöltés) gombra.

    ![Letöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. A szolgáltatás konfigurációs fájljának frissítése után töltse fel és alkalmazza a konfigurációs frissítéseket:

    ![Feltöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Válassza ki a. cscfg fájlt, majd kattintson **az OK**gombra.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [helyezhet üzembe egy felhőalapú szolgáltatást](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja az [Egyéni tartománynevet](cloud-services-custom-domain-name-portal.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage-portal.md).
* Konfigurálja az [SSL-tanúsítványokat](cloud-services-configure-ssl-certificate-portal.md).



