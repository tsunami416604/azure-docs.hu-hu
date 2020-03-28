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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75681035"
---
A helyi terminálablakba visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje * \<le a deploymentLocalGitUrl-from-create-step>-t* a Git-távlatok URL-címére, amelyet a [Webalkalmazás létrehozása](#create-a-web-app)szolgáltatásból mentett.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a Git Credential Manager hitelesítő adatokat kér, győződjön meg arról, hogy adja meg a létrehozott hitelesítő adatokat [a központi telepítési felhasználó konfigurálása,](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user)nem a hitelesítő adatokat, amelyeket az Azure Portalon való bejelentkezéshez használ.

```bash
git push azure master
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:
