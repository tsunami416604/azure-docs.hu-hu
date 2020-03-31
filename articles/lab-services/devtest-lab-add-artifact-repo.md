---
title: Git-tárház hozzáadása az Azure DevTest Labs laborjában | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá egy GitHub- vagy Azure DevOps-szolgáltatások Git-tárházat az azure DevTest Labs egyéni összetevők forrásához.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1e7587c60e180fb35e1a2bed735b053b6b0c388a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294605"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Git-tárház hozzáadása egyéni összetevők és Erőforrás-kezelő sablonok tárolásához

Egyéni [összetevőket hozhat létre](devtest-lab-artifact-author.md) a tesztkörnyezetben lévő virtuális gépekhez, vagy [az Azure Resource Manager-sablonok használatával egyéni tesztkörnyezetet hozhat létre.](devtest-lab-create-environment-from-arm.md) Hozzá kell adnia egy privát Git-tárházat a csoport által létrehozott összetevőkhöz vagy erőforrás-kezelő sablonokhoz. A tárház üzemeltethető a [GitHubon](https://github.com) vagy az [Azure DevOps-szolgáltatásokon.](https://visualstudio.com)

A [GitHub-tárházat kínáljuk,](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) amelyet a hogy üzembe helyezhet, vagy testre szabhatja őket a laborok számára. Összetevő testreszabásakor vagy létrehozásakor a műtermék nem tárolható a nyilvános tárházban. Létre kell hoznia saját magántárt az egyéni összetevőkhöz és a létrehozott összetevőkhöz. 

Virtuális gép létrehozásakor mentheti az Erőforrás-kezelő sablont, testreszabhatja, ha szeretné, majd később további virtuális gépek létrehozásához. Az egyéni Erőforrás-kezelő sablonok tárolásához saját privát tárházat kell létrehoznia.  

* A GitHub-tárház létrehozásáról a [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)című témakörben olvashat.
* Ha meg szeretné tudni, hogyan hozhat létre Git-tárházat tartalmazó Azure DevOps [Services-projektet: Csatlakozás az Azure DevOps-szolgáltatásokhoz című témakörben.](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)

Az alábbi ábra egy példa arra, hogyan egy tárház, amely összetevőket is meg a GitHub:  

![GitHub-összetevők tártárának minta](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>A tárház adatainak és hitelesítő adatainak beszereznie
Ha hozzá szeretne adni egy adattárat a laborhoz, először is fontos információkat kell leszereznie a tárházból. Az alábbi szakaszok ismertetik, hogyan szerezheti be a GitHubon vagy az Azure DevOps-szolgáltatásokban üzemeltetett adattárak szükséges adatait.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>A GitHub-tárház klónozási URL-címének és személyes hozzáférési jogkivonatának beszereznie

1. Nyissa meg a GitHub-tárház kezdőlapját, amely a műtermék- vagy erőforrás-kezelősablon-definíciókat tartalmazza.
2. Válassza a **Clone or download** (Klónozás vagy letöltés) lehetőséget.
3. Ha az URL-címet a vágólapra szeretné másolni, jelölje ki a **HTTPS klón url gombját.** Mentse az URL-címet későbbi használatra.
4. A GitHub jobb felső sarkában jelölje ki a profilképet, majd válassza a **Beállítások**lehetőséget.
5. A bal oldali **Személyes beállítások** menüben válassza a Személyes **hozzáférési jogkivonatok**lehetőséget .
6. Válassza **az Új jogkivonat létrehozása**lehetőséget.
7. Az **Új személyes hozzáférési jogkivonat** lap **Token leírása**csoportban adjon meg egy leírást. Fogadja el az alapértelmezett elemeket a **Hatókörkiválasztása**csoportban, majd válassza **a Jogkivonat létrehozása**lehetőséget.
8. Mentse a generált jogkivonatot. A token később használható.
9. Zárja be a GitHubot.   
10. Folytassa a [Labor csatlakoztatása a tárház](#connect-your-lab-to-the-repository) szakaszhoz.

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Az Azure Repos klónozott URL-címének és a személyes hozzáférési jogkivonatának beszereznie

1. Nyissa meg a csoportgyűjtemény kezdőlapját (például `https://contoso-web-team.visualstudio.com`), és válassza ki a projektet.
2. A projekt kezdőlapján válassza a **Kód**lehetőséget.
3. A klón URL-címének megtekintéséhez a projekt **kódlapján** válassza a **Klónozás**lehetőséget.
4. Mentse az URL-címet. Az URL-címet később használhatja.
5. Személyes hozzáférési jogkivonat létrehozásához a felhasználói fiók legördülő menüjében válassza a **Saját profil lehetőséget.**
6. A profilinformációs lapon válassza a **Biztonság**lehetőséget.
7. A **Biztonság** lapon válassza a **Hozzáadás gombot.**
8. A **Személyes hozzáférési jogkivonat létrehozása** lapon:
   1. Adja meg a token **leírását.**
   2. A **Expires In** listában válassza a **180 napot**.
   3. A **Fiókok** listában válassza a **Minden akadálymentes fiók lehetőséget.**
   4. Válassza az **Írásvédett** beállítást.
   5. Válassza **a Jogkivonat létrehozása**lehetőséget.
9. Az új jogkivonat megjelenik a **személyes hozzáférési jogkivonatok** listájában. Válassza **a Jogkivonat másolása**lehetőséget, majd mentse a token értékét későbbi használatra.
10. Folytassa a [Labor csatlakoztatása a tárház](#connect-your-lab-to-the-repository) szakaszhoz.

## <a name="connect-your-lab-to-the-repository"></a>A labor csatlakoztatása a tárházhoz
1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
2. Válassza a **További szolgáltatások**lehetőséget, majd a Szolgáltatások listájából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a labort. 
4. Válassza a **Konfigurációs és házirendek** > **adattárak** > +**Hozzáadás lehetőséget.**

    ![A Tárház hozzáadása gomb](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. A második **Adattárak** lapon adja meg a következő információkat:
   1. **Név**. Adja meg a tárház nevét.
   2. **Git klón URL**. Adja meg a Git HTTPS klón URL-jét, amelyet korábban másolt a GitHubról vagy az Azure DevOps-szolgáltatásokból.
   3. **Ág**. A definíciók beírásához adja meg az ágat.
   4. **Személyes hozzáférési jogkivonat**. Adja meg a személyes hozzáférési jogkivonatot, amelyet korábban kapott a GitHubról vagy az Azure DevOps-szolgáltatásokból.
   5. **Mappaelérési utak**. Adjon meg legalább egy mappaelérési utat a műtermék- vagy Erőforrás-kezelő sablondefinícióit tartalmazó klónozó URL-címhez képest. Ha alkönyvtárat ad meg, győződjön meg arról, hogy a perjelet a mappa elérési útjába helyezi.

      ![Adattárak területe](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Kattintson a **Mentés** gombra.

### <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Hibás összetevők hibáinak elhárítása a DevTest Labs ben](devtest-lab-troubleshoot-artifact-failure.md)
* [Virtuális gép csatlakoztatása meglévő Active Directory-tartományhoz a DevTest Labs Erőforrás-kezelő sablonjának használatával](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a saját Git-tárházat, az igényeitől függően az alábbi eket teheti meg:
* Tárolja az [egyéni összetevőket.](devtest-lab-artifact-author.md) Használhatja őket később új virtuális gépek létrehozásához.
* [Többvirtuális gépes környezeteket és PaaS-erőforrásokat hozhat létre az Erőforrás-kezelő sablonjainak használatával.](devtest-lab-create-environment-from-arm.md) Ezután tárolhatja a sablonokat a privát tárházban.

Virtuális gép létrehozásakor ellenőrizheti, hogy az összetevők vagy sablonok hozzá adódnak-e a Git-tárházhoz. Ezek azonnal elérhetők az összetevők vagy sablonok listájában. A magántár neve a forrást tartalmazó oszlopban jelenik meg. 
