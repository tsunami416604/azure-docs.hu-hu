---
title: Azure-DevOps feladat az Azure Adatkezelő
description: Ebben a témakörben megtudhatja, hogyan hozhat létre kiadási folyamatot, és hogyan helyezheti üzembe
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 6394d7149bd4e80f0a17a59a6259eedf4c806fd4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188179"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure-DevOps feladat az Azure Adatkezelő

Az [Azure DevOps Services](https://azure.microsoft.com/services/devops/) olyan fejlesztési együttműködési eszközöket kínál, mint például a nagy teljesítményű folyamatok, az ingyenes privát git-adattárak, a konfigurálható Kanban-táblák, valamint a kiterjedt automatizált és folyamatos tesztelési képességek. Az [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) egy Azure DevOps-képesség, amely lehetővé teszi a CI/CD felügyeletét a kód olyan nagy teljesítményű folyamatokkal való üzembe helyezéséhez, amelyek bármilyen nyelven, platformon és felhőben működnek.
[Azure adatkezelő – a rendszergazdai parancsok](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) az Azure-folyamatok feladata, amely lehetővé teszi a kiadási folyamatok létrehozását és az adatbázis-módosítások üzembe helyezését az Azure adatkezelő-adatbázisokban. A [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en ingyenesen elérhető.

Ez a dokumentum egy egyszerű példát mutat be az **Azure adatkezelő – admin parancsok** feladat használatára, amellyel a séma módosításait telepítheti az adatbázisában. A CI/CD-folyamatok teljes körű elvégzéséhez tekintse meg az [Azure DevOps dokumentációját](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Azure Adatkezelő-fürt beállítása:
    * [Azure adatkezelő-fürt és-adatbázis](/azure/data-explorer/create-cluster-database-portal)
    * Hozzon létre Azure Active Directory (Azure AD) alkalmazást [egy Azure ad-alkalmazás üzembe](/azure/kusto/management/access-control/how-to-provision-aad-app)helyezésével.
    * Az [azure adatkezelő Database-engedélyek kezelésével](/azure/data-explorer/manage-database-permissions)biztosíthatja a hozzáférést az Azure adatkezelő-adatbázis Azure ad alkalmazáséhez.
* Az Azure DevOps telepítése:
    * [Regisztráljon egy ingyenes szervezetre](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Szervezet létrehozása](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Projekt létrehozása az Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kód és git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Mappák létrehozása

Hozza létre a következő minta mappákat (*functions*, *policies*, *Tables*) a git-tárházban. Másolja a [fájlokat innen a](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) megfelelő mappákba az alább látható módon, és véglegesítse a módosításokat. A mintaadatok a következő munkafolyamat végrehajtásához vannak megadva.

![Mappák létrehozása](media/devops/create-folders.png)

> [!TIP]
> A saját munkafolyamatok létrehozásakor javasoljuk, hogy a kód idempotens. Használja például a [. Create-Merge táblát](/azure/kusto/management/create-table-command#create-merge-table) a [. Create](/azure/kusto/management/create-table-command)-szorzótábla helyett, és használja a. Create [-vagy a-Alter](/azure/kusto/management/functions#create-or-alter-function) függvényt a [. Create](/azure/kusto/management/functions#create-function) függvény helyett.

## <a name="create-a-release-pipeline"></a>Kiadási folyamat létrehozása

1. Jelentkezzen be az [Azure DevOps-szervezetbe](https://dev.azure.com/).
1. Válassza ki a **csatornákat** > a bal oldali menüből származó **kiadásokat** , és válassza az **új folyamat**elemet.

    ![Új folyamat](media/devops/new-pipeline.png)

1. Megnyílik az **új kiadási folyamat** ablak. A **folyamatok** lap **sablon kiválasztása** paneljén válassza az **üres feladatot**.

     ![Sablon kiválasztása](media/devops/select-template.png)

1. Válassza a **fázis** gombot. A **fázis** ablaktáblán adja hozzá a **szakasz nevét**. Válassza a **Mentés** lehetőséget a folyamat mentéséhez.

    ![A szakasz neve](media/devops/stage-name.png)

1. Válassza **az összetevő hozzáadása** gombot. Az **összetevő hozzáadása** panelen válassza ki azt a tárházat, amelyben a kód létezik, töltse ki a megfelelő információkat, majd kattintson a **Hozzáadás**gombra. Válassza a **Mentés** lehetőséget a folyamat mentéséhez.

    ![Összetevő hozzáadása](media/devops/add-artifact.png)

1. A **változók** lapon válassza a **+ Hozzáadás** elemet, és hozzon létre egy változót a feladatban használni kívánt **végpont URL-címhez** . Írja be a végpont **nevét** és **értékét** . Válassza a **Mentés** lehetőséget a folyamat mentéséhez. 

    ![Változó létrehozása](media/devops/create-variable.png)

    A Endpoint_URL megkereséséhez az Azure Portal **azure adatkezelő-fürt** Áttekintés lapja tartalmazza az Azure adatkezelő-fürt URI-ját. Hozza létre az URI-t a következő formátumban `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Például: https:\//kustodocs.westus.kusto.Windows.net? DatabaseName = SampleDB

    ![Azure Adatkezelő-fürt URI-ja](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Telepítendő feladatok létrehozása

1. A **folyamat** lapon kattintson az 1. feladat elemre **,** majd a feladatok hozzáadásához. 

    ![Feladatok hozzáadása](media/devops/add-task.png)

1. Hozzon létre három feladatot a **táblák**, **függvények**és **házirendek**telepítéséhez ebben a sorrendben. 

1. A **feladatok** lapon válassza az **+** ügynök szerint **feladat**lehetőséget. Keressen az **Azure Data Explorer** kifejezésre. A **piactéren**telepítse az **Azure adatkezelő – admin parancsok** bővítményt. Ezután válassza a **Hozzáadás** lehetőséget az **Azure adatkezelő parancs futtatásához**.

     ![Rendszergazdai parancsok hozzáadása](media/devops/add-admin-commands.png)

1. Kattintson a bal oldali **Kusto parancsra** , és frissítse a feladatot a következő információkkal:
    * **Megjelenítendő név**: a feladat neve
    * **Fájl elérési útja**: a **Tables (táblák** ) feladatban válassza a */Tables/* . CSL, mivel a tábla-létrehozási fájlok a *Table* mappában találhatók.
    * **Végpont URL-címe**: adja meg az előző lépésben létrehozott `EndPoint URL`változót.
    * Válassza a **szolgáltatás végpontjának használata** lehetőséget, és válassza az **+ új**lehetőséget.

    ![Kusto-utasítás frissítése feladat](media/devops/kusto-command-task.png)

1. Az **Azure adatkezelő Service-kapcsolatok hozzáadása** ablakban végezze el a következő információkat:

    |Beállítás  |Ajánlott érték  |
    |---------|---------|
    |**Kapcsolat neve**     |    Adja meg a szolgáltatási végpont azonosítására szolgáló nevet     |
    |**Fürt URL-címe**    |    Az érték az Azure Adatkezelő-fürt Áttekintés szakaszában található Azure Portal | 
    |**Egyszerű szolgáltatásnév azonosítója**    |    Adja meg a HRE-alkalmazás AZONOSÍTÓját (előfeltételként létrehozva)     |
    |**Egyszerű szolgáltatásnév alkalmazás kulcsa**     |    Adja meg a HRE-alkalmazás kulcsát (előfeltételként létrehozva)    |
    |**HRE-bérlő azonosítója**    |      Adja meg a HRE-bérlőt (például microsoft.com, contoso.com...)    |

    Jelölje be az **összes folyamat használatának engedélyezése a kapcsolódáshoz** jelölőnégyzetet. Kattintson az **OK** gombra.

    ![Szolgáltatási kapcsolatok hozzáadása](media/devops/add-service-connection.png)

1. Ismételje meg a 1-5. lépést még egyszer, hogy fájlokat telepítsen a *függvények* és *házirendek* mappából. Kattintson a **Mentés** gombra. A **feladatok** lapon tekintse meg a három létrehozott feladatot: **táblák üzembe**helyezése, **függvények**telepítése és **házirendek telepítése**.

    ![Az összes mappa üzembe helyezése](media/devops/deploy-all-folders.png)

1. Kiadás létrehozásához válassza a **+ kiadás** > **kiadás létrehozása** lehetőséget.

    ![Kiadás létrehozása](media/devops/create-release.png)

1. A **naplók** lapon győződjön meg arról, hogy a központi telepítés állapota sikeres.

    ![Az üzembe helyezés sikeres](media/devops/deployment-successful.png)

Ezzel befejezte a kiadási folyamat létrehozását a három feladat üzem előtti üzembe helyezéséhez.