---
title: Hogyan válik egy modell webszolgáltatássá?
titleSuffix: ML Studio (classic) - Azure
description: Az Azure Machine Learning Studio (klasszikus) modell fejlesztési kísérletből webszolgáltatásba történő előrehaladásának áttekintése.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217924"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Hogyan halad a Machine Learning Studio (klasszikus) modell kísérletből webszolgáltatásba?

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Az Azure Machine Learning Studio (klasszikus) egy interaktív vásznat biztosít, amely lehetővé teszi egy prediktív elemzési modellt képviselő ***kísérlet*** fejlesztését, futtatását, tesztelését és ismétlését. Vannak sokféle modul áll rendelkezésre, amelyek:

* Adatok bevitele a kísérletbe
* Az adatok kezelése
* Modell betanítása gépi tanulási algoritmusok használatával
* A modell pontozása
* Az eredmények kiértékelése
* Kimeneti végső értékek

Miután elégedett a kísérlettel, üzembe helyezheti ***azt klasszikus Azure Machine Learning webszolgáltatásként*** vagy ***új Azure Machine Learning webszolgáltatásként,*** hogy a felhasználók új adatokat küldhessenek, és eredményeket kaphassanak vissza.

Ebben a cikkben áttekintést adunk a Machine Learning-modell fejlesztési kísérletből egy működőképes webszolgáltatásba történő előrehaladásáról.

> [!NOTE]
> A gépi tanulási modellek fejlesztésének és üzembe helyezésének más módjai is vannak, de ez a cikk a Machine Learning Studio (klasszikus) használatával kapcsolatos. Ha például el szeretné olvasni a klasszikus prediktív webszolgáltatás R-rel való létrehozásának leírását, olvassa el a [Build & Deploy Predictive Web Apps using RStudio és az Azure Machine Learning studio című blogbejegyzést.](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)
>
>

Míg az Azure Machine Learning Studio (klasszikus) célja, hogy segítsen kifejleszteni és telepíteni egy *prediktív elemzési modell,* lehetséges, hogy a Studio (klasszikus) egy olyan kísérlet kifejlesztése, amely nem tartalmaz prediktív elemzési modell. Előfordulhat például, hogy egy kísérlet csak adatokat ad meg, manipulálja azokat, majd kiadja az eredményeket. Csakúgy, mint egy prediktív elemzési kísérlet, ezt a nem prediktív kísérletet webszolgáltatásként is telepítheti, de ez egy egyszerűbb folyamat, mert a kísérlet nem betanításvagy gépi tanulási modell pontozása. Bár ez nem a tipikus használata Studio (klasszikus) ily módon, akkor is azt a vitát, hogy tudunk adni egy teljes magyarázatot, hogyan Studio (klasszikus) működik.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Prediktív webszolgáltatás fejlesztése és üzembe helyezése
Az alábbiakban azokat a szakaszokat olvashatja, amelyeket egy tipikus megoldás követ a Machine Learning Studio (klasszikus) használatával történő fejlesztése és üzembe helyezése során:

