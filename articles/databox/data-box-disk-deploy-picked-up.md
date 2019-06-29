---
title: Az oktatóanyag az Azure Data Box-lemezek való visszaküldésére |} A Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan küldheti vissza Azure Data Box Diskjét a Microsofthoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7e7a1f119a2f2b0e60645cb776b26c124910cacb
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448215"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Oktatóanyag: Vissza az Azure Data Box-lemezek, és ellenőrizze az adatok feltöltése az Azure-bA

Ez az a sorozat utolsó oktatóanyaga: Helyezze üzembe az Azure Data Box-lemezek. Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A Data Box Disk elküldése a Microsoftnak
> * Az Azure-ba történő adatfeltöltés ellenőrzése
> * Adatok törlése a Data Box Diskről

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta a [oktatóanyag: Adatok másolása az Azure Data Box-lemezek, és ellenőrizze](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Data Box Disk visszaküldése

1. Miután az adatok érvényesítése befejeződött, válassza le a lemezeket. Távolítsa el a csatlakoztatott kábeleket.
2. Csomagolja be az összes lemezt és kábelt buborékfóliába, és helyezze őket a szállítási dobozba. Ha hiányoznak a Kellékek merülhetnek fel.
    - Újból felhasználhatja a csomagolás, a kezdeti szállításáról.  
    - Azt javasoljuk, hogy a lemez használatával egy jól biztonságos bubbled újraindulás csomag.
    - Ellenőrizze, hogy a laphoz snug bármely áthelyezések száma – a mezőn belüli csökkentése érdekében.

Az alábbi lépésekkel, az eszköz vannak visszaadó határozza meg.

### <a name="pick-up-in-us-canada"></a>Vegye fel az USA, Kanada

Ha az eszközök visszatérő az Egyesült Államokban és Kanadában, tegye a következőket.

1. Helyezze fel a fuvarlevélcímkét, amelyet a dobozhoz rögzített átlátszó műanyagzsebben talál. Ha a címke megsérült vagy elveszett:
    - Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra.

        ![Fuvarlevélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Ez a művelet egy, az alábbiakban is látható, a visszaküldéshez szükséges fuvarlevélcímkét tölt le.

        ![Példa a fuvarlevélcímkére](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Elhelyezi a címkét az eszközön.

2. Zárja le a szállítási dobozt, és győződjön meg arról, hogy a visszaküldési fuvarlevélcímke jól látható.
3. Ütemezhet begyűjtést a UPS. Ütemezhet begyűjtést:

    - Hívja meg a helyi UPS (ország/régió-specifikus díjmentesen ingyenes szám).
    - A hívás az ajánlat a fordított szállítmány követési szám a nyomtatott felirat látható módon.
    - A nyomkövetési azonosító szám není uveden v uvozovkách, ha a UPS kell, hogy külön díj fizessen a begyűjtés során.
    - Ahelyett, hogy a begyűjtés ütemezés is húzhatja a Data Box-lemezek, a legközelebbi gyűjtőhely ki.


### <a name="pick-up-in-europe"></a>Az Európai felvétele

Ha az eszköz visszaadása Európában, tegye a következőket.

1. Helyezze fel a fuvarlevélcímkét, amelyet a dobozhoz rögzített átlátszó műanyagzsebben talál. Ha a címke megsérült vagy elveszett:
    - Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra.

        ![Fuvarlevélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Ez a művelet egy, az alábbiakban is látható, a visszaküldéshez szükséges fuvarlevélcímkét tölt le.

        ![Példa a fuvarlevélcímkére](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Elhelyezi a címkét az eszközön.

2. Zárja le a szállítási dobozt, és győződjön meg arról, hogy a visszaküldési fuvarlevélcímke jól látható.
3. Ha az eszközt Európán belül, DHL-lel szeretné visszaküldeni, látogasson el a DHL webhelyére, kérjen csomagfelvételt, és adja meg a légi fuvarlevél számát.
4. Az ország/régió DHL Express webhelyén található, és válassza a **repülőjáratra Courier gyűjtemény > eReturn szállításáról**.

    ![DHL visszaszállításhoz](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Adja meg a fuvarlevél számát, és kattintson a **Futárrendelés** gombra a csomagfelvétel lefoglalásához.

      ![Csomagfelvétel ütemezése](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Vegye fel az ázsiai és csendes-óceáni régióban

Ebben a régióban japán, koreai, Ausztrália és Szingapúr begyűjtés vonatkozó utasításokat tartalmazza.

#### <a name="pick-up-in-australia"></a>Az ausztráliai felvétele

Az ausztráliai Azure-adatközpontok rendelkezik egy kiegészítő biztonsági értesítést. A bejövő szállítmányok rendelkeznie kell egy speciális értesítést. Az alábbi lépéseket az ausztráliai felvételhez.

1. E-mailek `adbops@microsoft.com` bejövő egyedi azonosítója vagy a TAU kóddal kérelem a szállítási címkét. Helyezze el a kérelmet a tervezett szállítási beolvasása a címke szerinti dátum előtt legalább 3 nap.
2. Az e-mail tárgya lehet – *fordított levélcímke TAU kódra vonatkozó kérelem*. Ügyeljen arra, hogy az e-mailben a következő részleteket tartalmazza: 

    - Rendelés neve
    - Cím
    - Kapcsolattartó neve

#### <a name="pick-up-in-japan"></a>A japán felvétele

1. A küldő adatai, a kísérő a nevét és címét adatokat írnak a vállalata.
2. Az e-mail-Quantium megoldás az alábbi e-mail-sablon használatával.

    - Ha a japán Post Chakubarai kísérő nem tartalmaz, vagy nincs megadva, vegye figyelembe, hogy az e-mail tartalma. Quantium megoldások japán japán Post ahhoz, hogy a kísérő felvétel után fog igényelni.
    - Ha több megrendelések, az e-mailben, győződjön meg, hogy az egyes begyűjtést.

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

3. Egy megerősítő e-mailt kap a Quantium megoldások után, de már begyűjtést. A megerősítő e-mailt a Chakubarai kísérő információkat is biztosít.

Szükség esetén is ügyfélszolgálatot Quantium megoldás (japán nyelv), a következő információkat: 

- E-mail cím:Customerservice.JP@quantiumsolutions.com 
- Telefon: 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Koreai felvétele

1. Győződjön meg arról, hogy tartalmazza a visszatérési kísérő.
2. A kérelem begyűjtés, kísérő megléte esetén:
    1. Hívás *Quantium Solutions International* telefonszámon 070-8231-1418 office órában (10-kor, 17: 00 hétfőtől péntekig). Ajánlat *Microsoft Azure begyűjtés* és a egy gyűjtemény rendezheti a kérelem száma.  
    2. Ha az információs vonal foglalt, e-mailben `microsoft@rocketparcel.com`, az az e-mail tárgyát *a Microsoft Azure begyűjtés* és a kérelmek száma hivatkozásként van listázva.
    3. Ha a courier nem érkezik a gyűjteményhez, hívja *Quantium Solutions International* információs vonal alternatív szabályokat. 
    4. A felvételi ütemezés egy megerősítő e-mailt kap.
3. Erre a lépésre csak akkor, ha nem áll rendelkezésre a kísérő szükség. Felvétel kérése:
    1. Hívás *Quantium Solutions International* telefonszámon 070-8231-1418 office órában (10-kor, 17: 00 hétfőtől péntekig). Ajánlat *Microsoft Azure begyűjtés* és a egy gyűjtemény rendezheti a kérelem száma. Adja meg, hogy kell-e egy új kísérő gyűjtemény elrendezése. Adja meg a feladó (ügyfél), a fogadó információkat (Azure-adatközpont) és a hivatkozás (szolgáltatási kérelem) számát. 
    2. Ha az információs vonal foglalt, e-mailben `microsoft@rocketparcel.com`, az az e-mail tárgyát *a Microsoft Azure begyűjtés* és a kérelmek száma hivatkozásként van listázva.
    3. Ha a courier nem érkezik a gyűjteményhez, hívja *Quantium Solutions International* információs vonal alternatív szabályokat. 
    4. Ha a kérés telefonos kapcsolaton keresztül kap eszkalációra megerősítést.

### <a name="pick-up-in-singapore"></a>Vegye fel a szingapúri

1. Nyomtassa ki a szállítási címkét, és csatolja a boxot. Ha a címke megsérült vagy elveszett:
    - Lépjen az **Áttekintés > Levélcímke letöltése** menüpontra.

        ![Fuvarlevélcímke letöltése](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Ez a művelet egy, az alábbiakban is látható, a visszaküldéshez szükséges fuvarlevélcímkét tölt le.

        ![Példa a fuvarlevélcímkére](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Elhelyezi a címkét az eszközön. Győződjön meg arról, hogy a felirat látható.

2. Felvétel kérése:
    - Hívás **SingPost** telefonszámon **6845 6485** office munkaidőben (9 -kor, 17: 00 hétfőtől péntekig).  
    - Ajánlat *Microsoft Azure begyűjtés* és a egy gyűjtemény elrendezése (szám nyomon követése a visszaszállítási címke) kérelem szolgáltatást. 
    - A felvételi ütemezés eszkalációra megerősítést fog kapni. 
    - Ha a courier nem érkezik a gyűjteményhez, hívja **SingPost** , **6845 6485** alternatív szabályokat. 
3. A courier az oldalon. 


## <a name="verify-data-upload-to-azure"></a>Az Azure-ba történő adatfeltöltés ellenőrzése

Miután a futár felvette a lemezeket, a rendelés állapota a portálon **Szállítás alatt** állapotra módosul. A nyomkövetési azonosító is megjelenik.

![Lemezek felvéve](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Amikor a Microsoft megkapja és átvizsgálja a lemezt, a feladat állapota **Megérkezett** értékre vált. 

![Lemezek átvéve](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Az adatok másolása automatikusan megtörténik, amint a lemezeket csatlakoztatják a kiszolgálóhoz az Azure-adatközpontban. Az adatok méretétől függően a másolási művelet több órát vagy napot is igénybe vehet. A másolási feladat folyamatát a portálon követheti nyomon.

A másolás végeztével a rendelés állapota **Befejezve** értéke vált.

![Adatok másolása befejezve](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Ha a másolási befejeződött, hibákkal, [feltöltési hibáinak elhárítása](data-box-disk-troubleshoot-upload.md).

Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról. Az adatok lehetnek:

- Az Azure Storage-fiókok. A Data Boxra másolt adatok a típusuktól függően a következő elérési utak egyikére lesznek feltöltve az Azure Storage-fiókban.

  - Blokkblobok és lapblobok esetében: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files esetében: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternatív megoldásként navigálhat az Azure Storage-fiókjából is az Azure Portalon.

- A felügyelt lemez erőforráscsoport(ok). Felügyelt lemezek létrehozásakor a virtuális merevlemezeket lapblobként feltöltött és majd a felügyelt lemezekké való konvertálása. A felügyelt lemezek vannak csatolva az erőforráscsoportok, a megadott sorrendben létrehozásának időpontjában.

  - Ha a példány az Azure-ban felügyelt lemezekre sikeres volt, megnyithatja a **rendelés részletei** az Azure Portalon, és jegyezze fel az erőforráscsoport a megadott felügyelt lemezek esetén ellenőrizze.

      ![Rendelés részleteinek megtekintése](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Nyissa meg a feljegyzett erőforráscsoportot, és keresse meg a felügyelt lemezek.

      ![Felügyelt lemezek erőforráscsoport](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Ha egy dinamikus vagy különbséglemez VHD vagy vhdx-fájlt másolja, majd a VHDX-/ VHD töltenek fel az előkészítési tárfiókból blokkblobként. Nyissa meg az átmeneti **tárfiók > Blobok** , és válassza ki a megfelelő tárolót - StandardSSD, StandardHDD vagy PremiumSSD. A VHDX vagy VHD-k kell jelennek meg az átmeneti tárfiók blokkblobok formájában.

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


