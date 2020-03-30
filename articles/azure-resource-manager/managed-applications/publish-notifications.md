---
title: Felügyelt alkalmazások értesítésekkel
description: Konfigurálja a felügyelt alkalmazásokat webhook-végpontokkal, hogy értesítéseket kapjanak a létrehozott, a frissítésekről, a törlésekről és a felügyelt alkalmazáspéldányok hibáiról.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715669"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure által kezelt alkalmazások értesítésekkel

Az Azure által felügyelt alkalmazásértesítések lehetővé teszik a közzétevők számára, hogy a felügyelt alkalmazáspéldányok életciklus-eseményei alapján automatizálják a műveleteket. A közzétevők egyéni értesítési webhook-végpontokat adhatnak meg az új és a meglévő felügyelt alkalmazáspéldányokról szóló eseményértesítések fogadásához. A közzétevők egyéni munkafolyamatokat állíthatnak be az alkalmazás kiépítésekor, a frissítésekben és a törléskor.

## <a name="getting-started"></a>Első lépések
Felügyelt alkalmazások fogadásának megkezdéséhez forgasson elő egy nyilvános HTTPS-végpontot, és adja meg, amikor közzéteszi a szolgáltatáskatalógus-alkalmazás-definíciót vagy az Azure Marketplace-ajánlatot.

Az alábbi lépések közül kaphatunk gyorsan:
1. Pörgessfel egy nyilvános HTTPS-végpontot, amely `200 OK`naplózza a bejövő POST-kérelmeket és visszaküldőket.
2. Adja hozzá a végpontot a szolgáltatáskatalógus-alkalmazás-definícióhoz vagy az Azure Marketplace-ajánlathoz a cikk későbbi részében ismertetett módon.
3. Hozzon létre egy felügyelt alkalmazáspéldányt, amely hivatkozik az alkalmazás-definíció vagy az Azure Marketplace-ajánlat.
4. Ellenőrizze, hogy az értesítések beérkeznek-e.
5. Engedélyezze az engedélyezést a cikk **Végpont-hitelesítéscímű** szakaszában leírtak szerint.
6. Kövesse a cikk **Értesítésséma** szakaszában található utasításokat az értesítési kérelmek elemzéséhez és az üzleti logika megvalósításához az értesítés alapján.

## <a name="add-service-catalog-application-definition-notifications"></a>Szolgáltatáskatalógus alkalmazásdefiníciós értesítéseinek hozzáadása
#### <a name="azure-portal"></a>Azure portál
Első lépések: [Szolgáltatáskatalógus-alkalmazás közzététele az Azure Portalon keresztül.](./publish-portal.md)

