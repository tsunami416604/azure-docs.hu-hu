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
ms.openlocfilehash: 4f061dc66829c49fba6722215dc36ed2ce6464ee
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919256"
---
Az Azure Cloud Shell üzembehelyezési hitelesítő adatokat konfigurálja a [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancsot. Erre az üzembe helyező felhasználóra ahhoz van szükség, hogy egy FTP-ről vagy a helyi Gitről tudjon üzembe helyezés végezni egy webalkalmazásba. A felhasználónév és jelszó fiókszinten történik. _Azok a azonosak az Azure-előfizetés hitelesítő adataival._

A következő példában cserélje le a  *\<username >* és  *\<jelszó >*, beleértve a zárójeleket, az új felhasználónévvel és jelszóval. A felhasználónév Azure-on belül egyedinek kell lennie. A jelszónak legalább nyolc karakter hosszú, a következő három elem közül kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenet jelenik meg a jelszót tartalmazó kap `null`. `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. ` 'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. Az üzembehelyezési felhasználónév nem tartalmazhat "@" helyi Git leküldi a szimbólum.

Üzembe helyező felhasználót csak egyszer konfigurálnia. Használhatja az összes Azure-környezetekben.

> [!NOTE]
> Jegyezze fel a felhasználónevet és jelszót. Szüksége lesz rájuk a webalkalmazás későbbi üzembe helyezésekor.
>
>
