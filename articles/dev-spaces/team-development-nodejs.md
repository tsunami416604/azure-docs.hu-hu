---
title: A Node.js és a VS Code Azure-fejlesztési szóközt tartalmazó csoportos fejlesztése
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: 'Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s '
ms.openlocfilehash: 5d89ac6a0a8918b30cac31580e7fc95c3f770b1a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436761"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Győződjön meg arról, a kód módosítása
Lépjen a VS Code ablakának `mywebapi` , és adja meg az alapértelmezett GET szerkesztése kódot `/` kezelő a `server.js`, például:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
