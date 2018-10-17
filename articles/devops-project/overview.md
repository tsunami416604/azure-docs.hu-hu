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
ms.openlocfilehash: 39dffad597b8382dea4df6fa1b0726d9582d67d1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44293626"
---
# <a name="overview-of-azure-devops-project"></a>Az Azure DevOps Project áttekintése

Az Azure DevOps Project megkönnyíti az Azure-ral való ismerkedést. A DevOps Project-erőforrás segítségével az Azure Portalon elvégzett néhány gyors lépéssel elindíthatja kedvenc alkalmazástípusát a választott Azure-szolgáltatásban. A DevOps Project az alkalmazás fejlesztéséhez, telepítéséhez és monitorozásához szükséges összes beállítást elvégzi.
A DevOps Project irányítópultja lehetővé teszi a kódok véglegesítésének, létrehozásának és üzembe helyezésének monitorozását – mindezt az Azure Portal egyetlen nézetében.

## <a name="why-should-i-use-the-azure-devops-project"></a>Miért érdemes használni az Azure DevOps Projectet?

Az Azure DevOps Project automatizálja a teljes folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamat beállítását az Azure-ban.  Használhatja a már meglévő kódját vagy a megadott mintaalkalmazások valamelyikét, és gyorsan üzembe helyezheti az adott alkalmazást az olyan Azure-szolgáltatásokban, mint például a Virtual Machines, az App Service, az Azure Container Service, az Azure SQL Database vagy az Azure Service Fabric.  

Az Azure DevOps Project a DevOps-folyamat kezdeti beállításához szükséges összes feladatot elvégzi, a kezdeti Git-adattár beállításától kezdve a CI/CD-folyamat konfigurálásán és a monitorozáshoz szükséges Application Insights-erőforrás létrehozásán át a teljes megoldást összefoglaló nézet biztosításáig, amit az Azure Portalon létrehozott Azure DevOps Project irányítópultja tesz lehetővé.

Az Azure DevOps Project felhasználási lehetőségei:

* Az alkalmazások gyors üzembe helyezése az Azure-ban
* Az Azure CI/CD-folyamat beállításának automatizálása
* A DevOps Project sablonként használható annak áttekintéséhez és megértéséhez, hogy a CI/CD hogyan állítható be megfelelően az Azure DevOps használatával az Azure-ban
* Kezdje a CI/CD-folyamattal az Azure-ban, majd az adott forgatókönyveknek megfelelően végezze el a kiadási folyamat testreszabását

## <a name="how-do-i-use-the-azure-devops-project"></a>Hogyan működik az Azure DevOps Project?

Az Azure DevOps Project az Azure Portalon érhető el.  Az Azure DevOps Project-erőforrás létrehozása ugyanúgy történik a portálon, mint bármelyik másik Azure-erőforrás esetében.  A DevOps Project egy varázslóhoz hasonló módon, lépésről lépésre vezeti végig a különböző konfigurációs beállításokon.  

A kezdeti beállítás során számos konfigurálási lehetőség közül választhat.  Ezek a lehetőségek a következők:

* A megadott mintaalkalmazás vagy saját kód használata
* Az alkalmazásnyelv kiválasztása
* Alkalmazás-keretrendszer kiválasztása a nyelv alapján
* Egy Azure-szolgáltatás kiválasztása (üzembehelyezési cél)
* Azure DevOps-szervezet (új vagy meglévő)
* Azure-előfizetés kiválasztása
* Az Azure-szolgáltatások helyének kiválasztása
* A megfelelő tarifacsomag kiválasztása az Azure-szolgáltatásokhoz

Az Azure DevOps Project használatát követően az összes erőforrást egyetlen helyről, az Azure Portalról elérhető Azure DevOps Project-irányítópultról törölheti.

## <a name="azure-devops-project-and-azure-devops-integration"></a>Az Azure DevOps Project és az Azure DevOps integrációja

Az összes DevOps Project működtetését az Azure DevOps biztosítja.  A DevOps Project az összes szükséges feladatot automatizálja az Azure DevOpson belül a CI/CD Azure-beli beállításához.  A Git-adattár egy új vagy már létező Azure DevOps-szervezetben jön létre.  A DevOps Project véglegesíti a mintaalkalmazást vagy a meglévő kódot egy új Git-adattárban.  Az automatizálás emellett egy CI-eseményindítót is létrehoz a buildhez, így minden új kódvéglegesítés kezdeményez egy buildet.  A DevOps Project is létrehoz egy CD-eseményindítót, és minden új, sikeres buildet üzembe helyez a választott Azure-szolgáltatásban.  A build és a kiadási folyamatok a további forgatókönyveknek megfelelően testre szabhatók.  A build és a kiadási folyamatok klónozhatók is, így más projektekben is fel lehet használni őket.

A DevOps Project létrehozása után a következőket teheti:

* A build és a kiadási folyamat testreszabása
* Lekérési kérelmek használata a kódfolyamat kezeléséhez és a minőség magas szinten tartásához
* Minden véglegesítés tesztelése és felépítése a kódok egyesítése előtt a minőség javítása érdekében
* A projektvárólista és a problémák nyomon követése az alkalmazással párhuzamosan

## <a name="how-do-i-start-using-the-azure-devops-project"></a>Hogyan kezdhetem el az Azure DevOps Project használatát?

* [Ismerkedés az Azure DevOps Projecttel](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

## <a name="azure-devops-project-videos"></a>Azure DevOps Project – videók

* [CI/CD létrehozása az Azure DevOps Projecttel](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
