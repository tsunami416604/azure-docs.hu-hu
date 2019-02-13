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
ms.openlocfilehash: aa6f18d4f667862687083c5db3679ce9d8e188cd
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213054"
---
A _helyi terminálablakba_ visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le a _&lt;deploymentLocalGitUrl-from-create-step>_ részt a távoli Git [webalkalmazás létrehozása](#create-a-web-app) során mentett URL-címére.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Ha a Git hitelesítőadat-kezelője kéri a hitelesítő adatokat, győződjön meg arról, hogy adjon meg a létrehozott hitelesítő adatok beállítása az üzembe helyező felhasználó, nem a hitelesítő adatokkal jelentkezzen be az Azure portal használatával.

```bash
git push azure master
```

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:
