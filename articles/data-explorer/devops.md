---
title: Az Azure Data Explorer az Azure DevOps-feladat
description: Ebben a témakörben elsajátíthatja a kibocsátási folyamat létrehozása és üzembe helyezése
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 0628d5c07d7258cc4d68727c364e65bd81c78e8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388998"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Az Azure Data Explorer az Azure DevOps-feladat

[Az Azure DevOps-szolgáltatásokkal](https://azure.microsoft.com/services/devops/) fejlesztési például nagy teljesítményű folyamatok, ingyenes privát Git-tárházakat, konfigurálható kanbantáblák és széles körű automatikus és folyamatos tesztelési funkciók eszközöket biztosít. [Az Azure folyamatok](https://azure.microsoft.com/services/devops/pipelines/) az Azure DevOps-képesség, amely lehetővé teszi a CI/CD üzembe helyezni a kódot a nagy teljesítményű folyamatok kezelését, hogy az bármilyen nyelv, a platform és a felhő használata.
[Az Azure Data Explorer – rendszergazdai parancsok](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) , az Azure-folyamatok feladat, amely lehetővé teszi, hogy a kiadási folyamatok létrehozásához, és az adatbázis üzembe helyezése az Azure Data Explorer adatbázisok változik. Az ingyenesen elérhető a [Visual Studio-piactér](https://marketplace.visualstudio.com/).

Ez a dokumentum ismerteti egy egyszerű példa használatakor a **Azure adatkezelő – rendszergazdai parancsok** feladatot a séma üzembe helyezése módosítja az adatbázishoz. Teljes CI/CD-folyamatok, tekintse meg a [Azure DevOps-dokumentáció](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Az Azure Data Explorer fürt beállítása:
    * Egy [Azure adatkezelő fürt és adatbázis](/azure/data-explorer/create-cluster-database-portal)
    * Hozzon létre az Azure Active Directory (Azure AD-) alkalmazás szerint [kiépítése az Azure AD-alkalmazást](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Az Azure AD-alkalmazás által az Azure Data Explorer adatbázisban való hozzáférést biztosít [adatkezelő Azure adatbázis-engedélyek kezelése](/azure/data-explorer/manage-database-permissions).
* Az Azure DevOps beállítása:
    * [Regisztráljon egy ingyenes szervezet](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Hozzon létre egy szervezet](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Az Azure DevOps-projekt létrehozása](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Git-kódok](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Mappák létrehozása

Hozza létre a következő minta mappákat (*funkciók*, *házirendek*, *táblák*) a Git-adattárban. Másolja a fájlokat [Itt](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) mappákba a megfelelő látható, az alábbi és véglegesítse a módosításokat. Hajtsa végre az alábbi munkafolyamat a mintafájlok állnak rendelkezésre.

![Mappák létrehozása](media/devops/create-folders.png)

> [!TIP]
> A saját munkafolyamat létrehozásakor, azt javasoljuk, így a kód idempotens. Például [.create / egyesítő tábla](/azure/kusto/management/tables#create-merge-tables) helyett [.create tábla](/azure/kusto/management/tables#create-table), és használja [.create vagy alter](/azure/kusto/management/functions#create-or-alter-function) helyett [.create](/azure/kusto/management/functions#create-function) a függvény.

## <a name="create-a-release-pipeline"></a>Kiadási folyamat létrehozása

1. Jelentkezzen be a [Azure DevOps-szervezet](https://dev.azure.com/).
1. Válassza ki **folyamatok** > **kiadásokban** bal oldali menüben, majd válassza **új adatcsatorna**.

    ![Új folyamat](media/devops/new-pipeline.png)

1. A **új kibocsátásában** ablak nyílik meg. Az a **folyamatok** lap a **válasszon ki egy sablont** ablaktáblán válassza ki az **üres feladat**.

     ![Válasszon ki egy sablont](media/devops/select-template.png)

1. Válassza ki **fázis** gombra. A **fázis** ablaktáblán adja hozzá a **fázis neve**. Válassza ki **mentése** a folyamat mentéséhez.

    ![A fázis neve](media/devops/stage-name.png)

1. Válassza ki **egy összetevő hozzáadása** gombra. Az a **egy összetevő hozzáadása** ablaktáblán válassza ki a tárházat, amelyben a kód létezik, töltse ki a megfelelő információkat, majd kattintson **Hozzáadás**. Válassza ki **mentése** a folyamat mentéséhez.

    ![Az összetevő hozzáadása](media/devops/add-artifact.png)

1. Az a **változók** lapon jelölje be **+ Hozzáadás** hozzon létre egy változót, **végponti URL-cím** , amely felhasználja a feladatot. Írás a **neve** és a **érték** a végpont. Válassza ki **mentése** a folyamat mentéséhez. 

    ![Változó létrehozása](media/devops/create-variable.png)

    A Endpoint_URL áttekintés oldalán található a **Azure Data Explorer fürtje** az Azure Portalon az Azure Data Explorer fürt URI-ja tartalmaz. Az URI-t a következő formátumban hozza létre `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Ha például https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Az Azure Data Explorer fürt URI-ja](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Hozzon létre az feladatok üzembe helyezéséhez

1. Az a **folyamat** fülre, kattintson a **1 feladat, 0 feladat** feladatok hozzáadásával. 

    ![Tevékenységek hozzáadása](media/devops/add-task.png)

1. Hozzon létre három feladatok üzembe helyezéséhez **táblák**, **funkciók**, és **házirendek**, ebben a sorrendben. 

1. Az a **feladatok** lapon jelölje be **+** által **ügynöki feladat**. Keressen az **Azure Data Explorer** kifejezésre. A **Marketplace**, telepítse a **Azure adatkezelő – rendszergazdai parancsok** bővítmény. Ezután válassza ki **Hozzáadás** a **futtatása az Azure Data Explorer parancs**.

     ![Felügyeleti parancsok hozzáadása](media/devops/add-admin-commands.png)

1. Kattintson a **Kusto parancs** bal és a frissítés a feladat a következő információkat:
    * **Megjelenített név**: A feladat neve
    * **Fájl elérési útja**: Az a **táblák** feladat, adja meg */Tables/* .csl, mert a tábla-létrehozási fájlok a *tábla* mappát.
    * **Végponti URL-cím**: Adja meg a `EndPoint URL`az előző lépésben létrehozott változó.
    * Válassza ki **használata szolgáltatásvégpont** válassza **+ új**.

    ![Kusto parancs feladat frissítése](media/devops/kusto-command-task.png)

1. Hajtsa végre a következő információkat a **hozzáadása Azure adatkezelő szolgáltatáskapcsolódási** ablakban:

    |Beállítás  |Ajánlott érték  |
    |---------|---------|
    |**Kapcsolat neve**     |    Adjon meg egy nevet a szolgáltatásvégpont azonosításához     |
    |**Fürt URL-címe**    |    Az Áttekintés szakaszban az Azure Data Explorer fürt az Azure Portalon található érték | 
    |**Service Principal Id**    |    Adja meg az AAD-Alkalmazásazonosító (előfeltétel létrehozott)     |
    |**Egyszerű szolgáltatás Alkalmazáskulcs**     |    Adja meg az AAD alkalmazás kulcsot (előfeltétel létrehozott)    |
    |**AAD-bérlő azonosítója**    |      Adja meg az AAD-bérlő (például a microsoft.com, a contoso.com...)    |

    Válassza ki **engedélyezése ehhez a kapcsolathoz használni minden folyamatok** jelölőnégyzetet. Kattintson az **OK** gombra.

    ![Szolgáltatás-kapcsolat hozzáadása](media/devops/add-service-connection.png)

1. Ismételje meg a másik 1 – 5 kétszer a központi telepítéshez a *funkciók* és *házirendek* mappákat. Kattintson a **Mentés** gombra. Az a **feladatok** fülre, tekintse meg a létrehozott három feladatokat: **Táblák üzembe**, **Functions üzembe helyezése**, és **házirendekkel**.

    ![Az összes mappa üzembe helyezése](media/devops/deploy-all-folders.png)

1. Válassza ki **+ kiadás** > **kiadás létrehozása** hozhat létre egy kiadást.

    ![Hozzon létre egy kiadás](media/devops/create-release.png)

1. Az a **naplók** lapot, ellenőrizze, hogy a központi telepítés állapota: sikeres.

    ![Központi telepítés rendszer sikeres](media/devops/deployment-successful.png)

Ezennel befejezte a három éles üzem előtti feladatok üzembe helyezéséhez egy kibocsátási folyamatok létrehozását.