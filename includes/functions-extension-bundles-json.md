---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878245"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

A következő tulajdonságok `extensionBundle`érhetők el:

| Tulajdonság | Leírás |
| -------- | ----------- |
| id | A Microsoft Azure Functions bővítmény csomagjainak névtere. |
| version | A telepítandó csomag verziója. A Functions futásidejű mindig a verziótartomány vagy intervallum által meghatározott maximális anamát választja. A fenti verzióérték lehetővé teszi az összes csomag verziót 1.0.0-tól a 2.0.0-ig. További információt a [verziótartományok megadásának időköze- jelölésben](/nuget/reference/package-versioning#version-ranges)talál. |

Bundle verziók növekmény csomagok a csomag változás. A főverzió akkor változik, ha a kötegben lévő csomagok egy főverzióval növekszik. A csomag főverzióinak módosításai általában egybeesnek a Functions futásidejű főverziójának változásával.  

Az alapértelmezett csomag által telepített bővítések aktuális készlete ebben a [extensions.json fájlban](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)van felsorolva.
