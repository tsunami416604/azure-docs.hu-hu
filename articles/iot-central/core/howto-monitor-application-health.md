---
title: Azure IoT Central-alkalmazás állapotának monitorozása | Microsoft Docs
description: Kezelőként vagy rendszergazdaként figyelje a IoT Central alkalmazáshoz csatlakoztatott eszközök általános állapotát.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1fb17d56c546511fee291f30b103a5310c8015cc
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122273"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>IoT Central alkalmazáshoz csatlakoztatott eszközök általános állapotának figyelése

> [!NOTE]
> A metrikák csak a 3. verziójú IoT Central alkalmazások esetében érhetők el. Az alkalmazás verziószámának megismeréséhez tekintse meg [az alkalmazásról szóló](./howto-get-app-info.md)témakört.

*Ez a cikk a kezelők és a rendszergazdákra vonatkozik.*

Ebből a cikkből megtudhatja, hogyan használhatja a IoT Central által biztosított mérőszámokat a IoT Central alkalmazáshoz csatlakoztatott eszközök általános állapotának felméréséhez.

A metrikák alapértelmezés szerint engedélyezve vannak a IoT Central alkalmazáshoz, és a [Azure Portal](https://portal.azure.com/)érheti el azokat. A [Azure monitor adatplatform elérhetővé teszi ezeket a mérőszámokat](../../azure-monitor/platform/data-platform-metrics.md) , és számos módszert biztosít a velük való kommunikációra. Használhat például diagramokat a PowerShellben vagy az Azure CLI-ben lévő Azure Portal, REST API vagy lekérdezésekben.

### <a name="trial-applications"></a>Próbaverziós alkalmazások

Az ingyenes próbaverziót használó alkalmazások nem rendelkeznek társított Azure-előfizetéssel, ezért nem támogatják Azure Monitor metrikákat. [Az alkalmazások standard díjszabási csomagra](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) válthatók, és hozzáférhetnek ezekhez a mérőszámokhoz.

## <a name="view-metrics-in-the-azure-portal"></a>Metrikák megtekintése a Azure Portalban

Az alábbi lépések feltételezik, hogy van egy [IoT Central alkalmazás](./quick-deploy-iot-central.md) néhány [csatlakoztatott eszközzel](./tutorial-connect-device.md).

IoT Central mérőszámok megtekintése a portálon:

1. Navigáljon a IoT Central alkalmazás-erőforráshoz a portálon. Alapértelmezés szerint a IoT Central-erőforrások egy **IOTC** nevű erőforráscsoporthoz találhatók.
1. Ha diagramot szeretne létrehozni az alkalmazás metrikái közül, válassza a **mérőszámok** lehetőséget a **figyelés** szakaszban.

![Azure-metrikák](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Engedélyek Azure Portal

A Azure Portal metrikáinak elérését az [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md)kezeli. A Azure Portal használatával adhat hozzá felhasználókat a IoT Central alkalmazáshoz/erőforráscsoporthoz vagy előfizetéshez, hogy hozzáférést biztosítson számukra. Hozzá kell adnia egy felhasználót a portálon, még akkor is, ha már hozzá lettek adva a IoT Central alkalmazáshoz. Az [Azure beépített szerepköreivel](../../role-based-access-control/built-in-roles.md) finomabb hozzáférés-vezérlést használhat.

## <a name="iot-central-metrics"></a>IoT Central metrikák

A IoT Central jelenleg elérhető metrikák listáját a [Azure monitor által támogatott mérőszámok](../../azure-monitor/platform/metrics-supported.md#microsoftiotcentraliotapps)részben tekintheti meg.

### <a name="metrics-and-invoices"></a>Metrikák és számlák

A metrikák eltérhetnek az Azure IoT Central-számlán látható számtól. Ez többek között a következő esetekben fordul elő:

- IoT Central [standard díjszabási csomag](https://azure.microsoft.com/pricing/details/iot-central/) két eszközt tartalmaz, és a különböző üzenetekre vonatkozó kvóták ingyenesen használhatók. Amíg az ingyenes elemek ki vannak zárva a számlázásból, a metrikák továbbra is beleszámítanak.

- IoT Central automatikusan egy test Device-azonosítót állít elő az alkalmazás minden egyes sablonja számára. Ez az eszköz-azonosító látható az adott eszköz sablonjának **ellenőrző eszközének kezelése** lapján. A megoldás-építők dönthetnek úgy, hogy megadják az [eszközök sablonjait](./overview-iot-central.md#create-device-templates) , mielőtt közzéteszik azokat a teszt-eszköz azonosítóit használó kód generálásával. Habár ezek az eszközök ki vannak zárva a számlázásból, a metrikák továbbra is beleszámítanak.

- Míg a metrikák az eszközről a felhőbe irányuló kommunikáció egy részhalmazát láthatják, az eszköz és a felhő közötti összes kommunikáció a [Számlázási üzenetnek számít](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az alkalmazás sablonjainak használatát, a javasolt következő lépés a [IoT Central kezelésének megismerése a Azure Portal](howto-manage-iot-central-from-portal.md)