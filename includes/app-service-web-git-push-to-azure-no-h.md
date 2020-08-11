---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88077678"
---
A helyi terminálablakba visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le a *\<deploymentLocalGitUrl-from-create-step>* elemet annak a git-távoli URL-címére, amelyet a [Webalkalmazás létrehozása](#create-a-web-app)során mentett.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Ha a git Hitelesítőadat-kezelő kéri a hitelesítő adatok megadását, akkor győződjön meg arról, hogy megadja az **üzembe helyezési felhasználó konfigurálása**során létrehozott hitelesítő adatokat, nem pedig a Azure Portalba való bejelentkezéshez használt hitelesítő adatokat.

```bash
git push azure master
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:
