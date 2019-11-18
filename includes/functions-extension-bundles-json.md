---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129072"
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

A `extensionBundle`ben a következő tulajdonságok érhetők el:

| Tulajdonság | Leírás |
| -------- | ----------- |
| id | Microsoft Azure functions bővítmények névterei. |
| version | A telepítendő csomag verziószáma. A functions Runtime mindig kiválasztja a verziószám vagy az intervallum által meghatározott maximálisan megengedett verziót. A fenti verzió értéke lehetővé teszi, hogy az összes köteg verziója a 1.0.0-ból, de nem tartalmazza a 2.0.0. További információ: a [verziók tartományának megadására szolgáló intervallum jelölése](/nuget/reference/package-versioning#version-ranges-and-wildcards). |

A csomag verziójának növekménye csomagokként a köteg változása. A főverzió módosításai akkor fordulnak elő, ha a csomagban lévő csomagok nagyobb verziójúak. A köteg főverziójának változásai általában egybeesnek a functions futtatókörnyezet főverziójának változásával.  

Az alapértelmezett csomag által telepített bővítmények jelenleg a [Extensions. JSON fájlban](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)vannak felsorolva.
