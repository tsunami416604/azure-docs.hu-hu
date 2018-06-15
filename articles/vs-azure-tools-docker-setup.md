---
title: Állítson be egy Docker-állomás VirtualBox |} Microsoft Docs
description: Részletes útmutatás a Docker gép és VirtualBox alapértelmezett Docker példányok konfigurálása
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
ms.openlocfilehash: 11e238fa901a164df1dfd896e38df828601e650b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190398"
---
# <a name="configure-a-docker-host-with-virtualbox"></a>VirtualBox egy Docker-állomás konfigurálása
## <a name="overview"></a>Áttekintés
Ez a cikk végigvezeti Önt egy Docker-számítógép és VirtualBox alapértelmezett Docker-példány konfigurálása. Használata esetén a [Docker for Windows beta](http://beta.docker.com/), ez a konfiguráció nincs szükség.

## <a name="prerequisites"></a>Előfeltételek
A következő eszközök telepítve kell lennie.

* [Docker eszközkészlet](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>A Docker-ügyfél konfigurálása a Windows PowerShell használatával
Egy Docker-ügyfél konfigurálásához, egyszerűen nyissa meg a Windows Powershellt, és hajtsa végre a következő lépéseket:

1. Hozzon létre alapértelmezett docker-állomás példányt.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Ellenőrizze, hogy az alapértelmezett példány, konfigurálni és futtatni. (Látnia kell egy futtató a "default" nevű példány.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![docker-gép ls kimeneti][0]
3. Alapértelmezett állítja be az aktuális állomás, és konfigurálja a rendszerhéj.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Megjeleníti az aktív Docker-tárolókat. Lehet, hogy a lista üres.
   
    ```PowerShell
    docker ps
    ```
   
    ![docker ps kimeneti][1]

> [!NOTE]
> Minden alkalommal, amikor a fejlesztői számítógépén, indítsa újra lesz szüksége a helyi docker host újraindítására.
> Ehhez adja ki a következő parancsot a parancssorba: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
