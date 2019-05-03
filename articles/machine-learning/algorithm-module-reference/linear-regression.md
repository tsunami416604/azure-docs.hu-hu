---
title: 'Lineáris regresszió: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a lineáris regressziós modul használata az Azure Machine Learning szolgáltatás használható egy lineáris regressziós modell létrehozása a kísérletet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029715"
---
# <a name="linear-regression-module"></a>Lineáris regressziós modul
Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával való használatra lineáris regressziós modell létrehozása az kísérlet.  Lineáris regressziós megpróbál létrehozni egy vagy több független változók és a egy numerikus serkenti az eredményt és a függő változó közötti lineáris kapcsolat. 

Ez a modul egy lineáris regressziós módszerrel meghatározására használni, és ezután a címkézett adatkészletet használó modell betanításához. A betanított modell használható, hogy előrejelzéseket végezzen.

## <a name="about-linear-regression"></a>Lineáris regresszió kapcsolatban

Lineáris regressziós modell futtatása egy közös statisztikai metódussal, amely elfogadni a machine Learning szolgáltatásban és a sor megfelel, és hiba méréséhez számos új módszerekkel fokozott. A legalapvetőbb értelemben regresszió egy numerikus cél előrejelzését hivatkozik. Lineáris regressziós esetén továbbra is megfelelő választás az egyszerű modell egy egyszerű prediktív feladat használni szeretne. Lineáris regresszió is általában nagy dimenziós, ritka adatkészletek nem rendelkező összetettséget is jól működik.

Az Azure Machine Learning regressziós modelleket, lineáris regresszió mellett számos támogat. Azonban lazán "regression" kifejezést kell értelmezni, és néhány más eszközökben megadott regressziós típusa nem támogatott.

+ A klasszikus regressziós probléma egy egyetlen független változók és a egy függő változó foglalja magában. Ezt nevezzük *egyszerű regressziós*.  Ez a modul támogatja az egyszerű regressziós.

+ *Többváltozós lineáris regresszió* magában foglalja a két vagy több független változók egy függő változó közreműködik. Problémák, amelyben több bemenet egy numerikus egyszeri eredmény előrejelzése segítségével is nevezünk *többváltozós lineáris regresszió*.

    A **lineáris regressziós** modul e problémák megoldását, a legtöbb más regressziós modul is.

+ *Több címke regressziós* előrejelzésére több függő változókra hivatkozhatnak egy adott modellt, a feladat. A logisztikai regressziós több címkét, például egy minta a több különböző címkékre rendelhető. (Ez különbözik a feladata, hogy több szinten belül egyetlen osztály változó előrejelzésére.)

    Az ilyen típusú regressziós nem támogatott az Azure Machine Learningben. Több változókat-előrejelzési, hozzon létre egy külön learner előre jelezni kívánt minden egyes kimeneti.

Éve Statisztikusok fejlesztésének regressziós egyre speciális módszerei rendelkezik lett. Ez a lineáris regressziós modell futtatása esetén is igaz. Ez a modul hiba mérni, és alkalmas a regressziós egyenes két módszert támogat: a legkisebb szokásos négyzetek módját, valamint gradiens módszeres.

- **Gradiens módszeres** módszer a minimálisra csökkenti a hiba történt a modell betanítási folyamat egyes lépései mennyisége. Gradiens módszeres számos változata, és a különböző tanulási problémákat az optimalizálás nagymértékben tanulmányait. Ha úgy dönt, ezt a beállítást **megoldás metódus**, tanulási ráta számos szabályozhatja a lépésköz mérete paraméter beállítása, és így tovább. Ez a beállítás egy integrált paraméteres használatát is támogatja.

- **Szokásos legkisebb négyzetek** lineáris regressziós a leggyakrabban használt módszerek egyike. A legkisebb négyzetek például a Microsoft Excel Analysis ToolPak használt módszer.

    A veszteség függvény, amely kiszámítja a hiba az összeg és az előre jelzett sor a tényleges érték közötti távolság négyzetét, és megfelel a modell a squared hiba termékeikkel szokásos legkisebb négyzetek hivatkozik. Ez a módszer azt feltételezi, hogy a bemeneti adatok és a függő változó között erős lineáris kapcsolat.

## <a name="configure-linear-regression"></a>Lineáris regresszió konfigurálása

Ez a modul egy regressziós modellt, különböző lehetőségekkel igyekeznek két módszert támogatja:

