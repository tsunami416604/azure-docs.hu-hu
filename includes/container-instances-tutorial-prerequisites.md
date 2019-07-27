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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67179677"
---
Az oktatóanyag teljesítéséhez a következők szükségesek:

**Azure CLI**: A helyi számítógépen telepítve kell lennie az Azure CLI 2.0.29 vagy újabb verziójának. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését][azure-cli-install] ismertető cikket.

**Docker**: Ez az oktatóanyag feltételezi az alapvető Docker-fogalmak (például a tárolók, a tárolók `docker` és az alapszintű parancsok) alapszintű megismerését. A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése][docker-get-started].

**Docker-motor**: Az oktatóanyag elvégzéséhez helyileg kell telepíteni a Docker Engine-t. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS][docker-mac], [Windows][docker-windows]és [Linux][docker-linux]rendszereken.

> [!IMPORTANT]
> Mivel az Azure Cloud Shell nem tartalmazza a Docker-démont, ezért az oktatóanyag teljesítéséhez telepíteni *kell* a *helyi számítógépen* az Azure CLI-t és a Docker Engine-t. Ehhez az oktatóanyaghoz nem használható az Azure Cloud Shell.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
