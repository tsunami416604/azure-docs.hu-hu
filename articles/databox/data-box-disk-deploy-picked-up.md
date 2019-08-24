---
title: Oktatóanyag a Azure Data Box Disk visszaszállításához | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan küldheti vissza Azure Data Box Diskjét a Microsofthoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 08/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6dcd55cc2c269024179e450d789df544bb8ff9a9
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012921"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Oktatóanyag: Visszatérési Azure Data Box Disk 

Ez az oktatóanyag azt ismerteti, hogyan ütemezhet beléptetést a Azure Data Box Disk visszaadásához. A pick up utasítás attól függ, hogy hol adja vissza az eszközt. 

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A Data Box Disk elküldése a Microsoftnak
> * Data Box Disk különböző régiókban való kiválasztása


## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta az [oktatóanyagot: Másolja az adatAzure Data Box Disk és ellenőrizze](data-box-disk-deploy-copy-data.md)a következőt:.


## <a name="ship-data-box-disk-back"></a>Data Box Disk visszaküldése

1. Miután az adatok érvényesítése befejeződött, válassza le a lemezeket. Távolítsa el a csatlakoztatott kábeleket.
2. Csomagolja be az összes lemezt és kábelt buborékfóliába, és helyezze őket a szállítási dobozba. Ha a tartozékok hiányoznak, díjkötelesek lehetnek.
    - A csomagolás újrafelhasználása a kezdeti szállítólevélből.  
    - Javasoljuk, hogy a lemezeket jól biztonságos, buborékokkal védett körbefuttatással csomagolja.
    - Győződjön meg arról, hogy a megfelelő illeszkedés a dobozon belüli mozgások csökkentése érdekében.

A következő lépéseket az eszköz visszaadása határozza meg.

## <a name="pick-up-in-us-canada"></a>Vegye fel velünk a kapcsolatot Kanadában

Ha az eszközt az USA-ban vagy Kanadában adja vissza, hajtsa végre a következő lépéseket.

