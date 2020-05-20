---
title: Azure arc-kompatibilis bevezetési egyszerű szolgáltatás létrehozása (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Azure arc-kompatibilis bevezetési egyszerű szolgáltatás létrehozása '
keywords: Kubernetes, arc, Azure, tárolók
ms.openlocfilehash: f9f750980d8a8b5d8190ba0b399fe068f1dd99c7
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680793"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Azure arc-kompatibilis bevezetési egyszerű szolgáltatás létrehozása (előzetes verzió)

## <a name="overview"></a>Áttekintés

Amikor egy fürt bekerül az Azure-ba, a fürtön futó ügynököknek hitelesíteniük kell magukat Azure Resource Manager a regisztráció részeként. Az `connectedk8s` Azure CLI-bővítmény automatikusan létrehozza az egyszerű szolgáltatást. Előfordulhat azonban, hogy a CLI-automatizálás nem működik:

* A szervezet általában korlátozza az egyszerű szolgáltatások létrehozását
* A fürtöt bevezető felhasználó nem rendelkezik megfelelő engedélyekkel az egyszerű szolgáltatások létrehozásához

Ehelyett hozzuk létre az egyszerű szolgáltatást sávon kívül, majd továbbítsa a rendszerbiztonsági tag számára az Azure CLI-bővítményt.

## <a name="create-a-new-service-principal"></a>Új egyszerű szolgáltatás létrehozása

Hozzon létre egy új szolgáltatásnevet egy tájékoztató névvel. Vegye figyelembe, hogy a névnek egyedinek kell lennie a Azure Active Directory bérlő számára:

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Kimeneti**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Engedélyek kiosztása

Az új egyszerű szolgáltatás létrehozása után rendelje hozzá az "Azure arc for Kubernetes bevezetéséhez" szerepkört az újonnan létrehozott rendszerbiztonsági tag számára. Ez egy beépített Azure-szerepkör korlátozott engedélyekkel, amelyek csak a rendszerbiztonsági tag számára engedélyezik a fürtök regisztrálását az Azure-ban. A rendszerbiztonsági tag nem tudja frissíteni, törölni vagy módosítani az előfizetésben lévő többi fürtöt és erőforrást.

A korlátozott képességek miatt az ügyfelek könnyedén újra használhatják ezt a résztvevőt több fürt bevezetéséhez.

Az engedélyek tovább korlátozhatók, `--scope` Ha a szerepkör hozzárendeléséhez a megfelelő argumentumot továbbítják. Ez lehetővé teszi, hogy az ügyfelek korlátozzák a fürt regisztrációját. A különböző paraméterek a következő forgatókönyveket támogatják `--scope` :

| Erőforrás  | `scope` argumentum| Hatás |
| ------------- | ------------- | ------------- |
| Előfizetés | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Az egyszerű szolgáltatás minden olyan fürtöt regisztrálhat egy meglévő erőforráscsoporthoz a megadott előfizetésben |
| Erőforráscsoport | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Az egyszerű szolgáltatásnév __csak__ a fürtök regisztrálását tudja regisztrálni az erőforráscsoporthoz`myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the apropriate scope
```

**Kimeneti**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Egyszerű szolgáltatásnév használata az Azure CLI-vel

Az újonnan létrehozott egyszerű szolgáltatásnév hivatkozása:

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>További lépések

* [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
