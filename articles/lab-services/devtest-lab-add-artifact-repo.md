---
title: Git-adattár hozzáadása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Útmutató az egyéni összetevők forrás-GitHub vagy a Visual Studio Team Services Git adattár hozzáadása az Azure DevTest Labs szolgáltatásban.
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
ms.openlocfilehash: 80724a7d8d2b5cec19bdbce27cdafd4a9c09eb47
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452498"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Egyéni összetevők és a Resource Manager-sablonok tárolása a Git-adattár hozzáadása

Is [egyéni összetevők létrehozása](devtest-lab-artifact-author.md) tesztkörnyezetben, virtuális gépek vagy [Azure Resource Manager-sablonok használatával hozzon létre egy egyéni tesztkörnyezetben](devtest-lab-create-environment-from-arm.md). Privát Git-tárházat az összetevők vagy a Resource Manager-sablonok, amely a csapat létrehozza hozzá kell adnia. A tárház üzemeltethető [GitHub](https://github.com) vagy a [Visual Studio Team Services](https://visualstudio.com).

Biztosítunk egy [GitHub-adattár az összetevők](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) üzembe helyezhető-van, vagy testre szabhatja a laborral kapcsolatos. Ha testre szabhatja, vagy hozzon létre egy, a nyilvános tárházban összetevő nem tárolhatja. Létre kell hoznia a saját privát tárházban az egyéni összetevők és az Ön által létrehozott összetevők. 

Amikor létrehoz egy virtuális Gépet, a Resource Manager-sablon mentése, testre szabni, ha szeretné, és, amellyel később további virtuális gépeket hozhat létre. Létre kell hoznia a saját privát tárházban az egyéni Resource Manager-sablonok.  

* Ismerje meg, hogyan hozhat létre egy GitHub-adattárból, lásd: [GitHub alapképzés](https://help.github.com/categories/bootcamp/).
* Ismerje meg, hogyan hozzon létre egy Team Services-projektet egy Git-tárház rendelkező, lásd: [kapcsolódás a Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Az alábbi ábra egy példát, hogyan nézhet ki egy adattár, amely rendelkezik az összetevők a GitHub:  

![Minta GitHub-összetevők adattárat](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Az adattár információit és a hitelesítő adatok beszerzése
A labor ad hozzá egy adattár, először kérje le kulcsfontosságú adatokat az adattárból. A következő szakaszok ismertetik, hogyan kérhet le a GitHub vagy a Visual Studio Team Services az üzemeltetett tárházak szükséges információkat.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>A GitHub-tárház clone URL-cím és a személyes hozzáférési jogkivonat beszerzése

1. Nyissa meg a kezdőlapon GitHub-adattár, amely az összetevő vagy a Resource Manager-sablon definíciókat tartalmazza.
2. Válassza ki **Klónozás vagy letöltés**.
3. Az URL-címet a vágólapra másolásához jelölje ki a **HTTPS clone URL-cím** gombra. Mentse az URL-cím későbbi használatra.
4. GitHub jobb felső sarkában, a profilkép, majd válassza ki és **beállítások**.
5. Az a **személyes beállítások** a bal oldalon válassza a menü **személyes hozzáférési jogkivonatok**.
6. Válassza ki **új jogkivonat létrehozása**.
7. Az a **új személyes hozzáférési tokent** lap **jogkivonat leírása**, adjon meg egy leírást. Fogadja el az alapértelmezett elemek **hatókörök kiválasztása**, majd válassza ki **azonosító létrehozása**.
8. A létrehozott jogkivonatának mentéset. A jogkivonat későbbi használatra.
9. Zárja be a Githubon.   
10. Továbbra is a [a labor csatlakozni a tárház](#connect-your-lab-to-the-repository) szakaszban.

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>A Visual Studio Team Services tárház clone URL-cím és a személyes hozzáférési jogkivonat beszerzése

1. Lépjen a kezdőlapra, a csapat gyűjtemény (például https://contoso-web-team.visualstudio.com), majd válassza ki a projekthez.
2. Válassza ki a projekt kezdőlapját, **kód**.
3. A klón URL-cím megtekintéséhez a projekt **kód** lapon válassza ki **klónozott**.
4. Mentse az URL-címet. Az URL-cím később használni.
5. A személyes hozzáférési tokent létrehozásához a felhasználói fiók legördülő menüben válassza **saját profil**.
6. A profil-információ lapon válassza ki **biztonsági**.
7. Az a **biztonsági** lapon jelölje be **Hozzáadás**.
8. Az a **személyes hozzáférési jogkivonat létrehozása** oldalon:
   1. Adjon meg egy **leírás** a jogkivonat.
   2. Az a **lejár a** listáról válassza ki **180 nap**.
   3. Az a **fiókok** listáról válassza ki **az összes elérhető fiókok**.
   4. Válassza ki a **minden hatókör** lehetőséget.
   5. Válassza ki **Token létrehozása**.
9. Megjelenik az új jogkivonatot a **személyes hozzáférési jogkivonatok** listája. Válassza ki **másolási Token**, majd mentse a token értéket későbbi használatra.
10. Továbbra is a [a labor csatlakozni a tárház](#connect-your-lab-to-the-repository) szakaszban.

## <a name="connect-your-lab-to-the-repository"></a>A labor csatlakozni a tárház
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **további szolgáltatások**, majd válassza ki **DevTest Labs** a szolgáltatások listájából.
3. Tesztkörnyezetek listájában jelölje ki a labor. 
4. Válassza ki **Konfigurace a zásady** > **Tárházak** > **+ Hozzáadás**.

    ![Adattár hozzáadása gombbal](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. A második **Tárházak** csoportjában adja meg a következő információkat:
  1. **Név**. Adja meg a tárház nevét.
  2. **Git Clone URL-cím**. Adja meg a Git HTTPS clone URL-cím, amely korábban vágólapra másolt a GitHub vagy a Visual Studio Team Services.
  3. **Ág**. A definíciók lekéréséhez adja meg az ágat.
  4. **Személyes hozzáférési tokent**. Adja meg a személyes hozzáférési tokent korábban a GitHub vagy a Visual Studio Team Services.
  5. **Mappa elérési útjának**. Adjon meg legalább egy mappa elérési útja a clone URL-cím, amely tartalmazza az összetevő vagy a Resource Manager-sablon definíciók viszonyítva. Alkönyvtár megadásakor ellenőrizze, hogy a perjel szerepel a mappa elérési útját.

     ![Tárházak terület](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Kattintson a **Mentés** gombra.

### <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [Ennek hiányában a DevTest Labs szolgáltatásban létrehozott összetevők hibáinak elhárítása](devtest-lab-troubleshoot-artifact-failure.md)
* [Meglévő Active Directory-tartomány egy Resource Manager-sablon használatával a DevTest Labs szolgáltatásban létrehozott virtuális gépek csatlakoztatása](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a privát Git-tárházzal, egyikét vagy mindkettőt a következőket teheti igényeitől függően:
* Store a [egyéni összetevők](devtest-lab-artifact-author.md). Használhatja őket később hozhat létre új virtuális gépeket.
* [Több virtuális gépes környezet és PaaS-erőforrásokhoz létrehozása Resource Manager-sablonok használatával](devtest-lab-create-environment-from-arm.md). Ezt követően a sablonok tárolhatók a privát tárházban.

Ha egy virtuális Gépet hoz létre, ellenőrizheti, hogy az összetevők és sablonok hozzáadódnak a Git-tárház. Azonnal elérhetők, összetevők vagy a sablonok listájában. A privát tárházban neve látható az oszlopot, amely meghatározza a forrást. 
