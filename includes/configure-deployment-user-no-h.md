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
ms.openlocfilehash: 463e8b0122339831d5d6a65b6e41d4f697a82013
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
ms.locfileid: "28986120"
---
A Cloud Shellben hozza létre az üzembehelyezési hitelesítő adatokat az [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set) paranccsal. Erre az üzembe helyező felhasználóra ahhoz van szükség, hogy egy FTP-ről vagy a helyi Gitről tudjon üzembe helyezés végezni egy webalkalmazásba. A felhasználónevek és a jelszavak megadása fiókszinten történik. _Ezek nem azonosak az Azure-előfizetés hitelesítő adataival._

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
