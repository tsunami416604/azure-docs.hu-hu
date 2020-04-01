---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836916"
---
Az FTP és a helyi Git üzembe helyezhető egy Azure-webalkalmazásba egy *központi telepítési felhasználó*használatával. Miután konfigurálta a központi telepítési felhasználót, használhatja az összes Azure-telepítéshez. A fiókszintű központi telepítési felhasználónév és jelszó eltér az Azure-előfizetés hitelesítő adataitól. 

A központi telepítési felhasználó konfigurálásához futtassa az [az webapp-telepítési felhasználói készlet parancsot](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) az Azure Cloud Shellben. Cserélje \<le a \<felhasználónevet> és jelszó> egy központi telepítési felhasználónévre és jelszóra. 

- A felhasználónév egyedinek kell lennie az Azure-ban, és a helyi Git leküldések, nem tartalmazhat a "@" szimbólum. 
- A jelszónak legalább nyolc karakter hosszúnak kell lennie, a következő három elem közül kettővel: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON kimenet a `null`jelszót a . `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Rögzítse felhasználónevét és jelszavát a webalkalmazások telepítéséhez.
