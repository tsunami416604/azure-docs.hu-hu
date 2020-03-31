---
title: Környezetek integrálása az Azure DevTest Labs-ben az Azure DevTest Labs környezetébe
description: Ismerje meg, hogyan integrálhatja az Azure DevTest Labs környezeteket az Azure DevOps folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamataiba.
services: devtest-lab,virtual-machines,lab-services
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
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169428"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Környezetek integrálása az Azure DevOps CI/CD-folyamatokba
Az Azure DevOps-feladatok bővítményt az Azure DevOps-szolgáltatásokban (korábbi nevén Visual Studio Team Services) telepített Azure DevTest Labs tasks-bővítmény segítségével egyszerűen integrálhatja a folyamatos integrációs (CI)/ folyamatos kézbesítési (CD) build-és kiadási folyamatot az Azure DevTest Labs alkalmazással. Ezek a bővítmények megkönnyítik egy [környezet](devtest-lab-test-env.md) gyors üzembe helyezését egy adott tesztfeladathoz, majd törlik azt, amikor a teszt befejeződött. 

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy környezetet, majd törölheti a környezetet, mindezt egy teljes folyamatban. Ezeket a feladatokat általában külön-külön hajtsa végre a saját egyéni build-teszt-üzembe helyezési folyamatában. A cikkben használt bővítmények a [Következő létrehozási/törlési virtuálisgép-feladatokon](devtest-lab-integrate-ci-cd-vsts.md)kívül találhatók:

- Környezet létrehozása
- Környezet törlése

## <a name="before-you-begin"></a>Előkészületek
Mielőtt integrálná ci/CD-folyamatát az Azure DevTest Labs alkalmazásával, telepítse az [Azure DevTest Labs feladatok](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) bővítményt a Visual Studio Piactérről. 

## <a name="create-and-configure-the-lab-for-environments"></a>A tesztkörnyezet létrehozása és konfigurálása környezetekben
Ez a szakasz azt ismerteti, hogyan hozhat létre és konfigurálhat egy tesztkörnyezetet, ahol az Azure-környezet telepítve lesz.

1. [Hozzon létre egy labort,](devtest-lab-create-lab.md) ha még nem rendelkezik ilyensel. 
2. Konfigurálja a labort, és hozzon létre egy környezetsablont a cikk ben található utasítások szerint: [Többvirtuális gépkörnyezetek és PaaS-erőforrások létrehozása az Azure Resource Manager-sablonokkal.](devtest-lab-create-environment-from-arm.md)
3. Ebben a példában használjon egy [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)meglévő Azure-gyorsindítási sablont.
4. Másolja a **201-web-app-redis-cache-sql-database** mappába az **ArmTemplate** mappába a 2.

## <a name="create-a-release-definition"></a>Kiadási definíció létrehozása
A kiadási definíció létrehozásához tegye a következőket:

1.  A Build & Release hub **Felenged és** **felszabadítja lapján**válassza a **pluszjel (+)** gombot.
2.  A **Kiadásdefiníció létrehozása** ablakban jelölje ki az **Üres** sablont, majd kattintson a **Tovább**gombra.
3.  Válassza **a Később kiválasztása**lehetőséget, majd a **Létrehozás gombra,** ha egy alapértelmezett környezettel rendelkező új kiadási definíciót szeretne létrehozni, csatolt összetevők nélkül.
4.  A helyi menü megnyitásához az új kiadási definícióban jelölje ki a három **pontot (...)** a környezet neve mellett, majd válassza a **Változók konfigurálása**lehetőséget.
5.  A **Konfigurálás - környezet** ablakban a kiadási definíciós feladatokban használt változókhoz adja meg a következő értékeket:
1.  A **administratorLogin**mezőbe írja be az SQL administrator bejelentkezési nevét.
2.  A **administratorLoginPassword**mezőbe írja be az SQL administrator bejelentkezési jelszavát. A jelszó elrejtéséhez és biztonságossá tétele érdekében használja a "lakat" ikont.
3.  Az **databaseName**mezőbe írja be az SQL Database nevet.
4.  Ezek a változók a példakörnyezetek, a különböző környezetekben különböző változók lehetnek.

