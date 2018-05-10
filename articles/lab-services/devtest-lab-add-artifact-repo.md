---
title: A Git-tárház hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban |} Microsoft Docs
description: 'Útmutató: Azure DevTest Labs adja hozzá az egyéni összetevők forrás egy Githubból vagy a Visual Studio Team Services Git-tárházat.'
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
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Vegyen fel egy Git-tárházat tárolásához egyéni összetevők és a Resource Manager-sablonok

Is [egyéni összetevők létrehozása](devtest-lab-artifact-author.md) a tesztkörnyezetben lévő virtuális géphez vagy [Azure Resource Manager-sablonok segítségével hozzon létre egy egyéni tesztkörnyezetben](devtest-lab-create-environment-from-arm.md). Hozzá kell adnia egy privát Git-tárház beállítása az összetevők vagy a csoport létrehozása Resource Manager-sablonok. A tárház az alábbiakon tárolható [GitHub](https://github.com) vagy a [Visual Studio Team Services](https://visualstudio.com).

Kínálunk egy [GitHub-tárházban összetevők](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) , hogy telepítheti-van, vagy testre szabhatja őket a tesztkörnyezetekhez. Testre szabhatja, vagy hozzon létre egy összetevő, a nyilvános tárház összetevő nem tárolja. Létre kell hoznia a saját személyes tárház egyéni összetevők és az Ön által létrehozott összetevők. 

A virtuális gépek létrehozásakor a Resource Manager sablon mentése, testre szabhatja, ha szeretné, és, amellyel később további virtuális gépek létrehozása. Létre kell hoznia a saját személyes tárház az egyéni Resource Manager-sablonok.  

* A GitHub-tárház létrehozásához, lásd: [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Team Services projekt, amely rendelkezik egy Git-tárház létrehozásához, lásd: [kapcsolódás a Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Az alábbi ábrán látható egy példa hogyan nézhet ki egy tárház, amely rendelkezik az összetevők a Githubon:  

![Példa GitHub-tárház összetevők](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>A tárház információkért és a hitelesítő adatok
A tárház hozzáadása a labor, először információk kulcs a tárházból. Az alábbi szakaszok azt ismertetik, hogyan lekérni a Githubból vagy a Visual Studio Team Services tárolt adattárak szükséges adatokat.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>A GitHub tárház klón URL-cím és a személyes hozzáférési jogkivonat beszerzése

1. Ugrás a kezdőlapra, a GitHub tárház, amely tartalmazza az összetevő vagy a Resource Manager sablon definíciókat.
2. Válassza ki **Klónozás vagy letöltési**.
3. Az URL-címet a vágólapra másolásához jelölje ki a **HTTPS-klón URL-címét** gombra. Mentse az URL-cím későbbi használatra.
4. GitHub jobb felső sarkában, az olyan profil képe, majd válassza ki és **beállítások**.
5. Az a **személyes beállítások** a bal oldalon válassza a menü **személyes hozzáférési jogkivonatok**.
6. Válassza ki **új Generate token**.
7. Az a **új személyes hozzáférési jogkivonat** lap **leírás Token**, adjon meg egy leírást. Fogadja el az alapértelmezett elemek **hatókörök kiválasztása**, majd válassza ki **azonosító létrehozása**.
8. Mentse a generált jogkivonat. A token később használhatja.
9. Zárja be a Githubon.   
10. Továbbra is a [a labor csatlakozni a tárház](#connect-your-lab-to-the-repository) szakasz.

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>A Visual Studio Team Services tárház klón URL-cím és a személyes hozzáférési jogkivonat beszerzése

1. Ugrás a kezdőlapra, a csapat gyűjtemény (például https://contoso-web-team.visualstudio.com), majd válassza ki a projekthez.
2. A projekt kezdőlapján válassza **kód**.
3. A klón URL-cím meg a projekt **kód** lapon jelölje be **Klónozás**.
4. Az URL-cím mentése. Később az URL-címet használja.
5. A felhasználói fiók legördülő menü személyes hozzáférési jogkivonat létrehozásához válassza **saját profil**.
6. A profil információ lapon válassza az **biztonsági**.
7. Az a **biztonsági** lapon jelölje be **Hozzáadás**.
8. Az a **személyes hozzáférési jogkivonat létrehozása** lap:
   1. Adjon meg egy **leírás** a jogkivonat esetében.
   2. Az a **lejár a** listáról válassza ki **180 nap**.
   3. Az a **fiókok** listáról válassza ki **elérhető fiókokhoz**.
   4. Válassza ki a **minden hatókör** lehetőséget.
   5. Válassza ki **jogkivonat létrehozása**.
9. Megjelenik az új jogkivonatot a **személyes hozzáférési jogkivonatok** listája. Válassza ki **másolási Token**, majd mentse a token értéknek a későbbi használatra.
10. Továbbra is a [a labor csatlakozni a tárház](#connect-your-lab-to-the-repository) szakasz.

## <a name="connect-your-lab-to-the-repository"></a>A tesztkörnyezet csatlakozni a tárházban
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **több szolgáltatások**, majd válassza ki **DevTest Labs** szolgáltatások közül.
3. Válassza ki a labor labs listájának megtekintéséhez. 
4. Válassza ki **konfigurációs és házirendek** > **Tárházak** > **+ Hozzáadás**.

    ![A tárház hozzáadása gomb](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. A második **Tárházak** lapján adja meg a következőket:
  1. **Név**. Adja meg a tárház nevét.
  2. **Git-klón URL-cím**. Adja meg a Git HTTPS klón URL-cím már korábban átmásolt a Githubból vagy a Visual Studio Team Services.
  3. **Fiókirodai**. Ahhoz, hogy a definíciók, adja meg a fiókirodákban.
  4. **Személyes hozzáférési jogkivonat**. Adja meg a személyes hozzáférési jogkivonat, amely korábban a Githubból vagy a Visual Studio Team Services kapott.
  5. **Mappák elérési útjaiban**. Adjon meg legalább egy mappa elérési útja, a klón URL-cím, amely tartalmazza az összetevő vagy a Resource Manager sablon definíciók viszonyítva. Egy alkönyvtárban megadása esetén ellenőrizze, hogy a perjel szerepeljenek a mappa elérési útja.

     ![Adattárak terület](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Kattintson a **Mentés** gombra.

### <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések
* [DevTest Labs szolgáltatásban lévő hiányában hibaelhárítása](devtest-lab-troubleshoot-artifact-failure.md)
* [Meglévő Active Directory-tartományban a DevTest Labs szolgáltatásban a Resource Manager-sablon használatával egy virtuális gép csatlakoztatása](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Privát Git-tárház létrehozása után a a következők valamelyikét teheti igényeitől függően:
* Tároló a [egyéni összetevők](devtest-lab-artifact-author.md). Használhatja őket később új virtuális gépek létrehozásához.
* [Több virtuális Gépre kiterjedő környezetek és PaaS erőforrások létrehozása Resource Manager-sablonok segítségével](devtest-lab-create-environment-from-arm.md). A sablonok tárolhatja, majd a saját tárházban.

Amikor egy virtuális Gépet hoz létre, ellenőrizheti, hogy az összetevők vagy a sablonok hozzáadódnak a Git-tárház. Azonnal elérhetők összetevők vagy a sablonok listáján. A privát tárház nevét, amely meghatározza a forrás oszlop látható. 
