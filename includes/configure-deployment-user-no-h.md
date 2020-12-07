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
ms.openlocfilehash: 4ceae4e7e2d10c80a929a4a822c877da8d8478f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748236"
---
Az FTP és a helyi git egy *üzembe helyezési felhasználó* használatával üzembe helyezhető egy Azure-webalkalmazásban. Miután konfigurálta az üzembe helyezési felhasználót, használhatja azt az összes Azure-környezetben. A fiók szintű központi telepítési felhasználóneve és jelszava eltér az Azure-előfizetés hitelesítő adataitól. 

Az üzembe helyezési felhasználó konfigurálásához futtassa az az [WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancsot a Azure Cloud shell. Cserélje \<username> le \<password> a és a értékét egy üzembe helyezési felhasználói felhasználónévre és jelszóra. 

- A felhasználónévnek egyedinek kell lennie az Azure-ban, a git-leküldések pedig nem tartalmazhatják a (z) " \@ " szimbólumot. 
- A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenet a jelszót jeleníti meg `null` . `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Jegyezze fel a felhasználónevet és a jelszót a webalkalmazások üzembe helyezéséhez.
