---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "67836916"
---
Az FTP és a helyi git egy *üzembe helyezési felhasználó*használatával üzembe helyezhető egy Azure-webalkalmazásban. Miután konfigurálta az üzembe helyezési felhasználót, használhatja azt az összes Azure-környezetben. A fiók szintű központi telepítési felhasználóneve és jelszava eltér az Azure-előfizetés hitelesítő adataitól. 

Az üzembe helyezési felhasználó konfigurálásához futtassa az az [WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancsot a Azure Cloud shell. Cserélje \<username> le \<password> a és a értékét egy üzembe helyezési felhasználói felhasználónévre és jelszóra. 

- A felhasználónévnek egyedinek kell lennie az Azure-ban, a helyi git-leküldések esetében pedig nem tartalmazhatja a "@" szimbólumot. 
- A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenet a jelszót jeleníti meg `null` . `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Jegyezze fel a felhasználónevet és a jelszót a webalkalmazások üzembe helyezéséhez.
