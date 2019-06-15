---
title: 'Hiányzó adatok törlése: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a Clean Missing Data modul az Azure Machine Learning szolgáltatás eltávolítás, csere vagy kikövetkeztetni a hiányzó értékeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: de81204219a102734f1820258a3c32e59a64c685
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028785"
---
# <a name="clean-missing-data-module"></a>Clean Missing Data modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával eltávolítás, csere vagy kikövetkeztetni a hiányzó értékeket. 

Adatszakértők gyakran ellenőrizze a hiányzó értékekkel kapcsolatos adatokat, majd hajtsa végre az adatok rögzítése, vagy új azonosítóinak különféle műveleteket. Az ilyen tisztítási művelet célja, hogy hiányzó adatok, amely akkor fordulhat elő, amikor egy modell által okozott problémák megelőzése érdekében. 

Ez a modul műveletek "tisztítási" hiányzó értékek, beleértve a több típusát támogatja:

+ Hiányzó értékeket cserélje le a helyőrző, átlag vagy más érték
+ Teljesen eltávolítja a sorokat és oszlopokat, amelyekből értékek hiányoznak
+ Statisztikai módszerek alapuló adatcsatornához értékek


Ez a modul használatával a forrás adatkészlet nem változik. Ehelyett hoz létre egy új adatkészlet a munkaterületén lévő, a soron következő munkafolyamat is használhat. Az új, megtisztított adatkészlet újbóli felhasználás céljából is mentheti.

Ez a modul is jelenít meg az átalakítást a hiányzó értékeket tisztítására meghatározását. Újból felhasználhatja az átalakítás más, ugyanazzal a sémával rendelkezik az adatkészletek a [átalakítás alkalmazása](./apply-transformation.md) modul.  

## <a name="how-to-use-clean-missing-data"></a>Clean Missing Data használata

Ez a modul lehetővé teszi, hogy meghatározhatja a tisztítási művelet. Így az új adatokra is alkalmazhat, később is mentheti a tisztítási művelet. Lásd az alábbi hivatkozásokat, hogyan hozhat létre, és mentse egy tisztítási folyamat leírása: 
 
+ Cserélje le a hiányzó értékek
  
+ Az új adatok egy tisztítási átalakítás alkalmazandó
 
> [!IMPORTANT]
> A tisztítási hiányzó kezeléséhez használt módszer jelentősen befolyásolják az eredményt. Azt javasoljuk, hogy kísérletezzen a különböző módszereket. Fontolja meg mindkét indoklásának a következőt használja egy adott metódust, és az eredmény minőségét.

### <a name="replace-missing-values"></a>Cserélje le a hiányzó értékek  

Minden alkalommal, amikor alkalmazza a [Clean Missing Data](./clean-missing-data.md) modult egy adatkészletet, ugyanazt a tisztítási művelet minden kiválasztott oszlopok vonatkozik. Ezért különböző módszereket használ a különböző oszlopokban törölni kell, ha használja a modul külön példányát.

1.  Adja hozzá a [Clean Missing Data](./clean-missing-data.md) modult a kísérletvászonra, és csatlakozzon az adatkészletet, amely rendelkezik a hiányzó értékeket.  
  
2.  A **oszlopok, el kell távolítani**, válassza ki a módosítani szeretné a hiányzó értékeket tartalmazó oszlop. Több oszlopot kiválaszthatja, de az azonos helyettesítő módszert kell alkalmaznia az összes kijelölt oszlop. Ezért általában kell tiszta karakterlánc és numerikus oszlopok külön-külön.

    Ha például az összes numerikus oszlopok a hiányzó értékeket:

    1. Nyissa meg az Oszlopválasztó, és válassza ki **a szabályok**.
    2. A **megkezdése a**válassza **nem oszlopok**.

        Az összes OSZLOPOT tartalmazó is elindíthatja, és ezután kizárhatja az oszlopok. Kezdetben szabályok nem jelennek meg ha, először kattintson **minden oszlop**, de kattinthat **nem oszlopok** és kattintson a **minden oszlop** újra indítsa el az összes oszlopot, és szűrjön rá a (kizárás) oszlop alapján a nevet, írja be az adatokat, vagy oszlopok index.

    3. A **Belefoglalás**, jelölje be **oszloptípus** a legördülő listából, és válassza ki a **numerikus**, vagy egy pontosabb numerikus típus. 
  
    Bármely Ön által választott tisztítás vagy helyettesítő metódus a alkalmazni kell **összes** a kijelölt oszlopok. Ha bármely oszlop adatai nem kompatibilis a megadott műveletet, a modul hibát ad vissza, és leállítja a kísérletet.
  
