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
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905867"
---
A App Service alkalmazás kódjában létrehozott konzol naplófájljainak eléréséhez kapcsolja be a diagnosztikai naplózást a következő parancs futtatásával a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

A lehetséges értékei a `--level` következők:,,, `Error` `Warning` `Info` és `Verbose` . Minden további szint az előző szintet tartalmazza. Például: `Error` csak a hibaüzeneteket tartalmazza, és `Verbose` tartalmazza az összes üzenetet.

Miután bekapcsolta a diagnosztikai naplózást, a következő parancs futtatásával tekintheti meg a log streamet:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

> [!NOTE]
> A naplófájlokat a böngészőből is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a következőt: `Ctrl` + `C` .
