---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
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

A következő tulajdonságok érhetők el a ben `extensionBundle` :

| Tulajdonság | Leírás |
| -------- | ----------- |
| id | Microsoft Azure functions bővítmények névterei. |
| version | A telepítendő csomag verziószáma. A functions Runtime mindig kiválasztja a verziószám vagy az intervallum által meghatározott maximálisan megengedett verziót. A fenti verzió értéke lehetővé teszi, hogy az összes köteg verziója a 1.0.0-ból, de nem tartalmazza a 2.0.0. További információ: a [verziók tartományának megadására szolgáló intervallum jelölése](/nuget/reference/package-versioning#version-ranges). |

A csomag verziójának növekménye csomagokként a köteg változása. A főverzió módosításai akkor fordulnak elő, ha a csomagban lévő csomagok nagyobb verziójúak. A köteg főverziójának változásai általában egybeesnek a functions futtatókörnyezet főverziójának változásával.  

Az alapértelmezett csomag által telepített bővítmények jelenleg a [extensions.jsfájlban](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)vannak felsorolva.
