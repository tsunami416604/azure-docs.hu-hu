---
title: Az Azure DevOps Starter áttekintése | Microsoft Docs
description: Ismerje meg, hogy az Azure DevOps Starter hogyan segíti az alkalmazások indítását, üzembe helyezését és kezelését egyetlen nézetből a Azure Portal.
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 3e6f28da37980717e15b05ab94c582aa5c77e9e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856862"
---
# <a name="overview-of-azure-devops-starter"></a>Az Azure DevOps Starter áttekintése

 Az Azure DevOps Starter megkönnyíti az Azure-ban való ismerkedést. A Azure Portalból néhány gyors lépéssel elindíthatja kedvenc alkalmazásait az Ön által választott Azure-szolgáltatásban. 

 A DevOps Starter minden, amire szüksége lehet az alkalmazás fejlesztéséhez, üzembe helyezéséhez és figyeléséhez. A DevOps alapszintű irányítópultján megfigyelheti a kód véglegesítő, épít és üzemelő példányait a Azure Portal egyetlen nézetében.

## <a name="advantages-of-using-devops-starter"></a>A DevOps Starter használatának előnyei

  A DevOps Starter automatizálja egy teljes folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamat telepítését az Azure-ba.  Megkezdheti a meglévő kódok használatát, vagy használhatja a megadott minta alkalmazások egyikét is. Ezután gyorsan üzembe helyezheti az alkalmazást különböző Azure-szolgáltatásokra, például a Virtual Machinesre, a App Servicera, az Azure Kubernetes Services (ak), a Azure SQL Database és az Azure Service Fabricra.  

  A DevOps Starter minden olyan munkát tartalmaz, amely a DevOps-folyamat kezdeti konfigurálásához szükséges, beleértve a kezdeti git-tárház beállítását, a CI/CD-folyamat konfigurálását, a figyeléshez Application Insights erőforrás létrehozását, valamint a teljes megoldás egyetlen nézetét, amely egy DevOps Projects irányítópultot hoz létre a Azure Portal.

A DevOps Starter használatával a következőket végezheti el:

* Az alkalmazások gyors üzembe helyezése az Azure-ban
* Egy CI/CD-folyamat beállításának automatizálása
* A CI/CD-folyamat megfelelő beállításának megtekintése és ismertetése
* A kiadási folyamatok további testreszabása az adott forgatókönyvek alapján

## <a name="how-to-use-devops-starter"></a>Az DevOps Starter használata

  A DevOps Starter a Azure Portal érhető el. A DevOps kezdő erőforrásait ugyanúgy hozza létre, mint bármely más Azure-erőforrást a portálon. DevOps Projects a különböző konfigurációs beállításokhoz nyújt lépésenkénti varázslót.  

A kezdeti beállítás során számos konfigurálási lehetőség közül választhat. Ezek a lehetőségek a következők:

* A megadott minta alkalmazás használata vagy saját kód benyújtása
* Alkalmazás nyelvének kiválasztása
* Alkalmazás-keretrendszer kiválasztása nyelv alapján
* Azure-szolgáltatás kiválasztása (telepítési cél)
* Új Azure DevOps-szervezet létrehozása vagy meglévő szervezet használata 
* Az Azure-előfizetés kiválasztása
* Az Azure-szolgáltatások helyének kiválasztása
* Az Azure-szolgáltatások különböző díjszabási szintjei közül választhat

A DevOps Starter használata után törölheti is az összes erőforrást egyetlen helyről a Azure Portal Starter irányítópultján.

## <a name="devops-starter-and-azure-devops-integration"></a>A DevOps Starter és az Azure DevOps integrációja

Az DevOps Starter-t az Azure DevOps működteti. A DevOps Starter automatizálja az Azure-folyamatokban szükséges összes munkát egy CI/CD-folyamat beállításához. Git-tárházat hoz létre egy új vagy meglévő Azure DevOps-szervezetben, majd egy minta alkalmazást vagy a meglévő kódot egy új git-tárházba véglegesíti.  

Az Automation egy CI-triggert is létrehoz a buildhez, hogy minden új kód véglegesítse a buildet. A DevOps Starter létrehoz egy CD-triggert, és minden új sikeres buildet üzembe helyez a választott Azure-szolgáltatásban.  

A build és a kiadási folyamatok a további forgatókönyveknek megfelelően testre szabhatók. Emellett a fordítási és a kiadási folyamatokat is klónozott más projektekben való használatra.

A DevOps kezdő létrehozása után a következőket teheti:

* A build és a kiadási folyamat testreszabása
* Lekérési kérelmek használata a kódfolyamat kezeléséhez és a minőség magas szinten tartásához
* Minden véglegesítés tesztelése és felépítése a kódok egyesítése előtt a minőség javítása érdekében
* A várakozó fájlok és a problémák nyomon követése az alkalmazással együtt

## <a name="getting-started-with-devops-starter"></a>Első lépések a DevOps Starter-vel

* [Ismerkedés a DevOps Starter-szel](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps Starter-videók

* [CI/CD létrehozása az Azure DevOps Starter-vel](https://www.youtube.com/watch?v=NuYDAs3kNV8)