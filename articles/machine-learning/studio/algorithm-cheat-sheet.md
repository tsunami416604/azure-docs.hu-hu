---
title: "Gépi tanulási algoritmus-Adatlap |} Microsoft Docs"
description: "Egy nyomtatható gépi tanulási algoritmus-Adatlap segítségével válassza ki a megfelelő a prediktív modell algoritmusa az Azure Machine Learning Studióban."
keywords: "algoritmus-Adatlap, Adatlap, gépi tanulási algoritmus"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: garye
ms.openlocfilehash: 5ecd569f68c2eada921193f2b7b88fe788120a9b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Gyorsreferencia a Machine Learning-algoritmusokhoz a Microsoft Azure Machine Learning Studióban
A **Microsoft Azure Machine Learning algoritmus Cheat lap** segít kiválasztani, a jobb oldali egy prediktív elemzési modell algoritmusa.

[Az Azure Machine Learning Studio](https://studio.azureml.net/) származó algoritmusok hatalmas katalógusában rendelkezik a ***regressziós***, ***besorolás***, ***Fürtszolgáltatás***, és ***anomáliadetektálás*** családok. Minden más típusú machine learning probléma megoldására készült.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Letöltés: Machine learning algoritmus-Adatlap
**Töltse le a Adatlap: [Machine Learning algoritmus Cheat lap (11 x 17.)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Machine Learning algoritmus-Adatlap: Útmutató: a gépi tanulási algoritmus kiválasztása.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Töltse le és lapot tud kinyomtatni a Machine Learning algoritmus Cheat tabloid méretben kéznél legyen, és segítségre van szüksége a választáshoz algoritmust.

> [!NOTE]
> A cikkben [algoritmusok kiválasztása a Microsoft Azure Machine Learning](algorithm-choice.md) egy részletes útmutató a Adatlap használatával.
> 
> 

## <a name="more-help-with-algorithms"></a>További segítséget itt találhat algoritmusok
* Ez Adatlap használva a megfelelő algoritmus, valamint a gépi tanulási algoritmusok és hogyan által használt különböző típusú mélyebb döntéseken kiválasztására vonatkozó útmutatásért lásd: [algoritmusok kiválasztása a Microsoft Azure Machine Learning](algorithm-choice.md).
* Letölthető infographic, amely leírja a algoritmusok, és a példákat mutat be, lásd: [letölthető Infographic: gépi tanulási algoritmus példákkal alapjai](basics-infographic-with-algorithm-examples.md).
* Kategória a Machine Learning Studióban elérhető összes gépi tanulási algoritmusok listájáért lásd: [inicializálása modell] [ initialize-model] a Machine Learning Studio algoritmus és a modul segítségével.
* Teljes betűrendben listáját az algoritmusok és a modulok a Machine Learning Studióban, tekintse meg a [a Machine Learning Studio moduljai A-Z listában] [ a-z-list] a Machine Learning Studio algoritmus és a modul segítségével.
* Ha szeretne egy letölthető és kinyomtatható diagramot a Machine Learning Studio funkcióiról, tekintse meg [Az Azure Machine Learning Studio funkcióit bemutató diagram](studio-overview-diagram.md) című cikket.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Megjegyzések és a gépi tanulási algoritmus terminológiai definíciók cheat lap

* A javaslatok érhető el az algoritmus-Adatlap hozzávetőleges szabályok-az-görgetőgomb. Néhány hajlított, és néhány is lehet flagrantly betartva. Ennek célja felajánlja a kiindulási pontot. Ne ijedjen head-to-head verseny futtatásához több algoritmusok a adatai között. Nincs egyszerűen nem helyettesíti a ismertetése az egyes algoritmustípusok modelljeinek ezeket az alapelveket és a rendszer az adatokat létrehozó ismertetése.

* Minden gépi tanulási algoritmus rendelkezik saját stílus vagy *induktív időeltérés*. Egy adott probléma lehet, hogy több algoritmusok megfelelő, és egy algoritmus lehet, mint a többire jobban megfelelnek. Azonban úgy is nem mindig tudja előre, ez az a legjobb térkihasználás érdekében. Ilyen esetekben több algoritmusok szereplő együtt a Adatlap. Próbálja meg egy algoritmust, és ha az eredmények még nem kielégítő, próbálja ki, a többi lehet a megfelelő stratégiát. A példában a [Azure Eszközintelligencia-katalógus](http://gallery.cortanaintelligence.com/) egy kísérlet, amely megpróbál több algoritmusok elleni ugyanazokat az adatokat, és összehasonlítja az eredményeket: [több osztály osztályozó összehasonlítása: felismerés betűs](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* Gépi tanulás három fő kategóriába sorolhatók: **felügyelt tanítással**, **felügyelt tanítást**, és **megerősítése tanulási**.

  * A **felügyelt tanítással**, minden adatpontnál feliratú vagy kategória vagy egyik fontos értékére.  Példa kategorikus címke van hozzárendelése "cat" vagy "kutya" kép.  Egy érték címkéje példa: egy használt autó kapcsolódó pénztári ár. Felügyelt tanítás során az a célja, hogy sok címkézett fentihez hasonló példához hasonló, ezek tanulmányozzák, majd előrejelzéseket készítsen a jövőbeli adatpontok tennie. Például új fényképek azonosítja a megfelelő állat, vagy más használt autók pontos pénztári árak hozzárendelése. Ez olyan népszerű és hasznos a gépi tanulás. Tanulási algoritmus kivételével az összes, az Azure Machine Learning modulok felügyelete [K-közép Fürtszolgáltatás][k-means-clustering].

  * A **felügyelt tanítást**, adatpontok hozzájuk társított címkéket nem rendelkezik. Ehelyett felügyeletlen tanulási algoritmus célja az adatok valamilyen módon vagy struktúrája ismertetik. Ez azt csoportosításával azt fürtök esetén a K-közép hasonlóan, vagy megtekint összetett adatokat úgy, hogy egyszerűbb különböző módokat kereséséhez.

  * A **megerősítése tanulási**, az algoritmus lekérdezi az egyes adatpontokban válaszul művelet kiválasztását. Általános gyakorlatként javasolt a robotics, ahol adatpont érzékelő szivattyútelepek érzékelőinek adatai egy helyen időben lesz, és az algoritmus ki kell választania a robot a következő művelet. Egyúttal természetes az eszközök internetes hálózatát alkalmazások alkalmas. A tanulási algoritmus is kap egy ellenszolgáltatás jel rövid időn belül később jelző mennyire jó a döntést. Ennek alapján, az algoritmus módosítja a stratégia a legmagasabb ellenszolgáltatás elérése érdekében. Jelenleg nincsenek nem tanulási algoritmus modulok Azure ml megerősítése.

* **Bayes-féle módszerek** statisztikailag független adatpontok feltételezi. Ez azt jelenti, hogy az egy-egy unmodeled variancia predikátumban másokkal, ez azt jelenti, hogy nem meghatározható. Például ha a rögzített adatok percben, amíg a következő subway vonat megérkeznek, két mérések naponta egymástól függetlenek statisztikailag. Azonban két mérések egy percet egymástól függetlenek nem statisztikailag - egyik értéke magas prediktív egyéb értékének.

* **Súlyozott döntési fa regressziós** kihasználja a szolgáltatás hozzon létre vagy szolgáltatások közötti kommunikáció. Ez azt jelenti, hogy, hogy bármely adott adatpontban, egy szolgáltatás értéke némileg prediktív egy másik érték. Például napi magas vagy alacsony hőmérséklet-adatok, az alacsony hőmérséklet ismerete, a nap lehetővé teszi, hogy csak akkor lehet egy ésszerű becslés a nagy. A két funkció szereplő információk már némileg redundáns.

* Adatok besorolása több mint két kategóriába sorolhatók végezhető vagy egy eredendően több osztály osztályozó, vagy csoportjának egyesítésével képzett két osztályú osztályozó be egy **ensemble**. A ensemble módszert használja, nincs külön két osztályú besorolás minden osztály - mindegyik elkülöníti az adatokat két kategóriába sorolhatók: "Ez az osztály" és "nem ez az osztály." Ezek a besorolások majd a megfelelő hozzárendelési az adatpont szavazhatnak. Ez az a működési elv mögött [egyik-vs-összes Multiclass][one-vs-all-multiclass].

* Tegyük fel, számos módszer, beleértve a logisztikai regresszió és Bayes pontozó gépet **lineáris osztály határok**. Ez azt jelenti, hogy azok feltételezik, hogy a határok osztályok között körülbelül egyenes vonalak (vagy az általános esetben hyperplanes). Gyakran ez jellemzője, addig nem tudja, miután azt próbálta adatokat, de azt olyan dolog, amire általában is amelyeket ismernie kell előzetesen megjelenítése. Ha az osztály határok nagyon szabálytalan, a döntési fák odatapadjon, jungles döntési, támogatja a vektoros gépek vagy a Neurális hálózatokat.

* Neurális hálózatokat használható kategorikus változók hozzon létre egy **típusú változó** kategórián, 1 értékre állítaná az esetekben, amikor a kategóriát, ahol nem 0.


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