![Szolgáltatáskatalógus-alkalmazásdefiníciós értesítések az Azure Portalon](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> Jelenleg csak egy végpontot adhat `notificationEndpoints` meg az alkalmazásdefiníció tulajdonságaiban.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Azure Marketplace-en kezelt alkalmazásértesítések hozzáadása
További információ: [Create an Azure application offer](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Az Azure Marketplace felügyelt alkalmazásértesítései az Azure Portalon](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Eseményindítók
Az alábbi táblázat az EventType és a ProvisioningState összes lehetséges kombinációját, valamint azok eseményindítóit ismerteti:

EventType (Eseménytípus) | ProvisioningState (KiépítésÁllapota) | Értesítési eseményindító
---|---|---
PUT | Elfogadva | A felügyelt erőforráscsoport létrehozása és előrejelzése sikeresen megtörtént a PUT alkalmazás után (a felügyelt erőforráscsoporton belüli központi telepítés megkezdése előtt).
PUT | Sikeres | A felügyelt alkalmazás teljes körű kiépítése a PUT után sikerült.
PUT | Sikertelen | Az alkalmazáspéldány-kiépítés put-jának meghibásodása.
Javítás | Sikeres | Miután a felügyelt alkalmazáspéldány sikeresen frissítette a címkéket, a JIT hozzáférési házirendet vagy a felügyelt identitást.
DELETE | Törlés | Amint a felhasználó elindítja a felügyelt alkalmazáspéldány törlését.
DELETE | Törölve | A felügyelt alkalmazás teljes és sikeres törlése után.
DELETE | Sikertelen | A törlési folyamat során a törlést letiltást letiltást okozó hiba után.
## <a name="notification-schema"></a>Értesítési séma
Amikor a webhook-végpontot az értesítések kezeléséhez, akkor elemeznie kell a hasznos terhet, hogy fontos tulajdonságokat kapjon, majd az értesítés után kell cselekednie. A szolgáltatáskatalógus és az Azure Marketplace felügyelt alkalmazásértesítései számos azonos tulajdonságot biztosítanak. A mintákat követő táblázat két kis különbséget vázol fel.

#### <a name="service-catalog-application-notification-schema"></a>Szolgáltatáskatalógus alkalmazásértesítési sémája
Az alábbiakban egy mintaszolgáltatás-katalógus értesítését olvashatja egy felügyelt alkalmazáspéldány sikeres kiépítése után:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Ha a kiépítés sikertelen, a rendszer értesítést küld a megadott végpontnak a hiba részleteivel.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Az Azure Marketplace alkalmazásértesítési sémája

Az alábbiakban egy mintaszolgáltatás-katalógus értesítését olvashatja egy felügyelt alkalmazáspéldány sikeres kiépítése után:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Ha a kiépítés sikertelen, a rendszer értesítést küld a megadott végpontnak a hiba részleteivel.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Paraméter | Leírás
---|---
eventType | Az értesítést kiváltó esemény típusa. (Például PUT, PATCH, DELETE.)
applicationId | Annak a felügyelt alkalmazásnak a teljesen minősített erőforrás-azonosítója, amelyhez az értesítés indult.
eventTime | Az értesítést kiváltó esemény időbélyege. (Dátum és idő UTC ISO 8601 formátumban.)
provisioningState | A felügyelt alkalmazáspéldány létesítési állapota. (Például Sikerült, Sikertelen, Törlés, Törölve.)
error | *Csak akkor adja meg, ha a kiépítési állapot sikertelen.* A hibát okozó hibakódot, üzenetet és részleteket tartalmazza.
alkalmazásdefinícióazonosító | *Csak a szolgáltatáskatalógus ban kezelt alkalmazásokhoz van megadva.* Annak az alkalmazásdefiníciónak a teljesen minősített erőforrás-azonosítóját jelöli, amelyhez a felügyelt alkalmazáspéldány ki lett építve.
Terv | *Csak az Azure Marketplace által felügyelt alkalmazásokhoz van megadva.* A felügyelt alkalmazáspéldány közzétevőt, ajánlatot, termékváltozatát és verzióját jelöli.
billingDetails | *Csak az Azure Marketplace felügyelt alkalmazásaihoz van megadva.* A felügyelt alkalmazáspéldány számlázási részletei. Tartalmazza a forrásUsageId, amely segítségével lekérdezheti az Azure Marketplace használati részletek.

## <a name="endpoint-authentication"></a>Végpont-hitelesítés
A webhook-végpont védelme és az értesítés hitelességének biztosítása:
1. Adjon meg egy lekérdezési paramétert a webhook\:URI tetején, így: https //your-endpoint.com?sig=Guid. Minden értesítésnél ellenőrizze, hogy `sig` a lekérdezési paraméter rendelkezik-e a várt értékkel. `Guid`
2. Get kiadása a felügyelt alkalmazáspéldányon az applicationId használatával. Ellenőrizze, hogy a provisioningState megfelel-e a provisioningState az értesítés a konzisztencia biztosítása érdekében.

## <a name="notification-retries"></a>Értesítés újrapróbálkozásai

A felügyelt alkalmazásértesítési `200 OK` szolgáltatás választ vár a webhook-végponttól az értesítésre. Az értesítési szolgáltatás újra próbálkozik, ha a webhook-végpont 500-nál nagyobb vagy azzal egyenlő HTTP-hibakódot ad vissza, ha 429-es hibakódot ad vissza, vagy ha a végpont ideiglenesen nem érhető el. Ha a webhook-végpont nem válik elérhetővé 10 órán belül, az értesítési üzenet megszakad, és az újrapróbálkozások leáll.
