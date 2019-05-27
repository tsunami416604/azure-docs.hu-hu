---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: da63a5418ab94623f6ce3c9f35a085dd8b198d1a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148994"
---
Az oktatóanyag teljesítéséhez a következők szükségesek:

**Az Azure CLI**: Rendelkeznie kell Azure CLI 2.0.29-es vagy újabb verziója szükséges a helyi számítógépen. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését][azure-cli-install] ismertető cikket.

**Docker**: Ez az oktatóanyag feltételezi, hogy egy alapvető ismeretekkel a Docker fő fogalmaira, például a tárolók, tárolórendszerképekre és az alapszintű `docker` parancsokat. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját][docker-get-started].

**Docker-motor**: Az oktatóanyag elvégzéséhez szüksége helyileg telepített Docker-motor. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS][docker-mac], [Windows][docker-windows] és [Linux] [docker-linux] rendszereken.

> [!IMPORTANT]
> Mivel az Azure Cloud Shell nem tartalmazza a Docker-démont, ezért az oktatóanyag teljesítéséhez telepíteni *kell* a *helyi számítógépen* az Azure CLI-t és a Docker Engine-t. Ehhez az oktatóanyaghoz nem használható az Azure Cloud Shell.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
