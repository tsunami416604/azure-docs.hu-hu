---
title: Értékek levágása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Clip Values modult az Azure Machine Learningben a kiugró értékek észlelésére és az értékek kivágására vagy cseréjére.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 6466cea9fe04bb308a670cb03fd3de5314758142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456607"
---
# <a name="clip-values"></a>Értékek levágása

Ez a cikk ismerteti az Azure Machine Learning designer modulját (előzetes verzió).

Az Értékek megvágása modul segítségével azonosíthatja és szükség esetén lecserélheti a megadott küszöbérték feletti vagy alatti adatértékeket egy átlaggal, állandóval vagy más helyettesítő értékkel.  

A modult olyan adatkészlethez csatlakoztatja, amely rendelkezik a klipolni kívánt számokkal, válassza ki a dolgozni kívánt oszlopokat, majd állítson be egy küszöbértéket vagy értéktartományt, valamint egy cseremódszert. A modul csak az eredményeket, vagy az eredeti adatkészlethez csatolt módosított értékeket adja ki.

## <a name="how-to-configure-clip-values"></a>A klipértékek beállítása

Mielőtt elkezdené, azonosítsa a klipolni kívánt oszlopokat és a használni kívánt módszert. Azt javasoljuk, hogy először tesztelje a vágási módszert az adatok egy kis részein.

A modul ugyanazokat a feltételeket és cseremódszert alkalmazza a kijelölésben megadott **összes** oszlopra. Ezért ügyeljen arra, hogy kizárja azokat az oszlopokat, amelyeket nem szeretne módosítani.

Ha egyes oszlopokra vágási módszereket vagy különböző feltételeket kell alkalmaznia, akkor a hasonló oszlopok minden egyes készletéhez a **Klipértékek** új példányát kell használnia.

1.  Adja hozzá a **Klipértékek** modult a folyamathoz, és csatlakoztassa a módosítani kívánt adatkészlethez. Ezt a modult az **Adatátalakítás**csoportban, a **Méretezés és csökkentés** kategóriában találja. 
  
1.  Az **Oszlopok listája mezőben**az Oszlopkijelölő vel válassza ki azokat az oszlopokat, amelyekre a **Klipértékek** lesznek alkalmazva.  
  
1.  A **Küszöbértékek beállítása**mezőben válasszon az alábbi lehetőségek közül a legördülő listából. Ezek a beállítások határozzák meg, hogyan állíthatja be az elfogadható értékek és a levágandó értékek felső és alsó határait.  
  
    - **ClipPeaks**: Ha csúcsok szerint vágja az értékeket, csak egy felső határvonalat ad meg. A határértéknél nagyobb értékeket a függvény lecseréli.
  
    -  **ClipSubpeaks**: Ha az értékeket mellékcsúcsok szerint vágja le, csak egy alsó határvonalat kell megadnia. A határértéknél kisebb értékeket a függvény lecseréli.  
  
    - **ClipPeaksAndSubpeaks**: Ha az értékeket csúcsok és alcsúcsok szerint vágja le, megadhatja mind a felső, mind az alsó határokat. A tartományon kívül rekedő értékek et cserélnek le. A határértékeknek megfelelő értékek nem változnak.
  
1.  Az előző lépésben megadott beállításoktól függően a következő küszöbértékeket állíthatja be: 

    + **Alsó küszöbérték**: Csak akkor jelenik meg, ha a **ClipSubPeaks lehetőséget választja**
    + **Felső küszöbérték**: Csak akkor jelenik meg, ha a **ClipPeaks lehetőséget választja**
    + **Küszöbérték**: Csak akkor jelenik meg, ha a **ClipPeaksAndSubPeaks lehetőséget választja**

    Minden küszöbértéktípushoz válassza az **Állandó** vagy **a Percentilis**lehetőséget.

1. Ha **az Állandó**lehetőséget választja, írja be a maximális vagy a minimális értéket a szövegmezőbe. Tegyük fel például, hogy a 999-es értéket placeholder értékként használták. A felső küszöbértékhez az **Állandó** lehetőséget választhatja, a 999 értéket pedig a **Felső küszöbérték Állandó érték mezőbe.**
  
