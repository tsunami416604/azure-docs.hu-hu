---
title: Együttműködésen alapuló kódolás a Git - csoportos adatelemzési folyamat
description: Együttműködési kód fejlesztés a Git használata az Agilis tervezéshez adatelemzési projektek módjáról.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 551d0cd149c4d1555a40ccf0d7baeff97c6809c2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863297"
---
# <a name="collaborative-coding-with-git"></a>Együttműködésen alapuló kódolás a Gittel

Ebben a cikkben azt ismertetjük, hogyan adatelemzési projektek készítése a Git használatával, a megosztott kód fejlesztési keretrendszer által biztosított együttműködési környezettel kód fejlesztése tennie. Bemutatja, hogyan adhat összekapcsolhatók a a tervezett munka tevékenységek kódolási [Agilis fejlesztési](agile-development.md) és értékelések code.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Hivatkozás egy munkaelemet a Git-ágak 

Az Azure DevOps-szolgáltatásokkal való kapcsolódáshoz (a történetet vagy a feladat) munkaelem kényelmes módot biztosít Git-ágak. Ez lehetővé teszi, hogy a történetet, vagy a feladat mutató közvetlen hivatkozás szerepel a hozzá társított kód. 

A munkaelem egy új ágba, kattintson duplán egy munkaelemet, és az előugró ablakban kattintson a **hozzon létre egy új ágat** alatt **+ Hozzáadás hivatkozás**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Adja meg az adatokat, az új ághoz, például a kiadásiág-nevet, kiindulási Git-tárházat és az ágat. A kiválasztott Git-tárház ugyanazon a projekten, amelyhez a munkaelem tartozik a tárházban kell lennie. Az alap ág a főágban vagy valamilyen más meglévő ág is lehet.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Bevált gyakorlat, hogy egy Git-ágak egyes történetet munkatétel létrehozása. Ezt követően minden feladatot munkatétel hozzon létre egy ágat a történetet ág alapján. Az ágak rendszerezése, amely megfelel a történetet-tevékenység kapcsolatokat hierarchikus így akkor hasznos, ha különböző történetek, ugyanazon a projekten dolgozik, a több felhasználó rendelkezik, vagy ha több felhasználó dolgozik a különböző feladatok azonos. Ütközések minimalizálható, ha minden egyes csapattag működik, ha másik ágat, és minden tagjának működik különböző kódok vagy más összetevők ág megosztásakor. 

A következő képen látható a javasolt elágazási stratégia a TDSP mutatja be. Előfordulhat, hogy nem kell számos ágak itt látható, különösen ha csak eggyel rendelkezik, vagy ugyanazon a projekten dolgozik, a két személynek, vagy csak egy személy dolgozik egy történet az összes feladat. Azonban mindig jó megoldás a fejlesztési ágban elválasztja a főágban. Ez segít megakadályozza, hogy a kiadási ág a fejlesztési tevékenységek által megszakított folyamatban van. Git-ág modell teljes leírása megtalálható [egy sikeres Git elágaztatás modellre](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

A használni kívánt ágat vált, a következő parancsot a rendszerhéj parancsban (Windows vagy Linux). 

    git checkout <branch name>

Módosítása a *< kiadásiág-nevet\>*  való **fő** biztonsági kapcsolók a **fő** ágat. Aktuális ág való váltás után elkezdheti a munkaelem dolgozik, az elem befejezéséhez szükséges kód vagy dokumentációs összetevők fejlesztéséhez. 

Egy meglévő ágban is hozzárendelhet egy munkaelemet. Az a **részletes** munkaelem, helyett kattintva lapján **hozzon létre egy új ágat**, kattint **+ Hozzáadás hivatkozás**. Ezután válassza ki az ágat szeretne kapcsolni munkaelemet. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Is létrehozhat egy új ágat a Git Bash parancsokat. Ha a < alap kiadásiág-nevet\> hiányzik, a < új ág nevét\> alapján _fő_ ágat. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Egy ágban dolgozik, és véglegesítse a módosításokat 

Most tegyük fel, hogy néhány módosítást, a *adatok\_Adatbetöltési* ág a munkaelem, például az R fájlt az ág hozzáadása a helyi gépen. Az R-fájl az ágat a munkaelem hozzá véglegesítheti, a következők a fiókirodában lévő a Git rendszerhéjban az alábbi Git-parancsok használatával:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Pull-kérelem létrehozása az Azure DevOps-szolgáltatásokkal 

Készen áll után néhány véglegesítéseket, és leküldéses értesítést, ha az aktuális ág egyesítése a kiinduló ág, küldhet egy **pull-kérelem** az Azure DevOps-szolgáltatásokkal. 

Nyissa meg a projekt fő lapján, és kattintson a **kód**. Válassza ki az ág az egyesíteni kívánt és az ágat az egyesíteni kívánt Git-adattár neve. Kattintson a **Pull-kérelmek**, kattintson a **új lekéréses kérelem** előtt létre szeretne hozni egy lekéréses kérelmek ellenőrzése a munkát az ág az egyesített az alap ágban.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Adja meg a lekéréses kérelem kapcsolatos leírás, felülvizsgálók hozzáadása, és továbbíthatja.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Tekintse át és egyesítése 

A pull-kérelem létrejöttekor a felülvizsgálók e-mail értesítés a pull-kérelmek áttekintése. A felülvizsgálók kell ellenőrizze, hogy a módosításokat, vagy nem működik, és a módosításokat a kérelmező tesztelése amennyiben lehetséges. Az értékelés alapján, a többi felhasználó jóváhagyhatja vagy elutasíthatja a pull-kérelmet. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Miután elvégezte a felülvizsgálatot, az aktuális ág egyesített az alap ágra kattintva a **Complete** gombra. Törölje az aktuális ág, ha azt egyesített választhatja. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Erősítse meg a bal felső sarokban, amely a kérés van megjelölve a **BEFEJEZVE**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Amikor visszatér a tárházban **kód**, hogy megtudja, hogy a főágba lett átadva.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

A munkaág, a kiinduló ágra egyesítése és törölni az aktuális ág egyesítése után is használhatja a következő Git-parancsokat:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>További lépések

[Az adatelemzési műveletek végrehajtása](execute-data-science-tasks.md) bemutatja, hogyan segédprogramok használatával számos gyakori adatelemzési feladatok például interaktív adatkutatási, adatelemzés, jelentés és modell létrehozásához hajtsa végre.

Forgatókönyvek, amelyek bemutatják, a folyamat összes lépését **meghatározott forgatókönyvek** is rendelkezésre állnak. Felsorolt, és a miniatűr leírásokat tartalmazó társított a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

