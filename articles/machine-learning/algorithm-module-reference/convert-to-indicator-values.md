---
title: Átalakítás mutatóértékekké
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Konvertálás indikátorértékekre modult az Azure Machine Learningben a kategorikus értékeket tartalmazó oszlopok bináris mutatóoszlopok sorozatává alakításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477663"
---
# <a name="convert-to-indicator-values"></a>Átalakítás mutatóértékekké
Ez a cikk az Azure Machine Learning-tervező modulját ismerteti.

Az Azure Machine Learning tervezőjének **Konvertálás indikátorérték-értékekre** moduljával a kategorikus értékeket tartalmazó oszlopokat bináris mutatóoszlopok sorozatává alakíthatja.  

Ez a modul a mutatóértékekké konvertáláshoz használt transzformáció definícióját is kiadja. Ezt az átalakítást más, ugyanazzal a sémával rendelkező adatkészleteken is felhasználhatja az [Átalakítás alkalmazása](apply-transformation.md) modul használatával.

## <a name="how-to-configure-convert-to-indicator-values"></a>A Konvertálás indikátorértékekké beállítás

1.  Keresse meg a **Konvertálás mutatóértékekké lehetőséget,** és húzza a folyamatvázlatra. Ez a modul az **Adattranszformáció** kategóriában található.
    > [!NOTE]
    > A [Metaadatok szerkesztése](edit-metadata.md) modult használhatja a **Konvertálás indiciator értékekké** modul előtt a céloszlop(ok) kategorikusként való megjelöléséhez.

1. Csatlakoztassa a **Konvertálás kijelzőértékekre** modult a konvertálni kívánt oszlopokat tartalmazó adatkészlethez. 

1. Válassza **a Szerkesztés oszlopot** egy vagy több kategorikus oszlop kiválasztásához.

1. Jelölje be a **Kategorikus oszlopok felülírása** jelölőnégyzetet, ha **csak** az új logikai oszlopokat szeretné kiadni. Alapértelmezés szerint ez a beállítás ki van kapcsolva.
    

    > [!TIP]
    >  Ha a felülírás i beállítását választja, a forrásoszlop ténylegesen nem törlődik vagy módosul. Ehelyett az új oszlopok jönnek létre, és megjelennek a kimeneti adatkészletben, és a forrásoszlop elérhető marad a munkaterületen. Ha meg szeretné tekinteni az eredeti adatokat, az [Oszlopok hozzáadása](add-columns.md) modulsegítségével bármikor hozzáadhatja a forrásoszlopot.

1. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

Tegyük fel, hogy van egy oszlopa, amely nek van egy olyan pontszáma, amely jelzi, hogy a kiszolgáló magas, közepes vagy alacsony hibavalószínűségű.T t you have a column in scores that indicate whether a server has a high, medium, or low sdak.  

| Kiszolgáló azonosítója | Hiba pontszám |
| --------- | ------------- |
| 10301     | Alacsony           |
| 10302     | Közepes        |
| 10303     | Magasság          |

A **Konvertálás mutatóértékekké**alkalmazásával a tervező egyetlen címkeoszlopot alakít át logikai értékeket tartalmazó oszlopokká:  

| Kiszolgáló azonosítója | Hiba pontszám - alacsony | Hiba pontszám - közepes | Hiba pontszám - Magas |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Az átalakítás a következőképpen működik:  

-   A **kockázatot** leíró Hiba pontszám oszlopban csak három lehetséges érték van (Magas, Közepes és Alacsony), és nincsenek hiányzó értékek. Tehát pontosan három új oszlop jön létre.  

-   Az új jelzőoszlopok neve a forrásoszlop oszlopfejlécei és értékei alapján lesz elnevezve, a következő minta használatával: * \<forrásoszlop>- \<adatérték>*.  

-   Pontosan egy mutatóoszlopban kell lennie 1-nek, és 0-nak az összes többi jelzőoszlopban, mivel minden kiszolgálónak csak egy kockázati besorolása lehet.  

