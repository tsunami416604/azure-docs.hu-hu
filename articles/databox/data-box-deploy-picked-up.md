---
title: Oktatóanyag a Azure Data Box visszaszállításához | Microsoft Docs
description: Megtudhatja, hogyan küldheti vissza az Azure Data Boxot a Microsofthoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 8/27/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 5b78dc8b815802502cda5baf2bf5e9646922d671
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241431"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Oktatóanyag: Az Azure-ba való adatfeltöltés Azure Data Boxának visszaküldése és ellenőrzése

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Az Azure-ba való adatfeltöltés Data Boxának visszaküldése és ellenőrzése

::: zone-end

::: zone target="docs"

Az oktatóanyagból megtudhatja, hogyan küldheti vissza az Azure Data Boxot, és ellenőrizheti az adatok az Azure-ba való feltöltését.

Ebben az oktatóanyagban az alábbi témaköröket ismerheti meg:

> [!div class="checklist"]
> * Előfeltételek
> * Szállításra való előkészítés
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy:

- Elvégezte az [oktatóanyagot: Másolja az adatAzure Data Box és ellenőrizze](data-box-deploy-copy-data.md)a következőt:. 
- A másolási feladatok készek. A szállításra való előkészítés nem futtatható, ha a másolási feladatok folyamatban vannak.

## <a name="prepare-to-ship"></a>Szállításra való előkészítés

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Az Adatmásolás befejezése után előkészíti és elszállítja az eszközt. Amikor az eszköz eléri az Azure-adatközpontot, az automatikusan feltölti az Azure-ba.

## <a name="prepare-to-ship"></a>Szállításra való előkészítés

A szállítás előkészítése előtt győződjön meg arról, hogy a másolási feladatok készek.

1. Nyissa meg a helyi webes kezelőfelület **Prepare to ship** (Szállítás előkészítése) lapját, és kezdje meg az előkészületeket a szállításra. 
2. Kapcsolja ki az eszközt a helyi webes kezelőfelületen. Válassza le a kábeleket az eszközről. 

A következő lépéseket az eszköz visszaadása határozza meg.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Data Box visszaküldése

Győződjön meg arról, hogy az eszközön lévő Adatmásolás befejeződött, és a **szállításra való előkészítés** futtatása sikeres. Az eljárás különbözik attól függően, hogy az eszköz melyik régióból szállítja az eszközt.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>USA, Kanada és Európa szállítása

Hajtsa végre az alábbi lépéseket, ha az eszközt az Egyesült Államokban, Kanadában vagy Európában adja vissza.

1. Győződjön meg arról, hogy az eszköz ki van kapcsolva, és a kábelek el lesznek távolítva. 
2. Tekerje fel, és biztonságosan helyezze el az eszközhöz mellékelt tápkábelt az eszköz hátuljában.
3. Győződjön meg arról, hogy a fuvarlevélcímke látható az E-ink kijelzőn, és egyeztesse a csomagfelvételt a szállítóval. Ha a címke sérült vagy elveszett, vagy nem jelenik meg az E-Ink megjelenítésnél, forduljon a Microsoft ügyfélszolgálatahoz. Ha a támogatás azt sugallja, az Áttekintés > a Azure Portal a **szállítási címke letöltése** lehetőségre. Töltse le a szállítási címkét, és helyezze el az eszközön. 
4. Ha az eszközt visszaküldi, a UPS-sel ütemezhet egy felvételt. Felvételi ütemterv:

    - Hívja meg a helyi UPS-t (ország/régió-specifikus díjmentes szám).
    - A hívásban adja meg a fordított szállítólevél nyomon követésének számát az E-Ink-megjelenítésben vagy a nyomtatott címkén látható módon.
    - Ha a nyomkövetési szám nem található, a UPS-nek további díjat kell fizetnie a felvétel során.

    A pickup ütemezése helyett a Data Box a legközelebbi legördülő helyen is elvégezheti.
4. Miután a futár felvette és beolvasta a Data Boxot, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.


## <a name="ship-in-australia"></a>Szállítás Ausztráliában

Az Ausztráliában található Azure-adatközpontok további biztonsági értesítésekkel rendelkeznek. Az összes bejövő szállítmánynak speciális értesítéssel kell rendelkeznie. Végezze el az alábbi lépéseket Ausztráliában való szállításhoz.


