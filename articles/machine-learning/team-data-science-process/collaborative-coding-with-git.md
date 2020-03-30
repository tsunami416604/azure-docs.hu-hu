---
title: Együttműködésen alapuló kódolás a Git -Team Data Science Process segítségével
description: Együttműködésen alapuló kódfejlesztés adatelemzési projektekhez aGit agilis tervezéssel.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721897"
---
# <a name="collaborative-coding-with-git"></a>Együttműködésen alapuló kódolás a Gittel

Ez a cikk ismerteti, hogyan használhatja a Git, mint az együttműködésen alapuló kód fejlesztési keretrendszer adatelemzési projektek. A cikk ismerteti, hogyan kapcsolhat össze kódot az Azure Repos-ban az Azure Boards ban afejlesztési munkaelemek [agilis,](agile-development.md) kód-felülvizsgálatok, és hogyan hozhat létre és egyesítheti a változáskérések lekéréses kérelmeket.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Munkaelem csatolása Azure-repos-fiókhoz 

Az Azure DevOps kényelmes módot biztosít az Azure Boards felhasználói története vagy a feladat munkaelem csatlakoztatására egy Azure Repos Git tárház-fiókkal. Felhasználói szövegegységét vagy feladatát közvetlenül a hozzá társított kódhoz kapcsolhatja. 

Ha munkaelemet szeretne új ághoz kapcsolni, válassza a munkaelem melletti **Műveletek** három ponttal (**...**) lehetőséget, majd a helyi menüben görgessen az Új ág elemre, és válassza az **Új ág**lehetőséget.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Az **ág létrehozása** párbeszédpanelen adja meg az új ág nevét és az alap Azure Repos Git-tárház és ág. Az alaptártárnak ugyanabban az Azure DevOps-projektben kell lennie, mint a munkaelemnek. Az alapág lehet a főág vagy egy másik meglévő ág. Válassza **az Ág létrehozása**lehetőséget. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Új ágat a következő Git bash paranccsal is létrehozhat Windows vagy Linux rendszerben:

```bash
git checkout -b <new branch name> <base branch name>

```
Ha nem ad \<meg alapágnevet>, az `master`új ág alapja a . 

A munkaágra való váltáshoz futtassa a következő parancsot: 

```bash
git checkout <working branch name>
```

Miután áttért a munkaágra, megkezdheti a kód- vagy dokumentációs összetevők fejlesztését a munkaelem befejezéséhez. A `git checkout master` futás visszakapcsol `master` az ágra.

Célszerű minden felhasználói történet munkaelemhez git-ágat létrehozni. Ezután minden egyes tevékenységmunkaelemhez létrehozhat egy elágazást a Felhasználói történet ág alapján. Az ágakat egy olyan hierarchiában rendszerezheti, amely megfelel a Felhasználói történet-tevékenység kapcsolatnak, ha több ember dolgozik különböző felhasználói történeteken ugyanahhoz a projekthez, vagy ugyanazon felhasználói történet különböző tevékenységein. Minimalizálhatja az ütközéseket, ha minden csapattag egy másik ágon, vagy különböző kódon vagy más műterméken dolgozik egy ág megosztásakor. 

Az alábbi ábra a TDSP ajánlott elágazási stratégiáját mutatja be. Előfordulhat, hogy nem kell annyi fiók, mint az itt látható, különösen akkor, ha csak egy vagy két személy dolgozik egy projekten, vagy csak egy személy dolgozik egy felhasználói történet összes feladatán. A fejlesztési ág nak a főágtól való elválasztása azonban mindig jó gyakorlat, és segíthet megakadályozni, hogy a fejlesztési tevékenységek megzavarják a kiadási ágat. A Git ágmodell teljes leírását a [Sikeres Git elágazási modell című témakörben ismertetjük.](https://nvie.com/posts/a-successful-git-branching-model/)

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Munkaelemet meglévő ághoz is csatolhat. A munkaelem **Részlet lapján** válassza a **Hivatkozás hozzáadása lehetőséget.** Ezután jelöljön ki egy meglévő ágat, amelyhez a munkaelemet csatolni szeretné, majd kattintson az **OK gombra.** 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Munka az ágon és módosítások véglegesítése 

Miután módosította a munkaelemet, például r-parancsfájlt adott a `script` helyi számítógép ágához, a következő Git bash-parancsok használatával véglegesítheti a helyi ágból származó módosítást a felsőbb szintbeli munkaágra:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Lekéréses kérelem létrehozása

Egy vagy több véglegesítés és leküldések után, amikor készen áll az aktuális működő ág alapágba való egyesítésére, létrehozhat és küldhet *lekéréses kérelmet* az Azure Repos-ban. 

Az Azure DevOps-projekt főlapján mutasson **a repos lekéréses** > **kérelmek** pontra a bal oldali navigációs sávban. Ezután válassza az **Új lekéréses kérelem** gomb vagy a **Lekéréses kérelem létrehozása hivatkozást.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Az **Új lekéréses kérelem** képernyőn szükség esetén keresse meg azt a Git-tárházat és ágat, amelybe egyesíteni szeretné a módosításokat. Adjon hozzá vagy módosítson bármilyen más információt. A **Véleményezők**csoportban adja hozzá az ellenőrzők nevét, majd válassza a **Létrehozás lehetőséget.** 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Véleményezés és egyesítés

Miután létrehozta a lekéréses kérelmet, a véleményezők e-mail értesítést kapnak a lekéréses kérelem áttekintéséhez. A véleményezők tesztelik, hogy a módosítások működnek-e, és ha lehetséges, ellenőrizze a módosításokat a kérelmezővel. Az ellenőrzők megjegyzéseket tehetnek, módosításokat kérhetnek, és jóváhagyhatják vagy elutasíthatják a lekéréses kérelmet az értékelésük alapján. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

Miután az ellenőrzők jóváhagyják a módosításokat, Ön vagy valaki más egyesítési engedéllyel egyesítheti a működő ágat az alapágával. Válassza a **Teljes**lehetőséget, majd a Teljes lekéréses kérelem párbeszédpanelen válassza a **Teljes lekéréses kérelem párbeszédpanelen** a Teljes **egyesítés** befejezése lehetőséget. Az egyesítés után törölheti a munkaágat. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Ellenőrizze, hogy a kérelem BEFEJEZETT jelöléssel **van-e ellátva.** 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Amikor visszatér a **repos** a bal oldali navigációs, láthatja, hogy már átváltott `script` a fő ág, mivel az ág törölték.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

A következő Git bash-parancsok segítségével `script` egyesítheti a működő ágat az alapágával, és az egyesítés után törölheti a munkaágat:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>További lépések

[Adatelemzési feladatok végrehajtása](execute-data-science-tasks.md) azt mutatja be, hogyan használhatja a segédprogramok számos gyakori adatelemzési feladatot, például interaktív adatfeltárást, adatelemzést, jelentést és modelllétrehozást.

[Példa forgatókönyvek](walkthroughs.md) listák forgatókönyvek, a hivatkozások és a miniatűr leírások. A kapcsolódó forgatókönyvek bemutatják, hogyan kombinálhatja a felhőbeli és helyszíni eszközöket és szolgáltatásokat munkafolyamatokban vagy folyamatokban intelligens alkalmazások létrehozásához. 

