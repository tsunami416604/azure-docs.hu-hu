---
title: Az oktatóanyag az Azure Data Box való visszaküldésére |} A Microsoft Docs
description: Megtudhatja, hogyan küldheti vissza az Azure Data Boxot a Microsofthoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 7/08/2019
ms.author: alkohli
ms.openlocfilehash: db0f0ac3073687b7c1cd8ca60e459e4bb3aa03f4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626360"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Oktatóanyag: Vissza az Azure Data Box, és ellenőrizze az adatok feltöltése az Azure-bA

Az oktatóanyagból megtudhatja, hogyan küldheti vissza az Azure Data Boxot, és ellenőrizheti az adatok az Azure-ba való feltöltését.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:

- Már elvégezte a [oktatóanyag: Adatok másolása az Azure Data Box, és ellenőrizze](data-box-deploy-copy-data.md). 
- Másolás feladatok elkészültek. Szállításra való nem futtatható, ha a másolási feladat folyamatban van.

## <a name="prepare-to-ship"></a>A szállítás előkészítése

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Data Box visszaküldése

Győződjön meg arról, hogy helyesek-e az adatok másolása eszköz és **szállításra való** Futtatás sikeres. A régió, ahol az eszköz szállítja alapján, az eljárás eltér.


### <a name="ship-in-us-canada-europe"></a>Szállításra, Kanada, USA Európában

Ha az eszköz visszaadása az USA, Kanada vagy Európában, tegye a következőket.

1. Győződjön meg arról, hogy az eszköz be van kapcsolva, és kábelek el lesznek távolítva. 
2. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
3. Győződjön meg arról, hogy a fuvarlevélcímke látható az E-ink kijelzőn, és egyeztesse a csomagfelvételt a szállítóval. Ha a címke megsérült vagy elveszett vagy nem jelenik meg az E-ink megjelenő, forduljon a Microsoft Support. A támogatási javasol, akkor elvégezheti **áttekintés > levélcímke letöltése** az Azure Portalon. Töltse le a szállítási címkét, és elhelyezi az eszközön. 
4. Ha az eszközök visszatérő, ütemezhet a UPS begyűjtést. Ütemezhet begyűjtést:

    - Hívja meg a helyi UPS (ország/régió-specifikus díjmentesen ingyenes szám).
    - A hívás az ajánlat a fordított szállítmány követési szám az E-ink megjelenítési vagy a nyomtatott felirat látható módon.
    - A nyomkövetési azonosító szám není uveden v uvozovkách, ha a UPS kell, hogy külön díj fizessen a begyűjtés során.

    Ahelyett, hogy a begyűjtés ütemezés is húzhatja a Data Box, a legközelebbi gyűjtőhely ki.
4. Miután a futár felvette és beolvasta a Data Boxot, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

### <a name="ship-in-asia-pacific-region"></a>Ázsia és csendes-óceáni régióban szállításra

#### <a name="ship-in-australia"></a>Az ausztráliai szállításra

Az ausztráliai Azure-adatközpontok rendelkezik egy kiegészítő biztonsági értesítést. A bejövő szállítmányok rendelkeznie kell egy speciális értesítést. Az alábbi lépéseket az ausztráliai szállításra.


1. Az eredeti mezőben használja a visszaszállításhoz az eszköz szállításra való megőrzése.
2. Győződjön meg arról, hogy helyesek-e az adatok eszközre való másolását, és **Futtatás szállításra való** sikeres volt.
3. Kapcsolja ki az eszközt, és távolítsa el a kábelek.
4. Várólistában hozzá tartozó, és biztonságosan helyezze el a tápkábelt az eszközhöz hátulján az eszköz a megadott.
5. E-mailek Quantium megoldások kérni. Tekintse meg a referencia az Azure Portalon megadott száma. A következő e-mail-sablont használja:- *fordított levélcímke TAU kódra vonatkozó kérelem*. Ügyeljen arra, hogy az e-mailben a következő részleteket tartalmazza: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium megoldások Ausztrália fog e-mailben egy visszaszállítási címkét.
7. A visszaadott címke a nyomtató- és elhelyezi azt a a szállítás mezőbe.
8. Kézi keresztül a csomag, a courier.

