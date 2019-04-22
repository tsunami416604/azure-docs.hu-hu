---
title: Környezetek integrálása az Azure-folyamatok az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan integrálhatja az Azure DevTest Labs-környezetben az Azure DevOps folyamatos integrációs (CI) és a folyamatos továbbítás (CD) folyamatokat.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357403"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Környezetek integrálása az Azure DevOps CI/CD-folyamatok
Használhatja az Azure DevTest Labs-feladatok-bővítmény, amely könnyen integrálható a folyamatos integrációs (CI) települ az Azure DevOps-szolgáltatásokkal (korábbi nevén Visual Studio Team Services) / folyamatos készregyártás (CD) build kibocsátási folyamat az Azure-ral DevTest Labs szolgáltatásban. Ezek a bővítmények könnyebb gyorsan üzembe helyezhet egy [környezet](devtest-lab-test-env.md) egy adott feladat tesztelése, és törölje a vizsgálat befejezésekor. 

Ez a cikk bemutatja, hogyan létrehozásához és üzembe helyezése a környezet, majd törli a környezetet, minden található egy teljes körű folyamatot. Általában elvégezhető minden egyes tevékenységet külön-külön saját egyéni buildelési, tesztelési és üzembe folyamatban. Az ebben a cikkben használt bővítmények vannak ezek [halasztott adó-eszköz VM-feladatok létrehozása/törlése](devtest-lab-integrate-ci-cd-vsts.md):

- Környezet létrehozása
- Egy környezet törlése

## <a name="before-you-begin"></a>Előkészületek
A CI/CD-folyamat integrálható az Azure DevTest Labs szolgáltatással, mielőtt telepíteni [Azure DevTest Labs-feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) bővítményt a Visual Studio-piactéren. 

## <a name="create-and-configure-the-lab-for-environments"></a>Létrehozhat és konfigurálhat a labor létrehozása környezetekhez
Ez a szakasz ismerteti, hogyan hozhat létre, és ha az Azure-környezetben lesznek telepítve tesztkörnyezet konfigurálása.

1. [Labor létrehozása](devtest-lab-create-lab.md) Ha még nem rendelkezik. 
2. A labor konfigurálásához, és hozzon létre egy környezetben sablon Ez a cikk utasításait követve: [Több virtuális gépes környezet és PaaS-erőforrások létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md).
3. Ebben a példában használja egy meglévő Azure gyorsindítási sablon [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Másolás a **201-web-app-redis-cache-sql-database** mappából a **ArmTemplate** a tárházban, a 2. lépésben konfigurált mappát.

## <a name="create-a-release-definition"></a>Kiadási definíció létrehozása
A kiadási definíció létrehozása, tegye a következőket:

1.  Az a **kiadásokban** lapján a **Build & Release hub**, jelölje be a **plusz jelre (+)** gombra.
2.  Az a **kiadási definíció létrehozása** ablakban válassza ki a **üres** sablont, és válassza ki **tovább**.
3.  Válassza ki **válasszon később**, majd válassza ki **létrehozás** új kiadási definíció létrehozása egy alapértelmezett környezetet, és nem társított összetevők.
4.  Az új kiadási definíció a helyi menü megnyitásához válassza a **három pontra (...)**  a Tovább gombra a környezet nevét, és adja meg **változók konfigurálása**.
5.  Az a **konfigurálása - környezet** ablakban, a változók a kiadási definíció feladatok során használt, adja meg a következő értékeket:
1.  A **administratorLogin**, adja meg az SQL-rendszergazdai bejelentkezési nevét.
2.  A **administratorLoginPassword**, adja meg a jelszót az SQL-rendszergazdai bejelentkezéshez használható. A "lakat" ikon használatával elrejtéséhez, és a jelszót biztonságos.
3.  A **databaseName**, adja meg az SQL-adatbázis nevét.
4.  Ezeket a változókat a példakörnyezetek jellemző, előfordulhat, hogy különböző környezetek különböző változók.

## <a name="create-an-environment"></a>Környezet létrehozása
A következő szintre a központi telepítést, ha a környezet a fejlesztési és tesztelési célra használható.

1. Válassza ki a kiadási definíció **tevékenységek hozzáadása a**.
2. Az a **feladatok** lapon maradva adja hozzá az Azure DevTest Labs környezet létrehozása feladat. A feladat a következőképpen konfigurálja:
    1. A **Azure RM-előfizetés**, válassza ki a kapcsolat a **elérhető Azure-szolgáltatás kapcsolatok** listában, vagy hozzon létre több korlátozott engedélyek kapcsolat az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager-szolgáltatásvégpont](/azure/devops/pipelines/library/service-endpoints).
