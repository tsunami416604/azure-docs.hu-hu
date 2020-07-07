---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179598"
---
Ahhoz, hogy egy közvetlen SSH-munkamenetet nyisson meg a tárolóval, az alkalmazásnak futnia kell.

Illessze be a következő URL-címet a böngészőjébe, és cserélje le az \<app-name> alkalmazás nevére:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Ha még nincs hitelesítve, a kapcsolódáshoz az Azure-előfizetését kell hitelesítenie. A hitelesítés után egy böngészőn belüli rendszerhéj jelenik meg, amelyen a tárolóban futtathat parancsokat.

![SSH-kapcsolatok](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
