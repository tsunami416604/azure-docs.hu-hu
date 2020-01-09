---
title: CreateUiDefinition-összetevő
description: Bemutatja, hogyan hozhatja létre az createUiDefinition-összetevőt egy Azure által felügyelt alkalmazáshoz. A fájl neve createUiDefinition. JSON.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651487"
---
# <a name="reference-user-interface-elements-artifact"></a>Hivatkozás: felhasználói felület elemeinek összetevője

Ez a cikk a Azure Managed Applications *createUiDefinition. JSON* összetevőjét ismerteti. A felhasználói felület elemeinek létrehozásáról további információt a [felhasználói felület elemeinek létrehozása](create-uidefinition-elements.md)című témakörben talál.

## <a name="user-interface-elements"></a>Felhasználó felület elemei

A következő JSON a Azure Managed Applications *createUiDefinition. JSON* fájljának példáját mutatja be:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: felügyelt alkalmazás létrehozása egyéni műveletekkel és erőforrásokkal](tutorial-create-managed-app-with-custom-provider.md)
- [Hivatkozás: telepítési sablon összetevő](reference-main-template-artifact.md)
- [Hivatkozás: definíciós összetevő megtekintése](reference-view-definition-artifact.md)