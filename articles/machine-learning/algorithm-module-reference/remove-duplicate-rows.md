---
title: 'Ismétlődő sorok eltávolítása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan távolíthatja el a lehetséges duplikált adatokat az adatkészletből a Azure Machine Learning szolgáltatás ismétlődő sorok eltávolítása moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b16e745de277d5aa262f1e1624df22f97d0cf29c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128527"
---
# <a name="remove-duplicate-rows-module"></a>Ismétlődő sorok moduljának eltávolítása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal távolíthatja el a lehetséges duplikált adatokat egy adatkészletből.

Tegyük fel például, hogy az adatok úgy néznek ki, mint a következő, és a betegek több rekordját jelöli. 

| PatientID | Monogram| Nem|Kor|Elismerte|
|----|----|----|----|----|
|1|F.M.| M| 53| jan.|
|2| F.A.M.| M| 53| jan.|
|3| F.A.M.| M| 24| jan.|
|3| F.M.| M| 24| feb.|
|4| F.M.| M| 23| feb.|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Egyértelmű, hogy ez a példa több, potenciálisan ismétlődő adattal rendelkező oszlopot tartalmaz. Függetlenül attól, hogy valóban duplikált elemek-e, az adatok ismerete függ. 

+ Előfordulhat például, hogy sok betegnek ugyanaz a neve. Nem távolítja el a duplikált elemeket bármely név oszlop használatával, csak az **azonosító** oszlopot. Így csak az ismétlődő azonosító értékekkel rendelkező sorok vannak kiszűrve, függetlenül attól, hogy a páciensek neve megegyezik-e.

+ Másik lehetőségként dönthet úgy is, hogy engedélyezi az ismétlődéseket az azonosító mezőben, és más fájlok kombinációját használja az egyedi rekordok, például az utónév, a vezetéknév, az életkor és a nemek kereséséhez.  

Ha meg szeretné határozni, hogy egy sor duplikált-e vagy sem, akkor adja meg a kulcsként használni kívánt egyetlen oszlopotvagy oszlopokat. Két sor csak akkor tekinthető ismétlődőnek, ha az **összes** kulcs oszlop értékei egyenlőek. Ha bármelyik sorban hiányzik a **kulcsok**értéke, nem lesznek ismétlődő sorok. Ha például a nemek és a kor értéke kulcsokként van beállítva a fenti táblázatban, a 6. és a 7. sor nem duplikált sorokból áll, mert az életkorban hiányzik az érték.

Amikor futtatja a modult, létrehoz egy jelölt adatkészletet, és olyan sorok halmazát adja vissza, amelyek nem rendelkeznek ismétlődéssel a megadott oszlopok között.

> [!IMPORTANT]
> A forrás-adatkészlet nem módosult; Ez a modul egy új adatkészletet hoz létre, amely a megadott feltételek alapján szűri a duplikált elemek kizárását.

## <a name="how-to-use-remove-duplicate-rows"></a>Ismétlődő sorok eltávolításának használata

1. Adja hozzá a modult a kísérlethez. Az **ismétlődő sorok eltávolítása** modult az adatátalakítás, a **manipuláció**lehetőség alatt találja.  

2. Kösse össze az adatkészletet, amelynek ismétlődő sorait szeretné megkeresni.

3. A **Tulajdonságok** ablaktáblában a **kulcs oszlop kiválasztása szűrő kifejezés**alatt kattintson az **oszlop kiválasztásának indítása**lehetőségre az ismétlődések azonosításához használandó oszlopok kiválasztásához.

    Ebben a kontextusban a **kulcs** nem egy egyedi azonosítót jelent. Az oszlop Választójának használatával kiválasztott összes oszlop **kulcs oszlopként**van megjelölve. Az összes nem kijelölt oszlop nem kulcsfontosságú oszlopnak minősül. A kulcsként kiválasztott oszlopok kombinációja határozza meg a rekordok egyediségét. (Úgy gondolja, hogy olyan SQL-utasításként működik, amely több egyenlő illesztést használ.)

    Példák:

    + "Szeretném biztosítani, hogy az azonosítók egyediek legyenek": Csak az azonosító oszlopot válassza.
    + "Szeretném biztosítani, hogy az utónév, a vezetéknév és az azonosító kombinációja egyedi legyen": Mindhárom oszlop kijelölése.

4. Az **első ismétlődő sor megőrzése** jelölőnégyzet bejelölésével jelezheti, hogy melyik sort kell visszaadnia az ismétlődések keresésekor:

    + Ha be van jelölve, a rendszer az első sort adja vissza, és a többi elvet. 
    + Ha törli ezt a beállítást, a rendszer az utolsó ismétlődő sort tárolja az eredmények között, másokat pedig elvet. 

5. Futtassa a kísérletet.

6. Az eredmények áttekintéséhez kattintson a jobb gombbal a modulra, válassza az **eredmények adatkészlet**lehetőséget, majd kattintson a **Megjelenítés**elemre. 

> [!TIP]
> Ha az eredmények nehezen érthetőek, vagy ha ki szeretne zárni egyes oszlopokat a megfontolásból, eltávolíthatja az oszlopokat az [adatkészlet kiválasztása](./select-columns-in-dataset.md) modulban.

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 