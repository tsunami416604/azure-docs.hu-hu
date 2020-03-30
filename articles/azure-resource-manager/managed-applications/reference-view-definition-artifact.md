---
title: Definíciós műtermék hivatkozásának megtekintése
description: Egy példa az Azure felügyelt alkalmazások nézetdefiníciós összetevő. A fájl neve viewDefinition.json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651201"
---
# <a name="reference-view-definition-artifact"></a>Hivatkozás: Definíciós műtermék megtekintése

Ez a cikk egy hivatkozás egy *viewDefinition.json* összetevő az Azure felügyelt alkalmazások. A nézetek konfigurációjának szerkesztéséről a [Definíciós termék megtekintése című témakörben](concepts-view-definition.md)talál további információt.

## <a name="view-definition"></a>Meghatározás megtekintése

A következő JSON egy példát mutat be az Azure felügyelt alkalmazások *viewDefinition.json* fájljára:

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

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal](tutorial-create-managed-app-with-custom-provider.md)
- [Hivatkozás: Felhasználói felület elemeinek összetevője](reference-createuidefinition-artifact.md)
- [Hivatkozás: Telepítési sablon műterméke](reference-main-template-artifact.md)