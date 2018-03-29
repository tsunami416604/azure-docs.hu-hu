---
title: A Git - Azure Machine Learning együttműködési kódolási |} Microsoft Docs
description: Együttműködési kód fejlesztési adatok tudományos projektek Git használatával gyors tervezéssel módjáról.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev
ms.openlocfilehash: f3eabf0b754f777f25811d30c158b647b1d3954e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="collaborative-coding-with-git"></a>Együttműködésen alapuló kódolás a Gittel

Ebben a cikkben azt ismerteti, hogyan együttműködési kód fejlesztési adatok tudományos projektek Git használatával, a megosztott kód fejlesztési keretrendszer elvégzéséhez. Bemutatja, hogyan adhat hivatkozás ezeket kódolás tevékenységekhez, hogy a tervezett munka [gyors fejlesztési](agile-development.md) és kód értékelést.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>A Git ág munkaelem hivatkozás 

VSTS (szövegegység vagy feladat) munkaelem csatlakozni kényelmes megoldást kínál a Git ág. Ez lehetővé teszi, hogy a szövegegység vagy feladat közvetlen hivatkozás a vele társított kód. 

Munkaelem egy új fiókirodai való kapcsolódáshoz kattintson duplán egy munkaelemet, és az előugró ablakban kattintson **hozzon létre egy új fiókirodai** alatt **+ Hozzáadás hivatkozásra**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Adja meg az új fiókirodai, például a fióknév alap Git-tárház vagy a fiókiroda az adatokat. A kiválasztott Git-tárház a tárház, amely a munkaelem tartozik azonos csapatprojekt alatt kell lennie. Az alap ág a főágba vagy valamilyen más meglévő ág lehet.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Bevált gyakorlat, ha minden szövegegység munkatétel Git ág. Ezután minden feladat munkatétel létrehoz egy ágat a szövegegység fiókirodai alapján. A hierarchikus módon, amely megfelel a szövegegység-kapcsolatokat a fiókirodák telephelyeire rendszerezéséhez akkor hasznos, ha több személy dolgozik a különböző szövegek az ugyanabban a projektben van, vagy több személy dolgozik a különböző feladatok a azonos szövegegység rendelkezik. Ütközések minimalizálható, ha minden csoport egy tagja egy másik ágat, és minden tag akkor működik a különböző kód vagy más összetevők, amikor egy ágat megosztása működik. 

Az alábbi képen a javasolt elágaztatási stratégiát a TDSP mutatja be. Előfordulhat, nem kell különböző elágazik itt látható, különösen ha csak rendelkezik egy vagy két dolgozó ugyanabban a projektben, vagy csak egy személy működik-e az összes feladat egy szövegegység. Azonban a fejlesztési fiókirodai mappától a főágba mindig célszerű. Ez segítséget nyújt a kiadási fiókirodai az éppen a fejlesztési tevékenységek miatt megszakadt. Git fiókirodai modell részletesebb leírását található [egy sikeres Git ugorhat modellt](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Váltson át a fiókiroda, amelyet szeretne dolgozni, a következő parancsot a rendszerhéj parancsban (Windows vagy Linux). 

    git checkout <branch name>

Módosítása a *< fióknév\>*  való **fő** biztonsági kapcsolók a **fő** ág. Miután a munkaágat váltani, indítsa el függ, hogy működjön, a cikk befejezéséhez szükséges kód vagy a dokumentációjával az összetevők fejleszt. 

Egy meglévő elágazási is hozzárendelhet egy munkaelemet. Az a **részletes** munkaelem, helyett kattintva oldalán **hozzon létre egy új fiókirodai**, kattint **+ Hozzáadás hivatkozásra**. Válassza ki a fiókiroda szeretne kapcsolni a munkaelemet. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Is létrehozhat egy új fiókirodai a Git bash eszközben parancsokat. Ha < alap fióknevének\> hiányzik, a < új fiókirodai neve\> alapuló _fő_ ág. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Működjenek az egy ágat, és véglegesítse a módosításokat 

Most tegyük fel, hogy néhány módosítást a *adatok\_adatfeldolgozást* ág a munkaelem, például a fiókiroda egy R fájl hozzáadása a helyi gépen. Az R fájl, a fiókiroda a munkaelem hozzá véglegesítheti, feltéve, hogy a fiókirodában lévő a Git rendszerhéj az alábbi Git-parancsok használatával:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Hozzon létre egy lekérési kérelmet a VSTS 

Ha készen áll után néhány véglegesíti és leküldéses értesítések, az aktuális ág egyesíteni az alap fiókirodai elküldheti a **lekérési kérelmet** VSTS-kiszolgálón. 

Nyissa meg a csapatprojekt fő lapján, és kattintson a **kód**. Válassza ki a fiókiroda való egyesíthetők és az ág az egyesíteni kívánt Git-tárház nevét. Kattintson a **lekérési kérelmek**, kattintson a **új lekérési kérelmet** előtt létre szeretne hozni egy lekérési kérelmet tekintse át a munkát az ág az alap elágazási egyesített.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Töltse ki a lekérési kérelmet néhány leírása, vegye fel a felülvizsgálók és továbbíthatja.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Tekintse át és egyesítése 

A lekérési kérelem létrehozásakor, akkor a felülvizsgálók áttekintheti a lekérési kérelmek e-mail értesítést kap. A felülvizsgálók ellenőrizze, hogy a módosítások dolgozik-e, és ha lehetséges tesztelése a módosításokat a kérelmező kell. Az értékelés alapján, a többi felhasználó jóváhagyhatja vagy elutasíthatja a lekérési kérelmet. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Miután elkészült a tekintse át, a munkaágat egyesített az alap elágazási kattintva a **Complete** gombra. Választhatja a munkaágat törlése után összevont rendelkezik. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Erősítse meg a bal felső sarkában, amely van megjelölve, a kérelem **BEFEJEZVE**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Ha később visszatérhet a tárházba **kód**, megtudja, hogy a főágba lett átadva.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

A következő Git-parancsok segítségével a munkaágat az alap elágazási egyesíteni, és törölje a munkaágat egyesítése után:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>További lépések

[Adatok tudományos feladatok végrehajtása](execute-data-science-tasks.md) több közös adatok tudományos feladatokhoz, mint az adatok interaktív áttekintését, a adatelemzés, a jelentéskészítés és a modell létrehozása befejezéséhez segédprogramok használatát ismerteti.

Forgatókönyvek, amelyek azt mutatják, a folyamat lépései **meghatározott forgatókönyvek** is rendelkezésre állnak. Szerepel a listában, és kapcsolódik a miniatűr leírásokat a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan lehet felhő, a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása. 

