---
title: Oktatóanyag a Azure Data Box Disk visszaszállításához | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan küldheti vissza Azure Data Box Diskjét a Microsofthoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: ca42532fe6ba954180ce06ed0e3561e42f1fb447
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424233"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Oktatóanyag: Az Azure-ba való adatfeltöltés Azure Data Box Diskának visszaküldése és ellenőrzése

Ez a sorozat utolsó oktatóanyaga: Azure Data Box Disk üzembe helyezése. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A Data Box Disk elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Diskről

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta az [oktatóanyagot: Másolja az adatAzure Data Box Disk és ellenőrizze](data-box-disk-deploy-copy-data.md)a következőt:.


## <a name="ship-data-box-disk-back"></a>Data Box Disk visszaküldése

1. Miután az adatok érvényesítése befejeződött, válassza le a lemezeket. Távolítsa el a csatlakoztatott kábeleket.
2. Csomagolja be az összes lemezt és kábelt buborékfóliába, és helyezze őket a szállítási dobozba. Ha a tartozékok hiányoznak, díjkötelesek lehetnek.
    - A csomagolás újrafelhasználása a kezdeti szállítólevélből.  
    - Javasoljuk, hogy a lemezeket jól biztonságos, buborékokkal védett körbefuttatással csomagolja.
    - Győződjön meg arról, hogy a megfelelő illeszkedés a dobozon belüli mozgások csökkentése érdekében.

A következő lépéseket az eszköz visszaadása határozza meg.

### <a name="pick-up-in-us-canada"></a>Vegye fel velünk a kapcsolatot Kanadában

Ha az eszközt az USA-ban vagy Kanadában adja vissza, hajtsa végre a következő lépéseket.

