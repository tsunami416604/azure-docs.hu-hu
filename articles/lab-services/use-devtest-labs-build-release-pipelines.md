---
title: A DevTest Labs használata az Azure Pipelines buildelési és kiadási folyamataiban
description: Megtudhatja, hogyan használhatja az Azure DevTest Labs az Azure-folyamatok létrehozása és kiadása folyamatok.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169234"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>A DevTest Labs használata az Azure Pipelines buildelési és kiadási folyamataiban
Ez a cikk arról nyújt tájékoztatást, hogyan devtest labs használható Az Azure-folyamatok létrehozása és kiadása folyamatok. 

## <a name="overall-flow"></a>Teljes áramlás
Az alapfolyamat egy **buildfolyamat,** amely a következő feladatokat végzi el:

1. Az alkalmazáskód létrehozása.
1. Hozza létre az alapkörnyezetet a DevTest Labs ben.
1. Frissítse a környezetet egyéni adatokkal.
1. Az alkalmazás üzembe helyezése a DevTest Labs környezetben
1. Tesztelje a kódot. 

Miután a build sikeresen befejeződött, a **kiadási folyamat** a buildösszetevőket fogja használni az átmeneti vagy éles környezet telepítéséhez. 

Az egyik szükséges telephely, hogy a tesztelt ökoszisztéma újralétrehozásához szükséges összes információ elérhető a build összetevők, beleértve az Azure-erőforrások konfigurációját. Mivel az Azure-erőforrások használata költséggel jár, a vállalatok ellenőrizni vagy nyomon követni szeretnék ezeknek az erőforrásoknak a használatát. Bizonyos esetekben az Azure Resource Manager-sablonokat, amelyek az erőforrások létrehozásához és konfigurálásához használt egy másik részleg, például it. És ezek a sablonok lehet tárolni egy másik tárházban. Ez egy érdekes helyzethez vezet, ahol létrehoznak és tesztelnek egy buildet, és mind a kódot, mind a konfigurációt a buildösszetevőkön belül kell tárolni a rendszer éles környezetben történő megfelelő újralétrehozásához. 

A DevTest Labs használatával a build/teszt fázisban azure Resource Manager-sablonokat és támogató fájlokat adhat hozzá a buildforrásokhoz, így a kiadási fázisban a teszteléshez használt pontos konfiguráció éles környezetben van telepítve. Az **Azure DevTest Labs-környezet létrehozása** feladat a megfelelő konfigurációval menti az Erőforrás-kezelő sablonokat a build-összetevőkközött. Ebben a példában az [oktatóanyag: .NET Core és SQL Database webalkalmazás létrehozása az Azure App Service-ben](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)a webalkalmazás azure-beli üzembe helyezéséhez és teszteléséhez fogja használni.

