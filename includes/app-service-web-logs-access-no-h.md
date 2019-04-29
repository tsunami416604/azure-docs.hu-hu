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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768724"
---
A konzolnaplófájlokban tárolón belül létrehozott is elérheti. Első lépésként kapcsolja be a tároló naplózása a következő parancsot a Cloud shellben való futtatásával:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Miután a tároló naplózása be van kapcsolva, a következő parancsot a naplófolyam lásd:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

> [!NOTE]
> Is vizsgálhatja meg a naplófájlokat a böngészőből, `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Bármikor naplóstreamelés leállításához írja be `Ctrl` + `C`.
