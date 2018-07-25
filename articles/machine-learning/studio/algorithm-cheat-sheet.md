---
title: Gépi tanulási algoritmus-Adatlap – Azure |} A Microsoft Docs
description: Nyomtatható machine learning algoritmus-Adatlap segít az Azure Machine Learning Studióban válassza ki a megfelelő algoritmus a prediktív modell.
keywords: algoritmus-Adatlap, Adatlap, gépi tanulási algoritmus
services: machine-learning
documentationcenter: ''
author: pakalra
ms.author: pakalra
manager: cgronlun
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: b080a739aa73e3c8ef95c7db9a6358d942e94bba
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238386"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Gyorsreferencia a Machine Learning-algoritmusokhoz a Microsoft Azure Machine Learning Studióban
A **a Microsoft Azure Machine Learning algoritmus Adatlap lap** segítségével válassza ki a megfelelő algoritmus a prediktív elemzési modellek.

[Az Azure Machine Learning Studio](https://studio.azureml.net/) rendelkezik egy nagy származó algoritmusok könyvtára a ***regressziós***, ***besorolási***, ***Fürtszolgáltatás***, és  ***anomáliadetektálás*** családhoz. Minden egyes célja egy más típusú machine learning a probléma megoldása érdekében.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Letöltés: A Machine learning algoritmus-Adatlap
**A Adatlap letöltése: [Machine Learning algoritmus Adatlap lap (11 x 17)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**

![Machine Learning algoritmus-Adatlap: ismerje meg, egy gépi tanulási algoritmus kiválasztása.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Töltse le, és a Machine Learning algoritmus Adatlap lap nyomtatása tabloid méretben kéznél legyen, és kérjen segítséget az algoritmus kiválasztása.

> [!NOTE]
> Tekintse meg a cikket [algoritmusok kiválasztása a Microsoft Azure Machine Learning](algorithm-choice.md) részletes útmutató a Adatlap használatával.
> 
> 

## <a name="more-help-with-algorithms"></a>További segítségért algoritmusokkal
* Használatával a Adatlap döntöttek a megfelelő algoritmus, valamint a különböző típusú gépi tanulási algoritmusok és a használatuk mélyebb hatásának a megbeszélését lásd [algoritmusok kiválasztása a Microsoft Azure Machine Learninghez](algorithm-choice.md).
* Letölthető infografika, amely ismerteti az algoritmusokkal, és példákat talál, lásd: [letölthető Infografika: Machine learning alapjai algoritmuspéldákkal](basics-infographic-with-algorithm-examples.md).
* A Machine Learning Studióban elérhető összes gépi tanulási algoritmusok, kategória szerint, lásd: [modell inicializálása] [ initialize-model] a Machine Learning Studio algoritmus-és modul segítségével.
* Betűrend szerinti rendezés listájáért algoritmusok és a Machine Learning Studióban modulok, lásd: [betűrend szerinti lista Machine Learning Studio-modulok] [ a-z-list] a Machine Learning Studio algoritmus- és modul segítségével.
* Ha szeretne egy letölthető és kinyomtatható diagramot a Machine Learning Studio funkcióiról, tekintse meg [Az Azure Machine Learning Studio funkcióit bemutató diagram](studio-overview-diagram.md) című cikket.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Megjegyzések és a gépi tanulási algoritmus esetében terminológiai definíciók – Adatlap

* A javaslat érhető el az algoritmus-Adatlap hozzávetőleges szabályok-az-bütykének. Néhány hajlított, és néhány is lehet flagrantly megsértve. Ennek célja, és kiindulási pontot javasolja. Ne féljen head-to-head versenyt között számos algoritmust az adatokon való futtatásához. Nincs egyszerűen nincs helyettesítő egyes algoritmus alapelveinek ismertetése, és a rendszer az adatokat létrehozó ismertetése.

* Minden gépi tanulási algoritmus rendelkezik a saját style vagy *induktív eltérés*. Egy meghatározott problémára vonatkozó számos algoritmust célszerű lehet, és a egy algoritmus lehet, mint a többi jobban megfelel. De nem mindig lehetséges tudja előre, azaz az ideális megoldást. Ilyen esetekben számos algoritmust szerepelnek együtt a Adatlap. Próbáljon meg egy algoritmust, és ha az eredmények egyelőre nem kielégítő, próbálja ki, a többi lehet a megfelelő stratégiát. Íme egy példa a a [Azure AI-katalógusban](http://gallery.cortanaintelligence.com/) kísérletet, amelyek az ugyanazon adatokra irányuló számos algoritmust megpróbál, és összehasonlítja az eredményeket: [többcsoportos deklarációkkal összehasonlítása: felismerés betűs](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

* A gépi tanulás három fő kategóriába sorolhatók: **felügyelt tanítással**, **tanítást**, és **megerősítő tanulást**.

  * A **felügyelt tanítással**, az egyes van címkézett, illetve a társított egy kategóriát vagy az egyik fontos értékére.  Egy példa egy kategorikus címke van hozzárendelése "cat" vagy "kutya" kép.  Egy érték felirat egyik példája az olyan kapcsolattal társított eladási ár. A felügyelt tanítás célja, hogy ezeket, mint számos címkézett példák tanulmánya majd tudnak kapcsolatos jövőbeli adatpontok előrejelzéseket. Például új fényképet azonosítja a megfelelő szolgáltatást, vagy más használt autók pontos értékesítési árak hozzárendelése. Ez a machine learning egy népszerű és hasznos típusú. Az Azure Machine Learning összes felügyelt tanulási algoritmus kivételével [K-közép-Fürtszolgáltatás][k-means-clustering].

  * A **tanítást**, adatpontok nem tartalmaz feliratokat társítva. Ehelyett egy felügyeletlen tanulási algoritmus célja valamilyen módon adatok rendezése, vagy a szerkezetét írja le. Ez azt csoportosítás, csoportosít, a K-közép hasonlóan, vagy különböző módszert is megnézzük az összetett, így egyszerűbb jelenik kereséséhez.

  * A **megerősítő tanulást**, az algoritmus beolvasása művelet kiválasztása az egyes adott válaszként. Általánosan használt megközelítés a robotika, ahol egy adatpont érzékelőinek adott ponton időben lesz, és az algoritmus ki kell választania a robot a következő művelet. Még egy természetes választás olyan IOT-alkalmazásokat. A tanulási algoritmus is fogad ellenszolgáltatás jel egy rövid ideig később, hogyan jó döntés lett megjelölve. Ennek alapján, az algoritmus módosítása igazítani a stratégiáját a legmagasabb ellenszolgáltatás növelhető. Jelenleg nincsenek nincs megerősítő tanulási algoritmus modulok az Azure gépi tanulás.

* **Bayes módszerek** statisztikailag független adatpontok feltételezik. Ez azt jelenti, hogy az egyik adatpont unmodeled hozzon predikátumban másokkal, vagyis nem becsülhető. Például ha az adatok rögzíti a hány perc múlva a következő subway vonat érkezik, két mérések napi egymástól függetlenek statisztikailag. Azonban két mérések egy perc közötti nem függetlenek statisztikailag – a egy értéke magas prediktív, a másik értékét.

* **Súlyozott regressziós döntési fa** kihasználja a szolgáltatás között átfedés van, vagy a szolgáltatások közötti interakció. Azt jelenti, hogy, hogy az adott adatpontok, egy szolgáltatás értéke némileg prediktív egy másik érték. Például a napi magas és alacsony hőmérsékletadatokat, a legalacsonyabb hőmérséklet, hogy a nap lehetővé teszi, hogy a nagy egy ésszerű találgatásos. A két funkció található információkat, valamennyire redundáns.

* Adatok besorolása több mint két kategóriába sorolhatók teheti meg vagy egy eredendően több osztály osztályozó, vagy csoportjának egyesítésével képzett két osztályú osztályozás be egy **ensemble**. A ensemble módszert használja, nincs külön két osztályú besorolás az egyes osztályok – az adatok mindegyik elválasztja a két kategóriába sorolhatók: "Ez az osztály" és "nem ez az osztály." Ezek a besorolások majd helyes hozzárendelését az adatpont is szavazhat. Ez az a működési elv mögött [egy-multi-osztályú osztályozási műveletet][one-vs-all-multiclass].

* Tegyük fel, több módszer, beleértve a logisztikai regressziós és a Bayes pontozó gépet **lineáris osztály határok**. Ez azt jelenti, hogy azt feltételezik, hogy az osztályok között határok körülbelül egyenes vonalak (vagy az általános esetben hyperplanes). Gyakran ez az adatok, amíg nem tudja után való elhatárolásra próbálta jellemzők, de valami, amely általában is lehet megtanult előre vizualizációjával. Az osztály határok nagyon szabálytalan tekinti meg, ha jól döntési fák, decision jungle algoritmust, támogatja a vektoros gépek vagy a Neurális hálózatokat.

* Neurális hálózatokkal használható kategorikus változók létrehozásával egy **helyőrző a változó** minden egyes kategóriánál, 1 értékre állítaná az esetekben, amikor a kategóriát, ahol nem 0.


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
