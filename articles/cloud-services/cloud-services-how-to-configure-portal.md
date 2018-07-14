---
title: Konfigurálása felhőszolgáltatáshoz (portál) |} A Microsoft Docs
description: Ismerje meg, a cloud services konfigurálása az Azure-ban. Megtanulhatja, hogyan frissítéséhez a felhőszolgáltatás-konfigurációt, és konfigurálja a távelérési szerepkör-példányok. Ezekben a példákban az Azure Portalon.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7308f3c0-825e-499d-bfa5-c60f86371921
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: jeconnoc
ms.openlocfilehash: 904056363c685ef0a16b229ce72383eb80701a39
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006413"
---
# <a name="how-to-configure-cloud-services"></a>A Cloud Services konfigurálása

A felhőszolgáltatások leggyakrabban használt beállításai az Azure Portalon konfigurálhatja. Azok az ügyfeleink, akik közvetlenül szeretnék frissíteni a konfigurációs fájlokat, letölthetik a frissítendő szolgáltatáskonfigurációs fájlt, amelyet módosítás után feltölthetnek, így frissül a felhőszolgáltatás konfigurációja. A rendszer mindkét esetben az összes szerepkörpéldányon elvégzi a konfiguráció módosítását.

A példányok be őket a felhőbeli szolgáltatási szerepkörökben vagy a távoli asztali is kezelheti.

Az Azure csak biztosítható a 99,95 %-os szolgáltatás rendelkezésre állása a konfigurációs frissítések során Ha minden egyes szerepkörhöz legalább két szerepkörpéldányt. Amely lehetővé teszi az ügyfél kérelmeket feldolgozni, míg a másik frissítése folyamatban van egy virtuális gépet. További információkért lásd: [szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Egy felhőalapú szolgáltatás módosítása

Megnyitása után a [az Azure portal](https://portal.azure.com/), lépjen a felhőszolgáltatás. Itt kezelheti számos aspektusát.

![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)

A **beállítások** vagy **minden beállítás** hivatkozások, megnyílik **beállítások** ahol módosíthatja a **tulajdonságok**, módosítsa a  **Konfigurációs**, kezelheti a **tanúsítványok**, állítsa be **riasztási szabályok**, és kezelheti a **felhasználók** akik hozzáférése a felhőalapú szolgáltatás.

![Az Azure cloud service-beállítások](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>Kezelheti a vendég operációs rendszer verziója

Alapértelmezés szerint az Azure rendszeres időközönként frissíti a vendég operációs rendszer a legújabb támogatott lemezképen belül a szolgáltatás konfigurációs (.cscfg), a megadott operációsrendszer-termékcsalád például a Windows Server 2016-ban.

Ha szeretne megcélozni egy adott operációsrendszer-verzió, beállíthatja **konfigurációs**.

![Operációsrendszer-verzió beállítása](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> Az operációs rendszer letiltja az adott operációsrendszer-verzió kiválasztása frissíti, és lehetővé teszi az Ön felelőssége javítása. Gondoskodnia kell arról, hogy a szerepkörpéldányok azért küldtük Önnek, frissítés, vagy előfordulhat, hogy elérhetővé teszi az alkalmazás biztonsági rések.

## <a name="monitoring"></a>Figyelés

A felhőszolgáltatások riasztásokat is hozzáadhat. Kattintson a **beállítások** > **riasztási szabályok** > **riasztás hozzáadása**.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

Itt beállíthat egy riasztást. Az a **metrika** legördülő mezőben, beállíthat egy riasztás a következő típusú adatokról.

* Lemezolvasás
* Lemezírás
* A hálózati
* Kimenő hálózati forgalom
* Processzorhasználat (%)

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Metrika csempékről figyelésének konfigurálása

Használata helyett **beállítások** > **riasztási szabályok**, kattinthat a metrika csempék az egyik a **figyelés** szakasz a felhőalapú szolgáltatás.

![Felhőalapú szolgáltatás figyelése](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

Innen testre a diagram a csempe használni, vagy adjon hozzá egy riasztási szabályt.

## <a name="reboot-reimage-or-remote-desktop"></a>Újraindítás, rendszerkép alaphelyzetbe állítását vagy a távoli asztal

A távoli asztal segítségével beállíthatja a [(a távoli asztal beállítása) az Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), vagy a Kiszolgálókezelő [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).

Az újraindítás, rendszerkép alaphelyzetbe állítását, vagy távolról csatlakozzon egy felhőalapú szolgáltatás, válassza ki a felhőszolgáltatás-példányok.

![Felhőszolgáltatás-példányok](./media/cloud-services-how-to-configure-portal/cs-instance.png)

Majd indítsa el a távoli asztali kapcsolat, távolról az-példány újraindítása, vagy távolról rendszerképének alaphelyzetbe állítása (friss rendszerképpel indítás) a példányt.

![Cloud Service példány gombok](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>Konfigurálja újra a .cscfg

Szükség lehet a felhőalapú szolgáltatás segítségével konfigurálja újra a [szolgáltatás konfigurációs (cscfg)](cloud-services-model-and-package.md#cscfg) fájlt. Először töltse le a .cscfg fájlban, módosítsa, majd töltse fel.

1. Kattintson a a **beállítások** ikonra vagy a **minden beállítás** hivatkozás megnyitásához **beállítások**.

    ![Beállítások lap](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. Kattintson a **konfigurációs** elemet.

    ![Konfigurációs panelen](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. Kattintson a **Download** (Letöltés) gombra.

    ![Letöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. Miután frissítette a szolgáltatás konfigurációs fájlja, töltse fel, és a konfigurációs frissítések alkalmazásához:

    ![Feltöltés](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. Válassza ki a .cscfg-fájlt, és kattintson a **OK**.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [egy felhőalapú szolgáltatás üzembe helyezése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* [A felhőszolgáltatások kezelése](cloud-services-how-to-manage-portal.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).
