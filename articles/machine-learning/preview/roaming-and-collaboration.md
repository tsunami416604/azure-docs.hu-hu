---
title: "Központi és az Azure-ban együttműködés gép munkaterület tanulási |} Microsoft Docs"
description: "Ismert problémák listája és a hibaelhárítás elősegítése érdekében az útmutató"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Központi és az Azure Machine Learning munkaterület együttműködés
A dokumentumból megtudhatja, hogyan segítik a Azure Machine Learning-munkaterület gépek, valamint a teammates együttműködve lehetővé teszik a projektek válasszanak. 

Egy távoli Git-tárház (tárház) hivatkozás az Azure Machine Learning projekt létrehozásakor, a projekt metaadatok és a pillanatfelvételeket tárolják a felhőben. A felhő hivatkozás lehetővé teszi egy másik számítógépről (központi) nyissa meg a projektet. Akkor is hozzáférést biztosíthat a munkatársai, így az együttműködés. 

## <a name="prerequisites"></a>Előfeltételek
Először telepítse az Azure Machine Learning-munkaterület kísérletezhet fiók hozzáférést. Kövesse a [a telepítési útmutató](quickstart-installation.md) további részleteket.

Második, hozzáférési [Visual Studio Team System](https://www.visualstudio.com) , és hozzon létre egy tárház a projektet. Git kapcsolatos részletes információkért lásd a [Git-tárház használata Azure Machine Learning munkaterület projekttel](using-git-ml-project.md) cikk.

## <a name="create-a-new-azure-machine-learning-project"></a>Új Azure Machine Learning-projekt létrehozása
Indítsa el az Azure Machine Learning-munkaterület, és hozzon létre egy új projektet (például _iris_). Töltse ki a **visualstudio.com webhelyre GIT-tárház URL-cím** egy érvényes VSTS Git-tárház URL-szövegmező. 
>[!IMPORTANT]
>Projekt létrehozása sikertelen lesz, ha nem rendelkezik olvasási/írási hozzáférést a Git-tárház, és a Git-tárház nem üres, azaz már tartalmazza a főágba.

A projekt létrehozása után küldje el a projekt parancsfájlokat néhány felhőkörnyezetben. Ez a művelet be a távoli Git-tárház futtatási előzményei fiókirodai projekt állapot érvényesítése. 

Ha telepítő Git hitelesítési, kíván közvetlenül is működik, a főágba, vagy létrehozni egy új fiókirodai. 

Példa: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Központi
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Nyissa meg az Azure Machine Learning-munkaterület második gépen
Miután a VSTS Git-tárház kapcsolódik a projekthez, elérheti a _iris_ bármely olyan számítógépről, amelyen telepítette az Azure Machine Learning-munkaterület projekt. 

Nyissa meg a iris projektet egy másik számítógépen, kell bejelentkezni az alkalmazásba azokkal a a projekt létrehozása során használt hitelesítő adatokkal. Ezen felül kell kísérletezhet fiók és a munkaterületen nyissa meg. A _iris_ projekt betűrendben szerepel a más projektek belül a munkaterületen. 

### <a name="download-project-on-second-machine"></a>A második gépen projekt letöltése
Ha a munkaterületet meg van nyitva, a második számítógép, a szomszédos ikonra a _iris_ projekt eltér a szokásos mappára. Letöltési ikon jelzi, hogy a projekt a tartalom a felhőben, és az aktuális számítógép le kell tölteni. 

![projekt létrehozása](./media/roaming-and-collaboration/downloadable-project.png)

Kattintson a _iris_ projekt elindítja egy letöltési művelet. Egy rövid, közben, amikor a letöltés befejezése után a projekt legyenek elérhetők a második számítógép készen áll. 

Windows, a rendszer`C:\Users\<username>\Documents\AzureML`

A macOS akkor a következő helyen:`/home/<username>/Documents/AzureML`

Egy későbbi kiadásban tervezzük kibővítik a funkciókat, hogy jelöljön ki egy célmappát meg. 

>Vegye figyelembe, hogy megtörténjen-e egy mappa a pontos neve megegyezik a projektet, a letöltés sikertelen Azure ML könyvtárban található. Jelenleg nevezze át a meglévő mappát ahhoz, hogy a probléma megoldásához szükséges.


### <a name="work-on-the-downloaded-project"></a>A letöltött projektből működik 
Az újonnan letöltött projektből a legutóbbi futtatás a projektben a projekt állapotot tükrözi. A projekt szerinti pillanatkép elkötelezett automatikusan a VSTS Git-tárházban futtatási előzményei ág minden alkalommal, amikor a kísérletek elküldését. A pillanatkép, a második számítógépre a projekt példányának létrehozásakor a legutóbbi futtatás társított használjuk. 
 

## <a name="collaboration"></a>Együttműködés
A projektek a VSTS Git-tárház kapcsolódó teammates együttműködhet. Engedélyeket rendelhet a felhasználókhoz a kísérleti fiók, a munkaterületet, és a projektet. Ekkor az Azure Resource Manager-parancsokat az Azure parancssori felület használatával végezheti el. Is [Azure-portálon](https://portal.azure.com). Lásd: [a következő szakasz](#portal).    

### <a name="using-command-line-to-add-users"></a>Felhasználók hozzáadása a parancssorból
Lehetővé teszi, hogy egy példát. Tegyük fel például, Ágnes th e_Iris_ projekt tulajdonosa, és szeretné Bob hozzáférési megosztása. 

Alice a hivatkozásra kattint a **fájl** menüt, és választják ki a **parancssor** menü a lehetőséggel elindíthatja a parancssorba, úgy konfigurálva, hogy a _iris_ projekt. Alice az Ezután eldöntheti, hogy milyen szintű fő hozzáféréssel, és szeretné adjon Bob a következő parancsok végrehajtásával.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

A szerepkör-hozzárendelés után közvetlenül vagy öröklés, Bob tekintheti meg a projektet a munkaterületet üzemeltető projekt listában. Az alkalmazás újraindítás módosítania kell a projekt láthatók. Bob ezután letöltheti a projekt leírtak szerint a [szakasz központi](#roaming) és Alice együttműködni. 

Az azonos távoli Git-tárház elkötelezett az összes felhasználó számára, a projekt dolgoznak futtatási előzményeit. Ezért futtató végrehajtásakor a Alice Bob tekintheti meg a Futtatás a munkaterület-alkalmazás projekt futtatási előzményei szakaszában. Bob is helyreállíthatja a projekt bármely Alice indította futtatása beleértve futtatás állapotát. 

A projekt egy távoli Git-tárház megosztása lehetővé teszi, hogy Alice és Bob is közösen a főágba történő. Ha szükséges, hogy is személyes fiókirodákat és Git lekéréses-kérelmek és összevonása együttműködést. 

### <a name="using-azure-portal-to-add-users"></a>Felhasználók hozzáadása az Azure-portál használatával
<a name="portal"></a>

Az Azure Machine Learning kísérletezhet fiókok, munkaterületekkel és projektek az Azure Resource Manager erőforrásokat. A hozzáférés-vezérlés hivatkozásra is használhatja a [Azure-portálon](https://portal.azure.com) rendelhet szerepköröket. 

Adja hozzá a felhasználók számára az összes erőforrásból szeretne erőforrás található. Kattintson a hozzáférés-vezérlés a Page (IAM) hivatkozásra. Felhasználók hozzáadása 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

