---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
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

Az `extensionBundle` alábbi tulajdonságai érhetők el:

| Tulajdonság | Leírás |
| -------- | ----------- |
| id | A Microsoft Azure Functions bővítménycsomagjainak névtere. |
| version | A telepítendő csomag verziója. A Functions futtatókörnyezete mindig a verziótartomány által meghatározott legmagasabb verziószámot választja ki. A fenti verzióérték minden csomagverziót támogat az 1.0.0-tól kezdve, ami a 2.0.0-nál kevesebb. A 2.0.0-t már nem támogatja. További információért lásd az [intervallumok verziótartományok megadásakor történő megjelölésével](/nuget/reference/package-versioning#version-ranges) foglalkozó részt. |

A csomagok verziószáma a termékcsomagban (kötegben) található csomagok változásával párhuzamosan növekszik. A főverzió akkor változik, ha a termékcsomagban (kötegben) található csomagok főverziója nő. A termékcsomag főverziójának változása általában egybeesik a Functions futtatókörnyezet főverziójának változásával.  

Az alapértelmezett termékcsomag részeként jelenleg telepített bővítmények listáját az [extensions.json fájl](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) tartalmazza.
