---
title: Docker ügyfél hibákat a Windows hibaelhárítása a Visual Studio használatával |} Microsoft Docs
description: Hozzon létre és telepíthet webalkalmazásokat az Docker Windows rendszeren Visual Studio 2017 használatával Visual Studio használata során felmerülő problémák megoldásához.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
ms.locfileid: "24002930"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>A Visual Studio 2017 fejlesztése a Docker hibaelhárítása

Ha a Visual Studio eszközök Docker dolgozik, problémák merülhetnek fel felépítése vagy az alkalmazás hibakeresése közben. Alább van néhány gyakori hibaelhárítási lépéseket.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Kötet megosztása nem engedélyezett. Kötet a Docker CE Windows beállításai (csak Linux tárolók) megosztásának engedélyezése

A probléma megoldásához:

1. Kattintson a jobb gombbal **Docker for Windows** az értesítési területen, és válassza a **beállítások**.
1. Válassza ki **megosztott meghajtók** és megoszthatja a rendszermeghajtón és a meghajtó, ahol a projektben található.

> [!NOTE]
> Ha a fájlok megosztott jelennek meg, előfordulhat, hogy továbbra is szeretné a párbeszédpanel alján "Alaphelyzetbe hitelesítő adatok..." hivatkozásra kattintva újra engedélyezni a kötet megosztása.

![megosztott meghajtók](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>Nem lehet elindítani a hibakeresés

Egyik oka kapcsolatos lehet elavult hibakeresési összetevők rendelkező felhasználói profil mappájába. Hajtsa végre a következő parancsok futtatásával távolítsa el ezeket a mappákat, hogy a legújabb hibakeresési összetevői letöltődnek a következő hibakeresési munkamenetben.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Hibák meghatározott hálózatkezelési, amikor az alkalmazás hibakeresése

Próbálja meg végrehajtani a parancsfájl letölthető [tisztítás tároló állomás hálózati](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), amely automatikusan frissíti a hálózattal kapcsolatos összetevők, a gazdagépen.


## <a name="microsoftdockertools-github-repo"></a>Microsoft/DockerTools GitHub-tárház

Bármely más problémákat tapasztal, lásd: [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) problémákat.