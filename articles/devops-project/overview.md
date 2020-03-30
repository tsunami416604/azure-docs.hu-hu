---
title: Az Azure DevOps-projektek áttekintése | Microsoft dokumentumok
description: Az Azure DevOps-projektek értékének megismerése
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
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: d39702f8e30046dd3cf634fc67ed7095471cd629
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "71971476"
---
# <a name="overview-of-azure-devops-projects"></a>Az Azure DevOps-projektek áttekintése

 Az Azure DevOps-projektek megkönnyítik az azure-ral való ismerkedést. Az Azure Portalnéhány gyors lépéssel elindíthatja kedvenc alkalmazását az Ön által kiválasztott Azure-szolgáltatásban. 

 A DevOps-projektek mindent beállít, amire szüksége van az alkalmazás fejlesztéséhez, üzembe helyezéséhez és figyeléséhez. A DevOps-projektek irányítópultján figyelheti a kódvéglegesítéseket, buildeket és üzemelő példányokat egyetlen nézetből az Azure Portalon.

## <a name="why-should-i-use-devops-projects"></a>Miért érdemes DevOps-projekteket használni?

  A DevOps Project automatizálja a teljes folyamatos integrációs (CI) és a folyamatos kézbesítési (CD) folyamat beállítását az Azure-ba.  Kezdheti a meglévő kóddal, vagy használhatja a megadott mintaalkalmazások egyikét. Ezután gyorsan telepítheti az alkalmazást különböző Azure-szolgáltatásokba, például a Virtuális gépekre, az App Service-re, az Azure Kubernetes-szolgáltatásokra (AKS), az Azure SQL Database-re és az Azure Service Fabricre.  

  DevOps-projektek a devops-folyamat kezdeti konfigurálása, beleértve mindent a kezdeti Git-tárház beállítása, konfigurálása a CI/CD-folyamat, egy Application Insights-erőforrás figyelése, és egyetlen nézet a teljes megoldás létrehozása a DevOps-irányítópult az Azure Portalon.

A DevOps-projektek segítségével:

* Az alkalmazások gyors üzembe helyezése az Azure-ban
* Ci/CD-folyamat beállításának automatizálása
* Ci/CD-folyamat megfelelő beállításának és megértésének megtekintése és megértése
* A kiadási folyamatok további testreszabása az adott forgatókönyvek alapján

## <a name="how-do-i-use-devops-projects"></a>Hogyan használhatom a DevOps-projekteket?

  A DevOps-projektek az Azure Portalon érhetők el. Hozzon létre egy DevOps-projektek erőforrást, mint bármely más Azure-erőforrást a portálról. DevOps-projektek nyújt lépésről-lépésre varázsló-szerű élményt a különböző konfigurációs lehetőségeket.  

A kezdeti beállítás során számos konfigurálási lehetőség közül választhat. Ezek a lehetőségek a következők:

* A megadott mintaalkalmazás használata vagy saját kód
* Alkalmazásnyelv kiválasztása
* Alkalmazáskeret kiválasztása a nyelv alapján
* Azure-szolgáltatás kiválasztása (telepítési cél)
* Új Azure DevOps-szervezet létrehozása vagy meglévő szervezet használata 
* Az Azure-előfizetés kiválasztása
* Az Azure-szolgáltatások helyének kiválasztása
* Választás az Azure-szolgáltatások különböző tarifacsomagjai közül

A DevOps-projektek használata után az összes erőforrást egyetlen helyről is törölheti az Azure Portal DevOps-projektek irányítópultjáról.

## <a name="devops-projects-and-azure-devops-integration"></a>DevOps-projektek és Az Azure DevOps-integráció

A DevOps-projekteket az Azure DevOps működteti. A DevOps-projektek automatizálják az Azure Pipelines-ban a CI/CD-folyamat beállításához szükséges összes munkát. Létrehoz egy Git-tárházat egy új vagy meglévő Azure DevOps-szervezetben, majd véglegesíti a mintaalkalmazást vagy a meglévő kódot egy új Git-tárházba.  

Az automatizálás is létrehoz egy CI-eseményindító a build, hogy minden új kód véglegesítése kezdeményezegy buildet. A DevOps-projektek létrehozegy CD-eseményindítót, és minden új sikeres buildet telepít az Ön által kiválasztott Azure-szolgáltatásba.  

A build és a kiadási folyamatok a további forgatókönyveknek megfelelően testre szabhatók. Emellett klónozhatja a build- és kiadási folyamatokat más projektekben való használatra.

A DevOps Project létrehozása után a következőket teheti:

* A build és a kiadási folyamat testreszabása
* Lekérési kérelmek használata a kódfolyamat kezeléséhez és a minőség magas szinten tartásához
* Minden véglegesítés tesztelése és felépítése a kódok egyesítése előtt a minőség javítása érdekében
* Kövesse nyomon a hátralékéseket és problémákat az alkalmazással együtt

## <a name="how-do-i-start-using-devops-projects"></a>Hogyan kezdhetem el használni a DevOps-projekteket?

* [A DevOps-projektek – első lépések](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>DevOps-projektek – videók

* [CI/CD létrehozása Azure DevOps-projektekkel](https://www.youtube.com/watch?v=NuYDAs3kNV8)
