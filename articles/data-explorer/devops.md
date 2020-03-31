---
title: Azure DevOps-feladat az Azure Data Explorerhez
description: Ebben a témakörben megtudhatja, hogyan hozhat létre kiadási folyamatot, és
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472043"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps-feladat az Azure Data Explorer szolgáltatáshoz

[Az Azure DevOps Services](https://azure.microsoft.com/services/devops/) fejlesztési együttműködési eszközöket, például nagy teljesítményű folyamatokat, ingyenes magángit-adattárakat, konfigurálható Kanban-táblákat, valamint kiterjedt automatizált és folyamatos tesztelési lehetőségeket kínál. [Az Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) egy Azure DevOps-funkció, amely lehetővé teszi, hogy kezelje a CI/CD-t a kód üzembe helyezéséhez nagy teljesítményű folyamatok, amelyek bármilyen nyelven, platformon és felhőben működik.
[Azure Data Explorer – A rendszergazdai parancsok](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) az Azure Pipelines feladat, amely lehetővé teszi a kiadási folyamatok létrehozását és az adatbázis-módosítások üzembe helyezését az Azure Data Explorer-adatbázisokban. Ingyenesen elérhető a [Visual Studio Piactéren.](https://marketplace.visualstudio.com/)

Ez a dokumentum egy egyszerű példát mutat be az **Azure Data Explorer – Admin parancsok** feladat használatával kapcsolatban a sémamódosítások adatbázisba való üzembe helyezéséhez. A teljes CI/CD-folyamatok, tekintse meg az [Azure DevOps dokumentációját.](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Az Azure Data Explorer fürt beállítása:
    * [Azure Data Explorer-fürt és -adatbázis](/azure/data-explorer/create-cluster-database-portal)
    * Hozzon létre Azure Active Directory (Azure AD) alkalmazást [egy Azure AD-alkalmazás kiépítésével.](/azure/kusto/management/access-control/how-to-provision-aad-app)
    * Az [Azure Data Explorer adatbázisának kezelésével](/azure/data-explorer/manage-database-permissions)hozzáférést biztosít az Azure AD alkalmazáshoz az Azure Data Explorer adatbázisában.
* Az Azure DevOps beállítása:
    * [Regisztráljon egy ingyenes szervezetre](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Szervezet létrehozása](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Projekt létrehozása az Azure DevOps-ban](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kód a Git-tel](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Mappák létrehozása

Hozza létre a következő mintamappákat (*Funkciók*, *Házirendek*, *Táblázatok*) a Git-tárházban. Másolja a fájlokat [innen](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) a megfelelő mappákba az alábbiak szerint, és véglegesítse a módosításokat. A mintafájlok a következő munkafolyamat végrehajtásához szolgálnak.

![Mappák létrehozása](media/devops/create-folders.png)

> [!TIP]
> A saját munkafolyamat létrehozásakor azt javasoljuk, hogy a kód idempotent. Például a [.create tábla](/azure/kusto/management/create-table-command)helyett használja a [.create táblát,](/azure/kusto/management/create-table-command#create-merge-table) a [.create-or-alter](/azure/kusto/management/create-alter-function) függvényt pedig [a .create](/azure/kusto/management/create-function) függvény helyett.

## <a name="create-a-release-pipeline"></a>Kiadási folyamat létrehozása

1. Jelentkezzen be az [Azure DevOps-szervezetbe.](https://dev.azure.com/)
1. Válassza a Bal oldali menü > **Folyamatfelszabadítások parancsát,** majd az **Új folyamat**lehetőséget. **Pipelines**

    ![Új folyamat](media/devops/new-pipeline.png)

1. Megnyílik **az Új kiadási folyamat** ablaka. A **Folyamatok** lap Sablon **kiválasztása** ablaktáblában válassza a **Feladat kiürítése**lehetőséget.

     ![Sablon kiválasztása](media/devops/select-template.png)

1. Válassza **a Színpad** gombot. A **Szakasz** ablaktáblán adja hozzá a **Színpad nevét**. A folyamat mentéséhez válassza a **Mentés** lehetőséget.

    ![Nevezze el a színpadot](media/devops/stage-name.png)

1. Válassza **a Műtermék hozzáadása** gombot. A **Műtermék hozzáadása** ablaktáblában jelölje ki azt a tárházat, amelyben a kód létezik, töltse ki a vonatkozó információkat, majd kattintson a **Hozzáadás gombra.** A folyamat mentéséhez válassza a **Mentés** lehetőséget.

    ![Összetevő hozzáadása](media/devops/add-artifact.png)

1. A **Változók** lapon válassza a **+ Hozzáadás** lehetőséget, ha a feladatban használni kívánt **végpont URL-címéhez** változót szeretne létrehozni. Írja be a végpont **nevét** és **értékét.** A folyamat mentéséhez válassza a **Mentés** lehetőséget. 

    ![Változó létrehozása](media/devops/create-variable.png)

    A Endpoint_URL megkereséséhez az **Azure Data Explorer-fürt** áttekintő lapja az Azure Portalon tartalmazza az Azure Data Explorer fürt URI-ját. Az URI-t a `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`következő formátumban hozd létre.  Például https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Azure Data Explorer fürt URI-ja](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Üzembe helyezendő feladatok létrehozása

1. A **Folyamat** lapon kattintson **az 1 feladatra, 0 feladatra** a feladatok hozzáadásához. 

    ![Tevékenységek hozzáadása](media/devops/add-task.png)

1. Hozzon létre három feladatot **a Táblák,** **funkciók**és **házirendek**központi telepítéséhez ebben a sorrendben. 

1. A **Feladatok** lapon **+** válassza az **Ügynök feladat**szerint lehetőséget. Keressen az **Azure Data Explorer** kifejezésre. A **Marketplace-en**telepítse az **Azure Data Explorer – Admin parancsok** bővítményt. Ezután válassza a **Hozzáadás** az **Azure Data Explorer futtatása parancsban lehetőséget.**

     ![Rendszergazdai parancsok hozzáadása](media/devops/add-admin-commands.png)

1. Kattintson a **Kusto Command** a bal oldalon, és frissítse a feladatot a következő információkat:
    * **Megjelenítendő név**: A feladat neve
    * **Fájl elérési útja**: A **Táblák** feladatban adja meg a */Tables/*.csl kapcsolót, mivel a táblalétrehozási fájlok a *Tábla* mappában vannak.
    * **Végpont URL-címe** `EndPoint URL`: adja meg az előző lépésben létrehozott változót.
    * Válassza **a Szolgáltatásvégpont használata,** majd **a + Új**lehetőséget.

    ![Kusto parancsfeladat frissítése](media/devops/kusto-command-task.png)

1. Töltse ki az alábbi információkat az **Azure Data Explorer hozzáadása szolgáltatáskapcsolat** ablakban:

    |Beállítás  |Ajánlott érték  |
    |---------|---------|
    |**Kapcsolat neve**     |    Adjon meg egy nevet a szolgáltatásvégpont azonosításához     |
    |**Fürt URL-címe**    |    Az érték az Azure Data Explorer-fürt áttekintő szakaszában található az Azure Portalon | 
    |**Szolgáltatásegyszerű azonosító**    |    Adja meg az AAD alkalmazásazonosítót (előfeltételként létrehozva)     |
    |**Egyszerű szolgáltatáskulcs**     |    Adja meg az AAD alkalmazáskulcsot (előfeltételként létrehozva)    |
    |**AAD-bérlőazonosító**    |      Adja meg az AAD-bérlőt (például microsoft.com, contoso.com...)    |

    Jelölje be **Az összes folyamat számára a kapcsolat használatának** engedélyezése jelölőnégyzetet. Válassza **az OK gombot.**

    ![Szolgáltatáskapcsolat hozzáadása](media/devops/add-service-connection.png)

1. Ismételje meg az 1-5. *Functions* *Policies* Kattintson a **Mentés** gombra. A **Feladatok** lapon tekintse meg a létrehozott három feladatot: **Táblák telepítése**, **Függvények telepítése**és **Házirendek telepítése**.

    ![Az összes mappa telepítése](media/devops/deploy-all-folders.png)

1. A kiadás létrehozásához válassza **a + Release** > Create release (Kiadás**létrehozása) lehetőséget.**

    ![Kiadás létrehozása](media/devops/create-release.png)

1. A **Naplók** lapon ellenőrizze, hogy a központi telepítés állapota sikeres-e.

    ![A telepítés sikeres](media/devops/deployment-successful.png)

Most már befejezte egy kiadási folyamat létrehozását három feladat üzembe helyezéséhez az üzem előtt.