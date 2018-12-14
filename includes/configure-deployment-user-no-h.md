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
ms.openlocfilehash: 95d89da66935ce933fee082a5f53ee2e36ea953f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344686"
---
A Cloud shellben üzembehelyezési hitelesítő adatokat konfigurálja a [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancsot. Erre az üzembe helyező felhasználóra ahhoz van szükség, hogy egy FTP-ről vagy a helyi Gitről tudjon üzembe helyezés végezni egy webalkalmazásba. A felhasználónevek és a jelszavak megadása fiókszinten történik. _Ezek nem azonosak az Azure-előfizetés hitelesítő adataival._

A következő példában cserélje le a *\<username>* és a *\<password>* elemet (beleértve a zárójeleket is) az új felhasználónévre és jelszóra. A felhasználónévnek egyedinek kell lennie az Azure-on belül. A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és a következő három elem közül kettőnek szerepelnie kell benne: betűk, számok, szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Egy JSON-kimenetet fog kapni, ahol a jelszóhoz a `null` érték jelenik meg. `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. ` 'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót.

Üzembe helyező felhasználót csak egyszer; konfigurálni kell használhatja az összes Azure-környezetekben.

> [!NOTE]
> Jegyezze fel a felhasználónevet és a jelszót. Szüksége lesz rájuk a webalkalmazás későbbi üzembe helyezésekor.
>
>
