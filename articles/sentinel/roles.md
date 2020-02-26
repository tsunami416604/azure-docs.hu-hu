---
title: Engedélyek az Azure Sentinelben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használja az Azure Sentinel a szerepköralapú hozzáférés-vezérlést a felhasználók engedélyeinek kiosztásához és az egyes szerepkörökhöz engedélyezett műveletek azonosításához.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587175"
---
# <a name="permissions-in-azure-sentinel"></a>Engedélyek az Azure Sentinelben

Az Azure Sentinel [szerepköralapú Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md)használatával biztosítja a [beépített szerepköröket](../role-based-access-control/built-in-roles.md) , amelyek az Azure-ban felhasználókhoz, csoportokhoz és szolgáltatásokhoz rendelhetők.

A RBAC használatával a biztonsági műveleti csapaton belül szerepköröket hozhat létre, és a megfelelő hozzáférést biztosíthatja az Azure Sentinelhez. A szerepkörök alapján részletesen szabályozhatja, hogy mely felhasználók férhetnek hozzá az Azure Sentinel szolgáltatáshoz. RBAC-szerepköröket közvetlenül az Azure Sentinel-munkaterületen, vagy egy olyan előfizetéshez vagy erőforráscsoporthoz rendelhet hozzá, amelyhez a munkaterület tartozik.

Három speciális beépített Azure Sentinel-szerepkör létezik.  
**Az Azure Sentinel beépített szerepkörei olvasási hozzáférést biztosítanak az Azure Sentinel-munkaterület adataihoz.**
- [Azure Sentinel-olvasó](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel-válaszadó](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel közreműködő](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Az Azure Sentinel dedikált RBAC szerepkörein kívül olyan Azure-és Log Analytics-szerepkörök is rendelkezésre állnak, amelyek az Azure Sentinel-munkaterülethez és egyéb erőforrásokhoz való hozzáférést biztosító, szélesebb körű engedélyeket is RBAC:

- **Azure-szerepkörök:** [tulajdonos](../role-based-access-control/built-in-roles.md#owner), [közreműködő](../role-based-access-control/built-in-roles.md#contributor)és [olvasó](../role-based-access-control/built-in-roles.md#reader). Az Azure-szerepkörök hozzáférést biztosítanak az összes Azure-erőforráshoz, beleértve a Log Analytics munkaterületeket és az Azure Sentinel-erőforrásokat.

-   **Log Analytics szerepkörök:** [Log Analytics közreműködő](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [log Analytics olvasó](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics szerepkörök hozzáférést biztosítanak az összes Log Analytics-munkaterülethez. 

> [!NOTE]
> Log Analytics szerepkörök olvasási hozzáférést is biztosítanak az összes Azure-erőforráshoz, de csak írási engedélyeket rendelnek Log Analytics erőforrásokhoz.


Például az **Azure Sentinel Reader** és az **Azure közreműködő** (nem az **Azure Sentinel közreműködői**) szerepkörökhöz hozzárendelt felhasználó szerkesztheti az Azure Sentinel-ben tárolt adatait, de csak a **Sentinel-olvasó** engedélyei vannak. Ezért, ha csak az Azure Sentinelben szeretne engedélyeket megadni, gondosan távolítsa el a felhasználó korábbi engedélyeit, így biztosítva, hogy ne szüntesse meg a szükséges jogosultsági szerepkört egy másik erőforráshoz.

> [!NOTE]
>- Az Azure Sentinel olyan forgatókönyveket használ, amelyekkel automatizált fenyegetésekre reagálhat. A forgatókönyvek kihasználják Azure Logic Apps és egy különálló Azure-erőforrást. Előfordulhat, hogy a Security Operations csapatának meghatározott tagjait szeretné hozzárendelni a biztonsági előkészítési, automatizálási és reagálási (SZÁRNYALó) műveletek Logic Apps használatához. A [logikai alkalmazás közreműködői](../role-based-access-control/built-in-roles.md#logic-app-contributor) szerepkörét vagy a [Logic app operátori](../role-based-access-control/built-in-roles.md#logic-app-operator) szerepkört használva explicit engedélyeket rendelhet a forgatókönyvek használatához.
>- Adatösszekötők hozzáadásához az egyes összekötők számára szükséges szerepköröket összekötő típusaként kell megadni, és a megfelelő összekötő lapon vannak felsorolva. Emellett az adatforrás összekapcsolásához írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.



## <a name="roles-and-allowed-actions"></a>Szerepkörök és engedélyezett műveletek

Az alábbi táblázat a szerepköröket és az engedélyezett műveleteket mutatja be az Azure Sentinelben. Az X azt jelzi, hogy a művelet engedélyezett a szerepkörhöz.

| Szerepkör | Forgatókönyvek létrehozása és futtatása| Irányítópultok, analitikai szabályok és egyéb Azure Sentinel-erőforrások létrehozása és szerkesztése | Incidensek kezelése (elutasítás, hozzárendelés stb.) | Információk, incidensek, irányítópultok és egyéb Azure Sentinel-erőforrások megtekintése |
|--- |---|---|---|---|
| Azure Sentinel-olvasó | -- | -- | -- | X |
| Azure Sentinel-válaszadó|--|--| X | X |
| Azure Sentinel közreműködő | -- | X | X | X |
| Azure Sentinel közreműködő + Logic app közreműködő | X | X | X | X |


> [!NOTE]
> - Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Például rendelje hozzá az Azure Sentinel közreműködő szerepkört csak azokhoz a felhasználókhoz, akiknek szabályokat vagy irányítópultokat kell létrehozniuk.
> - Azt javasoljuk, hogy állítsa be az Azure Sentinel engedélyeit az erőforráscsoport hatókörében, így a felhasználó hozzáférhet az ugyanabban az erőforráscsoporthoz tartozó összes Azure Sentinel-munkaterülethez.
>
## <a name="building-custom-rbac-roles"></a>Egyéni RBAC-szerepkörök kiépítése

A beépített RBAC-szerepkörök használata mellett vagy ahelyett is létrehozhat egyéni RBAC-szerepköröket az Azure Sentinel számára. Az Azure Sentinel egyéni RBAC szerepkörei ugyanúgy jönnek létre, mint a többi [Egyéni Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) -szerepkört az Azure Sentinel és az [Azure log Analytics erőforrásaira](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights) [adott engedélyek](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) alapján.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Speciális RBAC az Azure Sentinelben tárolt adattárakban
  
Az Azure Sentinel-munkaterületen található összes adathoz használhatja a Log Analytics speciális szerepköralapú hozzáférés-vezérlést. Ez magában foglalja a szerepköralapú hozzáférés-vezérlés adattípust és az erőforrás-központú szerepköralapú hozzáférés-vezérlést is. Log Analytics szerepkörökkel kapcsolatos további információkért lásd: [a naplózási adatok és munkaterületek kezelése a Azure monitorban](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan dolgozhat az Azure Sentinel-felhasználók szerepköreivel, és hogy az egyes szerepkörök hogyan teszik lehetővé a felhasználók számára.

* [Azure Sentinel blog](https://aka.ms/azuresentinelblog). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