+ [Online gradiens módszeres használatával regressziós modell létrehozása](#bkmk_GradientDescent)

    Gradiens módszeres modellek, amelyek összetett vagy túl kevés, változók hány betanítási adatok rendelkező jobb adatveszteség függvény.



+ [Egy regressziós modell használatával a szokásos legkisebb négyzetek igazítása](#bkmk_OrdinaryLeastSquares)

    A kis adatkészletekhez célszerű szokásos legkisebb négyzetek kiválasztásához. Hasonló eredményeket az Excelbe ez kell biztosítania.

## <a name="bkmk_OrdinaryLeastSquares"></a> Használatával a szokásos legkisebb négyzetek regressziós modell létrehozása

1. Adja hozzá a **lineáris regressziós modell** modult a kísérletvászonra a felületen.

    Ez a modul a annak a **Machine Learning** kategória. Bontsa ki a **modell inicializálása**, bontsa ki a **regressziós**, majd húzza a **lineáris regressziós modell** modult a kísérletvászonra.

2. Az a **tulajdonságok** ablaktáblán, a a **megoldás metódus** legördülő listában válassza **szokásos legkisebb négyzetek**. Ez a beállítás meghatározza a regressziós egyenes kereséséhez használt számítási módszert.

3. A **L2 regularizációs súly**, írja be az értéket a súly L2 regularizációs használja. Azt javasoljuk, hogy overfitting elkerülése érdekében használjon egy nullától eltérő értékre.

     Milyen hatással van a regularizációs modell méretezés kapcsolatos további információkért lásd: Ez a cikk: [L1 és gépi tanulási L2 Regularizációs](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. A beállítást, **Belefoglalás intercept kifejezés**, ha meg szeretné tekinteni a kifejezés a intercept.

    Törölje ezt a beállítást, ha nincs szüksége, tekintse át a regressziós képletben.

5. A **véletlenszerű szám kezdőérték**, kitöltheti a véletlenszám-generátor a modell által használt kezdőszámát értéket.

    Egy kezdeti érték használata akkor hasznos, ha meg szeretné tartani a ugyanazokat az eredményeket az ugyanazon kísérletben különböző kísérletei. Ellenkező esetben az alapértelmezett érték a rendszeróra származó értéket használjon.


7. Adja hozzá a [tanítási modell](./train-model.md) modult a kísérletvászonra, és csatlakozzon a címkézett adatkészlet.

8. Futtassa a kísérletet.

## <a name="results-for-ordinary-least-squares-model"></a>Szokásos legkisebb négyzetek modell eredményei

Képzési befejezése után:

+ A Modellparaméterek megtekintéséhez kattintson a jobb gombbal a trainer kimenetet, és válassza **Visualize**.

+ Az előrejelzéseket, csatlakozzon a betanított modellt a [Score Model](./score-model.md) modult, új értékeket az adatkészlet mellett. 


## <a name="bkmk_GradientDescent"></a> Online gradiens módszeres használatával regressziós modell létrehozása

1. Adja hozzá a **lineáris regressziós modell** modult a kísérletvászonra a felületen.

    Ez a modul a annak a **Machine Learning** kategória. Bontsa ki a **modell inicializálása**, bontsa ki a **regressziós**, és húzza a **lineáris regressziós modell** modult a kísérletvászonra

2. Az a **tulajdonságok** ablaktáblán, a a **megoldás metódus** legördülő menüben válassza ki **Online gradiens módszeres** a regressziós egyenes kereséséhez használt számítási módszerként.

3. A **létrehozási trainer módban**, adja meg, hogy kívánja-e paraméterek egy előre meghatározott készletével együtt a modell betanítását, vagy hogy szeretné-e a modell optimalizálása paraméteres használatával.

    + **Egyetlen paraméter**: Ha tudja, hogyan szeretné lineáris regressziós-hálózat konfigurálására, megadhat egy adott értékhalmazt argumentumként.

   
4. A **tanulási ráta**, adja meg a kezdeti tanulási rátát a sztochasztikus gradiens módszeres optimalizáló.

5. A **hány betanítási alapidőszakkal**, írja be egy értéket, amely azt jelzi, hogy hány alkalommal az algoritmus kell iterálódnak példákat. Példák kis számú-adatkészletek esetében ez a szám elérni a convergence nagynak kell lennie.

6. **Szolgáltatások normalizálása**: Ha Ön már rendelkezik normalized a numerikus adatokat a modell betanításához használja, akkor is törölje ezt a beállítást. Alapértelmezés szerint a modul normalizálja az összes numerikus bemeneti adatokat egy 0 és 1 közötti tartományba esik.

    > [!NOTE]
    > 
    > Ne felejtse el a alkalmazni normalizálási ugyanezzel a módszerrel új adatok pontozásához használt.

7. A **L2 regularizációs súly**, írja be az értéket a súly L2 regularizációs használja. Azt javasoljuk, hogy overfitting elkerülése érdekében használjon egy nullától eltérő értékre.

    Milyen hatással van a regularizációs modell méretezés kapcsolatos további információkért lásd: Ez a cikk: [L1 és gépi tanulási L2 Regularizációs](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. A beállítást, **tanulási ráta csökkenése**, ha azt szeretné, hogy a megfelelő tanulási rátát csökkentheti az ismétlések állapotát.  

10. A **véletlenszerű szám kezdőérték**, kitöltheti a véletlenszám-generátor a modell által használt kezdőszámát értéket. Egy kezdeti érték használata akkor hasznos, ha meg szeretné tartani a ugyanazokat az eredményeket az ugyanazon kísérletben különböző kísérletei.


12. Adja hozzá a címkézett adatkészlet és a képzési modulok egyike.

    Ha nem használ a paraméteres, használja a [tanítási modell](train-model.md) modul.

13. Futtassa a kísérletet.

## <a name="results-for-online-gradient-descent"></a>Online gradiens módszeres eredményei

Képzési befejezése után:

+ Az előrejelzéseket, csatlakozzon a betanított modellt a [Score Model](./score-model.md) modul új bemeneti adatokkal együtt.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 