---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-instances
author: mmacy
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 95a8cf4eca7969631d069a31c9d08ba52c8b02b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
Az oktatóanyag teljesítéséhez a következők szükségesek:

**Azure CLI**: Az Azure CLI 2.0.29-es vagy újabb verziójának telepítve kell lennie a helyi számítógépen. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése][azure-cli-install].

**Docker**: Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és az alapszintű `docker`-parancsokra vonatkozó alapvető ismeretekkel. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját][docker-get-started].

**Docker Engine**: Az oktatóanyag elvégzéséhez helyileg telepíteni kell a Docker Engine-t. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS][docker-mac], [Windows][docker-windows] és [Linux] [docker-linux] rendszereken.

> [!IMPORTANT]
> Mivel az Azure Cloud Shell nem tartalmazza a Docker-démont, ezért az oktatóanyag teljesítéséhez telepíteni *kell* a *helyi számítógépen* az Azure CLI-t és a Docker Engine-t. Ehhez az oktatóanyaghoz nem használható az Azure Cloud Shell.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli