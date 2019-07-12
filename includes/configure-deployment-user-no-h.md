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
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836916"
---
Az FTP és a helyi Gitből az Azure-webalkalmazás segítségével telepíthet egy *üzembe helyező felhasználó*. Miután az üzembe helyező felhasználó konfigurál, használhatja az összes Azure-környezetekben. A fiókszintű üzembehelyezési felhasználónevet és jelszót különböznek az Azure-előfizetés hitelesítő adatait. 

Az üzembe helyező felhasználó konfigurálásához futtassa a [az webapp deployment felhasználó beállított](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancsot az Azure Cloud Shellben. Cserélje le \<username > és \<jelszó > üzembe helyezési felhasználói felhasználónévvel és jelszóval. 

- A felhasználónév Azure-on belül egyedinek kell lennie, és a helyi git leküldés, nem tartalmazhat a "@" szimbólummal. 
- A jelszónak legalább nyolc karakter hosszú, a következő három elem közül kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenet megjeleníti a jelszót, mint `null`. `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

Jegyezze fel a felhasználónevét és jelszavát, a web Apps alkalmazások telepítéséhez.