## <a name="create-an-environment"></a>Környezet létrehozása
A központi telepítés következő szakasza a fejlesztési vagy tesztelési célokra használandó környezet létrehozása.

1. A kiadási definícióban válassza a **Tevékenységek hozzáadása**lehetőséget.
2. A **Feladatok** lapon adjon hozzá egy Azure DevTest Labs create environment feladatot. Állítsa be a feladatot az alábbiak szerint:
    1. Az **Azure RM-előfizetés**esetén válasszon egy kapcsolatot a **Rendelkezésre álló Azure-szolgáltatáskapcsolatok** listában, vagy hozzon létre egy korlátozottabb engedélykapcsolatot az Azure-előfizetéshez. További információ: [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints).
2. A **Labor neve**területen válassza ki a korábban létrehozott példány nevét*.
3. A **tárház neve,** válassza ki a tárház, ahol az Erőforrás-kezelő sablon (201) lett leküldéses*.
4. A **Sablon neve mezőben**válassza ki a forráskódtárba mentett környezet nevét*. 
5. A **Labor neve,** **tárház neve**és a sablon **neve** az Azure-erőforrás-azonosítók rövid ábrázolásai. A rövid név manuális megadása hibákat okoz, a legördülő listák segítségével válassza ki az adatokat.
6. A **Környezet neve**mezőbe írjon be egy nevet, amely egyedileg azonosítja a környezetpéldányt a laboron belül.  Egyedinek kell lennie a laborban.
7. A **paraméterfájl** és a **Paraméterek**lehetővé teszi kontrájának egyéni paraméterek átvitelét a környezetben. A paraméterértékek beállítására vagy mindkettőre használható. Ebben a példában a Paraméterek szakasz lesz használva. Használja a környezetben definiált változók nevét, például:`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. A környezeti sablonon belüli információk a sablon kimeneti szakaszában továbbíthatók. Jelölje be **a Kimeneti változók létrehozása a környezeti sablon kimenetén alapuló jelölőnégyzetet,** hogy más feladatok is használhassák az adatokat. `$(Reference name.Output Name)`a minta, hogy kövesse. Ha például a Hivatkozási név DTL volt, és a sablonban a kimenetneve hely volt, akkor a változó a . `$(DTL.location)`

## <a name="delete-the-environment"></a>A környezet törlése
Az utolsó szakasz az Azure DevTest Labs-példányban üzembe helyezett környezet törlése. Általában törölni kell a környezetet a fejlesztési feladatok végrehajtása vagy a telepített erőforrásokon szükséges tesztek futtatása után.

A kiadási definícióban válassza a **Feladatok hozzáadása**lehetőséget, majd a **Telepítés** lapon adjon hozzá egy **Azure DevTest Labs delete environment** feladatot. Állítsa be a következőképpen:

1. A virtuális gép törléséről az [Azure DevTest Labs-feladatok című témakörben látható:](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
    1. Az **Azure RM-előfizetés**esetén válasszon egy kapcsolatot a **Rendelkezésre álló Azure-szolgáltatáskapcsolatok** listában, vagy hozzon létre egy korlátozottabb engedélykapcsolatot az Azure-előfizetéshez. További információ: [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints).
    2. A **Labor neve**, válassza ki a labor, ahol a környezet létezik.
    3. A **Környezet neve mezőbe**írja be az eltávolítandó környezet nevét.
2. Adja meg a kiadási definíció nevét, majd mentse.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 
- [Többvirtuális gépes környezeteket hozhat létre az Erőforrás-kezelő sablonjaival.](devtest-lab-create-environment-from-arm.md)
- A [DevTest Labs GitHub-tárházból](https://github.com/Azure/azure-quickstart-templates)származó Gyorsindítási Erőforrás-kezelő sablonok a DevTest Labs automatizálásához.
- [VSTS hibaelhárítási lap](/azure/devops/pipelines/troubleshooting)

