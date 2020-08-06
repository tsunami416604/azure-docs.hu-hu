---
title: Engedélyek az Azure Sentinelben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használja az Azure Sentinel a szerepköralapú hozzáférés-vezérlést a felhasználók engedélyeinek kiosztására, valamint az egyes szerepkörökre vonatkozó engedélyezett műveletek azonosítására.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: 7dded3b938444198e72d6eb87476f571dd3f4d78
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836768"
---
# <a name="permissions-in-azure-sentinel"></a>Engedélyek az Azure Sentinelben

Az Azure Sentinel az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) használatával biztosítja az Azure-beli felhasználókhoz, csoportokhoz és szolgáltatásokhoz hozzárendelhető [beépített szerepköröket](../role-based-access-control/built-in-roles.md)   .

A RBAC segítségével szerepköröket hozhat létre és rendelhet hozzá a biztonsági operatív csapatához, hogy megfelelő hozzáférést biztosítson az Azure Sentinelhez. A különböző szerepkörök részletesen szabályozzák, hogy az Azure Sentinel mely felhasználóinak láthatják és tudják megtekinteni. Az Azure-szerepkörök közvetlenül az Azure Sentinel-munkaterülethez rendelhetők (lásd az alábbi megjegyzést), vagy egy olyan előfizetésben vagy erőforráscsoporthoz, amelyhez a munkaterület tartozik, amelyet az Azure Sentinel örököl.

## <a name="roles-for-working-in-azure-sentinel"></a>Az Azure Sentinel használatának szerepkörei

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel-specifikus szerepkörök

Három dedikált beépített Azure Sentinel-szerepkör található.

**Az Azure Sentinel beépített szerepkörei olvasási hozzáférést biztosítanak az Azure Sentinel-munkaterület adataihoz.**

- Az [Azure Sentinel Reader](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) képes megtekinteni az adatforrásokat, az incidenseket, a munkafüzeteket és az egyéb Azure Sentinel-erőforrásokat.

- Az [Azure Sentinel-válaszadó](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) a fentiek mellett az incidensek (hozzárendelés, Elvetés stb.) kezelésére is képes.

- Az [Azure Sentinel közreműködő](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) a fentiek kiegészítéseként létrehozhat és szerkeszthet munkafüzeteket, elemzési szabályokat és más Azure Sentinel-erőforrásokat.

> [!NOTE]
>
> - A legjobb eredmények érdekében ezeket a szerepköröket az Azure Sentinel munkaterületet tartalmazó **erőforráscsoporthoz** kell rendelni. Így a szerepkörök az Azure Sentinel támogatásához központilag telepített összes erőforrásra érvényesek lesznek, mivel ezeket az erőforrásokat is ugyanabba az erőforráscsoporthoz kell helyezni.
>
> - Egy másik lehetőség, hogy közvetlenül az Azure Sentinel **munkaterületen** rendeli hozzá a szerepköröket. Ha ezt teszi, ugyanezeket a szerepköröket is hozzá kell rendelnie a SecurityInsights- **megoldás erőforráshoz** a munkaterületen. Előfordulhat, hogy más erőforrásokhoz is hozzá kell rendelnie őket, és az erőforrásokon folyamatosan kell felügyelni a szerepkör-hozzárendeléseket.

### <a name="additional-roles-and-permissions"></a>További szerepkörök és engedélyek

Előfordulhat, hogy az adott feladatra vonatkozó követelményekkel rendelkező felhasználóknak további szerepköröket vagy konkrét engedélyeket kell rendelniük a feladatok elvégzéséhez.

