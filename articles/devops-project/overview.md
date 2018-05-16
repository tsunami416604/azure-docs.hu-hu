---
title: Az Azure DevOps Project áttekintése | Microsoft Docs
description: Az Azure DevOps Project által képviselt érték megértése
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: b87cb14fc707d14638c2d6a52af6f295276a2279
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="overview-of-azure-devops-project"></a>Az Azure DevOps Project áttekintése

Az Azure DevOps Project megkönnyíti az Azure-ral való ismerkedést. A DevOps-projekterőforrás segítségével az Azure Portalon elvégzett néhány gyors lépéssel elindíthatja kedvenc alkalmazástípusát a választott Azure-szolgáltatásban. A DevOps Project az alkalmazás fejlesztéséhez, telepítéséhez és monitorozásához szükséges összes beállítást elvégzi.
A DevOps Project irányítópultja lehetővé teszi a kódok véglegesítésének, létrehozásának és üzembe helyezésének monitorozását – mindezt az Azure Portal egyetlen nézetében.

## <a name="why-should-i-use-the-azure-devops-project"></a>Miért érdemes használni az Azure DevOps Projectet?

Az Azure DevOps Project automatizálja a teljes folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamat beállítását az Azure-ban.  Használhatja a már meglévő kódját vagy a megadott mintaalkalmazások valamelyikét, és gyorsan üzembe helyezheti az adott alkalmazást az olyan Azure-szolgáltatásokban, mint például a Virtual Machines, az App Service, az Azure Container Service, az Azure SQL Database vagy az Azure Service Fabric.  

Az Azure DevOps Project a DevOps-folyamat kezdeti beállításához szükséges összes feladatot elvégzi, a kezdeti Git-adattár beállításától kezdve a CI/CD-folyamat konfigurálásán és a monitorozáshoz szükséges Application Insights-erőforrás létrehozásán át a teljes megoldást összefoglaló nézet biztosításáig, amit az Azure Portalon létrehozott Azure DevOps Project irányítópultja tesz lehetővé.

Az Azure DevOps Project felhasználási lehetőségei:

* Az alkalmazások gyors üzembe helyezése az Azure-ban
* A VSTS CI/CD-folyamat beállításának automatizálása
* A DevOps projekt sablonként használható annak áttekintéséhez és megértéséhez, hogy a CI/CD hogyan állítható be megfelelően a VSTS használatával az Azure-ban
* Kezdje a CI/CD-folyamattal az Azure-ban, majd az adott forgatókönyveknek megfelelően végezze el a kiadási folyamat testreszabását

## <a name="how-do-i-use-the-azure-devops-project"></a>Hogyan működik az Azure DevOps Project?

Az Azure DevOps Project az Azure Portalon érhető el.  Az Azure DevOps-projekterőforrás létrehozása ugyanúgy történik a portálon, mint bármelyik másik Azure-erőforrás esetében.  A DevOps Project egy varázslóhoz hasonló módon, lépésről lépésre vezeti végig a különböző konfigurációs beállításokon.  

A kezdeti beállítás során számos konfigurálási lehetőség közül választhat.  Ezek a lehetőségek a következők:

* A megadott mintaalkalmazás vagy saját kód használata
* Az alkalmazásnyelv kiválasztása
* Alkalmazás-keretrendszer kiválasztása a nyelv alapján
* Egy Azure-szolgáltatás kiválasztása (üzembehelyezési cél)
* VSTS-fiók (új vagy meglévő)
* Azure-előfizetés kiválasztása
* Az Azure-szolgáltatások helyének kiválasztása
* A megfelelő tarifacsomag kiválasztása az Azure-szolgáltatásokhoz

Az Azure DevOps Project használatát követően az összes erőforrást egyetlen helyről, az Azure Portalról elérhető Azure DevOps Project-irányítópultról törölheti.

## <a name="azure-devops-project-and-vsts-integration"></a>Az Azure DevOps Project és a VSTS integrációja

A DevOps-projektek a VSTS-en alapulnak.  A DevOps-projekt a VSTS-ben szükséges összes feladatot automatizálja a CI/CD Azure-beli beállításához.  A Git-adattár egy új vagy már létező VSTS-fiókban jön létre.  A DevOps-projekt véglegesíti a mintaalkalmazást vagy a meglévő kódot egy új Git-adattárban.  Az automatizálás emellett egy CI-eseményindítót is létrehoz a buildhez, így minden új kódvéglegesítés kezdeményez egy buildet.  A DevOps-projekt is létrehoz egy CD-eseményindítót, és minden új, sikeres buildet üzembe helyez a választott Azure-szolgáltatásban.  A build és a kiadás definíciói a további forgatókönyveknek megfelelően testre szabhatók.  A build és a kiadási definíciók klónozhatók is, így más projektekben is fel lehet használni őket.

A DevOps Project létrehozása után a következőket teheti:

* A build és a kiadási folyamat testreszabása
* Lekérési kérelmek használata a kódfolyamat kezeléséhez és a minőség magas szinten tartásához
* Minden véglegesítés tesztelése és felépítése a kódok egyesítése előtt a minőség javítása érdekében
* A projektvárólista és a problémák nyomon követése az alkalmazással párhuzamosan

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Hogyan kezdhetem el az Azure DevOps Project használatát?

* [Ismerkedés az Azure DevOps Projecttel](https://docs.microsoft.com/vsts/build-release/actions/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Azure DevOps Project – videók

* [CI/CD létrehozása az Azure DevOps Projecttel](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
