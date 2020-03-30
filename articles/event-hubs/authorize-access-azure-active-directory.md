---
title: Azure Active Directoryval való hozzáférés engedélyezése
description: Ez a cikk az Azure Active Directory használatával az Event Hubs-erőforrásokhoz való hozzáférés engedélyezéséről nyújt tájékoztatást.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 1fa8f7a48c03ead7e939185b23834b3049b3e21c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064865"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával
Az Azure Event Hubs támogatja az Azure Active Directory (Azure AD) használatát az Event Hubs-erőforrásokra vonatkozó kérelmek engedélyezéséhez. Az Azure AD használatával szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket adhat egy rendszerbiztonsági tagnak, amely lehet felhasználó vagy egy egyszerű alkalmazásszolgáltatás. Ha többet szeretne megtudni a szerepkörökről és a szerepkör-hozzárendelésekről, olvassa [el a Különböző szerepkörök ismertetése című témakört.](../role-based-access-control/overview.md)

## <a name="overview"></a>Áttekintés
Amikor egy rendszerbiztonsági tag (egy felhasználó vagy egy alkalmazás) megpróbál hozzáférni egy Event Hubs erőforráshoz, a kérelmet engedélyezni kell. Az Azure AD-vel az erőforráshoz való hozzáférés két lépésből áll. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és egy OAuth 2.0-s jogkivonatot ad vissza. A jogkivonat ot kérő `https://eventhubs.azure.net/`erőforrás neve a. A Kafka-ügyfelek esetében a jogkivonatot kérő erőforrás a `https://<namespace>.servicebus.windows.net`.
 1. Ezután a jogkivonat egy kérelem részeként kerül átadásra az Event Hubs szolgáltatásnak a megadott erőforráshoz való hozzáférés engedélyezéséhez.

A hitelesítési lépés megköveteli, hogy egy alkalmazáskérelem tartalmazzon egy OAuth 2.0 hozzáférési jogkivonatot futásidőben. Ha egy alkalmazás fut egy Azure-entitás, például egy Azure virtuális gép, egy virtuális gép méretezési csoport, vagy egy Azure Function-alkalmazás, egy felügyelt identitás használatával hozzáférhet az erőforrásokhoz. Ha meg szeretné tudni, hogyan hitelesítheti a felügyelt identitás okait az Event Hubs szolgáltatásba, olvassa [el az Azure Event Hubs-erőforrásokhoz való hozzáférés hitelesítése az Azure Active Directoryval és az Azure Resources felügyelt identitásaival.](authenticate-managed-identity.md) 

