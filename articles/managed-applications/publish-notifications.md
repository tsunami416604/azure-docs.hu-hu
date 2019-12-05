---
title: Azure Managed Applications értesítésekkel
description: A felügyelt alkalmazás konfigurálása webhook-végpontokkal a felügyelt alkalmazás példányain a létrehozással, a frissítésekkel, a törléssel és a hibákkal kapcsolatos értesítések fogadásához.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 8cf9fc0b3d9c13ebc5309be6d27c7be0f2e60878
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805688"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure Managed Applications értesítésekkel

Az Azure Managed Application Notifications lehetővé teszi, hogy a közzétevők a felügyelt alkalmazás példányainak Életciklus-eseményei alapján automatizálják a műveleteket. A közzétevők egyéni értesítési webhook-végpontokat is megadhatnak az új és a meglévő felügyelt alkalmazási példányokkal kapcsolatos esemény-értesítések fogadásához. Lehetővé teszi, hogy a közzétevő egyéni munkafolyamatokat állítson be az alkalmazások üzembe helyezése, frissítése és törlése során.

## <a name="getting-started"></a>Bevezetés
A felügyelt alkalmazások fogadásának megkezdéséhez hozzon létre egy nyilvános HTTPS-végpontot, és adja meg a Service Catalog alkalmazás definíciójának vagy a Piactéri ajánlat közzétételének idejét.

Az alábbi lépésekkel gyorsan megkezdheti a működést:
1. Hozzon létre egy nyilvános HTTPS-végpontot, amely naplózza a beérkező POST kéréseket, és visszaadja a `200 OK`.
2. Adja hozzá a végpontot a Service Catalog alkalmazás-definícióhoz vagy a Marketplace-ajánlathoz az alább leírtak szerint.
3. Hozzon létre egy felügyelt alkalmazás-példányt, amely az alkalmazás definícióján vagy a Marketplace-ajánlaton hivatkozik.
4. Ellenőrizze, hogy az értesítések fogadása sikeres volt-e.
5. Engedélyezze az engedélyezést az alábbi **végpont-hitelesítés** részben leírtak szerint.
6. Kövesse az alábbi **értesítési séma** dokumentációját az értesítési kérelmek elemzéséhez és az üzleti logikák megvalósításához az értesítés alapján.

## <a name="adding-service-catalog-application-definition-notifications"></a>Service Catalog alkalmazás-definíciós értesítések hozzáadása
#### <a name="azure-portal"></a>Azure Portal
A kezdéshez olvassa el [a Service Catalog-alkalmazás közzététele Azure Portalon keresztül](./publish-portal.md) című cikkét.

