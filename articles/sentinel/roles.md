---
title: Engedélyek az Azure Sentinelben | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogy az Azure Sentinel hogyan használja a szerepköralapú hozzáférés-vezérlést a felhasználók engedélyek hozzárendeléséhez, és azonosítja az egyes szerepkörökhöz engedélyezett műveleteket.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587175"
---
# <a name="permissions-in-azure-sentinel"></a>Engedélyek az Azure Sentinelben

Az Azure Sentinel [szerepköralapú hozzáférés-vezérlési (RBAC)](../role-based-access-control/role-assignments-portal.md)használatával biztosít [olyan beépített szerepköröket,](../role-based-access-control/built-in-roles.md) amelyek hozzárendelhetők az Azure-ban lévő felhasználókhoz, csoportokhoz és szolgáltatásokhoz.

Az RBAC használatával szerepköröket használhat és hozhat létre a biztonsági műveleti csapaton belül, hogy megfelelő hozzáférést biztosítson az Azure Sentinelhez. A szerepkörök alapján részletesen szabályozhatja, hogy az Azure Sentinelhez hozzáféréssel rendelkező felhasználók mit láthatnak. RBAC-szerepköröket közvetlenül rendelhet az Azure Sentinel-munkaterületen, vagy egy olyan előfizetéshez vagy erőforráscsoporthoz, amelyhez a munkaterület tartozik.

Három speciális beépített Azure Sentinel-szerepkör van.  
**Az Azure Sentinel beépített szerepkörei olvasási hozzáférést biztosítanak az Azure Sentinel-munkaterületen lévő adatokhoz.**
- [Azure Sentinel olvasó](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel válaszadó](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Az Azure Sentinel közreműködője](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Az Azure Sentinel dedikált RBAC-szerepkörein kívül vannak olyan Azure- és Log Analytics RBAC-szerepkörök is, amelyek szélesebb körű engedélyeket adhatnak, amelyek hozzáférést biztosítanak az Azure Sentinel-munkaterülethez és más erőforrásokhoz:

- **Azure-szerepkörök:** [Tulajdonos](../role-based-access-control/built-in-roles.md#owner), [Közreműködő](../role-based-access-control/built-in-roles.md#contributor)és [Olvasó](../role-based-access-control/built-in-roles.md#reader). Az Azure-szerepkörök hozzáférést biztosítanak az összes Azure-erőforráshoz, beleértve a Log Analytics-munkaterületeket és az Azure Sentinel-erőforrásokat.

-   **Log Analytics-szerepkörök:** [Log Analytics-közreműködő](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [Log Analytics-olvasó](../role-based-access-control/built-in-roles.md#log-analytics-reader). A Log Analytics-szerepkörök hozzáférést biztosítanak az összes Log Analytics-munkaterületen. 

> [!NOTE]
> A Log Analytics-szerepkörök olvasási hozzáférést is biztosítanak az összes Azure-erőforrásközött, de csak írási engedélyeket rendelnek a Log Analytics-erőforrásokhoz.


Például egy azure **Sentinel-olvasóhoz** és **Azure-közreműködőhöz** (nem **azure Sentinel közreműködői)** szerepkörhöz rendelt felhasználó szerkeszthet adatokat az Azure Sentinelben, bár csak **Sentinel-olvasó-engedélyekkel** rendelkeznek. Ezért ha csak az Azure Sentinel ben szeretne engedélyeket adni, gondosan távolítsa el a felhasználó korábbi engedélyeit, ügyelve arra, hogy ne szakítsa meg a szükséges engedélyszerepkört egy másik erőforráshoz.

> [!NOTE]
>- Az Azure Sentinel forgatókönyveket használ az automatikus fenyegetésre adott válaszhoz. A forgatókönyvek az Azure Logic Apps alkalmazást használják, és különálló Azure-erőforrást jelentenek. Előfordulhat, hogy szeretné hozzárendelni a biztonsági műveleti csapat bizonyos tagjait azzal a lehetőséggel, hogy a Logic Apps biztonsági vezénylési, automatizálási és válasz (szárnyalási) műveleteket használjon. Használhatja a [Logic App közreműködői](../role-based-access-control/built-in-roles.md#logic-app-contributor) szerepkör vagy a [Logic App operátor](../role-based-access-control/built-in-roles.md#logic-app-operator) szerepkör explicit engedélyt a forgatókönyvek használatához.
>- Adatösszekötők hozzáadásához az egyes összekötők szükséges szerepkörök összekötőtípusonként vannak, és a megfelelő összekötő lapon jelennek meg. Emellett az adatforrások csatlakoztatásához írási engedéllyel kell rendelkeznie az Azure Sentinel-munkaterülethez.



## <a name="roles-and-allowed-actions"></a>Szerepkörök és engedélyezett műveletek

Az alábbi táblázat az Azure Sentinel szerepköreit és engedélyezett feladatait jeleníti meg. Az X azt jelzi, hogy a művelet engedélyezett az adott szerepkörhöz.

| Szerepkör | Forgatókönyvek létrehozása és futtatása| Irányítópultok, analitikus szabályok és egyéb Azure Sentinel-erőforrások létrehozása és szerkesztése | Incidensek kezelése (elvetés, hozzárendelés stb.) | Adatok, incidensek, irányítópultok és egyéb Azure Sentinel-erőforrások megtekintése |
|--- |---|---|---|---|
| Azure Sentinel olvasó | -- | -- | -- | X |
| Azure Sentinel válaszadó|--|--| X | X |
| Az Azure Sentinel közreműködője | -- | X | X | X |
| Az Azure Sentinel közreműködője + Logic App közreműködője | X | X | X | X |


> [!NOTE]
> - Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Például rendelje hozzá az Azure Sentinel közreműködői szerepkört csak azokhoz a felhasználókhoz, akiknek szabályokat vagy irányítópultokat kell létrehozniuk.
> - Azt javasoljuk, hogy az Azure Sentinel engedélyeket az erőforráscsoport hatókörében állítson be, így a felhasználó hozzáférhet az összes Azure Sentinel-munkaterülethez ugyanabban az erőforráscsoportban.
>
## <a name="building-custom-rbac-roles"></a>Egyéni RBAC-szerepkörök létrehozása

A beépített RBAC-szerepkörök mellett vagy azok helyett egyéni RBAC-szerepköröket is létrehozhat az Azure Sentinelhez. Az Azure Sentinel egyéni RBAC szerepkörei ugyanúgy jönnek létre, mint más [egyéni Azure RBAC-szerepköröket](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) az Azure Sentinel és az Azure Log [Analytics-erőforrásokra](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)vonatkozó [engedélyek](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) alapján.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Speciális RBAC az Azure Sentinelben tárolt adatokhoz
  
Használhatja a Log Analytics speciális szerepköralapú hozzáférés-vezérlés az azure Sentinel-munkaterületen az adatok között. Ez magában foglalja a szerepköralapú hozzáférés-vezérlésadattípusonként és az erőforrás-központú szerepköralapú hozzáférés-vezérlést is. A Log Analytics-szerepkörökről további információt a [Naplóadatok és -munkaterületek kezelése az Azure Monitorban című témakörben](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)talál.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan dolgozhat az Azure Sentinel-felhasználók szerepköreivel, és hogy az egyes szerepkörök mire teszik lehetővé a felhasználók számára.

* [Azure Sentinel Blog](https://aka.ms/azuresentinelblog). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