1. Helyezze fel a fuvarlevélcímkét, amelyet a dobozhoz rögzített átlátszó műanyagzsebben talál. Ha a címke sérült vagy elveszett:
    - Nyissa meg az **áttekintés > a szállítási címke letöltése** és a visszaszállítási címke letöltése lehetőséget.

        ![Levélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    - A címke elhelyezése az eszközön.

2. Zárja le a szállítási dobozt, és győződjön meg arról, hogy a visszaküldési fuvarlevélcímke jól látható.
3. Pickupok beosztása a UPS-szel. Felvételi ütemterv:

    - Hívja meg a helyi UPS-t (ország/régió-specifikus díjmentes szám).
    - A hívásban adja meg a fordított szállítólevél nyomon követésének számát a nyomtatott címkén látható módon.
    - Ha a nyomkövetési szám nem található, a UPS-nek további díjat kell fizetnie a felvétel során.
    - A pickup ütemezése helyett a Data Box Disk a legközelebbi legördülő helyen is elvégezheti.


## <a name="pick-up-in-europe"></a>Felvétel Európában

Ha az eszközt Európa-ban adja vissza, hajtsa végre a következő lépéseket.

1. Helyezze fel a fuvarlevélcímkét, amelyet a dobozhoz rögzített átlátszó műanyagzsebben talál. Ha a címke sérült vagy elveszett:
    - Nyissa meg az **áttekintés > a szállítási címke letöltése** és a visszaszállítási címke letöltése lehetőséget.

        ![Levélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    - A címke elhelyezése az eszközön.

2. Zárja le a szállítási dobozt, és győződjön meg arról, hogy a visszaküldési fuvarlevélcímke jól látható.
3. Ha az eszközt Európán belül, DHL-lel szeretné visszaküldeni, látogasson el a DHL webhelyére, kérjen csomagfelvételt, és adja meg a légi fuvarlevél számát.
4. Nyissa meg az ország/régió DHL Express webhelyét, és válassza **a Courier Collection > EReturn szállítás**lehetőséget.

    ![DHL visszáru-szállítólevél](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Adja meg a fuvarlevél számát, és kattintson a **Futárrendelés** gombra a csomagfelvétel lefoglalásához.

      ![Átvétel ütemezése](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

## <a name="pick-up-in-asia-pacific"></a>Felvétel Ázsiában – csendes-óceáni térség

Ez a régió a japán, Korea, Ausztrália és Szingapúr felvételére vonatkozó utasításokat tartalmazza.

### <a name="pick-up-in-australia"></a>Felvétel Ausztráliában

Az Ausztráliában található Azure-adatközpontok további biztonsági értesítésekkel rendelkeznek. Az összes bejövő szállítmánynak speciális értesítéssel kell rendelkeznie. Kövesse az alábbi lépéseket az Ausztráliában való felvételhez.

1. E `adbops@microsoft.com` -mail-cím, amely egyedi bejövő azonosítóval vagy a Tau-kóddal kéri a küldemények címkézését. Helyezze el a kérést legalább 3 nappal a tervezett szállítási dátum előtt, hogy időben beolvassa a címkét.
2. Az e-mail-tárgynak a-reverse típusú visszaszállítási címkéhez a *Tau-kóddal*kell rendelkeznie. Ügyeljen arra, hogy a következő adatokat tartalmazza az e-mailben: 

    - Rendelés neve
    - Cím
    - Kapcsolattartó neve

### <a name="pick-up-in-japan"></a>Felvétel Japánban

1. Adja meg a cég nevét és a címadatokat a küldeményre vonatkozó információként a küldő adatai alapján.
2. E-mail-Quantium-megoldás a következő e-mail-sablon használatával.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **Ha az osakaban**végez beszedést, akkor módosítsa a tárgyat az e-mail sablonban `Pickup request for Microsoft Azure OSA`a következőre:.
    - Ha a Japan post Chakubarai-fuvarlevél nem tartalmazott vagy hiányzik, jegyezze fel ezt az e-mailt. A Quantium-megoldások Japán a postán kérik a küldeményt, hogy vegye fel a figyelmét a felvételre.
    - Ha több megrendelés is van, e-mailben gondoskodhat az egyéni felvételről.

3. A Quantium-megoldásoktól kapott e-mail-visszaigazolást, miután elkönyvelt egy felvételt. Az e-mail-visszaigazolás a Chakubarai-szállítmány megjegyzésével kapcsolatos információkat is tartalmaz.

Ha szükséges, lépjen kapcsolatba a Quantium-megoldás támogatásával (Japán nyelvvel) a következő információkkal: 

- E-mailCustomerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 

### <a name="pick-up-in-korea"></a>Felvétel Koreában

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
    - Nyissa meg az **áttekintés > a szállítási címke letöltése** és a visszaszállítási címke beszerzése című cikket.

        ![Levélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    - A címke elhelyezése az eszközön. Győződjön meg arról, hogy a felirat látható.

2. Felvétel kérése:
    - Hívja meg az **SingPost** forródrótot **6845 6485** -kor az irodai óra alatt (9 – 5 óra, hétfő és péntek között).  
    - A gyűjtemény rendezéséhez adja meg *Microsoft Azure* a pickupot és a szolgáltatási kérelem számát (a visszaszállítási címke nyomon követési számát). 
    - A felvételi ütemtervre vonatkozó szóbeli megerősítést fog kapni. 
    - Ha a futár nem érkezik meg a gyűjteményhez, a **SingPost** címen hívhatja meg a **6845 6485** -at Alternatív megoldásként. 
3. Adja át a futárnak. 


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * A Data Box Disk elküldése a Microsoftnak
> * Data Box Disk különböző régiókban való kiválasztása

Folytassa a következő útmutatóval, amelyből megtudhatja, hogyan ellenőrizheti az adatok feltöltését Data Box Diskról az Azure Storage-fiókba.

> [!div class="nextstepaction"]
> [Adatok feltöltésének ellenőrzése Azure Data Box Disk](./data-box-disk-deploy-picked-up.md)

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-to-azure"></a>Lemez visszaküldése az Azure-ba

1. Az adatellenőrzés befejezése után húzza ki a lemezeket. Távolítsa el a csatlakoztatott kábeleket.
2. Csomagolja be az összes lemezt és kábelt buborékfóliába, és helyezze őket a szállítási dobozba. Ha a tartozékok hiányoznak, díjkötelesek lehetnek.
    - A csomagolás újrafelhasználása a kezdeti szállítólevélből.  
    - Javasoljuk, hogy a lemezeket jól biztonságos, buborékokkal védett körbefuttatással csomagolja.
    - Győződjön meg arról, hogy a megfelelő illeszkedés a dobozon belüli mozgások csökkentése érdekében.

3. A következő lépéseket az eszköz visszaadása határozza meg.

    - [Ha az eszközt az Egyesült Államokban és Kanadában](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada)küldi el, a UPS-be ütemezhet.
    - Regisztráljon a [DHL for Europe szolgáltatásra](data-box-disk-deploy-picked-up.md#pick-up-in-europe) a webhelyének meglátogatásával és a légúti számla számának megadásával.
    - Az ausztráliai, csendes-óceáni régióban, például Ausztráliában, Japánban, Koreában és Szingapúrban [található országok számára is ütemezhet egy felvételt](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific) .

Miután a szállító bevette a lemezeket, a portál frissítései és a nyomkövetési azonosító megjelenik.

::: zone-end


