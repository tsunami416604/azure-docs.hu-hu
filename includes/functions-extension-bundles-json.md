---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381557"
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

Az alapértelmezett csomag által telepített bővítések aktuális készlete ebben a [extensions.json fájlban](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)van felsorolva.