![Teljes áramlás](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása
Van néhány elem, amelyet előre létre kell hozni:

- Két tároló. Az első az oktatóanyagból származó kóddal és egy Erőforrás-kezelő sablonnal két további virtuális géptel. A második az alap Azure Resource Manager-sablont (meglévő konfigurációt) fogja tartalmazni.
- Erőforráscsoport a termelési kód és a konfiguráció telepítéséhez.
- Egy tesztkörnyezet kell beállítani [a kapcsolatot a konfigurációs tárház](devtest-lab-create-environment-from-arm.md) a build-folyamat. A Resource Manager-sablont be kell adni a konfigurációs tárházba azuredeploy.json ként a metadata.json használatával, hogy a DevTest Labs felismerhesse és üzembe helyezhesse a sablont.

A buildfolyamat létrehoz egy DevTest Labs-környezetet, és telepíti a kódot tesztelésre.

## <a name="set-up-a-build-pipeline"></a>Buildelési folyamat beállítása
Az Azure-folyamatokban hozzon létre egy buildfolyamatot az oktatóanyagból származó kód [használatával: .NET Core és SQL Database webalkalmazás létrehozása az Azure App Service szolgáltatásban.](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md) Használja a **ASP.NET Core** sablont, amely feltölti a szükséges feladatot a kód létrehozásához, teszteléséhez és közzétételéhez.

![A ASP.NET sablon kiválasztása](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Három további feladatot kell hozzáadnia a környezet devtest labs és a környezetben üzembe helyezéséhez.

![Három feladattal rendelkező folyamat](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Környezeti feladat létrehozása
A hozzon létre környezetfeladat **(Azure DevTest Labs create environment** feladat) használja a legördülő listákat a következő értékek kiválasztásához:

- Azure-előfizetés
- a labor neve
- az adattár neve
- a sablon neve (amely a környezetet tároló mappát jeleníti meg). 

Azt javasoljuk, hogy az adatok manuális megadása helyett használja a legördülő listákat az oldalon. Ha manuálisan adja meg az adatokat, adja meg a teljesen minősített Azure-erőforrás-azonosítók. A tevékenység az erőforrás-azonosítók helyett a rövid neveket jeleníti meg. 

A környezet neve a DevTest Labs ben megjelenített név. Minden buildnek egyedi névnek kell lennie. Például: **TestEnv$(Build.BuildId)**. 

Megadhatja a paraméterfájlt vagy a paramétereket az Erőforrás-kezelő sablonba történő adatadáshoz. 

Jelölje be a **Kimeneti változók létrehozása a környezeti sablon kimeneti beállítása alapján beállítást,** és adjon meg egy hivatkozásnevet. Ebben a példában adja meg a **BaseEnv** értéket a hivatkozási névhez. Ezt a BaseEnv-et fogja használni a következő feladat konfigurálásakor. 

![Azure DevTest Labs környezet létrehozása feladat létrehozása](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Környezeti feladat feltöltése
A második feladat **(Az Azure DevTest Labs feltöltése környezet** feladat) a meglévő DevTest Labs környezet frissítése. A környezeti feladat létrehozása kimenetek **BaseEnv.environmentResourceId,** amely a feladat környezeti nevének konfigurálására szolgál. A példa Resource Manager sablonja két paramétert mutat be : **adminUserName** és **adminPassword**. 

![Feltölti az Azure DevTest Labs környezet feladatát](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Az App Service üzembe helyezési feladata
A harmadik feladat az **Azure App Service üzembe helyezési** feladat. Az alkalmazás típusa **Web App,** az App Service neve **pedig $(Webhely)** lesz.

![App Service-üzembe helyezési feladat](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Kiadási folyamat beállítása
Két feladattal hoz létre kiadási folyamatot: **Azure Deployment: Erőforráscsoport létrehozása vagy frissítése** és az Azure App Service **telepítése.** 

Az első tevékenységhez adja meg az erőforráscsoport nevét és helyét. A sablon helye egy csatolt műtermék. Ha az Erőforrás-kezelő sablon csatolt sablonokat tartalmaz, egyéni erőforráscsoport-telepítést kell megvalósítani. A sablon a közzétett cseppösszetevőben található. Az Erőforrás-kezelő sablonparamétereinek felülbírálása. A fennmaradó beállításokat az alapértelmezett értékekkel hagyhatja. 

A második feladat **üzembe helyezése Azure App Service**, adja meg az Azure-előfizetés, válassza a Web **App** az **alkalmazás típusa**, és **$(Webhely)** az **App Service neve.** A fennmaradó beállításokat az alapértelmezett értékekkel hagyhatja. 

## <a name="test-run"></a>Próbaüzem
Most, hogy mindkét folyamat be van állítva, manuálisan sorakasztja fel a buildet, és látni, hogy működik. A következő lépés a megfelelő eseményindító beállítása a buildhez, és csatlakoztassa a buildet a kiadási folyamathoz.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Integrálja az Azure DevTest Labs-t az Azure Pipelines folyamatos integrációs és kézbesítési folyamatába](devtest-lab-integrate-ci-cd-vsts.md)
- [Környezetek integrálása az Azure Pipelines CI/CD-folyamataiba](integrate-environments-devops-pipeline.md)
