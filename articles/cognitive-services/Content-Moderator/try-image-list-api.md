---
title: Képek moderálása egyéni listákkal és API-konzollal – Tartalommoderátor
titleSuffix: Azure Content Moderator
description: Az Azure Content Moderator listakezelési API-ját a képek egyéni listáinak létrehozásához használhatja.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 27d9b12d9e1a0237050243c2b5f07edaa8d8857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757193"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Az API-konzol egyéni képlistáival mérsékelt

Az Azure Content Moderator [listakezelési API-ját](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) a képek egyéni listáinak létrehozásához használhatja. Használja a képek egyéni listáit a Képmoderálás API-val. A képmoderálási művelet kiértékeli a képet. Ha egyéni listákat hoz létre, a művelet összehasonlítja azokat az egyéni listákban szereplő képekkel is. Az egyéni listák segítségével letilthatja vagy engedélyezheti a képet.

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

A Listakezelés API-t a következő feladatok elvégzésére használhatja:

- Lista létrehozása.
- Képek hozzáadása listához.
- Képernyőképek a listában szereplő képekhez.
- Képek törlése a listából.
- Lista törlése.
- Listaadatok szerkesztése.
- Frissítse az indexet, hogy az új beolvasások észleljék a lista módosításait.

## <a name="use-the-api-console"></a>Az API-konzol használata
Mielőtt tesztelheti az API-t az online konzolon, szüksége van az előfizetési kulcsra. Ez a **Beállítások** lapon, az **Ocp-Apim-Subscription-Key** mezőben található. További információkért lásd az [Áttekintést](overview.md).

## <a name="refresh-search-index"></a>Keresési index frissítése

Miután módosította a képlistát, frissítenie kell az indexét, hogy a módosítások bekerüljenek a későbbi vizsgálatokba. Ez a lépés hasonló ahhoz, ahogyan egy keresőmotor az asztalon (ha engedélyezve van) vagy egy webes keresőmotor folyamatosan frissíti az indexét, hogy új fájlokat vagy oldalakat tartalmazzon.

1. A [Képlista kezelése API-hivatkozásban](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)válassza a bal oldali menü **Képlisták**parancsát, majd a **Keresési tárgymutató frissítése**lehetőséget.

   Megnyílik **a Képlisták - Keresési tárgy index frissítése** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 
 
    ![Képlisták – Keresési index oldalterület-kijelölés frissítése](images/test-drive-region.png)

    Megnyílik **a Képlisták – Frissítés keresési tárgyú** API-konzol.