Ha szükséges, írhat e-mailt Quantium megoldás támogatását Azure@quantiumsolutions.com vagy telefonszámát.


Lekérdezési telefonon keresztül a rendelés vonatkozóan:

- Először a felvételre e-mail küldése.
- Adja meg a rendelés nevét a telefonon.

#### <a name="ship-in-japan"></a>A japán szállításra 

1. Az eredeti mezőben használja a visszaszállításhoz az eszköz szállításra való megőrzése.
2. Kapcsolja ki az eszközt, és távolítsa el a kábelek.
3. Várólistában hozzá tartozó, és biztonságosan helyezze el a tápkábelt az eszközhöz hátulján az eszköz a megadott.
4. A küldő adatai, a kísérő a nevét és címét adatokat írnak a vállalata.
5. Az e-mail-Quantium megoldás az alábbi e-mail-sablon használatával.

    - Ha a japán Post Chakubarai kísérő nem tartalmaz, vagy nincs megadva, vegye figyelembe, hogy az e-mail tartalma. Quantium megoldások japán japán Post ahhoz, hogy a kísérő felvétel után fog igényelni.
    - Ha több megrendelések, az e-mailben, győződjön meg, hogy az egyes begyűjtést.

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

3. Egy megerősítő e-mailt kap a Quantium megoldások után, de már begyűjtést. A megerősítő e-mailt a Chakubarai kísérő információkat is biztosít.

Szükség esetén is ügyfélszolgálatot Quantium megoldás (japán nyelv), a következő információkat: 

- E-mail cím:Customerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 


## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor a Microsoft megkapja és beolvassa az eszközt, a rendelés **Megérkezett** állapotra vált. Az eszközt ezután fizikai ellenőrzésnek vetjük alá, sérüléseket vagy illetéktelen hozzáférés jeleit kutatva.

Az ellenőrzés végeztével a Data Boxot csatlakoztatjuk a hálózatra az Azure-adatközpontban. Az adatok másolása automatikusan megkezdődik. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

Győződjön meg arról, hogy az adatok feltöltése az Azure-ba, a forrás-törlés előtt. Az adatok lehetnek:

- Az Azure Storage-fiókok. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- A felügyelt lemez erőforráscsoport(ok). Felügyelt lemezek létrehozásakor a virtuális merevlemezeket lapblobként feltöltött és majd a felügyelt lemezekké való konvertálása. A felügyelt lemezek vannak csatolva az erőforráscsoportok, a megadott sorrendben létrehozásának időpontjában. 

    - Ha a példány az Azure-ban felügyelt lemezekre sikeres volt, megnyithatja a **rendelés részletei** az Azure Portalon, és jegyezze fel az erőforráscsoportokat a felügyelt lemezek megadott ügyeljen.

        ![Felügyelt lemez az erőforrás-csoportok azonosítása](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Nyissa meg a feljegyzett erőforráscsoportot, és keresse meg a felügyelt lemezek.

        ![Felügyelt erőforráscsoport csatlakoztatott lemez](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Ha egy dinamikus vagy különbséglemez VHD vagy vhdx-fájlt másolja, majd a VHDX-/ VHD töltenek fel egy lapblob, de a virtuális merevlemez átalakítása felügyelt lemez meghibásodik, az előkészítési tárfiókból. Nyissa meg az átmeneti **tárfiók > Blobok** , és válassza ki a megfelelő tárolót – Standard SSD, Standard HDD vagy prémium SSD-re. A virtuális merevlemezeket lapblobként az átmeneti tárfiókban lesznek feltöltve.

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A szállítás előkészítése
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

A következő cikk a Data Box a helyi webes felületen keresztül történő felügyeletét mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box felügyelete a helyi webes felhasználói felülettel](./data-box-local-web-ui-admin.md)


