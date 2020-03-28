---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179595"
---
A tárolón belülről létrehozott konzolnaplók at érheti el. Először kapcsolja be a tárolónaplózást a következő parancs futtatásával a Cloud Shellben:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

A tárolónaplózás bekapcsolása után futtassa a következő parancsot a naplófolyam megtekintéséhez:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

> [!NOTE]
> A naplófájlokat a böngészőből `https://<app-name>.scm.azurewebsites.net/api/logs/docker`is megtekintheti a(.

Ha bármikor le szeretné állítani `Ctrl` + `C`a naplóstreamelést, írja be a következőt:
