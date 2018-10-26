---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: b5512ed5810ebd05596dc3ca7a29957b4c595c59
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035133"
---
A Cloud Shellben hozza létre az üzembehelyezési hitelesítő adatokat az [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) paranccsal. Erre az üzembe helyező felhasználóra ahhoz van szükség, hogy egy FTP-ről vagy a helyi Gitről tudjon üzembe helyezés végezni egy webalkalmazásba. A felhasználónevek és a jelszavak megadása fiókszinten történik. _Ezek nem azonosak az Azure-előfizetés hitelesítő adataival._

A következő példában cserélje le a *\<username>* és a *\<password>* elemet (beleértve a zárójeleket is) az új felhasználónévre és jelszóra. A felhasználónévnek egyedinek kell lennie az Azure-on belül. A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és a következő három elem közül kettőnek szerepelnie kell benne: betűk, számok, szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Egy JSON-kimenetet fog kapni, ahol a jelszóhoz a `null` érték jelenik meg. `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. ` 'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót.

Ezt az üzembe helyező felhasználót csak egyszer kell létrehoznia, és minden Azure-környezetben használhatja.

> [!NOTE]
> Jegyezze fel a felhasználónevet és a jelszót. Szüksége lesz rájuk a webalkalmazás későbbi üzembe helyezésekor.
>
>
