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
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "67179598"
---
Ahhoz, hogy közvetlen SSH-munkamenetet nyisson meg a tárolóval, az alkalmazásának futnia kell.

Illessze be a következő URL-címet a böngészőbe, és cserélje le az \<app-name> kifejezést az alkalmazása nevére:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Ha még nem végezte el a hitelesítést, a csatlakozáshoz el kell végeznie a hitelesítést az Azure-előfizetésével. A hitelesítés után egy böngészőn belüli felület jelenik meg, ahol a tárolón belül futtathat parancsokat.

![SSH-kapcsolat](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
