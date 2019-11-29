---
title: Microsoft Azure Data Box Disk visszaküldésével kapcsolatos oktatóanyag | Microsoft Docs
description: Megtudhatja, hogyan küldheti vissza az Azure Data Boxot a Microsofthoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1c375370ec6bae2775ad758688825b92cbbbca50
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407026"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Oktatóanyag: Az Azure Data Box visszaküldése, majd az Azure-ba történő adatfeltöltés ellenőrzése

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>A Data Box visszaküldése, majd az Azure-ba történő adatfeltöltés ellenőrzése

::: zone-end

::: zone target="docs"

Az oktatóanyagból megtudhatja, hogyan küldheti vissza az Azure Data Boxot, és ellenőrizheti az adatok az Azure-ba való feltöltését.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- Elvégezte az [Oktatóanyag: Adatok másolása az Azure Data Boxra, majd ellenőrzése](data-box-deploy-copy-data.md) lépéseit. 
- A másolási feladatok befejeződtek. A szállítás előkészítése nem futtatható, ha másolási feladatok vannak folyamatban.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Az adatok másolásának befejeződése után előkészítheti és elküldheti az eszközt. Amikor az eszköz megérkezik az Azure-adatközpontba, az adatok automatikusan fel lesznek töltve az Azure-ba.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

A szállítás előkészítése előtt győződjön meg arról, hogy a másolási feladatok befejeződtek.

1. Nyissa meg a helyi webes kezelőfelület **Prepare to ship** (Szállítás előkészítése) lapját, és kezdje meg az előkészületeket a szállításra. 
2. Kapcsolja ki az eszközt a helyi webes kezelőfelületen. Válassza le a kábeleket az eszközről. 

A következő lépések attól függenek, hogy hol küldi vissza az eszközt.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Data Box visszaküldése

Győződjön meg arról, hogy az adatok eszközre történő másolása befejeződött, és a **Szállításra való előkészítés** futtatása sikeres volt. Az eljárások attól függnek, hogy hol küldi vissza az eszközt.

::: zone-end

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[Az Egyesült Államokban, Kanadában és Európában](#tab/in-us-canada-europe)

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]



::: zone-end


## <a name="in-australiatabin-australia"></a>[Ausztráliában](#tab/in-australia)

Az Ausztráliában található Azure-adatközpontok egy további biztonsági értesítéssel rendelkeznek. Az összes bejövő csomagnak előzetes értesítéssel kell rendelkeznie. Az Ausztráliában történő szállításhoz az alábbi lépéseket végezze el.


1. Őrizze meg az eszköz szállításához használt eredeti dobozt a visszaküldéshez.
2. Győződjön meg arról, hogy az adatok eszközre történő másolása befejeződött, és a **Szállításra való előkészítés** futtatása sikeres volt.
3. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
4. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
5. A [DHL-hivatkozásra](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) kattintva foglaljon egy csomagfelvételt az interneten.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="in-japantabin-japan"></a>[Japánban](#tab/in-japan) 

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end