1. Őrizze meg az eszköz a visszaszállításra való szállításához használt eredeti mezőt.
2. Győződjön meg arról, hogy az eszközön lévő Adatmásolás befejeződött, és a **szállításra való előkészítés futtatása** sikeres.
3. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
4. A nyomtatásisor-kezelő és az eszköz hátulján lévő eszközzel biztosított tápkábelt helyezze el biztonságosan.
5. E-mail-Quantium-megoldások pickup igényléséhez. Tekintse át a Azure Portal megadott szolgáltatási hivatkozási számot. Használja a következő e-mail-sablont:- *Request for visszirányú Shipping címke a Tau-kóddal*. Ügyeljen arra, hogy a következő adatokat tartalmazza az e-mailben: 

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
6. A Quantium Solutions Ausztrália e-mailben küldi el a visszaküldött szállítási címkéket.
7. Nyomtassa ki a visszatérési címkét, és helyezze el a szállítás mezőben.
8. Adja át a csomagot a futárnak.

Ha szükséges, e-mailben Quantium a megoldás támogatását a Azure@quantiumsolutions.com vagy telefonon.

A telefonon keresztüli rendeléssel kapcsolatos lekérdezés:

- Először küldjön egy e-mailt a felvételhez.
- Adja meg a rendelés nevét a telefonon.

## <a name="ship-in-japan"></a>Szállítás Japánban 

1. Őrizze meg az eszköz a visszaszállításra való szállításához használt eredeti mezőt.
2. Kapcsolja ki az eszközt, és távolítsa el a kábeleket.
3. A nyomtatásisor-kezelő és az eszköz hátulján lévő eszközzel biztosított tápkábelt helyezze el biztonságosan.
4. Adja meg a cég nevét és a címadatokat a küldeményre vonatkozó információként a küldő adatai alapján.
5. E-mail-Quantium-megoldás a következő e-mail-sablon használatával.

    - Ha a Japan post Chakubarai-fuvarlevél nem tartalmazott vagy hiányzik, jegyezze fel ezt az e-mailt. A Quantium-megoldások Japán a postán kérik a küldeményt, hogy vegye fel a figyelmét a felvételre.
    - Ha több megrendelés is van, e-mailben gondoskodhat az egyéni felvételről.

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

3. A Quantium-megoldásoktól kapott e-mail-visszaigazolást, miután elkönyvelt egy felvételt. Az e-mail-visszaigazolás a Chakubarai-szállítmány megjegyzésével kapcsolatos információkat is tartalmaz.

Ha szükséges, lépjen kapcsolatba a Quantium-megoldás támogatásával (Japán nyelvvel) a következő információkkal: 

- E-mailCustomerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Amikor a Microsoft megkapja és beolvassa az eszközt, a rendelés **Megérkezett** állapotra vált. Az eszközt ezután fizikai ellenőrzésnek vetjük alá, sérüléseket vagy illetéktelen hozzáférés jeleit kutatva.

Az ellenőrzés végeztével a Data Boxot csatlakoztatjuk a hálózatra az Azure-adatközpontban. Az adatok másolása automatikusan megkezdődik. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

Győződjön meg arról, hogy az adatok fel vannak töltve az Azure-ba, mielőtt törölné a forrásból. Az adatai a következőket vehetik fel:

- Azure Storage-fiókja (ke) t. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- A felügyelt lemez erőforrás-csoportja (i). A felügyelt lemezek létrehozásakor a virtuális merevlemezeket blobként kell feltölteni, majd felügyelt lemezekre konvertáljuk. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoporthoz vannak csatolva. 

    - Ha az Azure-beli felügyelt lemezekre történő másolás sikeres volt, lépjen a Azure Portal **rendelés részleteibe** , és jegyezze fel a felügyelt lemezekhez megadott erőforráscsoportokat.

        ![Felügyelt lemezes erőforráscsoportok azonosítása](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Nyissa meg a feljegyzett erőforráscsoportot, és keresse meg a felügyelt lemezeket.

        ![Erőforráscsoporthoz csatolt felügyelt lemez](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Ha egy VHDX vagy egy dinamikus/különbséglemezek VHD-t másolt, akkor a VHDX/VHD-t oldal blobként kell feltölteni az átmeneti tárolóba, de a VHD-t felügyelt lemezre való átalakítás meghiúsul. Nyissa meg az átmeneti **tárolási fiókot > blobokat** , majd válassza ki a megfelelő tároló-standard SSD, standard HDD vagy prémium SSD. A virtuális merevlemezeket a rendszer az átmeneti tárolási fiókban lévő blobként töltse fel.

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Adatok törlése a Data Boxról
 
Ha az adatok az Azure-ba történő feltöltése befejeződött, a Data Box törli a lemezeken található adatokat a [NIST SP 800-88 Revision 1 útmutatásainak](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi) megfelelően.

::: zone target="docs"

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Szállításra való előkészítés
> * A Data Box elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Boxról

A következő cikk a Data Box a helyi webes felületen keresztül történő felügyeletét mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box felügyelete a helyi webes felhasználói felülettel](./data-box-local-web-ui-admin.md)

::: zone-end


