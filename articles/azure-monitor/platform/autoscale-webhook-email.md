---
title: E-mailek és webhook-értesítési értesítések automatikus skálázásának használata
description: Megtudhatja, hogyan használhatja az automatikus skálázási műveleteket webes URL-címek hívására vagy e-mail értesítések küldésére az Azure Monitorban.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: c82b170bb3801bdc701ed84230db57f5691523ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120685"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Automatikus skálázási műveletek használata e-mailek és webhook-riasztási értesítések küldéséhez az Azure Monitorban
Ez a cikk bemutatja, hogyan állítsa be az eseményindítókat, hogy adott webes URL-címeket hívhat, vagy e-maileket küldjön az Azure-beli automatikus skálázási műveletek alapján.  

## <a name="webhooks"></a>Webhookok
A webhookok lehetővé teszik az Azure riasztási értesítéseinek más rendszerekre történő továbbítását utófeldolgozási vagy egyéni értesítésekhez. Például a riasztás tágítása olyan szolgáltatásokhoz, amelyek képesek kezelni egy bejövő webes kérést SMS küldésére, hibák naplózására, csevegési vagy üzenetküldő szolgáltatásokkal való értesítésére stb. A webhook URI-nak érvényes HTTP- vagy HTTPS-végpontnak kell lennie.

## <a name="email"></a>E-mail
Az e-mail eket bármely érvényes e-mail címre elküldheti. A szabály futtatásához szükséges előfizetés rendszergazdái és társrendszergazdái is értesítést kapnak.

## <a name="cloud-services-and-app-services"></a>Felhőszolgáltatások és alkalmazásszolgáltatások
Az Azure Portalon a Felhőszolgáltatások és a kiszolgálófarmok (App Services) szolgáltatáshoz iratkozhat fel.

* Válassza ki a **skálát metrika szerint.**

![méretarányos an](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok
Az Erőforrás-kezelővel létrehozott újabb virtuális gépek (virtuálisgép-méretezési csoportok) esetén konfigurálhatja ezt rest API, Erőforrás-kezelő sablonok, PowerShell és CLI használatával. A portálfelület még nem érhető el.
A REST API vagy az Erőforrás-kezelő sablon használatakor az automatikus [skálázási beállítások](https://docs.microsoft.com/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) ban az értesítések elem szerepeljen az alábbi beállításokkal.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Mező | Kötelező? | Leírás |
| --- | --- | --- |
| Művelet |igen |az értéknek "Lépték" értékűnek kell lennie |
| sendToSubscriptionAdministrator |igen |értéknek "igaz" vagy "hamis" értékűnek kell lennie |
| sendToSubscriptionCoRendszergazdák |igen |értéknek "igaz" vagy "hamis" értékűnek kell lennie |
| egyéni e-mailek |igen |az érték lehet null [] vagy e-mailek karakterlánctömbje |
| webhookok |igen |az érték lehet null vagy érvényes Uri |
| serviceUri |igen |érvényes https Uri |
| properties |igen |az értéknek {} üresnek kell lennie, vagy kulcs-érték párokat tartalmazhat |

## <a name="authentication-in-webhooks"></a>Hitelesítés webhookokban
A webhook hitelesíthető jogkivonat-alapú hitelesítéssel, ahol mentse a webhook URI egy token id lekérdezési paraméterként. Https például\/https: /mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Automatikus skálázási értesítés webhook hasznos sémája
Az automatikus skálázási értesítés létrehozásakor a webhook-tartalom a következő metaadatokat tartalmazza:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Mező | Kötelező? | Leírás |
| --- | --- | --- |
| status |igen |Az az állapot, amely azt jelzi, hogy automatikus skálázási művelet jött létre |
| Művelet |igen |A példányok számának növelése esetén "Horizontális felskálázás" lesz, és a példányok csökkenése esetén "Méretezés" lesz |
| Összefüggésben |igen |Az automatikus skálázási műveletkörnyezet |
| időbélyeg |igen |Időbélyegző az automatikus skálázási művelet aktiválásakor |
| id |Igen |Az automatikus skálázási beállítás erőforrás-kezelőazonosítója |
| név |Igen |Az automatikus skálázási beállítás neve |
| Részletek |Igen |Az automatikus skálázási szolgáltatás által végrehajtott művelet és a példányszám változásának magyarázata |
| subscriptionId |Igen |A méretezés alatt álló célerőforrás előfizetés-azonosítója |
| resourceGroupName |Igen |A méretezés alatt álló célerőforrás erőforráscsoport neve |
| resourceName |Igen |A méretezendő célerőforrás neve |
| resourceType |Igen |A három támogatott érték: "microsoft.classiccompute/domainnames/slots/roles" - Cloud Service szerepkörök, "microsoft.compute/virtualmachinescalesets" - Virtuálisgép-méretezési csoportok és "Microsoft.Web/serverfarms" – Web App |
| resourceId |Igen |A méretezés alatt álló célerőforrás erőforrás-kezelőazonosítója |
| portalLink |Igen |Az Azure Portal hivatkozása a célerőforrás összefoglaló oldalára |
| régiKapacitás |Igen |Az aktuális (régi) példányok száma, amikor az automatikus skálázás méretezési műveletet végzett |
| újkapacitás |Igen |Az új példányok száma, amely az automatikus skálázás az erőforrást |
| properties |Nem |Választható. <kulcs, érték> párok készlete (például Szótár <karakterlánc, Karakterlánc>). A tulajdonságok mező nem kötelező. Egy egyéni felhasználói felületen vagy logikai alkalmazásalapú munkafolyamatban megadhatja a hasznos adattal átadható kulcsokat és értékeket. Az egyéni tulajdonságok nak a kimenő webhook-hívásnak való visszaadásának másik módja a webhook URI használata (lekérdezési paraméterekként) |