1. Ha a **Percentilis lehetőséget választja,** az oszlopértékeket percentilis tartományra korlátozza. 

    Tegyük fel például, hogy csak a 10-80 százalékos tartományban lévő értékeket szeretné megtartani, és le szeretné cserélni a többiértéket. Válassza a **Percentilis ( Percentilis**) lehetőséget, majd írja be a 10 értéket az alsó **küszöbértékhez,** majd írja be a 80 értéket **a felső küszöbértékhez.** 

    A percentilis tartományok használatának néhány példáját a [percentilis](#examples-for-clipping-using-percentiles) tartományok használatának néhány részét lásd.  
  
1.  Adjon meg egy helyettesítő értéket.

    Azok a számok, amelyek pontosan megfelelnek a megadott határoknak, a megengedett értéktartományon belül vannak, és így nem kerülnek lecserélésre. A megadott tartományon kívül esik összes szám helyébe a helyettesítő érték lép. 
  
    + **Csúcsok helyettesítő értéke:** A megadott küszöbértéknél nagyobb oszlopértékek et helyettesítő értéket határozza meg.  
    + **Alcsúcsok helyettesítő értéke:** A megadott küszöbértéknél kisebb oszlopértékek helyettesítőjeként használandó értéket határozza meg.  
    + Ha a **ClipPeaksAndSubpeaks** beállítást használja, külön csereértékeket adhat meg a felső és az alsó levágott értékekhez.  

    A következő helyettesítő értékek támogatottak:  
  
    -   **Küszöbérték:** A levágott értékeket a megadott küszöbértékre cseréli.  
  
    -   **Átlag:** A levágott értékeket az oszlopértékek átlagára cseréli. Az átlag ot a levágás előtt kiszámítja a függvény.  
  
    -   **Medián**: A levágott értékeket az oszlopértékek mediánjára cseréli. A medián kiszámítása az értékek levágása előtt történik.   
  
    -   **Hiányzik**. A levágott értékeket a hiányzó (üres) értékre cseréli.  
  
1.  **Jelölőoszlopok hozzáadása**: Akkor válassza ezt a lehetőséget, ha olyan új oszlopot szeretne létrehozni, amely megmondja, hogy a megadott vágási művelet vonatkozik-e az adott sorban lévő adatokra. Ez a beállítás akkor hasznos, ha új vágási és helyettesítési értékeket tesztel.  
  
1. **Felülírás jelző**: Adja meg, hogyan szeretné létrehozni az új értékeket. Alapértelmezés szerint a **Clip Values** egy új oszlopot hoz létre, amelynek csúcsértékei a kívánt küszöbértékre vannak vágva. Az új értékek felülírják az eredeti oszlopot.  
  
    Ha meg szeretné tartani az eredeti oszlopot, és új oszlopot szeretne hozzáadni a levágott értékekkel, törölje a jelet a jelölőnégyzetből.  
  
1.  Küldje el a folyamatot.  
  
    Kattintson a jobb gombbal a **Clip Values** modulra, és válassza a **Visualize parancsot,** vagy válassza ki a **modult,** és váltson a jobb oldali panel Kimenetek fülére, kattintson a hisztogram ikonra a **Port kimeneteken,** hogy áttekintse az értékeket, és győződjön meg arról, hogy a vágási művelet megfelel az elvárásainak.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Példák a percentilisek használatával történő vágásra

A percentilisek általi vágás működésének megértéséhez fontolja meg egy 10 sorral rendelkező adatkészletet, amely nek 1–10-es értékük ggyel rendelkezik.  
  
- Ha a percentilist használja felső küszöbértékként, a 90.  
  
- Ha a percentilist használja alacsonyabb küszöbértékként, a 10. percentilis értékén él az adatkészlet összes értékének 10 százalékának kisebbnek kell lennie ennél az értéknél.  
  
1.  A **Küszöbértékek beállítása mezőben**válassza a **ClipPeaksAndSubPeaks**lehetőséget.  
  
1.  A **Felső küszöbérték**mezőbe válassza a **Percentilis**lehetőséget, a **Percentilis szám**mezőbe pedig a 90-es mezőt.  
  
1.  A **Felső helyettesítő érték (Felső helyettesítő érték)** területen válassza a Hiányzó érték **lehetőséget.**  
  
1.  **Az Alsó küszöbérték**mezőbe válassza a **Percentilis**lehetőséget, a **Percentilis szám**mezőbe pedig a 10-es mezőbe írja be a mezőt.  
  
1.  **Az Alsó helyettesítő érték (Alsó helyettesítő érték)** területen válassza a Hiányzó érték **lehetőséget.**  
  
1.  Törölje a jelet a **Jelző felülírása**jelölőnégyzetből, és válassza a **Jelölő hozzáadása oszlop lehetőséget.**  
  
Most próbálja meg ugyanazt a folyamatot, amely 60-at használ a felső percentilis küszöbértékként, és 30-at az alsó percentilis küszöbértékként, és használja a küszöbértéket helyettesítő értékként. Az alábbi táblázat a következő két eredményt hasonlítja össze:  
  
1.  Cserélje ki a hiányzó; Felső küszöbérték = 90; Alsó küszöbérték = 20  
  
1.  Cserélje ki a küszöbértéket; Felső percentilis = 60; Alsó percentilis = 40  
  
|Eredeti adatok|Csere a hiányzóra|Csere küszöbértékre|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|IGAZ<br /><br /> IGAZ<br /><br /> 3, HAMIS<br /><br /> 4, HAMIS<br /><br /> 5, HAMIS<br /><br /> 6, HAMIS<br /><br /> 7, HAMIS<br /><br /> 8, HAMIS<br /><br /> 9, HAMIS<br /><br /> IGAZ|4, IGAZ<br /><br /> 4, IGAZ<br /><br /> 4, IGAZ<br /><br /> 4, IGAZ<br /><br /> 5, HAMIS<br /><br /> 6, HAMIS<br /><br /> 7, IGAZ<br /><br /> 7, IGAZ<br /><br /> 7, IGAZ<br /><br /> 7, IGAZ| 
 
## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
