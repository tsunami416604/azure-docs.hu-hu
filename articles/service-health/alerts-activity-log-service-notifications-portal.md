---
title: Eseménynapló-riasztások fogadása az Azure szolgáltatási értesítésein Azure Portal használatával
description: Értesítés küldése SMS-ben, e-mailben vagy webhookon az Azure-szolgáltatás bekövetkeztekor.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a8723698cddfb519687525820475517b93219a4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568699"
---
# <a name="create-activity-log-alerts-on-service-notifications-using-the-azure-portal"></a>Műveletnapló-riasztások létrehozása a szolgáltatási értesítéseken a Azure Portal használatával
## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan állíthatja be a Azure Portal a szolgáltatás állapotával kapcsolatos értesítésekhez a Azure Portal használatával.  

A szolgáltatás állapotára vonatkozó értesítések tárolása az [Azure-tevékenység naplójában](../azure-monitor/platform/platform-logs-overview.md) történik, mivel a tevékenységi naplóban tárolt adatok valószínűleg nagy mennyiségű információval rendelkeznek, külön felhasználói felület áll rendelkezésre, amely megkönnyíti a riasztások megtekintését és beállítását a szolgáltatás állapotával kapcsolatos értesítéseken. 

Riasztást kaphat, ha az Azure szolgáltatás-állapotra vonatkozó értesítéseket küld az Azure-előfizetésre. A riasztást a következő alapján állíthatja be:

- A szolgáltatás állapotáról szóló értesítés (szolgáltatási problémák, tervezett karbantartás, egészségügyi tanácsadók) osztálya.
- Az érintett előfizetés.
- Az érintett szolgáltatás (ok).
- Az érintett régió (k).

> [!NOTE]
> A szolgáltatás állapotára vonatkozó értesítések nem küldenek riasztást az erőforrás-állapottal kapcsolatos eseményekről.

Azt is beállíthatja, hogy a rendszer kik számára küldje a riasztást:

- Válasszon ki egy meglévő műveleti csoportot.
- Hozzon létre egy új műveleti csoportot (amely a jövőbeli riasztásokhoz használható).

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

A Service Health értesítési riasztások Azure Resource Manager sablonok használatával történő konfigurálásával kapcsolatos információkért lásd: [Resource Manager-sablonok](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Tekintse meg az első Azure Service Health riasztás beállítását bemutató videót

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Riasztás és új műveleti csoport a Azure Portal használatával
1. A [portálon](https://portal.azure.com)válassza a **Service Health**lehetőséget.

    ![A "Service Health" szolgáltatás](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. A **riasztások** szakaszban válassza az **állapot riasztások**lehetőséget.

    ![Az "állapot-riasztások" lap](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Válassza a **szolgáltatás állapotának létrehozása riasztás** lehetőséget, és töltse ki a mezőket.

    ![A "szolgáltatás állapotára figyelmeztető riasztás létrehozása" parancs](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Válassza ki azokat az **előfizetéseket**, **szolgáltatásokat**és **régiókat** , amelyekről riasztást szeretne kapni.

    ![A "műveletnapló hozzáadása riasztás" párbeszédpanel](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Ez az előfizetés a műveletnapló riasztásának mentéséhez használatos. A riasztási erőforrás erre az előfizetésre van telepítve, és figyeli az eseményeket a tevékenység naplójában.

1. Válassza ki a riasztáshoz használni kívánt **esemény típusát** : *szolgáltatási probléma*, *tervezett karbantartás*és *állapot-tanácsadók* 

1. Adja meg a riasztás részleteit a **riasztási szabály nevének** és **leírásának**megadásával.

1. Válassza ki azt az **erőforráscsoportot** , amelyben menteni szeretné a riasztást.

1. Hozzon létre egy új műveleti csoportot az **új műveleti csoport**lehetőség kiválasztásával. Írjon be egy nevet a **műveleti csoport neve** mezőbe, és adjon meg egy nevet a **rövid név** mezőben. A rövid név a riasztás elküldésekor küldött értesítésekben hivatkozik.

    ![Új műveleti csoport létrehozása](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Adja meg a fogadók listáját a fogadó:

    a. **Név**: adja meg a fogadó nevét, aliasát vagy azonosítóját.

    b. **Művelettípus**: válassza az SMS-t, e-mailt, webhookot, az Azure-alkalmazást és egyebeket.

    c. **Részletek**: a kiválasztott Művelettípus alapján adja meg a telefonszámot, az e-mail-címet, a webhook URI-ját stb.

1. A műveleti csoport létrehozásához kattintson **az OK gombra** , majd **hozzon létre riasztási szabályt** a riasztás befejezéséhez.

Néhány percen belül a riasztás aktív, és a létrehozáskor megadott feltételek alapján kezdi meg az indítást.

Megtudhatja, hogyan [konfigurálhat webhook-értesítéseket a meglévő probléma-felügyeleti rendszerekhez](service-health-alert-webhook-guide.md). A műveletnapló-riasztások webhook-sémájával kapcsolatos információkért lásd: [webhookok az Azure-beli tevékenység naplójában](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Az ezekben a lépésekben definiált műveleti csoport az összes jövőbeli riasztási definícióhoz használható meglévő műveleti csoportként.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Riasztás meglévő műveleti csoporttal Azure Portal használatával

1. A szolgáltatás állapotáról szóló értesítés létrehozásához kövesse az előző szakasz 1 – 6. lépését. 

1. A **műveleti csoport meghatározása**területen kattintson a **műveleti csoport kiválasztása** gombra. Válassza ki a megfelelő műveleti csoportot.

1. Válassza a **Hozzáadás** lehetőséget a műveleti csoport hozzáadásához, majd **hozzon létre riasztási szabályt** a riasztás befejezéséhez.

Néhány percen belül a riasztás aktív, és a létrehozáskor megadott feltételek alapján kezdi meg az indítást.


## <a name="next-steps"></a>További lépések
- További információ [a Azure Service Health riasztások beállításával kapcsolatos ajánlott eljárásokról](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Ismerje meg, hogyan állíthat be [Azure Service Health Mobile leküldéses értesítéseket](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Megtudhatja, hogyan [konfigurálhat webhook-értesítéseket a meglévő probléma-felügyeleti rendszerekhez](service-health-alert-webhook-guide.md).
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](service-notifications.md).
- Tudnivalók az [értesítési ráta korlátozásáról](../azure-monitor/platform/alerts-rate-limiting.md).
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](../azure-monitor/platform/activity-log-alerts-webhook.md).
- [Tekintse át a tevékenységek naplójának riasztásait](../azure-monitor/platform/alerts-overview.md), és Ismerje meg, hogyan fogadhat riasztásokat.
- További információ a [műveleti csoportokról](../azure-monitor/platform/action-groups.md).
