---
title: Tevékenységnapló-értesítések fogadása az Azure szolgáltatásértesítéseiről
description: Értesítést kaphat SMS-ben, e-mailben vagy webhookon keresztül, ha az Azure-szolgáltatás megtörténik.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749317"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Tevékenységnapló-riasztások létrehozása szolgáltatási értesítésekhez
## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan állíthatja be a tevékenységnapló-riasztásokat a szolgáltatás állapoti értesítéseihez az Azure Portal használatával.  

A szolgáltatásállapot-értesítések az [Azure tevékenységnaplójában](../azure-monitor/platform/platform-logs-overview.md) tárolódnak, mivel a tevékenységnaplóban esetleg nagy mennyiségű információ tárolható, külön felhasználói felület található, amely megkönnyíti a szolgáltatásállapot-értesítések rekedésének megtekintését és beállítását. 

Riasztást kaphat, amikor az Azure szolgáltatásállapot-értesítéseket küld az Azure-előfizetéséhez. A riasztás a következők alapján konfigurálható:

- A szolgáltatás állapotértesítésének osztálya (szolgáltatási problémák, tervezett karbantartás, egészségügyi tanácsok).
- Az érintett előfizetés.
- Az érintett szolgáltatás(ok).
- Az érintett régió(k).

> [!NOTE]
> A szolgáltatásállapot-értesítések nem küld riasztást az erőforrás-állapoteseményekről.

Azt is beállíthatja, hogy kinek kell elküldeni a riasztást:

- Jelöljön ki egy meglévő műveletcsoportot.
- Hozzon létre egy új műveletcsoportot (amely későbbi riasztásokhoz használható).

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

A szolgáltatásállapot-értesítési riasztások Azure Resource Manager-sablonok használatával történő konfigurálásáról az [Erőforrás-kezelő-sablonok című témakörben olvashat.](../azure-monitor/platform/alerts-activity-log.md)

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Nézzen meg egy videót az első Azure Service Health-riasztás beállításáról

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Riasztás és új műveletcsoport az Azure Portal használatával
1. A [portálon](https://portal.azure.com)válassza a **Szolgáltatás állapota**lehetőséget.

    ![A "Service Health" szolgáltatás](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. A **Riasztások csoportban** válassza az **Állapotriasztások lehetőséget.**

    ![Az "Egészségügyi riasztások" lap](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Válassza **a Szolgáltatásállapot-riasztás létrehozása lehetőséget,** és töltse ki a mezőket.

    ![A "Szolgáltatás állapotriasztásának létrehozása" parancs](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Válassza ki azt az **Előfizetést**, **szolgáltatásokat**és **régiókat,** amelyekről értesítést szeretne kapni.

    ![A "Tevékenységnapló-riasztás hozzáadása" párbeszédpanel](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Ez az előfizetés a tevékenységnapló-riasztás mentésére szolgál. A riasztási erőforrás telepítve van az előfizetésben, és figyeli az eseményeket a tevékenységnaplóban.

1. Válassza ki azokat az **eseménytípusokat,** amelyekről értesítést szeretne kapni: *Szolgáltatási probléma,* *Tervezett karbantartás*és Egészségügyi *tanácsadás* 

1. Adja meg a riasztás részleteit a **riasztási szabály nevének** és **leírásának megadásával.**

1. Jelölje ki azt az **erőforráscsoportot,** amelybe a riasztást menteni szeretné.

1. Új műveletcsoport létrehozása az **Új műveletcsoport**kiválasztásával. Írjon be egy nevet a **Műveletcsoport neve** mezőbe, és írjon be egy nevet a **Rövid név** mezőbe. A rövid név reked az értesítésekben, amelyek a riasztás tüzek esetén küldött.

    ![Új műveletcsoport létrehozása](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Határozza meg a fogadók listáját a fogadó félkészülék ének megadásával:

    a. **Név**: Adja meg a címzett nevét, aliasát vagy azonosítóját.

    b. **Művelettípus:** Válassza ki az SMS-t, az e-mailt, a webhookot, az Azure-alkalmazást és egyebeket.

    c. **Részletek**: A kiválasztott művelettípus alapján adjon meg egy telefonszámot, e-mail címet, webhook URI-t stb.

1. A műveletcsoport létrehozásához válassza az **OK gombot,** majd a **Riasztási szabály létrehozása** a riasztás befejezéséhez lehetőséget.

Néhány percen belül a riasztás aktív, és a létrehozás során megadott feltételek alapján aktiválódik.

További információ [arról, hogyan konfigurálható a webhook-értesítések a meglévő problémakezelő rendszerekhez.](service-health-alert-webhook-guide.md) A tevékenységnapló-riasztások webhook-sémájáról a [Webhooks for Azure-tevékenységnapló-riasztások](../azure-monitor/platform/activity-log-alerts-webhook.md)című témakörben talál további információt.

>[!NOTE]
>Az ezekben a lépésekben definiált műveletcsoport újrafelhasználható, mint egy meglévő műveletcsoport az összes jövőbeli riasztásdefiníciók.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Riasztás meglévő műveletcsoporttal az Azure Portal használatával

1. Kövesse az előző szakasz 1–6. 

1. A **Műveletcsoport definiálása csoportban**kattintson a **Műveletcsoport kijelölése** gombra. Válassza ki a megfelelő műveletcsoportot.

1. Válassza a **Hozzáadás** lehetőséget a műveletcsoport hozzáadásához, majd a **Riasztási szabály létrehozása a** riasztás befejezéséhez lehetőséget.

Néhány percen belül a riasztás aktív, és a létrehozás során megadott feltételek alapján aktiválódik.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Riasztás és új műveletcsoport az Azure Resource Manager-sablonok használatával

Az alábbiakban egy példa, amely létrehoz egy műveletcsoportot egy e-mail cél, és lehetővé teszi az összes szolgáltatás állapoti értesítések a cél-előfizetés.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>A riasztások kezelése

Miután létrehozott egy riasztást, az látható lesz a **Figyelő** **Riasztások** szakaszában. Válassza ki a kezelni kívánt riasztást:

* Edit it.
* Törölje.
* Tiltsa le vagy engedélyezze azt, ha ideiglenesen le szeretné állítani vagy folytatni szeretné a riasztási értesítések fogadását.

## <a name="next-steps"></a>További lépések
- Ismerje meg [az Azure Service Health-riasztások beállításával kapcsolatos gyakorlati tanácsokat.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)
- Ismerje meg, hogyan [állíthat be mobil leküldéses értesítéseket az Azure Service Health szolgáltatáshoz.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)
- További információ [arról, hogyan állíthat be webhook-értesítéseket a meglévő problémakezelő rendszerekhez.](service-health-alert-webhook-guide.md)
- További információ a [szolgáltatásállapot-értesítésekről.](service-notifications.md)
- További információ az [értesítési sebesség korlátozásáról.](../azure-monitor/platform/alerts-rate-limiting.md)
- Tekintse át a [tevékenységnapló-riasztási webhook-sémáját.](../azure-monitor/platform/activity-log-alerts-webhook.md)
- Áttekintést [kaphat a tevékenységnapló-riasztásokról,](../azure-monitor/platform/alerts-overview.md)és megtudhatja, hogyan fogadhat értesítéseket.
- További információ a [műveletcsoportokról](../azure-monitor/platform/action-groups.md).
