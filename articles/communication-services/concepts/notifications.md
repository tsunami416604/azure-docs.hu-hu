---
title: Értesítések az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Értesítéseket küldhet az Azure kommunikációs szolgáltatásokra épülő alkalmazások felhasználói számára.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: abc2367c309f46ee1b29a51145c67e8d71919774
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91665395"
---
# <a name="communication-services-notifications"></a>Kommunikációs szolgáltatások értesítései

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások csevegési és Meghívási ügyfélszoftvere olyan valós idejű üzenetkezelési csatornát hoz létre, amely lehetővé teszi, hogy a rendszer hatékony, megbízható módon továbbítsa az üzeneteket a csatlakoztatott ügyfeleknek. Ez lehetővé teszi, hogy a bonyolult HTTP-lekérdezési logika megvalósítása nélkül hozzon létre gazdag és valós idejű kommunikációs funkciókat az alkalmazásaiba. A mobil alkalmazásokban azonban ez a jelző csatorna csak akkor marad csatlakoztatva, ha az alkalmazás az előtérben van aktív. Ha azt szeretné, hogy a felhasználók fogadják a bejövő hívásokat vagy csevegési üzeneteket, amíg az alkalmazása a háttérben van, akkor leküldéses értesítéseket kell használnia.

A leküldéses értesítések segítségével adatokat küldhet az alkalmazásból a felhasználók mobileszközökön. Leküldéses értesítések segítségével megjelenítheti a párbeszédpanelt, hanglejátszást készíthet, vagy megjelenítheti a bejövő hívás felhasználói felületét. Az Azure kommunikációs szolgáltatásai [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) -és Azure- [Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) integrációs szolgáltatásokat biztosítanak, amelyek lehetővé teszik leküldéses értesítések hozzáadását az alkalmazásokba.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Leküldéses értesítések indítása Azure Event Grid használatával

Az Azure kommunikációs szolgáltatásai megbízható, méretezhető és biztonságos módon biztosítják a valós idejű események bejelentésének [Azure Event Gridét](https://azure.microsoft.com/services/event-grid/) . Ezt az integrációt kihasználva olyan értesítési szolgáltatást hozhat létre, amely mobil leküldéses értesítéseket továbbít a felhasználóknak egy Azure- [függvényt](https://docs.microsoft.com/azure/azure-functions/functions-overview) vagy webhookot indító Event Grid-előfizetés létrehozásával.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Ábra, amely bemutatja, hogyan integrálódik a kommunikációs szolgáltatások a Event Grid.":::

További információ az [Azure kommunikációs szolgáltatásokban zajló események kezelésére](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Leküldéses értesítések kézbesítése az Azure Notification Hubs használatával

Csatlakoztathat egy Azure Notification hub-t a kommunikációs szolgáltatások erőforrásához, hogy automatikusan küldjön leküldéses értesítéseket a felhasználó mobileszközön, amikor bejövő hívást kapnak. Ezeket a leküldéses értesítéseket kell használnia az alkalmazás háttérben való felébresztéséhez és a felhasználói felület megjelenítéséhez, amely lehetővé teszi a felhasználó számára a hívás elfogadását vagy elutasítását. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Ábra, amely bemutatja, hogyan integrálódik a kommunikációs szolgáltatások a Event Grid.":::

A kommunikációs szolgáltatások az Azure Notification hub-t áteresztő szolgáltatásként használják a platform-specifikus leküldéses értesítési szolgáltatásokkal való kommunikációhoz a [Direct Send](https://docs.microsoft.com/rest/api/notificationhubs/direct-send) API használatával. Ez lehetővé teszi a meglévő Azure Notification hub-erőforrások és-konfigurációk újrafelhasználását, hogy alacsony késésű, megbízható hívási értesítéseket nyújtson az alkalmazásaihoz.

### <a name="notification-hub-provisioning"></a>Értesítési központ kiépítés 

Ha Notification Hubs használatával szeretne leküldéses értesítéseket küldeni az ügyféleszközök számára, [hozzon létre egy értesítési](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal) központot a kommunikációs szolgáltatások erőforrásával megegyező előfizetésen belül. Az Azure Notification Hubs-t konfigurálni kell a használni kívánt platform Notifications szolgáltatáshoz. Ha meg szeretné tudni, hogyan kérhet le leküldéses értesítéseket az ügyfélalkalmazás Notification Hubsről, tekintse meg a [Notification Hubs első lépéseivel foglalkozó](https://docs.microsoft.com/azure/notification-hubs/ios-sdk-get-started) témakört, és válassza ki a cél ügyféloldali platformot a lap tetején található legördülő listából.

Miután konfigurálta az értesítési központot, társíthatja azt a kommunikációs szolgáltatások erőforrásaihoz úgy, hogy a Azure Resource Manager ügyfelet vagy a Azure Portalon keresztül megadja a hub kapcsolati karakterláncát. A kapcsolatok karakterláncának "Send" engedélyeket kell tartalmaznia. Javasoljuk, hogy hozzon létre egy másik hozzáférési szabályzatot, és csak az Ön központjának megfelelő engedélyeket küldje el. További információ a [Notification Hubs biztonsági és hozzáférési szabályzatokról](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security)

> Megjegyzés: Apple Push Notification Service VOIP-értesítések engedélyezéséhez be kell állítania az értesítési központ nevét az alkalmazáscsomag AZONOSÍTÓjának az utótaggal való megadása érdekében `.voip` . Lásd: [a APNS VoIP használata a Notification Hubson](https://docs.microsoft.com/azure/notification-hubs/voip-apns).

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Az értesítési központ konfigurálása a Azure Resource Manager ügyfél használatával

Az Azure Resource Managerba való bejelentkezéshez hajtsa végre a következőket, és jelentkezzen be a hitelesítő adataival.

```console
armclient login
```

 Miután sikeresen bejelentkezett, futtassa a következőt az értesítési központ kiépítéséhez:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Az értesítési központ konfigurálása a Azure Portal használatával

A portálon navigáljon az Azure kommunikációs szolgáltatások erőforrásaihoz. A kommunikációs szolgáltatások erőforráson belül válassza a kommunikációs szolgáltatások lap bal oldali menüjének leküldéses értesítések elemét, és kapcsolja össze a korábban kiépített értesítési központot. Itt meg kell adnia a kapcsolódási karakterláncot és az erőforrás-azonosítót:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Ábra, amely bemutatja, hogyan integrálódik a kommunikációs szolgáltatások a Event Grid.":::

#### <a name="device-registration"></a>Eszközregisztráció 

Tekintse át a [hanghívási](../quickstarts/voice-video-calling/getting-started-with-calling.md) rövid útmutatót, amelyből megtudhatja, hogyan regisztrálja az eszközt a kommunikációs szolgáltatásokkal.

## <a name="next-steps"></a>További lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](https://docs.microsoft.com/azure/event-grid/overview)
* Ha többet szeretne megtudni az Azure Notification hub-fogalmakról, tekintse meg az [azure Notification Hubs dokumentációját](https://docs.microsoft.com/azure/notification-hubs/) .
