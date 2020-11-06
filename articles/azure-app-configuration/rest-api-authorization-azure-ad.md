---
title: Azure app Configuration REST API – Azure Active Directory engedélyezés
description: Azure Active Directory használata az Azure-alkalmazások konfigurálásához a REST API használatával
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424182"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory Authorization-REST API referenciája

Ha Azure Active Directory (Azure AD-) hitelesítést használ, az engedélyezést az Azure szerepköralapú Access Control (RBAC) kezeli. Az Azure RBAC használatához a felhasználóknak hozzá kell rendelniük a szerepköröket az erőforrásokhoz való hozzáférés biztosítása érdekében. Minden szerepkör olyan műveleteket tartalmaz, amelyeket a szerepkörhöz rendelt felhasználók el tudnak végezni.

## <a name="roles"></a>Szerepkörök

A következő szerepkörök olyan beépített szerepkörök, amelyek alapértelmezés szerint az Azure-előfizetésekben érhetők el:

- **Azure-alkalmazás konfigurációs adatának tulajdonosa** : Ez a szerepkör teljes hozzáférést biztosít az összes művelethez.
- **Azure-alkalmazás konfigurációs Adatolvasója** : Ez a szerepkör lehetővé teszi az olvasási műveleteket.

## <a name="actions"></a>Műveletek

A szerepkörök tartalmazzák az adott szerepkörhöz rendelt felhasználók által végrehajtható műveletek listáját. Az Azure app Configuration a következő műveleteket támogatja:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Ez a művelet lehetővé teszi az alkalmazás-konfigurációs kulcs-érték erőforrások, például a/KV és a/labels. olvasási hozzáférését
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Ez a művelet lehetővé teszi az írási hozzáférést az alkalmazás konfigurációs kulcs-érték erőforrásaihoz.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Ez a művelet lehetővé teszi az alkalmazások konfigurációs kulcs-érték erőforrásainak törlését. Megjegyzés: az erőforrás törlése visszaadja a törölt kulcs-értéket.

## <a name="errors"></a>Hibák

```http
HTTP/1.1 403 Forbidden
```

**OK:** A kérést készítő résztvevő nem rendelkezik a kért művelet végrehajtásához szükséges engedélyekkel.
**Megoldás:** Rendelje hozzá a kért művelet végrehajtásához szükséges szerepkört a kérést elvégző résztvevőhöz.

## <a name="managing-role-assignments"></a>Szerepkör-hozzárendelések kezelése

A szerepkör-hozzárendelések kezelése az [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) eljárásainak használatával történik, amelyek az összes Azure-szolgáltatásban standard szintűek. Ez az Azure CLI-vel, a PowerShell-lel, a Azure Portalsal és egyebekkel is lehetséges. A szerepkör-hozzárendelések létrehozásáról az [itt](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)található hivatalos dokumentációban olvashat.