Most már használhatja a három jelző oszlopok, mint a gépi tanulási modell funkciók.

A modul két kimenetet ad vissza:

- **Eredmények adatkészlet**: Átalakított kijelzőértékek oszlopokkal rendelkező adatkészlet. A tisztításra nem kiválasztott oszlopok is "áthaladnak".
- **Inikátorértékek átalakítása**: A mutatóértékekké konvertáláshoz használt adatátalakítás, amely a munkaterületre menthető és később új adatokra alkalmazható.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Mentett jelzőérték-művelet alkalmazása új adatokra

Ha gyakran kell megismételnie a jelzőértékek műveleteket, az adatmanipulációs lépéseket *átalakításként* mentheti, hogy újra felhasználhassa azt ugyanazzal az adatkészlettel. Ez akkor hasznos, ha gyakran újra kell importálnia, majd meg kell tisztítania az azonos sémával rendelkező adatokat.

1. Adja hozzá az [Átalakítás alkalmazása](apply-transformation.md) modult a folyamathoz.

1. Adja hozzá a tisztára szeretne menni, és csatlakoztassa az adatkészletet a jobb oldali bemeneti porthoz.

1. Bontsa ki az **Adattranszformáció** csoportot a tervező bal oldali ablaktáblájában. Keresse meg a mentett átalakítást, és húzza a folyamatba.

1. Csatlakoztassa a mentett átalakítást az [Átalakítás alkalmazása bal](apply-transformation.md)oldali bemeneti portjához.

   Mentett átalakítás alkalmazásakor nem választhatja ki az átalakítandó oszlopokat. Ennek az az oka, hogy az átalakítás definiálva van, és automatikusan vonatkozik az eredeti műveletben megadott adattípusokra.

1. Küldje el a folyamatot.
 
## <a name="technical-notes"></a>Technikai megjegyzések  

Ez a szakasz a megvalósítás részleteit, tippjeit és a gyakran feltett kérdésekre adott válaszokat tartalmazza.

### <a name="usage-tips"></a>Használati tippek

-   Csak a kategorikusként megjelölt oszlopok konvertálhatók jelzőoszlopokká. Ha a következő hiba jelenik meg, valószínű, hogy a kiválasztott oszlopok egyike nem kategorikus:  

     0056-os hiba: \<Oszlopnévvel ellátott oszlopnév> nem engedélyezett kategóriában van.  

     Alapértelmezés szerint a legtöbb karakterláncoszlop karakterlánc-szolgáltatásként lesz kezelve, ezért a [Metaadatok szerkesztése](edit-metadata.md)funkcióval explicit módon kategorikusként kell megjelölni őket.  

-   Nincs korlátozva, hogy hány oszlopot konvertálhat jelzőoszlopokká. Mivel azonban az értékek minden oszlopa több mutatóoszlopot is eredményezhet, célszerű egyszerre csak néhány oszlopot konvertálni és áttekinteni.  

-   Ha az oszlop hiányzó értékeket tartalmaz, egy külön mutatóoszlop jön létre a hiányzó kategóriához, ezzel a névvel: * \<forrásoszlop>- Hiányzó*  

-   Ha a mutatóértékekké konvertált oszlop számokat tartalmaz, azokat kategorikusként kell megjelölni, mint bármely más jellemzőoszlopot. Miután ezt megtette, a számokat a függvény különálló értékként kezeli. Ha például egy 25 és 30 közötti MPG-értékkel rendelkező numerikus oszlopot hoz létre, akkor minden különálló értékhez új jelzőoszlop jön létre:  

    | Gyártmány       | Autópálya mpg -25 | Autópálya mpg -26 | Autópálya mpg -27 | Autópálya mpg -28 | Autópálya mpg -29 | Autópálya mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso autók | 0               | 0               | 0               | 0               | 0               | 1               |

- Annak elkerülése érdekében, hogy túl sok dimenziót adjon az adatkészlethez. Javasoljuk, hogy először ellenőrizze az oszlopban lévő értékek számát, és megfelelően raktározhassa vagy kkkálja az adatokat.  


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
