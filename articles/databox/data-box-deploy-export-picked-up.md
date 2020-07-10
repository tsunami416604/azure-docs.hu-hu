---
title: Oktatóanyag a Azure Data Box exportálási sorrendben történő szállításához | Microsoft Docs
description: Megtudhatja, hogyan szállíthatja a Azure Data Box a Microsoftnak az exportálási sorrend befejezése után
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 7023d29bcb559f4edf11b374b9bfb959e968f626
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86210402"
---
# <a name="tutorial-return-azure-data-box-preview"></a>Oktatóanyag: visszatérési Azure Data Box (előzetes verzió)


Ez az oktatóanyag azt ismerteti, hogyan lehet visszaadni Azure Data Box és az adattörlést, amint az eszköz beérkeznek az Azure-beli adatba.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Adatok törlése a Data Boxról

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Elvégezte az [oktatóanyagot: adatok másolása Azure Data Boxról ](data-box-deploy-export-copy-data.md). 
- A másolási feladatok befejeződtek. A szállítás előkészítése nem futtatható, ha másolási feladatok vannak folyamatban.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

A következő lépések attól függenek, hogy hol küldi vissza az eszközt.

## <a name="ship-data-box-back"></a>Data Box visszaküldése

Győződjön meg arról, hogy az eszközről származó adatok másolása befejeződött, és a **szállításra való előkészítés** futtatása sikeres volt. Az eljárások attól függnek, hogy hol küldi vissza az eszközt.

## <a name="in-us-canada-europe"></a>[Az Egyesült Államokban, Kanadában és Európában](#tab/in-us-canada-europe)

Az alábbi lépéseket akkor végezze el, ha az eszközt az Egyesült Államokban, Kanadában vagy Európában küldi vissza.

1. Győződjön meg arról, hogy az eszköz ki van kapcsolva, és a kábelek el lettek távolítva. 
2. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
3. Győződjön meg arról, hogy a fuvarlevélcímke látható az E-ink kijelzőn, és egyeztesse a csomagfelvételt a szállítóval. Ha a címke sérült, elveszett vagy nem jelenik meg az E-ink kijelzőn, vegye fel a kapcsolatot a Microsoft ügyfélszolgálatával. Ha az ügyfélszolgálat azt javasolja, lépjen az Azure Portalon az **Áttekintés > Fuvarlevélcímke letöltése** területre. Töltse le a fuvarlevélcímkét, és rögzítse az eszközre. 
4. Az eszköz visszaküldésekor egyeztessen egy csomagfelvételi időpontot a UPS-szel. Csomagfelvétel ütemezéséhez:

    - Hívja fel a helyi UPS-t (ország-/régióspecifikus ingyenes szám).
    - A hívás során adja meg az E-ink kijelzőn vagy a nyomtatott címkén látható visszaszállítási fuvarlevélcímke nyomkövetési számát.
    - Ha nem adja meg a nyomkövetési számot, felárat kell fizetnie a UPS-nek a csomagfelvételkor.

    A csomagfelvétel ütemezése helyett a legközelebbi átadóhelyen is leadhatja a Data Box Disket.
4. Miután a futár felvette és beolvasta a Data Boxot, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.


## <a name="in-australia"></a>[Ausztráliában](#tab/in-australia)

Az Ausztráliában található Azure-adatközpontok egy további biztonsági értesítéssel rendelkeznek. Az összes bejövő csomagnak előzetes értesítéssel kell rendelkeznie. Az Ausztráliában történő szállításhoz az alábbi lépéseket végezze el.


1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Győződjön meg arról, hogy az adatok eszközre történő másolása befejeződött, és a **Szállításra való előkészítés** futtatása sikeres volt.
3. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
4. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
5. A [DHL-hivatkozásra](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) kattintva foglaljon egy csomagfelvételt az interneten.

## <a name="in-japan"></a>[Japánban](#tab/in-japan) 

1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
3. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
4. A küldő információiként írja a vállalat nevét és címét a fuvarlevélre.
5. Küldjön e-mailt a Quantium Solutionsnek az alábbi e-mail-sablonnal.

    - Ha nem kapott japán postai Chakubarai-fuvarlevelet, vagy hiányzik, ezt jelezze az e-mailben. A Quantium Solutions Japan a csomagfelvételkor elküldi a fuvarlevelet a japán postával.
    - Ha több megrendelése van, küldjön e-mailt a külön felvételek érdekében.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

3. A csomagfelvétel lefoglalása után megerősítő e-mailt kap a Quantium Solutionstől. A megerősítő e-mail a Chakubarai-fuvarlevéllel kapcsolatos információkat is tartalmazza.

Szükség esetén a következő módokon léphet kapcsolatba a Quantium Solutions támogatásával (japán nyelven): 

- E-mail: Customerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 


## <a name="in-singapore"></a>[Szingapúrban](#tab/in-singapore) 

1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Jegyezze fel a nyomkövetési számot (ez a Data Box helyi webes felhasználói felületének „Prepare to Ship” (Szállítás előkészítése) lapján hivatkozási számként (reference number) jelenik meg). Ez akkor érhető el, amikor a szállítás-előkészítési lépés sikeresen befejeződik. Töltse le a fuvarlevélcímkét erről az oldalról, és rögzítse a dobozra. 
3. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
4. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában. 
5. Írjon e-mailt a SingPost ügyfélszolgálatának a következő sablonnal, és adja meg a nyomkövetési számot.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > A munkanapokon fogadott foglalási kérelmek esetében:
   > - 15 óra előtt a csomagfelvétel a következő munkanapon 9 és 13 óra között lesz.
   > - 15 óra előtt a csomagfelvétel a következő munkanapon 14 és 18 óra között lesz.  


## <a name="self-managed"></a>[Önállóan felügyelt](#tab/in-selfmanaged) 

Ha a Data Boxot Japánban, Szingapúrban, Koreában vagy Nyugat-Európában használja, és a rendelés létrehozása során a Self-Managed (Önállóan felügyelt) szállítás lehetőséget választotta, kövesse az alábbi utasításokat. 

1. Jegyezze fel a hitelesítési kódot (Authorization code), amely a Data Box helyi webes felhasználói felületének „Prepare to Ship” (Szállítás előkészítése) lapján jelenik meg a lépés sikeres befejeződése után.
2. Kapcsolja ki az eszközt, és távolítsa el a kábeleket. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljánál.
3. Az alábbi sablon használatával küldjön e-mailt az Azure Data Box üzemeltetési csapatának, ha készen áll az eszköz visszaküldésére.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
---

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Miután az eszköz eléri az Azure-adatközpontot, a Data Box a lemezén lévő adatmennyiséget a [NIST SP 800-88 változat 1 irányelveinek](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)megfelelően törli.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következő témaköröket:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Adatok törlése a Data Boxról

A következő cikkből megtudhatja, hogyan kezelheti Data Boxeit.

> [!div class="nextstepaction"]
> [Data Box kezelése Azure Portal használatával](./data-box-portal-admin.md)