1. Helyezze fel a fuvarlevélcímkét, amelyet a dobozhoz rögzített átlátszó műanyagzsebben talál. Ha a címke sérült vagy elveszett:
    - Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra.

        ![Fuvarlevélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Ez a művelet egy, az alábbiakban is látható, a visszaküldéshez szükséges fuvarlevélcímkét tölt le.

        ![Példa a fuvarlevélcímkére](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - A címke elhelyezése az eszközön.

2. Zárja le a szállítási dobozt, és győződjön meg arról, hogy a visszaküldési fuvarlevélcímke jól látható.
3. Pickupok beosztása a UPS-szel. Felvételi ütemterv:

    - Hívja meg a helyi UPS-t (ország/régió-specifikus díjmentes szám).
    - A hívásban adja meg a fordított szállítólevél nyomon követésének számát a nyomtatott címkén látható módon.
    - Ha a nyomkövetési szám nem található, a UPS-nek további díjat kell fizetnie a felvétel során.
    - A pickup ütemezése helyett a Data Box Disk a legközelebbi legördülő helyen is elvégezheti.


### <a name="pick-up-in-europe"></a>Felvétel Európában

Ha az eszközt Európa-ban adja vissza, hajtsa végre a következő lépéseket.

1. Helyezze fel a fuvarlevélcímkét, amelyet a dobozhoz rögzített átlátszó műanyagzsebben talál. Ha a címke sérült vagy elveszett:
    - Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra.

        ![Fuvarlevélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Ez a művelet egy, az alábbiakban is látható, a visszaküldéshez szükséges fuvarlevélcímkét tölt le.

        ![Példa a fuvarlevélcímkére](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - A címke elhelyezése az eszközön.

2. Zárja le a szállítási dobozt, és győződjön meg arról, hogy a visszaküldési fuvarlevélcímke jól látható.
3. Ha az eszközt Európán belül, DHL-lel szeretné visszaküldeni, látogasson el a DHL webhelyére, kérjen csomagfelvételt, és adja meg a légi fuvarlevél számát.
4. Nyissa meg az ország/régió DHL Express webhelyét, és válassza **a Courier Collection > EReturn szállítás**lehetőséget.

    ![DHL visszáru-szállítólevél](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Adja meg a fuvarlevél számát, és kattintson a **Futárrendelés** gombra a csomagfelvétel lefoglalásához.

      ![Átvétel ütemezése](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Felvétel az ázsiai-csendes-óceáni régióban

Ez a régió a japán, Korea, Ausztrália és Szingapúr felvételére vonatkozó utasításokat tartalmazza.

#### <a name="pick-up-in-australia"></a>Felvétel Ausztráliában

Az Ausztráliában található Azure-adatközpontok további biztonsági értesítésekkel rendelkeznek. Az összes bejövő szállítmánynak speciális értesítéssel kell rendelkeznie. Kövesse az alábbi lépéseket az Ausztráliában való felvételhez.

1. E `adbops@microsoft.com` -mail-cím, amely egyedi bejövő azonosítóval vagy a Tau-kóddal kéri a küldemények címkézését. Helyezze el a kérést legalább 3 nappal a tervezett szállítási dátum előtt, hogy időben beolvassa a címkét.
2. Az e-mail-tárgynak a-reverse típusú visszaszállítási címkéhez a *Tau-kóddal*kell rendelkeznie. Ügyeljen arra, hogy a következő adatokat tartalmazza az e-mailben: 

    - Rendelés neve
    - Cím
    - Kapcsolattartó neve

#### <a name="pick-up-in-japan"></a>Felvétel Japánban

1. Adja meg a cég nevét és a címadatokat a küldeményre vonatkozó információként a küldő adatai alapján.
2. E-mail-Quantium-megoldás a következő e-mail-sablon használatával.

    - Ha a Japan post Chakubarai-fuvarlevél nem tartalmazott vagy hiányzik, jegyezze fel ezt az e-mailt. A Quantium-megoldások Japán a postán kérik a küldeményt, hogy vegye fel a figyelmét a felvételre.
    - Ha több megrendelés is van, e-mailben gondoskodhat az egyéni felvételről.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box Disk｜Job Name： 
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

#### <a name="pick-up-in-korea"></a>Felvétel Koreában

1. Ügyeljen arra, hogy tartalmazza a visszaküldött szállítmány megjegyzését.
2. Ha a feladási Megjegyzés van jelen:
    1. Hívja meg az *Quantium Solutions nemzetközi* forródrótot az 070-8231-1418-as időszakra az irodai óra alatt (10 – 5 óráig, hétfőtől péntekig). Ajánlat *Microsoft Azure pickup* és a szolgáltatásra vonatkozó kérelem száma a gyűjtemény rendezéséhez.  
    2. Ha a forródrót foglalt, e `microsoft@rocketparcel.com`-mail-címet, az e-mail tárgyát *Microsoft Azure pickupot* és a szolgáltatási kérelem számát hivatkozásként.
    3. Ha a futár nem érkezik meg a gyűjteményhez, hívja a *Quantium Solutions nemzetközi* forródrótot alternatív megoldásokra. 
    4. E-mail-megerősítést kap a felvételi ütemtervhez.
3. Ezt a lépést csak akkor hajtsa végre, ha a fuvarlevél nem szerepel. Felvétel kérése:
    1. Hívja meg az *Quantium Solutions nemzetközi* forródrótot az 070-8231-1418-as időszakra az irodai óra alatt (10 – 5 óráig, hétfőtől péntekig). Ajánlat *Microsoft Azure pickup* és a szolgáltatásra vonatkozó kérelem száma a gyűjtemény rendezéséhez. Itt adhatja meg, hogy egy gyűjtemény elrendezéséhez új feljegyzésre van szükség. Adja meg a küldő (ügyfél), a fogadó adatait (Azure Datacenter) és a hivatkozási számot (szolgáltatási kérelem száma). 
    2. Ha a forródrót foglalt, e `microsoft@rocketparcel.com`-mail-címet, az e-mail tárgyát *Microsoft Azure pickupot* és a szolgáltatási kérelem számát hivatkozásként.
    3. Ha a futár nem érkezik meg a gyűjteményhez, hívja a *Quantium Solutions nemzetközi* forródrótot alternatív megoldásokra. 
    4. Ha a kérést telefonon keresztül kezdeményezik, a rendszer szóbeli megerősítést küld.

### <a name="pick-up-in-singapore"></a>Felvétel Szingapúrban

1. Nyomtassa ki a szállítási címkét, és csatolja a dobozhoz. Ha a címke sérült vagy elveszett:
    - Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra.

        ![Fuvarlevélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Ez a művelet egy, az alábbiakban is látható, a visszaküldéshez szükséges fuvarlevélcímkét tölt le.

        ![Példa a fuvarlevélcímkére](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - A címke elhelyezése az eszközön. Győződjön meg arról, hogy a felirat látható.

2. Felvétel kérése:
    - Hívja meg az **SingPost** forródrótot **6845 6485** -kor az irodai óra alatt (9 – 5 óra, hétfő és péntek között).  
    - A gyűjtemény rendezéséhez adja meg *Microsoft Azure* a pickupot és a szolgáltatási kérelem számát (a visszaszállítási címke nyomon követési számát). 
    - A felvételi ütemtervre vonatkozó szóbeli megerősítést fog kapni. 
    - Ha a futár nem érkezik meg a gyűjteményhez, a **SingPost** címen hívhatja meg a **6845 6485** -at Alternatív megoldásként. 
3. Adja át a futárnak. 


## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Miután a futár felvette a lemezeket, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

![Lemezek felvéve](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Amikor a Microsoft megkapja és átvizsgálja a lemezt, a feladat állapota **Megérkezett** értékre vált. 

![Lemezek átvéve](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Az adatok másolása automatikusan megtörténik, amint a lemezeket csatlakoztatják a kiszolgálóhoz az Azure-adatközpontban. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

![Adatok másolása befejezve](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Ha a másolás hibákkal fejeződött be, tekintse meg a [feltöltési hibák elhárítása](data-box-disk-troubleshoot-upload.md)című témakört.

Mielőtt törölné az eszközről az adatokat, ellenőrizze, hogy azok megtalálhatók-e a tárfiók(ok)ban. Az adatai a következőket vehetik fel:

- Azure Storage-fiókja (ke) t. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- A felügyelt lemez erőforrás-csoportja (i). A felügyelt lemezek létrehozásakor a virtuális merevlemezeket blobként kell feltölteni, majd felügyelt lemezekre konvertáljuk. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoporthoz vannak csatolva.

  - Ha az Azure-beli felügyelt lemezekre történő másolás sikeres volt, lépjen a Azure Portal **rendelés részleteire** , és jegyezze fel a felügyelt lemezek számára megadott erőforráscsoportot.

      ![Megrendelés részleteinek megtekintése](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Nyissa meg a feljegyzett erőforráscsoportot, és keresse meg a felügyelt lemezeket.

      ![Felügyelt lemezek erőforráscsoport](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Ha egy VHDX vagy egy dinamikus/különbséglemezek VHD-t másolt, akkor a VHDX/VHD-t a rendszer egy blokk-blobként feltölti az átmeneti tárolási fiókba. Nyissa meg az átmeneti **tárolási fiókot > blobokat** , majd válassza ki a megfelelő StandardSSD, StandardHDD vagy PremiumSSD. A VHDX/VHD-k blokk-blobként jelennek meg az átmeneti tárolási fiókban.

Az adatok Azure-ba történő feltöltését az alábbi lépésekkel ellenőrizheti:

1. Lépjen a lemezrendeléshez kapcsolódó tárfiókra.
2. Lépjen a **Blob szolgáltatás > Blobok tallózása** elemre. Itt megjelenik a tárolók listája. A *BlockBlob* és *PageBlob* mappában létrehozott almappákhoz hasonlóan a tárfiókban azonos névvel ellátott tárolók jöttek létre.
    Ha a mappanevek nem felelnek meg az Azure elnevezési konvencióinak, az Azure-ba történő adatfeltöltés sikertelen lesz.

4. Ha szeretné ellenőrizni, hogy a teljes adatkészlet fel lett-e töltve, használja a Microsoft Azure Storage Explorert. Csatlakoztassa a lemezbérlési rendeléshez tartozó tárfiókot, majd tekintse meg a blobtárolók listáját. Válasszon ki egy tárolót, kattintson a **Továbbiak**, majd a **Mappastatisztikák** lehetőségre. A **Tevékenységek** ablaktáblán megjelennek a mappára vonatkozó statisztikák (pl. a blobok száma és azok teljes mérete). A blobok teljes méretének meg kell egyeznie az adatkészlet méretével.

    ![A mappa statisztikái a Storage Explorerben](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Adatok törlése a Data Box Diskről

Miután a másolás befejeződött, és meggyőződött róla, hogy az adatok megtalálhatók az Azure Storage-fiókban, a lemezekről az NIST szabványnak megfelelően minden adat biztonságosan törlődik.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * A Data Box Disk elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Diskről


A következő oktatóanyag a Data Box Diskek Azure Portalon keresztül történő felügyeletét mutatja be.

> [!div class="nextstepaction"]
> [Azure Data Box Diskek kezelése az Azure Portal segítségével](./data-box-portal-ui-admin.md)

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>A lemez visszaküldése és az Azure-ba való feltöltés ellenőrzése

## <a name="ship-to-azure"></a>Elküldés az Azure-nak

1. Az adatellenőrzés befejezése után húzza ki a lemezeket. Távolítsa el a csatlakoztatott kábeleket.
2. Csomagolja be az összes lemezt és kábelt buborékfóliába, és helyezze őket a szállítási dobozba. Ha a tartozékok hiányoznak, díjkötelesek lehetnek.
    - A csomagolás újrafelhasználása a kezdeti szállítólevélből.  
    - Javasoljuk, hogy a lemezeket jól biztonságos, buborékokkal védett körbefuttatással csomagolja.
    - Győződjön meg arról, hogy a megfelelő illeszkedés a dobozon belüli mozgások csökkentése érdekében.
3. A következő lépéseket az eszköz visszaadása határozza meg.
    - [Ha az eszközt az Egyesült Államokban és Kanadában](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada)küldi el, a UPS-be ütemezhet.
    - Regisztráljon a [DHL for Europe szolgáltatásra](data-box-disk-deploy-picked-up.md#pick-up-in-europe) a webhelyének meglátogatásával és a légúti számla számának megadásával.
    - Az ausztráliai, csendes-óceáni régióban, például Ausztráliában, Japánban, Koreában és Szingapúrban [található országok számára is ütemezhet egy felvételt](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region) .
4. Miután a szállító bevette a lemezeket, a portál frissítései és a nyomkövetési azonosító megjelenik.

## <a name="verify-upload-to-azure"></a>Az Azure-ba való feltöltés ellenőrzése

Az adatok Azure-ba való feltöltése után ellenőrizze, hogy az adatok a Storage-fiók (ok) ban vannak-e, mielőtt törölné a forrásból. Az adatai a következőket vehetik fel:

- Azure Storage-fiókja (ke) t. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

    - **A blobok és az oldal Blobok esetében**: https://< storage_account_name >. blob. Core. Windows<containername>. net//files/a.txt

    - **Azure Files esetén**: https://< storage_account_name >. file. Core. Windows. net/<sharename>/Files/a.txt

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- A felügyelt lemez erőforrás-csoportja (i). A felügyelt lemezek létrehozásakor a virtuális merevlemezeket blobként kell feltölteni, majd felügyelt lemezekre konvertáljuk. A felügyelt lemezek a rendelés létrehozásakor megadott erőforráscsoporthoz vannak csatolva.

::: zone-end


