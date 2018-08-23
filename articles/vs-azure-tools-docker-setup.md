---
title: Docker-gazdagép konfigurálására a VirtualBox |} A Microsoft Docs
description: Részletes útmutatás a Docker Machine és a VirtualBox használatával alapértelmezett Docker-példány konfigurálása
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 7c78f27fa948c15202e83df4ed42a805a414a72e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060828"
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Docker-gazdagép VirtualBox konfigurálása
## <a name="overview"></a>Áttekintés
Ez a cikk végigvezeti a Docker Machine és a VirtualBox használatával alapértelmezett Docker-példány konfigurálását. Ha használja a [Docker for Windows](https://www.docker.com/products/docker-desktop), nem kell ezt a konfigurációt.

## <a name="prerequisites"></a>Előfeltételek
A következő eszközök kell telepíteni.

* [Docker-eszközkészlet](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>A Docker-ügyfél konfigurálása a Windows PowerShell-lel
Egy Docker-ügyfél konfigurálása, egyszerűen nyissa meg a Windows Powershellt, és hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy alapértelmezett docker-gazdagép példány.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Ellenőrizze, hogy az alapértelmezett példányon be állítva és fut-e. (Megtekintheti az egy példány futtatása "default" nevű.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![a docker-machine ls-kimenet][0]
3. Alapértelmezett beállítása a jelenlegi gazdagép, és konfigurálhatja a felületet.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Megjeleníti az aktív Docker-tárolót. Lehet, hogy a lista üres.
   
    ```PowerShell
    docker ps
    ```
   
    ![a docker ps-kimenet][1]

> [!NOTE]
> Minden alkalommal, amikor a fejlesztői gépén, indítsa újra kell indítani a helyi docker-gazdagép.
> Ehhez adja ki a következő parancsot a parancssorba: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