3.  A **minimális érték arány hiányzó**, adja meg a hiányzó értékeket kell elvégezni a művelethez szükséges minimális számát.  
  
    Ezzel a beállítással együtt használhatja **maximális érték arány hiányzó** meghatározásához a feltételeket, amelyek alapján a tisztítási művelet történik az adatkészlethez. Ha túl sok vagy túl kevés sorok, amelyekről hiányoznak az értékek, a művelet nem hajtható végre. 
  
    Száma, adja meg azt a **arány** hiányzó értékek az oszlopban szereplő összes értékhez. Alapértelmezés szerint a **minimális hiányzó értéket arány** tulajdonság 0 értékre van állítva. Ez azt jelenti, hogy hiányzó törölve lettek, még akkor is, ha csak egy hiányzó értékkel. 

    > [!WARNING]
    > Ez a feltétel teljesülniük kell ahhoz, hogy a alkalmazni a megadott művelet minden oszlop szerint. Tegyük fel például, a kiválasztott három oszlopot, és állítsuk be a hiányzó értékek minimális aránya a.2 (20 %-át), de csak egy oszlop rendelkezik-e 20 %-át a hiányzó értékeket. Ebben az esetben a törlési műveletet alkalmazandó csak az oszlop a 20 %-át a hiányzó értékeket. Ezért a többi oszlop változatlan lenne.
    > 
    > Ha bármilyen kétségeink e hiányzó értékek módosultak, válassza ki a lehetőséget, **készítése a hiányzó értéket kijelző oszlop**. Egy oszlopot a rendszer hozzáfűzi az adatkészlet-e minden oszlop teljesül-e a minimális és maximális címtartományok esetében a megadott feltételeknek megfelelő jelzi.  
  
4. A **maximális érték arány hiányzó**, adja meg a hiányzó értékeket, amelyek a végrehajtandó műveletet a jelenlegi maximális számát.   
  
    Például előfordulhat, hogy végrehajtására vonatkozó szándékát behelyettesítési. értéke hiányzik. csak akkor, ha a 30 % vagy kevesebb sort a hiányzó értékeket tartalmaznak, de hagyja meg az értékeket, mint-akkor, ha több mint 30 %-a sorok amelyekből értékek hiányoznak.  
  
    A hiányzó értékek az oszlopban szereplő összes értékhez aránya, meghatározhatja a számot. Alapértelmezés szerint a **maximális érték arány hiányzó** 1 értékre van állítva. Ez azt jelenti, hogy hiányzó törölve lettek, még akkor is, ha 100 %-a az oszlopban szereplő értékek hiányoznak.  
  
   
  
5. A **tisztítás mód**, válassza ki, és cserélje le a következő lehetőségek közül, vagy eltávolítja a hiányzó értékeket:  
  
  
    + **Egyéni helyettesítő érték**: Ez a beállítás használatával adja meg a helyőrző értékét (például 0 vagy NA), amely az összes hiányzó értékek vonatkozik. Az érték helyett megadott oszlop adattípusát kompatibilisnek kell lennie.
  
    + **Cserélje le középérték**: Oszlop középértékét számítja ki, és az átlag használja az oszlop egyes hiányzó értékeinek csereértékre.  
  
        Csak érvényes Integer, Double rendelkező oszlopok vagy logikai adattípusokat.  
  
    + **Cserélje le középérték**: Kiszámítja a oszlop középérték, és használja a középérték a helyettesítője bármely hiányzik az oszlopban lévő értéknek.  
  
        Csak egész szám vagy Double típusú adatokat tartalmazó oszlopokra vonatkozik. 
  
    + **Cserélje le mód**: Kiszámítja a módot, az oszlop, és módot használ helyettesítő értéket az összes hiányzó az oszlopban lévő érték.  
  
        Integer, Double, logikai vagy Kategorikus adatok adattípusú oszlopokra vonatkozik. 
  
    + **Teljes sor eltávolítása**: Az adatkészlet egy vagy több hiányzó értékekkel rendelkező teljesen eltávolítja az összes sort. Ez akkor hasznos, ha a hiányzó értékeket véletlenszerűen hiányzó tekinthetők.  
  
    + **Távolítsa el a teljes oszlop**: Teljesen eltávolítja az oszlopot, amely egy vagy több értékek hiányoznak az adatkészletben.  
  
    
  
6. A beállítás **csereértékre** érhető el, ha a megoldás, kiválasztotta **egyéni behelyettesítési értéke**. Írjon be egy új értéket használandó csereértékre az összes hiányzó értékek az oszlopban.  
  
    Vegye figyelembe, hogy ez a beállítás csak az oszlopok Integer, Double, logikai vagy dátum típusú adatokat használhatja. A dátum oszlop, csereértékre is meg lehet adni, 100 nanoszekundumos számát 1/1/0001 óta 12:00-kor  
  
7. **Hiányzó értéket kijelző oszlop készítése**: Válassza ezt a lehetőséget, ha azt szeretné, a kimenetben néhány arra utalhat, hogy az oszlop értékeinek teljesülését tisztítás hiányzó értéket feltételeit. Ez a beállítás különösen hasznos, ha Ön egy új tisztítási művelet beállítását, és győződjön meg arról, hogy megfelelően működik.
  
8. Futtassa a kísérletet.

### <a name="results"></a>Results (Eredmények)

A modul két kimenetek adja vissza:  

-   **Megtisztított adathalmazt**: Ha ezt a lehetőséget választotta a megadott, és a egy mutató oszlopot, egy adatkészlet-csoportból áll, a kijelölt oszlopokat a hiányzó értékeket kezeli.  

    Tisztítási nem kijelölt oszlopok is "továbbítja a rendszer".  
  
-  **Átalakítás tisztítás**: A tisztításhoz, adatátalakítás, amely a munkaterületen menti, és alkalmazza az új adatok később.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>A mentett tisztítási művelet új adatokra vonatkoznak  

Ha meg kell ismételni a tisztítási műveletek gyakran, javasoljuk, hogy mentse el a recept Adattisztítás a egy *átalakítása*, újra felhasználhatja az ugyanabból az adatkészletből. Egy tisztítási átalakítás mentése akkor különösen hasznos, ha gyakran importálja újra kell és majd tisztítsa meg adatokat, amely ugyanazzal a sémával rendelkezik.  
      
1.  Adja hozzá a [átalakítás alkalmazása](./apply-transformation.md) modult a kísérletvászonra.  
  
2.  Adja hozzá a törölni kívánt adatkészletet, és az adatkészlet csatlakozik a jobb oldali bemeneti portját.  
  
3.  Bontsa ki a **alakítja át az** csoportot a bal oldali ablaktábla a felület. Keresse meg a mentett átalakítást, és húzza a kísérletvászonra.  
  
4.  A mentett átalakítási csatlakozni a bal oldali bemeneti portjával [átalakítás alkalmazása](./apply-transformation.md). 

    Amikor egy mentett átalakítás alkalmazza, az oszlopok, amelyekre az átalakítás vonatkoznak nem választhat. Ennek oka a transzformáció már definiálva van, és automatikusan alkalmazza az eredeti művelet a megadott oszlopokat.

    Előfordulhat azonban hogy létrehozott egy átalakítás a numerikus oszlopok egy része. Alkalmazhat az átalakítás vegyes oszloptípusainak az adatkészlet nem emeli a hibát, mert hiányoznak az értékek módosulnak, csak a numerikus oszlopokat.

6.  Futtassa a kísérletet.  

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 