- A fenyegetésekre adott válaszok automatizálására szolgáló forgatókönyvek használata

    **Az Azure** Sentinel olyan forgatókönyveket használ, amelyekkel automatizált fenyegetésekre reagálhat. A forgatókönyvek **Azure Logic Appsre**épülnek, és külön Azure-erőforrások. Előfordulhat, hogy a biztonsági műveletek csapatának meghatározott tagjaihoz szeretne hozzárendelni Logic Apps biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) műveletekhez. A [logikai alkalmazás közreműködői](../role-based-access-control/built-in-roles.md#logic-app-contributor) szerepkörét vagy a [Logic app operátori](../role-based-access-control/built-in-roles.md#logic-app-operator) szerepkört használva explicit engedélyeket rendelhet a forgatókönyvek használatához.

- Adatforrások összekapcsolása az Azure Sentinel szolgáltatással

    Ahhoz, hogy egy felhasználó **adatösszekötőket**vegyen fel, hozzá kell rendelnie a felhasználó írási engedélyeit az Azure Sentinel munkaterületen. Emellett jegyezze fel a szükséges további engedélyeket az egyes összekötők számára, amint az a megfelelő összekötő lapon szerepel.

A párhuzamos összehasonlításhoz tekintse meg az [alábbi táblázatot](#roles-and-allowed-actions).

### <a name="other-roles-you-might-see-assigned"></a>A hozzárendelt egyéb szerepkörök

Az Azure Sentinel-specifikus Azure-szerepkörök hozzárendelésével más Azure-beli és Log Analytics Azure-szerepköröket is megtalálhat, amelyek más célra is hozzárendelhetők a felhasználókhoz. Vegye figyelembe, hogy ezek a szerepkörök szélesebb körű engedélyeket biztosítanak, amelyek az Azure Sentinel-munkaterülethez és egyéb erőforrásokhoz is hozzáférést biztosítanak:

- **Azure-szerepkörök:** [tulajdonos](../role-based-access-control/built-in-roles.md#owner), [közreműködő](../role-based-access-control/built-in-roles.md#contributor)és [olvasó](../role-based-access-control/built-in-roles.md#reader). Az Azure-szerepkörök hozzáférést biztosítanak az összes Azure-erőforráshoz, beleértve a Log Analytics munkaterületeket és az Azure Sentinel-erőforrásokat.

- **Log Analytics szerepkörök:** [Log Analytics közreműködő](../role-based-access-control/built-in-roles.md#log-analytics-contributor) és [log Analytics olvasó](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics szerepkörök hozzáférést biztosítanak a Log Analytics-munkaterületekhez. 

Ha például az Azure Sentinel **olvasó** szerepkörhöz hozzárendelt felhasználó, de az **Azure Sentinel közreműködő** szerepkört nem, akkor továbbra is szerkesztheti az elemeket az Azure sentinelben, ha az Azure-szintű **közreműködő** szerepkörhöz van rendelve. Ezért ha csak az Azure Sentinelben szeretne engedélyeket adni egy felhasználónak, gondosan távolítsa el a felhasználó korábbi engedélyeit, és ügyeljen arra, hogy ne szüntesse meg a szükséges hozzáférést egy másik erőforráshoz.

## <a name="roles-and-allowed-actions"></a>Szerepkörök és engedélyezett műveletek

Az alábbi táblázat összefoglalja az Azure Sentinel szerepköreit és engedélyezett műveleteit. 

| Role | Forgatókönyvek létrehozása és futtatása| Munkafüzetek, analitikai szabályok és egyéb Azure Sentinel-erőforrások létrehozása és szerkesztése | Incidensek kezelése (elutasítás, hozzárendelés stb.) | Információk, incidensek, munkafüzetek és egyéb Azure Sentinel-erőforrások megtekintése |
|---|---|---|---|---|
| Azure Sentinel Reader | -- | -- | -- | &#10003; |
| Azure Sentinel Responder | -- | -- | &#10003; | &#10003; |
| Azure Sentinel Contributor | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel közreműködő + Logic app közreműködő | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>Egyéni szerepkörök és speciális RBAC

- Az Azure beépített szerepköreinek használatával vagy ahelyett, hogy az Azure Sentinelhez egyéni Azure-szerepköröket is létrehozhat. Az Azure Sentinel Azure-beli egyéni szerepkörei ugyanúgy jönnek létre, mint a többi [Egyéni Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) -szerepkört az Azure Sentinel és az [Azure log Analytics erőforrásaira](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights) [adott engedélyek](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) alapján.

- Az Azure Sentinel-munkaterületen található összes adathoz használhatja a Log Analytics speciális szerepköralapú hozzáférés-vezérlést. Ebbe beletartozik az adattípuson alapuló RBAC és az erőforrás-központú RBAC is. Log Analytics szerepkörökkel kapcsolatos további információkért lásd: [a naplózási adatok és munkaterületek kezelése a Azure monitorban](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan dolgozhat az Azure Sentinel-felhasználók szerepköreivel, és hogy az egyes szerepkörök hogyan teszik lehetővé a felhasználók számára.

* [Azure Sentinel blog](https://aka.ms/azuresentinelblog). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