![Telepítési folyamat](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*1. ábra – Egy tipikus prediktív elemzési modell szakaszai*

### <a name="the-training-experiment"></a>A képzési kísérlet
A ***betanítási kísérlet*** a webszolgáltatás fejlesztésének kezdeti fázisa a Machine Learning Studio (klasszikus) alkalmazásban. A betanítási kísérlet célja, hogy helyet adjon a fejlesztéshez, teszteléshez, iteitáláshoz és végül egy gépi tanulási modell betanításához. Akár több modellt is betaníthat egyszerre, miközben a legjobb megoldást keresi, de ha végzett a kísérletezéssel, egyetlen betanított modellt választ ki, és a többit kiirtja a kísérletből. Egy prediktív elemzési kísérlet kifejlesztésének például: [Prediktív elemzési megoldás fejlesztése a hitelkockázat-értékeléshez az Azure Machine Learning Studio (klasszikus) alkalmazásban című témakörben.](tutorial-part1-credit-risk.md)

### <a name="the-predictive-experiment"></a>A prediktív kísérlet
Ha már rendelkezik betanított modellel a betanítási kísérletben, kattintson **a Webszolgáltatás beállítása** gombra, és válassza **a Prediktív webszolgáltatás** lehetőséget a Machine Learning Studio (klasszikus) programban a betanítási kísérlet ***prediktív kísérletté***történő átalakításának elindításához. A prediktív kísérlet célja, hogy a betanított modell segítségével új adatokat szerezzen, azzal a céllal, hogy végül azure-webszolgáltatásként működőképessé váljon.

Ez a konverzió a következő lépéseken keresztül történik:

* A betanításhoz használt modulok készletének átalakítása egyetlen modulba, és mentse betanított modellként
* Távolítson el minden olyan idegen modult, amely nem kapcsolódik a pontozáshoz
* Adja hozzá a végső webszolgáltatás által használni kívánt bemeneti és kimeneti portokat

Előfordulhat, hogy további módosításokat szeretne végrehajtani, hogy a prediktív kísérlet készen álljon a webszolgáltatásként való üzembe helyezésre. Ha például azt szeretné, hogy a webszolgáltatás csak az eredmények egy részét adja ki, a kimeneti port előtt hozzáadhat egy szűrőmodult.

Ebben az átalakítási folyamatban a betanítási kísérlet nem kerül ki. Amikor a folyamat befejeződött, két lap van a Studio -ban (klasszikus): az egyik a betanítási kísérlethez, a másik pedig a prediktív kísérlethez. Így módosíthatja a betanítási kísérletet a webszolgáltatás üzembe helyezése és a prediktív kísérlet újraépítése előtt. Vagy mentheti a betanítási kísérlet egy példányát egy másik kísérletezési vonal elindításához.

> [!NOTE]
> Ha a **Prediktív webszolgáltatásra** kattint, automatikus folyamatot indít a betanítási kísérlet prediktív kísérletté alakításához, és ez a legtöbb esetben jól működik. Ha a betanítási kísérlet összetett (például több útvonal a képzés, amely összekapcsolódik), előfordulhat, hogy inkább ezt a konverziót manuálisan. További információ: [A modell előkészítése az Azure Machine Learning Studio (klasszikus) üzembe helyezéséhez](convert-training-experiment-to-scoring-experiment.md)című témakörben talál.
>
>

### <a name="the-web-service"></a>A webszolgáltatás
Miután meggyőződött arról, hogy a prediktív kísérlet készen áll, üzembe helyezheti a szolgáltatást klasszikus webszolgáltatásként vagy az Azure Resource Manager en alapuló új webszolgáltatásként. Ha a modellt *klasszikus gépi tanulási webszolgáltatásként*szeretné üzembe helyezni, kattintson **a Webszolgáltatás telepítése** elemre, és válassza a **Webszolgáltatás telepítése [Klasszikus] lehetőséget.** *Az Új gépi tanulási webszolgáltatásként*történő telepítéshez kattintson **a Webszolgáltatás telepítése gombra,** és válassza **a Webszolgáltatás telepítése [Új] lehetőséget.** A felhasználók most már adatokat küldhetnek a modellnek a WEBszolgáltatás REST API használatával, és visszakaphatják az eredményeket. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>A nem tipikus eset: nem prediktív webszolgáltatás létrehozása
Ha a kísérlet nem tanít be egy prediktív elemzési modellt, akkor nem kell betanítási kísérletet és pontozási kísérletet is létrehoznia – csak egy kísérlet van, és webszolgáltatásként is üzembe helyezheti. A Machine Learning Studio (klasszikus) a használt modulok elemzésével észleli, hogy a kísérlet tartalmaz-e prediktív modellt.

Miután iterated a kísérlet, és elégedett vele:

1. Kattintson **a Webszolgáltatás beállítása** gombra, és válassza **az Átképzés webszolgáltatás** lehetőséget – a bemeneti és kimeneti csomópontok automatikusan hozzáadódnak
2. Kattintson a **Futtatás gombra**
3. Kattintson **a Webszolgáltatás telepítése** gombra, és válassza **a Webszolgáltatás [Klasszikus] telepítése** vagy a **Webszolgáltatás [Új] központi telepítése lehetőséget** attól függően, hogy melyik környezetben szeretné telepíteni.

A webszolgáltatás most már telepítve van, és elérheti és kezelheti, mint egy prediktív webszolgáltatás.

## <a name="updating-your-web-service"></a>A webszolgáltatás frissítése
Most, hogy webszolgáltatásként telepítette a kísérletet, mi történik, ha frissítenie kell?

Ez attól függ, hogy mit kell frissíteni:

**Módosítani szeretné a bemenetet vagy a kimenetet, vagy módosítani szeretné, hogy a webszolgáltatás hogyan**

Ha nem módosítja a modellt, de csak módosítja az adatok kezelésének módját, szerkesztheti a prediktív kísérletet, majd kattintson a **Webszolgáltatás telepítése gombra,** és válassza a **Webszolgáltatás telepítése [Klasszikus]** vagy **a Webszolgáltatás [Új] telepítése** lehetőséget. A webszolgáltatás leáll, a frissített prediktív kísérlet telepítve van, és a webszolgáltatás újraindul.

Íme egy példa: Tegyük fel, hogy a prediktív kísérlet a bemeneti adatok teljes sorát adja vissza a várt eredménnyel. Dönthet úgy, hogy azt szeretné, hogy a webszolgáltatás csak az eredményt adja vissza. Így hozzáadhat egy **Project Columns modult** a prediktív kísérlethez közvetlenül a kimeneti port előtt, hogy kizárja az eredményt eltérő oszlopokat. Ha a **Webszolgáltatás telepítése gombra** kattint, és ismét **a [Klasszikus] webszolgáltatás telepítése** vagy az **[Új] webszolgáltatás telepítése lehetőséget választja,** a webszolgáltatás frissül.

**Új adatokkal szeretné újraképezni a modellt**

Ha meg szeretné tartani a gépi tanulási modellt, de új adatokkal szeretné újrabetanítását, két lehetősége van:

1. **A modell újratanítása a webszolgáltatás futása közben** – Ha a modellt a prediktív webszolgáltatás futása közben szeretné újraképezni, ezt úgy teheti meg, hogy néhány módosítást hajt végre a betanítási kísérleten, hogy ***átképzési kísérletté***tegye, majd ** *átképzési webszolgáltatásként* **telepítheti. Ennek módjáról a Machine [Learning-modellek programozott újratanítása témakörben talál.](/azure/machine-learning/studio/retrain-machine-learning-model)
2. **Lépjen vissza az eredeti betanítási kísérlethez, és használjon különböző betanítási adatokat a modell fejlesztéséhez** – A prediktív kísérlet a webszolgáltatáshoz kapcsolódik, de a betanítási kísérlet nem kapcsolódik közvetlenül ily módon. Ha módosítja az eredeti betanítási kísérletet, és a **Webszolgáltatás beállítása**gombra kattint, új prediktív kísérletet hoz létre, amely telepítéskor *új* webszolgáltatást hoz létre. *new* Nem csak az eredeti webszolgáltatást frissíti.

   Ha módosítania kell a betanítási kísérletet, nyissa meg, és a **Másolás másként** gombra kattintva készítsen másolatot. Ez érintetlenül hagyja az eredeti betanítási kísérletet, a prediktív kísérletet és a webszolgáltatást. Most már létrehozhat egy új webszolgáltatást a módosításokkal. Az új webszolgáltatás üzembe helyezése után eldöntheti, hogy leállítja-e az előző webszolgáltatást, vagy továbbra is az új mellett fut.

**Másik modellt szeretne beiktatni**

Ha módosítani szeretné az eredeti prediktív kísérletet, például egy másik gépi tanulási algoritmus kiválasztását, egy másik betanítási módszert stb., akkor a modell átképzéséhez a fent leírt második eljárást kell követnie: nyissa meg a betanítási kísérletet, kattintson a **Mentés másként** gombra a másoláshoz, majd kezdje el a modell fejlesztésének új útvonalát, létrehozza a prediktív kísérletet és telepítse a webszolgáltatást. Ez létrehoz egy új webszolgáltatást, amely nem kapcsolódik az eredetihez - eldöntheti, hogy melyiket vagy mindkettőt szeretné tovább futtatni.

## <a name="next-steps"></a>További lépések
A fejlesztés és a kísérlet folyamatáról a következő cikkekben talál további részleteket:

* a kísérlet konvertálása – [A modell előkészítése az Azure Machine Learning Studio-ban való üzembe helyezéshez (klasszikus)](convert-training-experiment-to-scoring-experiment.md)
* a webszolgáltatás telepítése – [Azure Machine Learning webszolgáltatás telepítése](deploy-a-machine-learning-web-service.md)
* a modell átképzése – [A Machine Learning-modellek programozott újratanítása](/azure/machine-learning/studio/retrain-machine-learning-model)

A teljes folyamatra vonatkozó példákat lásd:

* [Gépi tanulási oktatóanyag: Az első kísérlet létrehozása az Azure Machine Learning Studio-ban (klasszikus)](create-experiment.md)
* [Forgatókönyv: Prediktív elemzési megoldás kidolgozása a hitelkockázat-értékeléshez az Azure Machine Learningben](tutorial-part1-credit-risk.md)