3. A **listOló mezőbe** írja be a listaazonosítót. Írja be az előfizetési kulcsot, és válassza a **Küldés lehetőséget.**

   ![Képlisták – Keresési index konzolválasz tartalmának frissítése](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Képlista létrehozása

1. Nyissa meg a [Képlista-kezelés API-hivatkozását.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)

   Megnyílik **a Képlisták – Létrehozás** lap. 

3. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét.

   ![Képlisták – Oldalterület-kijelölés létrehozása](images/test-drive-region.png)

   Megnyílik **a képlisták –** API-konzol létrehozása.
 
4. Az **Ocp-Apim-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

5. A **Kérelem törzsmezőbe** írja be a **Név** (például MyList) és a **Leírás**értékét.

   ![Képlisták – Konzolkérelem törzsének és leírásának létrehozása](images/try-terms-list-create-1.png)

6. A kulcsérték párhelyőrzőivel több leíró metaadatot rendelhet a listához.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Listametaadatok hozzáadása kulcsérték-párokként, nem pedig a tényleges képek.
 
7. Válassza a **Küldés**lehetőséget. Létrejön a lista. Jegyezze fel az új listához társított **azonosítóértéket.** Szüksége van erre az azonosítóra más képlista-kezelő funkciókhoz.

   ![Képlisták – A konzolválasz tartalmának létrehozása mező a listaazonosítót jeleníti meg](images/try-terms-list-create-2.png)
 
8. Ezután adjon hozzá képeket a MyList listéhez. A bal oldali menüben válassza a **Kép**lehetőséget, majd a **Kép hozzáadása parancsot.**

   Megnyílik **a Kép hozzáadása** lap. 

9. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét.

   ![Kép – Képoldalterület kijelölésének hozzáadása](images/test-drive-region.png)

   Megnyílik **a Image - Add Image** API konzol.
 
10. A **listId** mezőbe írja be a létrehozott listaazonosítót, majd adja meg a hozzáadni kívánt kép URL-címét. Írja be az előfizetési kulcsot, és válassza a **Küldés lehetőséget.**

11. Annak ellenőrzéséhez, hogy a kép hozzá lett-e adva a listához, a bal oldali menüben válassza a **Kép**lehetőséget, majd az **Összes képazonosító beszerzői parancsát.**

    Megnyílik **a Image - Get All Image Ids** API-konzol.
  
12. A **listId** mezőbe írja be a listaazonosítót, majd írja be az előfizetési kulcsot. Válassza a **Küldés**lehetőséget.

    ![Kép - Get All Image Ids konzol Válasz tartalom doboz felsorolja a képeket, hogy a megadott](images/try-image-list-create-11.png)
 
10. Adjon hozzá még néhány képet. Most, hogy létrehozott egy egyéni képlistát, próbálja meg kiértékelni a [képeket](try-image-api.md) az egyéni képlista használatával. 

## <a name="delete-images-and-lists"></a>Képek és listák törlése

Egy kép vagy lista törlése egyszerű. Az API-val a következő feladatokat teheti meg:

- Rendszerkép törlése. (**Kép - Törlés**)
- A lista törlése nélkül törölje a listában szereplő összes képet. (**Kép - Az összes kép törlése**)
- Egy lista törlése annak teljes tartalmával együtt. (**Képlisták - Törlés**)

Ez a példa egyetlen képet töröl:

1. A [Képlista kezelése API-hivatkozásban](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)válassza a bal oldali menü **Kép**parancsát, majd a **Törlés parancsot.** 

   Megnyílik **a Kép - Törlés** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

   ![Kép – Oldalterület kijelölésének törlése](images/test-drive-region.png)
 
   Megnyílik **a Image - Delete** API console.
 
3. A **listId** mezőbe írja be annak a listának az azonosítóját, amelyből képet szeretne törölni.  Ez a kép - A MyList **összes képazonosítójának beszerzői** konzolján visszaadott szám. Ezután írja be a törölni kívánt kép **ImageId azonosítóját.** 

A példánkban a lista azonosítója **58953**, a **ContentSource**értéke. A képazonosító **értéke 59021**, a **ContentIds**értéke.

1. Írja be az előfizetési kulcsot, és válassza a **Küldés lehetőséget.**

1. A kép törlésének ellenőrzéséhez használja a **Kép – Minden képazonosító beszerezni konzolját.**
 
## <a name="change-list-information"></a>Listaadatok módosítása

Módosíthatja a lista nevét és leírását, és metaadat-elemeket adhat hozzá.

1. A [Képlista kezelése API-hivatkozásban](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)válassza a bal oldali menü **Képlisták**parancsát, majd a **Részletek frissítése parancsot.** 

   Megnyílik **a Képlisták – Részletek frissítése** lap.

2. Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét.  

    ![Képlisták – Részletek frissítése lapterület-kijelölés](images/test-drive-region.png)

    Megnyílik **a Képlisták – Részletek frissítése** API-konzol.
 
3. A **listId** mezőbe írja be a listaazonosítót, majd írja be az előfizetési kulcsot.

4. A **Testtest kérése párbeszédpanelen** tegye meg a módosításokat, majd kattintson a **küldés** gombra a lapon.

   ![Képlisták – Részletek frissítése konzol Törzsszerkesztések kérése](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>További lépések

Használja a REST API-t a kódban, vagy kezdje a [lemezképlisták .NET gyorsindítással](image-lists-quickstart-dotnet.md) az alkalmazásba való integráláshoz.
