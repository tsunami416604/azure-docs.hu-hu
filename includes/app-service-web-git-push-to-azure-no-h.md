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
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75681035"
---
A helyi terminálablakba visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le * \<a deploymentLocalGitUrl-from-Create-Step>* elemet annak a git-távoli URL-címére, amelyet a [create a Web App (Webalkalmazás létrehozása](#create-a-web-app)) mentett.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Ha a git Hitelesítőadat-kezelő kéri a hitelesítő adatok megadását, akkor győződjön meg arról, hogy megadja az [üzembe helyezési felhasználó konfigurálása](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user)során létrehozott hitelesítő adatokat, nem pedig a Azure Portalba való bejelentkezéshez használt hitelesítő adatokat.

```bash
git push azure master
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:
