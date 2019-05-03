---
title: 'Adatok felosztása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a felosztási adatok modul az Azure Machine Learning szolgáltatás adatkészlet felosztása két különböző eljáráscsoport.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029400"
---
# <a name="split-data-module"></a>Split Data modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával egy adatkészlet felosztása két különböző eljáráscsoport.

Ez a modul különösen hasznos, ha egy betanítási és egy tesztelési válassza szét az adatokat kell. Testre szabható módon, hogy adatokat, valamint van-e osztva. Néhány lehetőség támogatja a véletlenszerű adatok; mások számára egy adott adattípus vagy a modell típusa vannak igazítva.

## <a name="how-to-configure"></a>Konfigurálása

> [!TIP]
> Felosztási mód kiválasztása előtt olvassa el a minden beállításnál az split kell meghatározni.
> Ha a felosztó mód módosításához az összes többi beállítást sikerült alaphelyzetbe állítani.

1. Adja hozzá a **Split Data** modult a kísérletvászonra a felületen. Ez a modul alatt található **adatátalakítás**, a a **minta és a felosztás** kategória.

2. **Felosztás mód**: Válasszon egyet a következő módok, adatok, és hogyan szeretné osztani, típusától függően. Minden egyes bontásával mód különböző beállításokkal rendelkezik. Kattintson a következő témakörök részletes útmutatásért és példákért. 

    - **Sorok felosztása**: Használja ezt a beállítást, ha csak át szeretné az adatok felosztása két részből áll. A százalékos arányát el az egyes felosztása a adatokat is megadhat, de az adatok alapértelmezés szerint 50 – 50 van osztva.

        Véletlenszerűvé tétele a kijelölt sorokat az egyes csoportokban, és használja a rétegzett mintavételi is. A rétegzett mintavételi választania kell az adatok, amelyek esetében szeretne értékek egyaránt felosztani a két eredményt adatkészletek csak egy oszlop.  

    - **Reguláris kifejezés Split** válassza ezt a beállítást, ha meg szeretné az adatkészlet nullával való tesztelés a egy értéke csak egy oszlop.

        Például ha vélemények elemzése vannak sikerült egy adott termék nevének egy szövegmezőben meglétének ellenőrzése, és az adatkészlet majd ezt követően sorokra a termék nevét, és azok nélkül.

    - **Relatív kifejezés Split**:  Használja ezt a beállítást, ha szeretné alkalmaz egy feltételt egy szám típusú oszlop. A szám egy dátum/idő mezőt, egy oszlop tartalmazó korra vagy összegeket és százalékos is lehet. Például előfordulhat, hogy szeretné osztani az adatkészlethez, attól függően, a cikkek kora sávokban csoport személyek vagy egy naptári dátum szerint külön adatok költségét.

### <a name="split-rows"></a>Sorok felosztása
1.  Adja hozzá a [Split Data](./split-data.md) modult a kísérletvászonra a felületén, és csatlakozzon a felosztani kívánt adatkészlet.
  
2.  A **megosztás mód**, válassza a **sorok felosztása**. 

3.  **Az első kimeneti adatkészletnél a sorok**. Ez a beállítás segítségével meghatározhatja a sorok számát az első (bal oldali) kimeneti lépnek. Az összes többi sort halad át a második (jobb oldali) kimenet.

    A arány sorok elküldhetők az első kimeneti adatkészletnél, így egy 0 és 1 közötti tizedes tört kell megadnia a százalékát jelenti.
     
     Például 0,75 értéket írja be, ha az adatkészlet lenne lehet megosztani egy 75:25 arány, a 75 %-a sorokat, elküldi az első kimeneti adatkészletnél és 25 % a második kimeneti adatkészlet küldött használatával.
  
4. Válassza ki a **Randomized split** beállítást, ha a kívánt adatok kiválasztása a két csoportra véletlenszerűvé tétele. Ez a előnyben részesített lehetőség, amikor tanítási és tesztelési adatkészleteket hoz létre.

5.  **Véletlenszám-generálás kezdőértéke**: Írja be a példányok használandó pseudorandom sorozatát inicializálása egy nem negatív egész érték. Az alapértelmezett kezdőérték készítése a véletlenszerű számok összes modult használja. 

     Adja meg az egyik kezdőérték teszi általában reprodukálható az eredményeket. Ha meg kell ismételni a felosztási művelet eredményeinek, adjon meg egy a véletlenszám-generátor kezdőértékét. Ellenkező esetben a véletlenszám-generálás kezdőértéke értéke 0, ami azt jelenti, hogy a kezdeti alapérték kérhető le a rendszeróra alapértelmezés szerint. Ennek eredményeképpen az adatok eloszlása eltérhet minden alkalommal, amikor egy split hajt végre. 

6. **Rétegzett split**: Ezt a beállítást **igaz** annak érdekében, hogy a két kimeneti adatkészletek tartalmaznak egy reprezentatív mintát értékek a *strata oszlop* vagy *csoportosítási oszlopot*. 

    A rétegzett mintavételezése esetén, az adatok úgy, hogy minden egyes kimeneti adatkészlet lekérdezi nagyjából azonos százalékos arányának minden célként megadott van osztva. Például előfordulhat, hogy szeretné győződjön meg arról, hogy a tanítási és egy tesztelési vannak nagyjából elosztott terhelésű kapcsolatban az eredménye, vagy véve zásoktól néhány másik oszlop, például a nemek.

7. Futtassa a kísérletet.


## <a name="regular-expression-split"></a>Reguláris kifejezés felosztása

1.  Adja hozzá a [Split Data](./split-data.md) modult a kísérletvászonra, és csatlakoztassa a felosztani kívánt adatkészletet bemenetként.  
  
2.  A **megosztás mód**válassza **reguláris kifejezés split**.

3. Az a **reguláris kifejezés** mezőbe írja be egy érvényes reguláris kifejezés. 
  
   A reguláris kifejezés Python reguláris kifejezési szintaxist kell követniük.


4. Futtassa a kísérletet.

    Azt adja meg a reguláris kifejezés alapján, az adatkészlet oszlik sorok két készletnyi: sorok, amelyek megfelelnek a kifejezést, és minden fennmaradó sorok értékekkel. 

## <a name="relative-expression-split"></a>Relatív kifejezés felosztása.

1. Adja hozzá a [Split Data](./split-data.md) modult a kísérletvászonra, és csatlakoztassa a felosztani kívánt adatkészletet bemenetként.
  
2. A **megosztás mód**válassza **relatív kifejezés split**.
  
3. Az a **relációs kifejezés** szövegmezőbe írjon be egy kifejezést, amely csak egy oszlop-összehasonlító műveletet hajtja végre:


 - Numerikus oszlop:
    - Az oszlop számokból áll bármely numerikus adattípusú, többek között a dátum/idő típusú adatokat.

    - A kifejezés egyetlen oszlop neve legfeljebb hivatkozhat.

    - Használja az és jel karaktert (&) és a művelet és használatára vonatkozó függőleges vonal (|) karaktert a vagy művelet.

    - A következő operátor használata támogatott: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - A műveletek nem csoportosíthatók `(` és `)`.

 - Karakterláncokat tartalmazó oszlopot: 
    - A következő operátor használata támogatott: `==`, `!=`



4. Futtassa a kísérletet.

    A kifejezés osztja fel az adatkészletet sorok két készletnyi: a feltételnek megfelelő értéket tartalmazó sorok, és az összes többi sorra.

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 