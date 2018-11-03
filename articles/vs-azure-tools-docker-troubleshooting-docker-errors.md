---
title: Docker-ügyfél hibákat a Windows hibaelhárítása a Visual studióval |} A Microsoft Docs
description: Tapasztal, amikor a Visual Studio használatával hozhat létre és helyezhet üzembe webalkalmazásokat Docker a Windows a Visual Studio 2017 használatával kapcsolatos problémák elhárítása.
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
ms.openlocfilehash: cd88dec2ad79ad9f4b4c004866060be86b777cd9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969327"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Hibaelhárítás a Visual Studio 2017-fejlesztés a docker használatával

Ha a Visual Studio Tools for Docker dolgozik, problémák merülhetnek fel létrehozásához vagy az alkalmazás hibakeresése közben. Alább van néhány gyakori hibaelhárítási lépéseket.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Kötet, megosztás nem engedélyezett. Megosztás a Docker CE for Windows beállításai (csak Linux-tárolók esetében) a kötet engedélyezése

A probléma megoldásához:

1. Kattintson a jobb gombbal **Docker for Windows** az értesítési területről, és válassza ki a **beállítások**.
1. Válassza ki **megosztott meghajtók** és megoszthatja a rendszermeghajtó együtt a meghajtó, ahol a projekt található.

> [!NOTE]
> Ha a fájlok megosztott jelennek meg, továbbra is szükség lehet a "Visszaállítása a hitelesítő adatok..." hivatkozásra a párbeszédpanel alján kattintson az újbóli engedélyezését a kötet, megosztás. Hitelesítő adatok visszaállítása után a folytatáshoz, akkor előfordulhat, hogy indítsa újra a Visual Studio.

![a megosztott meghajtók](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="mounts-denied"></a>Megtagadva csatlakoztatása

MacOS-hez készült Docker használatakor előfordulhat, hogy olyan hibaüzenetbe ütközik a mappa /usr/local/share/dotnet/sdk/NuGetFallbackFolder hivatkozik. A mappa hozzáadása a Docker fájlmegosztás lapjára.

## <a name="unable-to-start-debugging"></a>Nem sikerült elindítani a hibakeresés

A felhasználói profil mappájába elavult hibakeresési összetevők kellene egyik oka kapcsolatos lehet. Hajtsa végre a következő parancsokat, hogy a legújabb hibakeresési összetevői letöltődnek az a következő hibakeresési munkamenet, távolítsa el ezeket a mappákat.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Adott, a hibakeresés során hálózati hibák

Próbálja ki, hajtsa végre a parancsprogramot, amely a letölthető [karbantartása tároló gazdagép hálózati](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), amely frissíti a hálózattal kapcsolatos összetevők a gazdagépen.


## <a name="microsoftdockertools-github-repo"></a>A Microsoft/DockerTools GitHub-adattár

Bármely más, előforduló hibák, lásd: [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) problémákat.