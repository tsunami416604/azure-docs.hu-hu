---
title: "A automatikus skálázási műveletek is elküldhetik e-mailek és a webhook riasztási értesítéseket. | Microsoft Docs"
description: "Webes URL-címek vagy értesítő e-mailek küldése az Azure a figyelő automatikus skálázási műveletek használata című részben találja. "
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: eb9a4c98-0894-488c-8ee8-5df0065d094f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: ancav
ms.openlocfilehash: 16caf14028494800e9259f0296c292b606d0210a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Automatikus skálázási műveletek segítségével e-mailek és a webhook riasztási értesítések küldése az Azure-figyelő
Ez a cikk bemutatja, hogyan lehet beállítani az eseményindítók, hogy az adott webes URL-címek hívja, vagy az Azure-ban automatikus skálázási műveletek alapján a e-mailek küldése.  

## <a name="webhooks"></a>webhook
Webhook lehetővé teszik a más rendszerekkel utófeldolgozási vagy egyéni értesítések az Azure riasztási értesítések továbbításához. Például útválasztási a figyelmeztető szolgáltatásokról, amelyek egy bejövő webes kérelem küldése SMS, napló hibák, értesítse a csapat Csevegés használata vagy üzenetküldési szolgáltatások kezelni tud, stb. A webhook URI kell lennie egy érvényes HTTP vagy HTTPS-végponton.

## <a name="email"></a>E-mail cím
E-mail küldhető bármilyen érvényes e-mail címet. A rendszergazdák és a társadminisztrátorok az előfizetés, amelyen fut a szabály is értesítést fog kapni.

## <a name="cloud-services-and-web-apps"></a>Cloud Services és a Web Apps
Ön részt az Azure portálról felhőalapú szolgáltatásokhoz és a kiszolgálófarmok (webalkalmazások).

* Válassza ki a **szerint** metrikát.

![Bővítse a](./media/insights-autoscale-to-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok
Újabb virtuális gépek létrehozása a Resource Manager (virtuálisgép-méretezési csoportok) konfigurálhatja a REST API-t, a Resource Manager sablonok, a PowerShell és a parancssori felület használatával. A portál felület még nem érhető el.
A REST API-t vagy az erőforrás-kezelő sablont használ, adja meg az értesítések elem a következő beállításokkal.

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
| művelet |igen |Az értéknek kell lennie a "Méretezési" |
| sendToSubscriptionAdministrator |igen |érték lehet "true" vagy "false" |
| sendToSubscriptionCoAdministrators |igen |érték lehet "true" vagy "false" |
| customEmails |igen |érték lehet null értékű [] vagy e-mailek karakterlánc tömbje |
| webhook |igen |értéke lehet null vagy érvénytelen Uri |
| serviceUri |igen |egy érvényes https Uri |
| properties |igen |érték üres {} kell lennie, vagy tartalmazhat kulcs-érték párok |

## <a name="authentication-in-webhooks"></a>A webhook hitelesítés
A webhook hitelesítheti a jogkivonat-alapú hitelesítés használatát, mentési helyét a webhook URI lekérdezési paraméterként jogkivonat-azonosítóval. Például https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>Automatikus skálázás értesítési webhook hasznos séma
Az automatikus skálázás értesítést generál, ha a következő metaadatokat tartalmazza a webhook hasznos:

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
| status |igen |Állapot jelzi, hogy létrejött-e egy automatikus skálázási művelet |
| művelet |igen |A példányok növelését "Horizontális Felskálázás" lesz, és példányok csökkenése, lesz az "Méretezési a" |
| A környezetben |igen |Az automatikus skálázási művelet környezetben |
| időbélyeg |igen |Időbélyegzőt, ha az automatikus skálázási művelet lett elindítva. |
| id |Igen |Automatikus skálázási beállítás erőforráskezelő-azonosító |
| név |Igen |Az automatikus skálázási beállítás neve |
| Részletek |Igen |A művelet, amely az automatikus skálázás szolgáltatás tartott és a példányszámot az módosítása ismertetése |
| subscriptionId |Igen |A célerőforrás méretezett alatt álló előfizetés-azonosító |
| erőforráscsoport-név |Igen |A célerőforrás alatt méretezett erőforráscsoport neve |
| resourceName |Igen |A cél erőforráson alatt méretezett neve |
| a resourceType |Igen |A három támogatott értékek: "microsoft.classiccompute/domainnames/slots/roles" - szerepköröket, a "microsoft.compute/virtualmachinescalesets" - virtuálisgép-méretezési csoportok, és a "Microsoft.Web/serverfarms" - webalkalmazás felhőalapú szolgáltatás |
| resourceId |Igen |Erőforrás-kezelő Azonosítóját a cél erőforráson méretezett folyamatban |
| portalLink |Igen |Azure portál hivatkozásra az összefoglaló lap a célerőforrás |
| oldCapacity |Igen |Az aktuális (régi) példányszám amikor automatikus skálázás tartott tartozó skálázási műveletek |
| newCapacity |Igen |Az új automatikus skálázás méretezve, az erőforrás megadott példányszám |
| Tulajdonságok |Nem |Választható. < Kulcs értéke > Set párok (például szótár < String, String >). A Tulajdonságok mező kitöltése nem kötelező. Egy egyéni felhasználói felület vagy a Logic app alapú munkafolyamat, a kulcsok és értékek, amelyek átadhatók a tartalom használatával is megadhatja. Egyéni tulajdonságok vissza átadása a kimenő webhook hívás más módja, hogy használja a webhook URI-JÁT magát (lekérdezési paraméterek) |