2. A **labor nevét**, válassza ki a nevét, a korábban létrehozott példány *.
3. A **adattár neve**, válassza ki a tárházba, ahol a Resource Manager-sablon (201) lett leküldve *.
4. A **sablonnevet**, válassza ki a környezetet, a forrás kód tárház * mentett nevét. 
5. A **labor nevét**, **adattár neve**, és **sablonnevet** vannak az Azure erőforrás-azonosítók rövid ábrázolásai. Manuális megadásánál valódi nevét hibákhoz vezethet, a legördülő listák segítségével válassza ki az adatokat.
6. A **környezetnevet**, adjon meg egy nevet a belül a labor környezet-példány egyedi azonosításához.  A labor egyedinek kell lennie.
7. A **paraméterfájl** és a **paraméterek**, lehetővé teszi a környezet átadandó egyéni paraméterek. Egyik vagy mindkét paraméter értékét állítsa használható. Ebben a példában a Paraméterek szakaszban fog használni. A változók, például meghatározott a környezetben, a neveket használja: `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. A környezet sablon információi a sablon kimeneti szakaszának keresztül adhatók át. Ellenőrizze **létrehozás kimeneti változók alapján a környezetben a sablon kimeneti** , más feladatok is használhatja az adatokat. `$(Reference name.Output Name)` van a mintát kövesse. Például, ha a hivatkozás neve halasztott adó-eszköz és a kimeneti név a sablonban helye változó lenne `$(DTL.location)`.

## <a name="delete-the-environment"></a>A környezet törlése
Az utolsó fáziséit, hogy az Azure DevTest Labs-példány az a környezet törlése. Általában akkor törli a környezetet, után hajtsa végre a fejlesztési feladatokat, vagy az üzembe helyezett erőforrásokon futtatott – a teszteket, amelyekre szüksége.

Válassza ki a kiadási definíció **tevékenységek hozzáadása a**, majd a a **telepítés** lapon maradva adja hozzá egy **Azure DevTest Labs törlése környezet** feladat. Ez a következőképpen konfigurálja:

1. Törölje a virtuális Gépet, tekintse meg [Azure DevTest Labs-feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. A **Azure RM-előfizetés**, válassza ki a kapcsolat a **elérhető Azure-szolgáltatás kapcsolatok** listában, vagy hozzon létre több korlátozott engedélyek kapcsolat az Azure-előfizetéshez. További információkért lásd: [Azure Resource Manager-szolgáltatásvégpont](/azure/devops/pipelines/library/service-endpoints).
    2. A **labor nevét**, válassza ki a tesztkörnyezetben, ahol a környezet létezik.
    3. A **környezetnevet**, el kell távolítani a környezet nevét adja meg.
2. Adja meg a kiadási definíció nevét, és mentse azt.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 
- [Több virtuális gépes környezet létrehozása a Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md).
- A DevTest Labs automation rövid útmutató Resource Manager-sablonokkal a [DevTest Labs GitHub-adattár](https://github.com/Azure/azure-quickstart-templates).
- [VSTS-hibaelhárítás lap](/azure/devops/pipelines/troubleshooting)

