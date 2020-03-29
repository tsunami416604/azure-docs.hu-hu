---
title: Felhőszolgáltatás (portál) konfigurálása | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a felhőszolgáltatásokat az Azure-ban. Ismerje meg a felhőszolgáltatás konfigurációjának frissítését és a szerepkörpéldányok távelérésének konfigurálását. Ezek a példák az Azure Portalt használják.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: tagore
ms.openlocfilehash: 554d3e465b42ca889ba03565e87193f80e89ed1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75361007"
---
# <a name="how-to-configure-cloud-services"></a>A Cloud Services Konfigurálása

Konfigurálhatja a felhőszolgáltatás leggyakrabban használt beállításait az Azure Portalon. Vagy ha közvetlenül szeretné frissíteni a konfigurációs fájlokat, töltse le a frissítéshez szükséges szolgáltatáskonfigurációs fájlt, majd töltse fel a frissített fájlt, és frissítse a felhőszolgáltatást a konfigurációs módosításokkal. Akárhogy is, a konfigurációs frissítések et minden szerepkörpéldány kilöki.

A felhőszolgáltatási szerepkörök példányait vagy a távoli asztalt is kezelheti.

Az Azure csak akkor tudja biztosítani a szolgáltatás 99,95%-os rendelkezésre állását a konfigurációs frissítések során, ha minden szerepkörhöz legalább két szerepkörpéldány van. Ez lehetővé teszi, hogy az egyik virtuális gép feldolgozza az ügyfélkérelmeket, miközben a másik frissítés alatt áll. További információt a [Szolgáltatásiszint-szerződések című témakörben talál.](https://azure.microsoft.com/support/legal/sla/)

## <a name="change-a-cloud-service"></a>Felhőszolgáltatás módosítása

Az [Azure Portal](https://portal.azure.com/)megnyitása után keresse meg a felhőszolgáltatás. Innen, akkor kezelni sok szempontból is.

![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)

A **Beállítások** vagy **a Minden beállítás** hivatkozás megnyitja a Beállítások **párbeszédpanelt,** ahol módosíthatja a **Tulajdonságokat,** módosíthatja a **Konfigurációt**, kezelheti a **Tanúsítványokat**, **riasztási szabályokat**állíthat be , és kezelheti a felhőszolgáltatáshoz hozzáféréssel rendelkező **felhasználókat.**

![Az Azure felhőszolgáltatásbeállításai](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Vendég operációs rendszer verziójának kezelése

Alapértelmezés szerint az Azure rendszeresen frissíti a vendég operációs rendszert a szolgáltatáskonfigurációban (.cscfg) megadott operációsrendszer-családon belüli legújabb támogatott lemezképre, például a Windows Server 2016-ra.

Ha egy adott operációsrendszer-verziót kell megcéloznia, beállíthatja azt a **Configuration (Konfiguráció) mezőben.**

![Operációs rendszer verziójának beállítása](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Egy adott operációsrendszer-verzió kiválasztása letiltja az operációs rendszer automatikus frissítéseit, és megkönnyíti a javítást. Meg kell győződnie arról, hogy a szerepkörpéldányok frissítéseket kapnak, vagy az alkalmazást biztonsági réseknek teheti ki.

## <a name="monitoring"></a>Figyelés

Riasztásokat adhat hozzá a felhőszolgáltatáshoz. Kattintson **a Beállítások** > **riasztási szabályok** > **– Riasztás hozzáadása gombra.**

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Itt riasztást állíthat be. A **Metrika** legördülő lista segítségével riasztást állíthat be a következő típusú adatokhoz.

* Lemez olvasása
* Lemez írása
* Hálózat a
* Hálózat ki
* Processzorhasználat (%)

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Figyelés konfigurálása metrikus csempéről

A **Beállítások riasztási** > **szabályok**használata helyett a felhőszolgáltatás **Figyelés** szakaszában található metrikacsempék egyikére kattinthat.

![Felhőszolgáltatás figyelése](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Itt testreszabhatja a csempével használt diagramot, vagy hozzáadhat egy figyelmeztetési szabályt.

## <a name="reboot-reimage-or-remote-desktop"></a>Újraindítás, újraképezés vagy távoli asztal

A távoli asztalt az [Azure Portalon (távoli asztal beállítása)](cloud-services-role-enable-remote-desktop-new-portal.md), a [PowerShellen](cloud-services-role-enable-remote-desktop-powershell.md)vagy a [Visual Studio-n](cloud-services-role-enable-remote-desktop-visual-studio.md)keresztül állíthatja be.

Újraindítás, újraképzése, vagy a távoli egy felhőalapú szolgáltatás, válassza ki a felhőszolgáltatás-példányt.

![Felhőszolgáltatás-példány](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Ezután kezdeményezhet egy távoli asztali kapcsolatot, távolról újraindíthatja a példányt, vagy távolról újraképet (friss lemezképpel kezdheti) a példányt.

![Felhőszolgáltatás-példány gombjai](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>A .cscfg újrakonfigurálása

Előfordulhat, hogy újra kell konfigurálnia a felhőszolgáltatást a [szolgáltatás konfigurációs (cscfg)](cloud-services-model-and-package.md#cscfg) fájlján keresztül. Először le kell töltenie a .cscfg fájlt, módosítania kell, majd feltöltenie kell.

1. A **Beállítások**megnyitásához kattintson a **Beállítások** ikonra vagy a **Minden beállítás** hivatkozásra.

    ![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Kattintson a **Konfiguráció** elemre.

    ![Konfigurációs panel](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Kattintson a **Download** (Letöltés) gombra.

    ![Letöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. A szolgáltatás konfigurációs fájljának frissítése után töltse fel és alkalmazza a konfigurációs frissítéseket:

    ![Feltöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Jelölje ki a .cscfg fájlt, és kattintson **az OK gombra.**

## <a name="next-steps"></a>További lépések

* További információ a [felhőszolgáltatások üzembe helyezéséről.](cloud-services-how-to-create-deploy-portal.md)
* Egyéni [tartománynév konfigurálása](cloud-services-custom-domain-name-portal.md).
* [A felhőszolgáltatás kezelése.](cloud-services-how-to-manage-portal.md)
* [Ssl-tanúsítványok konfigurálása](cloud-services-configure-ssl-certificate-portal.md).



