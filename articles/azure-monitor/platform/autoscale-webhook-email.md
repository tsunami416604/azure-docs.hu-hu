---
title: Az automatikus méretezés használatával küldjön e-mailt és webhookot riasztási értesítések
description: 'Automatikus skálázási műveletek hívás webes URL-címeket vagy e-mail-értesítések küldése az Azure monitorban való használatáról. '
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 25ef2541dfa0b4cbd6e11d64381da645acfe653a
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259295"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Automatikus skálázási műveletek segítségével szeretne küldeni az e-mailt és webhookot riasztási értesítéseket az Azure monitorban
Ez a cikk bemutatja, hogyan lehet beállítani eseményindítók, hogy hívás meghatározott webes URL-címek, vagy küldjön e-mailek automatikus skálázási műveletek az Azure-ban alapján.  

## <a name="webhooks"></a>Webhookok
Webhookok lehetővé teszik az Azure-riasztási értesítések átirányítása utófeldolgozási vagy egyéni értesítések más rendszerekre. Például a riasztás útválasztást, amely képes kezelni egy bejövő webes kérelem küldése SMS-log-hibák, értesítse csevegés használatával, vagy üzenetküldés egy team services-szolgáltatások, és így tovább. A webhook URI érvényes HTTP vagy HTTPS-végpontot kell lennie.

## <a name="email"></a>E-mail
E-mailben bármilyen érvényes e-mail-címmel lehet küldeni. Az előfizetés, ahol a szabály futtatásához rendszergazdái és társadminisztrátorai is értesítést kapnak.

## <a name="cloud-services-and-web-apps"></a>A cloud Services és a Web Apps
Ön vehetnek részt az Azure Portalról a Felhőszolgáltatásokat és a kiszolgálófarmok (webalkalmazások).

* Válassza ki a **a skálázás** metrikát.

![a skálázás](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok
Újabb virtuális gépek létrehozása a Resource Manager (a Virtual Machine scale sets) beállíthatja a REST API-t, a Resource Manager sablonok, a PowerShell és a parancssori felület használatával. A portál felület még nem érhető el.
A REST API vagy a Resource Manager-sablon használatakor közé tartozik az értesítések elem a következő lehetőségekkel.

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
| művelet |igen |Az értéknek kell lennie a "Méretezés" |
| sendToSubscriptionAdministrator |igen |érték lehet "igaz" vagy "false" |
| sendToSubscriptionCoAdministrators |igen |érték lehet "igaz" vagy "false" |
| customEmails |igen |érték lehet null értékű [] vagy e-mailek karakterlánc tömbje |
| Webhookok |igen |az érték lehet null értékű vagy érvénytelen Uri |
| serviceUri |igen |egy érvényes https Uri |
| properties |igen |értéknek üresnek kell lennie {} vagy kulcs-érték párok |

## <a name="authentication-in-webhooks"></a>Hitelesítés a webhookok
A webhook hitelesítheti a mentési helyét a webhook URI-lekérdezési paraméterként egy jogkivonat-azonosító jogkivonat-alapú hitelesítés használatával. Például https: \/ /mysamplealert/webcallback? tokenid sometokenid & someparameter = érték1 =

## <a name="autoscale-notification-webhook-payload-schema"></a>Automatikus skálázási értesítések adattartalom webhookséma
Ha az automatikus skálázási értesítés jön létre, a webhook hasznos adatai a következő metaadatokat tartalmazza:

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
| status |igen |Az állapot, amely azt jelzi, hogy létrejött-e egy automatikus skálázási műveletet |
| művelet |igen |Példányok tervezni "Horizontális Felskálázás" lesz, és példányok csökkenését, a "Méretezés a" |
| Környezet |igen |Az automatikus skálázási művelet környezet |
| időbélyeg |igen |Időbélyeg, ha az automatikus skálázási műveletet |
| id |Igen |Automatikus skálázási beállítás erőforrás-kezelő azonosítója |
| név |Igen |Az automatikus skálázási beállítás neve |
| részletek |Igen |A művelet az autoscale szolgáltatás igénybe vett és a példányszám módosítása ismertetése |
| subscriptionId |Igen |A célként megadott erőforrás méretezése folyamatban van, előfizetés-azonosítója |
| resourceGroupName |Igen |A célként megadott erőforrás méretezése folyamatban van, az erőforráscsoport neve |
| resourceName |Igen |A célként megadott erőforrás folyamatban méretezett neve |
| resourceType |Igen |A három támogatott értékek: "microsoft.classiccompute/domainnames/slots/roles" – Cloud Service szerepkörök, a "microsoft.compute/virtualmachinescalesets" – Virtual Machine Scale Sets, és a "Microsoft.Web/serverfarms" - webalkalmazás |
| resourceId |Igen |A célként megadott erőforrás méretezése folyamatban van, a Resource Manager-Azonosítót |
| portalLink |Igen |A célként megadott erőforrás összefoglaló oldala az Azure portál hivatkozása |
| oldCapacity |Igen |Az aktuális (régi) példányok száma, amikor az automatikus méretezés tartott egy skálázási műveletet |
| newCapacity |Igen |Az új példányok száma, amelyek az automatikus méretezés az erőforrás méretezése |
| Tulajdonságok |Nem |Választható. < Kulcs értéke > Set párok (például Dictionary < karakterlánc, karakterlánc >). A Tulajdonságok mező kitöltése nem kötelező. Egy egyéni felhasználói felület vagy a Logic app-alapú munkafolyamat, a kulcsok és értékek, amelyek használatával a hasznos átadhatók is megadhatja. Egyéni tulajdonságok vissza átadása a kimenő webhook hívása egy másik módja az, hogy a webhook URI-JÁT magát (lekérdezési paraméterek) használata |

