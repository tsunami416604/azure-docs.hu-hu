---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179598"
---
Ahhoz, hogy közvetlen SSH-munkamenetet nyisson meg a tárolóval, az alkalmazásnak futnia kell.

Illessze be a következő \<URL-címet a böngészőbe, és cserélje le az alkalmazásnév-> az alkalmazás nevére:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Ha még nem hitelesített, akkor a csatlakozáshoz az Azure-előfizetéssel kell hitelesítenie magát. A hitelesítés után megjelenik egy böngészőben lévő rendszerhéj, ahol parancsokat futtathat a tárolón belül.

![SSH kapcsolat](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