Az engedélyezési lépés megköveteli, hogy egy vagy több RBAC szerepkörök legyenek hozzárendelve a rendszerbiztonsági taghoz. Az Azure Event Hubs olyan RBAC-szerepköröket biztosít, amelyek az Event Hubs-erőforrások engedélykészleteit foglalják magukban. A rendszerbiztonsági taghoz rendelt szerepkörök határozzák meg a rendszerbiztonsági tag engedélyeit. Az RBAC-szerepkörökről további információt az [Azure Event Hubs beépített RBAC szerepkörei című témakörben talál.](#built-in-rbac-roles-for-azure-event-hubs) 

Natív alkalmazások és webes alkalmazások, amelyek az Event Hubs kéréseket is engedélyezheti az Azure AD-vel. Ha meg szeretné tudni, hogyan kérhet hozzáférési jogkivonatot, és hogyan használhatja az Event Hubs-erőforrásokra vonatkozó kérelmek engedélyezésére, olvassa [el az Azure Event Hubs-hoz való hozzáférés hitelesítése az Azure AD-vel egy alkalmazásból.](authenticate-application.md) 

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-szerepkörök hozzárendelése a hozzáférési jogokhoz
Az Azure Active Directory (Azure AD) [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)révén engedélyezi a védett erőforrásokhoz való hozzáférési jogokat. Az Azure Event Hubs olyan beépített RBAC-szerepköröket határoz meg, amelyek az eseményközpont-adatok eléréséhez használt közös engedélykészleteket foglalják magukban, és egyéni szerepköröket is definiálhat az adatok eléréséhez.

Ha egy RBAC-szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az Access hatóköre az előfizetés, az erőforráscsoport, az Event Hubs névtér vagy bármely erőforrás szintje. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, vagy egy alkalmazás egyszerű alkalmazás, vagy egy [felügyelt identitás az Azure-erőforrások.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Beépített RBAC-szerepkörök az Azure Event Hubs-hoz
Az Azure a következő beépített RBAC-szerepköröket biztosítja az Event Hubs-adatokhoz való hozzáférés engedélyezéséhez az Azure AD és az OAuth használatával:

- [Azure Event Hubs Data tulajdonosa:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)Ezzel a szerepkörral teljes hozzáférést biztosít az Event Hubs-erőforrásokhoz.
- [Azure Event Hubs Data feladó:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)Ezzel a szerepkörral, hogy a küldési hozzáférést az Event Hubs erőforrások.
- [Azure Event Hubs Data receiver:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)Ezzel a szerepkörral adja meg a fogyasztó/fogadó hozzáférést az Event Hubs erőforrásokhoz.

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt rbac szerepkört rendelne egy rendszerbiztonsági taghoz, határozza meg a rendszerbiztonsági tag hozzáférési tartományát. A legjobb gyakorlatok azt diktálják, hogy mindig a legjobb, ha csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyeken a legszűkebb hatókörtől kezdve az Event Hubs-erőforrásokhoz való hozzáférést hatókörbe lehet érni:

- **Fogyasztói csoport**: Ebben a hatókörben a szerepkör-hozzárendelés csak erre az entitásra vonatkozik. Jelenleg az Azure Portalon nem támogatja az RBAC-szerepkör hozzárendelése egy rendszerbiztonsági tag ezen a szinten. 
- **Eseményközpont**: A szerepkör-hozzárendelés az Event Hub entitásra és az alatta lévő fogyasztói csoportra vonatkozik.
- **Névtér**: A szerepkör-hozzárendelés az Event Hubs teljes topológiájára kiterjed a névtér alatt és a hozzá társított fogyasztói csoportra.
- **Erőforráscsoport**: A szerepkör-hozzárendelés az erőforráscsoport összes Eseményközpont-erőforrásra vonatkozik.
- **Előfizetés:** A szerepkör-hozzárendelés az összes Event Hubs erőforrásra vonatkozik az előfizetés összes erőforráscsoportjában.

> [!NOTE]
> - Ne feledje, hogy az RBAC szerepkör-hozzárendelések propagálása akár öt percet is igénybe vehet. 
> - Ez a tartalom az Apache Kafka eseményelosztóira és eseményközpontjaira egyaránt vonatkozik. A Kafka-támogatás eseményközpontjairól további információt a [Kafka - biztonság és hitelesítés eseményközpontjaiban talál.](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)


A beépített szerepkörök definiálásáról a [Szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md#management-and-data-operations)című témakörben talál további információt. Az egyéni RBAC-szerepkörök létrehozásáról az [Egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérléshez című](../role-based-access-control/custom-roles.md)témakörben talál további információt.



## <a name="samples"></a>Példák
- [Microsoft.Azure.EventHubs mintákat](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Ezek a minták a régi **Microsoft.Azure.EventHubs-könyvtárat** használják, de könnyedén frissítheti azt a legújabb **Azure.Messaging.EventHubs-könyvtár** használatával. Ha át szeretné helyezni a mintát a régi könyvtár használatából az újkönyvtárba, olvassa el a [Microsoft.Azure.EventHubs-ról az Azure.Messaging.EventHubs szolgáltatásba való áttelepítés útmutatóját.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)
- [Az Azure.Messaging.EventHubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Ez a minta frissítve lett a legújabb **Azure.Messaging.EventHubs-könyvtár** használatával.
- [Eseményközpontok a Kafka - OAuth mintákhoz.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) 


## <a name="next-steps"></a>További lépések
- Megtudhatja, hogy miként rendelhet hozzá egy beépített RBAC-szerepkört egy rendszerbiztonsági taghoz, olvassa [el a Hozzáférés hitelesítése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával című témakört.](authenticate-application.md)
- [Ismerje meg, hogyan hozhat létre egyéni szerepköröket az RBAC segítségével.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)
- Az [Azure Active Directory eh szolgáltatással való használata](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Lásd a következő kapcsolódó cikkeket:

- [Az Azure-eseményközpontoknak az Azure Active Directory használatával történő hitelesítése](authenticate-application.md)
- [Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez](authenticate-managed-identity.md)
- [Az Azure Event Hubs-ra irányuló kérelmek hitelesítése megosztott hozzáférésű aláírásokkal](authenticate-shared-access-signature.md)
- [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz megosztott hozzáférési aláírásokkal](authorize-access-shared-access-signature.md)
