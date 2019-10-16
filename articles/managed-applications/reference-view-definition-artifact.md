---
title: Az Azure Managed Application View definition összetevő-referenciája
description: Példát mutat a Azure Managed Applications megjelenítésének definíciós összetevője számára. A fájl neve viewDefinition. JSON.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 1f56f6c9c519bd29423d92a8dc8b8ce5904b523c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332633"
---
# <a name="reference-view-definition-artifact"></a>Hivatkozás: definíciós összetevő megtekintése

Ez a cikk a Azure Managed Applications *viewDefinition. JSON* összetevőjét ismerteti. További információ a létrehozási nézetek konfigurációjával kapcsolatban: a [definíciós összetevő megtekintése](concepts-view-definition.md).

## <a name="view-definition"></a>Meghatározás megtekintése

A következő JSON a Azure Managed Applications *viewDefinition. JSON* fájljának példáját mutatja be:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal](tutorial-create-managed-app-with-custom-provider.md)
- [Hivatkozás: felhasználói felület elemeinek összetevője](reference-createuidefinition-artifact.md)
- [Hivatkozás: telepítési sablon összetevő](reference-main-template-artifact.md)