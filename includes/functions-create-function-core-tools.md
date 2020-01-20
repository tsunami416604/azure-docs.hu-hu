---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 21b7226bc82aa8041db6bbd29b4874814e2a47d0
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279489"
---
## <a name="create-a-function"></a>Függvény létrehozása

Az alábbi parancs egy HTTP által indított függvényt hoz létre `MyHttpTrigger` néven.

```command
func new --name MyHttpTrigger --template "HttpTrigger"
```

A parancs végrehajtásakor a következő kimenethez hasonlót fog látni:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