![Service Catalog alkalmazás-definíciós értesítések a portálon](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>REST API

> [!NOTE]
> Jelenleg csak egy végpont támogatott a **notificationEndpoints** részeként az alkalmazás definíciójának tulajdonságaiban.

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
## <a name="adding-marketplace-managed-application-notifications"></a>Piactéren felügyelt alkalmazások értesítéseinek hozzáadása
További információkért lásd: [Azure-alkalmazási ajánlat létrehozása](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Service Catalog alkalmazás-definíciós értesítések a portálon](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Eseményindítók
Az alábbi táblázat a EventType + ProvisioningState és az eseményindítók lehetséges kombinációit ismerteti:

EventType | ProvisioningState | Értesítési trigger
---|---|---
PUT | Elfogadva | A felügyelt erőforráscsoport létre lett hozva, és az alkalmazás üzembe helyezése után sikeresen megtörtént. (Az üzembe helyezés előtt a felügyelt RG-on belül a központi telepítés indul.)
PUT | Sikeres | A felügyelt alkalmazás teljes kiépítés sikerült egy PUT után.
PUT | Meghiúsult | Az alkalmazás-példány üzembe helyezésének meghibásodása bármely ponton.
JAVÍTÁS | Sikeres | A felügyelt alkalmazás példányának sikeres JAVÍTÁSát követően a címkék, a JIT hozzáférési házirend vagy a felügyelt identitás frissítése sikerült.
DELETE | Törlés folyamatban | Amint a felhasználó elindít egy felügyelt alkalmazás példányának TÖRLÉSét.
DELETE | Törölve | A felügyelt alkalmazás teljes és sikeres törlése után.
DELETE | Meghiúsult | A törlést blokkoló megszüntetési folyamat során felmerülő bármilyen hiba.
## <a name="notification-schema"></a>Értesítési séma
Amikor felgyorsítja a webhook-végpontot az értesítések kezeléséhez, elemezni kell a hasznos adatokat, hogy a fontos tulajdonságok beolvasása után az értesítésre reagáljon. A Service Catalog és a piactér által felügyelt alkalmazások értesítései számos azonos tulajdonságot biztosítanak az alább látható kis különbséggel.

#### <a name="service-catalog-application-notification-schema"></a>Service Catalog-alkalmazás értesítési sémája
Íme egy példa a Service Catalog szolgáltatásra a felügyelt alkalmazás példányának sikeres kiépítés után.
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

Ha a kiépítés sikertelen, a rendszer a hiba részleteivel kapcsolatos értesítést küld a megadott végpontnak.

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

#### <a name="marketplace-application-notification-schema"></a>Marketplace-alkalmazás értesítési sémája

Íme egy példa a Service Catalog szolgáltatásra a felügyelt alkalmazás példányának sikeres kiépítés után.
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

Ha a kiépítés sikertelen, a rendszer a hiba részleteivel kapcsolatos értesítést küld a megadott végpontnak.

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
EventType | Az értesítést kiváltó esemény típusa. (például "PUT", "javítás", "DELETE")
applicationId | Annak a felügyelt alkalmazásnak a teljes erőforrás-azonosítója, amelyhez az értesítés aktiválva lett. 
eventTime | Az értesítést kiváltó esemény időbélyegzője. (Dátum és idő UTC ISO 8601 formátumban)
ProvisioningState | A felügyelt alkalmazás példányának kiépítési állapota. (például "sikeres", "sikertelen", "Törlés", "törölt")
billingDetails | A felügyelt alkalmazás példányának számlázási adatai. Azokat a resourceUsageId tartalmazza, amelyekkel lekérdezhető a piactér használati adatai.
error | *Csak akkor van megadva, ha a ProvisioningState sikertelen*. A hibát okozó probléma kódját, üzenetét és részleteit tartalmazza.
applicationDefinitionId | *Csak a Service Catalog által felügyelt alkalmazásokhoz van megadva*. Annak az alkalmazás-definíciónak a teljes erőforrás-azonosítóját jelöli, amelyhez a felügyelt alkalmazás példánya ki lett építve.
útiterv | *Csak a piactér által felügyelt alkalmazásokhoz van megadva*. A felügyelt alkalmazás példányának közzétevőjét, ajánlatát, SKU-jának és verziószámát jelöli.

## <a name="endpoint-authentication"></a>Végponti hitelesítés
A webhook-végpont biztonságossá tételéhez és az értesítés hitelességének biztosításához:
- Adjon meg egy lekérdezési paramétert a webhook URI-ja felett, például https://your-endpoint.com?sig=Guid. Az egyes értesítésekkel gyorsan megtekintheti, hogy a lekérdezési paraméter `sig` a várt érték `Guid`-e.
- Adja ki a beolvasás a felügyelt alkalmazás példányát a applicationId. Ellenőrizze, hogy a provisioningState megegyezik-e az értesítés provisioningState, hogy a konzisztencia biztosítható legyen.

## <a name="notification-retries"></a>Értesítési újrapróbálkozások

A felügyelt alkalmazás értesítési szolgáltatása `200 OK` választ vár a webhook-végpontról az értesítésre. Az értesítési szolgáltatás újra próbálkozik, ha a webhook végpontja egy HTTP-hibakódot ad vissza > = 500, 429, vagy ha a végpont átmenetileg nem érhető el. Ha a webhook végpontja 10 órán belül nem érhető el, az értesítési üzenet el lesz dobva, és az újrapróbálkozások le fognak állni.

