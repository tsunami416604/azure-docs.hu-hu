---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e2c5794e5ce6e23b60bff513562f69c9333d6e34
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743800"
---
Elérheti a tárolón belül létrehozott konzol naplóit.

Először kapcsolja be a tároló naplózását a következő parancs futtatásával:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Cserélje `<app-name>` le `<resource-group-name>` a és a nevet a webalkalmazásának megfelelő nevekre.

Miután bekapcsolta a tárolók naplózását, a következő parancs futtatásával tekintheti meg a log streamet:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a **CTRL C billentyűt** + **C** .

A naplófájlokat egy böngészőben is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
