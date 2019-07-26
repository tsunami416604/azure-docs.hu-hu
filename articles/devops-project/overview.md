---
title: A Azure DevOps Projects áttekintése | Microsoft Docs
description: Azure DevOps Projects értékének megismerése
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: fed4f0976dd118186c122b0152f255d0c452c0ce
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404499"
---
# <a name="overview-of-azure-devops-projects"></a>A Azure DevOps Projects áttekintése

 Az Azure DevOps Projects megkönnyíti az Azure használatának első lépéseit. A Azure Portalból néhány gyors lépéssel elindíthatja kedvenc alkalmazásait az Ön által választott Azure-szolgáltatásban. 

 DevOps Projects beállítja az alkalmazás fejlesztéséhez, üzembe helyezéséhez és monitorozásához szükséges összes információt. A DevOps Projects irányítópulton figyelheti a kód véglegesítő, Build és központi telepítéseit a Azure Portal egyetlen nézetében.

## <a name="why-should-i-use-devops-projects"></a>Miért érdemes használni a DevOps Projects?

  A DevOps Project automatizálja egy teljes folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamat telepítését az Azure-ba.  Megkezdheti a meglévő kódok használatát, vagy használhatja a megadott minta alkalmazások egyikét is. Ezután gyorsan üzembe helyezheti az alkalmazást különböző Azure-szolgáltatásokra, például a Virtual Machinesre, a App Servicera, az Azure Kubernetes Services (ak), a Azure SQL Database és az Azure Service Fabricra.  

  DevOps Projects a DevOps-folyamat kezdeti konfigurálásához szükséges összes munkát, beleértve a kezdeti git-tárház beállítását, a CI/CD folyamat konfigurálását, Application Insights erőforrás létrehozását a figyeléshez és egyetlen a teljes megoldás nézete egy DevOps Projects irányítópult létrehozásával a Azure Portalban.

A DevOps Projects a következőhöz használható:

* Az alkalmazások gyors üzembe helyezése az Azure-ban
* Egy CI/CD-folyamat beállításának automatizálása
* A CI/CD-folyamat megfelelő beállításának megtekintése és ismertetése
* A kiadási folyamatok további testreszabása az adott forgatókönyvek alapján

## <a name="how-do-i-use-devops-projects"></a>Hogyan használja DevOps Projects?

  A DevOps Projects a Azure Portal érhető el. A portálról ugyanúgy hozhat létre DevOps Projects erőforrást, mint bármely más Azure-erőforrást. DevOps Projects a különböző konfigurációs beállításokhoz nyújt lépésenkénti varázslót.  

A kezdeti beállítás során számos konfigurálási lehetőség közül választhat. Ezek a lehetőségek a következők:

* A megadott minta alkalmazás használata vagy saját kód benyújtása
* Alkalmazás nyelvének kiválasztása
* Alkalmazás-keretrendszer kiválasztása nyelv alapján
* Azure-szolgáltatás kiválasztása (telepítési cél)
* Új Azure DevOps-szervezet létrehozása vagy meglévő szervezet használata 
* Az Azure-előfizetés kiválasztása
* Az Azure-szolgáltatások helyének kiválasztása
* Az Azure-szolgáltatások különböző díjszabási szintjei közül választhat

DevOps Projects használata után törölheti az összes erőforrást egyetlen helyről az Azure Portal DevOps Projects irányítópultján.

## <a name="devops-projects-and-azure-devops-integration"></a>DevOps Projects és az Azure DevOps-integrációja

DevOps Projects az Azure DevOps működteti. DevOps Projects automatizálja az Azure-folyamatokban szükséges összes munkát egy CI/CD-folyamat beállításához. Git-tárházat hoz létre egy új vagy meglévő Azure DevOps-szervezetben, majd egy minta alkalmazást vagy a meglévő kódot egy új git-tárházba véglegesíti.  

Az Automation egy CI-triggert is létrehoz a buildhez, hogy minden új kód véglegesítse a buildet. A DevOps Projects CD-triggert hoz létre, és minden új sikeres buildet üzembe helyez a választott Azure-szolgáltatásban.  

A build és a kiadási folyamatok a további forgatókönyveknek megfelelően testre szabhatók. Emellett a fordítási és a kiadási folyamatokat is klónozott más projektekben való használatra.

A DevOps Project létrehozása után a következőket teheti:

* A build és a kiadási folyamat testreszabása
* Lekérési kérelmek használata a kódfolyamat kezeléséhez és a minőség magas szinten tartásához
* Minden véglegesítés tesztelése és felépítése a kódok egyesítése előtt a minőség javítása érdekében
* A várakozó fájlok és a problémák nyomon követése az alkalmazással együtt

## <a name="how-do-i-start-using-devops-projects"></a>Hogyan megkezdeni a DevOps Projects használatát?

* [Ismerkedés a DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>DevOps Projects videók

* [CI/CD létrehozása Azure DevOps Projects](https://www.youtube.com/watch?v=NuYDAs3kNV8)
