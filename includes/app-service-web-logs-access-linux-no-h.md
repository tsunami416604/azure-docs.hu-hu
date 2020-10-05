---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "84905621"
---
Elérheti a tárolón belül létrehozott konzol naplóit. Először kapcsolja be a tároló naplózását a következő parancs futtatásával a Cloud Shellban:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Miután bekapcsolta a tárolók naplózását, a következő parancs futtatásával tekintheti meg a log streamet:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

> [!NOTE]
> A naplófájlokat a böngészőből is megtekintheti a következő címen: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

A `Ctrl`+`C` billentyűparanccsal bármikor leállíthatja a naplóstreamelést.
