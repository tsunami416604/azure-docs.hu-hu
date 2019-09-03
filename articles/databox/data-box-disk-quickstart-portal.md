---
title: Rövid útmutató – Microsoft Azure Data Box Disk | Microsoft Docs
description: A rövid útmutató segítségével gyorsan üzembe helyezheti az Azure Data Box Disket az Azure Portalon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: b07adc955b7904c6e6a3278480cec5a2be91ec35
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232911"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Gyors útmutató: Azure Data Box Disk üzembe helyezése a Azure Portal használatával

::: zone-end

::: zone target="chromeless"

## <a name="get-started-with-azure-data-box-disk"></a>Ismerkedés a Azure Data Box Disk 

::: zone-end

::: zone target="docs"

A rövid útmutató az Azure Data Box Disk az Azure Portal használatával való üzembe helyezését írja le. A lépések bemutatják, hogyan hozhat gyorsan létre rendeléseket, hogyan kaphatja kézhez, csomagolhatja ki és csatlakoztathatja a meghajtókat, majd másolhatja rájuk az adatokat azok az Azure-ba való feltöltéséhez.

A részletes üzembe helyezési és követési utasításokért keresse fel az [oktatóanyagot: Megrendelés Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone-end

::: zone target="chromeless"

Ez az útmutató végigvezeti a Azure Portal Azure Data Box Disk használatának lépésein. Ez az útmutató segítséget nyújt a következő kérdések megválaszolásához.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Ellenőrizze, hogy az előfizetésen engedélyezve van-e az Azure Data Box szolgáltatás. A szolgáltatás az előfizetésen való engedélyezéséhez [regisztráljon a szolgáltatásra](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs) webhelyen.

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Előfeltételek áttekintése**: A lemezek, a kábelek, az operációs rendszer és az egyéb szoftverek számának ellenőrzéséhez.
> - **Kapcsolat és feloldás**: Az eszköz csatlakoztatása és a lemez zárolásának feloldása az adatmásoláshoz.
> - **Másolja az adatlemezre, és ellenőrizze az**alábbiakat: Másolja az adatlemezeket az előlétrehozott mappákba.
> - **A lemezek visszaküldése**: Visszaadja a lemezeket az Azure-adatközpontba, ahol a rendszer feltölti az adatait a Storage-fiókjába.
> - **Az Azure-ban tárolt adatellenőrzés**: Mielőtt törölné a forrás-adatkiszolgálóról, ellenőrizze, hogy az adatok fel lettek-e töltve a Storage-fiókjába.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Rendelés

Ez a lépés nagyjából 5 percet vesz igénybe.

1. Hozzon létre egy új Azure Data Box-erőforrást az Azure Portalon. 
2. Válasszon ki egy előfizetést a szolgáltatáshoz, és az átvitel típusánál válassza az **Importálás** lehetőséget. Adja meg a **Forrásország** mezőben azt a helyet, ahol az adatok jelenleg találhatók, az **Azure-beli célrégió** mezőben pedig az adatátvitel célját.
3. Válassza a **Data Box Disk** lehetőséget. A megoldás maximális kapacitása 35 TB, nagyobb mennyiségű adat esetén több meghajtórendelést is létrehozhat.  
4. Adja meg a rendelés részleteit és a szállítási adatokat. Ha a szolgáltatás elérhető az Ön régiójában, adja meg az értesítési e-mail-címeket, tekintse át az összefoglalót, és hozza létre a rendelést.

A rendelés létrehozását követően megtörténik a meghajtók szállításra való előkészítése.

## <a name="unpack"></a>Kicsomagolás

Ez a lépés nagyjából 5 percet vesz igénybe.

A Data Box Disk-meghajtót egy UPS Express dobozban küldjük el Önnek. Nyissa ki a dobozt, és ellenőrizze, hogy tartalmazza-e a következőket:

- 1–5 buborékfóliába csomagolt USB-meghajtó.
- Meghajtónként egy csatlakozókábel.
- Fuvarlevélcímke a csomag visszaküldéséhez.

## <a name="connect-and-unlock"></a>Csatlakoztatás és a zárolás feloldása

Ez a lépés nagyjából 5 percet vesz igénybe.

1. A csomagban foglalt kábellel csatlakoztassa a meghajtót a Windows vagy Linux egy támogatott verzióját futtató számítógéphez. A támogatott operációsrendszer-verziókkal kapcsolatos további információkért lásd [az Azure Data Box Disk rendszerkövetelményeit](data-box-disk-system-requirements.md). 
2. A meghajtó zárolásának feloldása:

    1. Az Azure Portalon lépjen az **Általános > Eszköz adatai** menüpontra, és kérje le a hozzáférési kulcsot.
    2. Töltse le és csomagolja ki a megfelelő operációs rendszerhez tartozó Data Box Disk zárolását feloldó eszközt az adatok a meghajtókra való másolásához használt számítógépen. 
    3. Futtassa a Data Box Disk lemezzárolás-feloldó eszközt és adja meg a hozzáférési kulcsot. Új lemezek behelyezésekor futtassa újra a zárolást feloldó eszközt, és adja meg a hozzáférési kulcsot. **Ne használja a BitLocker párbeszédpanelt vagy a BitLocker kulcsot a lemez zárolásának feloldására.** A lemezek feloldásával kapcsolatos további információkért nyissa meg a [lemezek zárolásának feloldása Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) -ügyfélen vagy [a lemezek feloldása Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)-ügyfélen című témakört.
    4. A meghajtóhoz rendelt betűjelet az eszköz mutatja. Jegyezze fel az egyes meghajtók betűjelét. Ezt majd a következő lépésekben fogjuk felhasználni.

## <a name="copy-data-and-validate"></a>Adatok másolása és ellenőrzés

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. A meghajtó *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk*és *DataBoxDiskImport* mappákat tartalmaz. A blokkblobokként importálandó adatokat húzással másolja a *BlockBlob* mappába. Ehhez hasonlóan húzza át és dobja el az adatfájlokat, például a VHD-t/VHDX-t a *PageBlob* mappába, és a megfelelő *AzureFile*-fájlokat. Másolja a felügyelt lemezként feltölteni kívánt virtuális merevlemezeket egy mappába a *ManagedDisk*alatt.

    A rendszer a *BlockBlob* és a *PageBlob* mappa alatt található minden almappához létrehoz egy tárolót az Azure-tárfiókban. A rendszer létrehoz egy fájlmegosztást a *AzureFile*alatti almappákhoz.

    A *BlockBlob* és a *PageBlob* mappa alatt található összes fájl az Azure Storage-fiók alatti alapértelmezett `$root` tárolóba lesz átmásolva. Fájlok másolása egy mappába a *AzureFile*belül. A közvetlenül a *AzureFile* mappába másolt fájlok sikertelenek lesznek, és a rendszer blokk blobként feltölti őket.

    > [!NOTE]
    > - Minden tárolónak, blobnak és fájlnak meg kell felelnie az [Azure elnevezési konvencióinak](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ha a szabályok nem teljesülnek, az adatok az Azure-ba való feltöltése meghiúsul.
    > - Ügyeljen arra, hogy a fájlok ne lépjék túl a ~ 4,75 TiB-ot a blokkos Blobok esetében, ~ 8 TiB az oldal Blobok esetében, és ~ 1 TiB a Azure Files.

2. **(Nem kötelező, de ajánlott)** A másolás befejezése után javasoljuk, hogy legalább a `DataBoxDiskValidation.cmd` *DataBoxDiskImport* mappában adja meg a megadott beállítást, és válassza az 1. lehetőséget a fájlok ellenőrzéséhez. Javasoljuk továbbá, hogy a 2. lehetőséget is használja az érvényesítéshez (az adatmérettől függően időt is igénybe vehet). Ezek a lépések az Azure-ba történő adatfeltöltés során fellépő hibák esélyét csökkenthetik.
3. Biztonságosan távolítsa el a meghajtót.

## <a name="ship-to-azure"></a>Elküldés az Azure-nak

Ez a lépés kb. 5–7 percet vesz igénybe.

1. Helyezze vissza az összes meghajtót együtt az eredeti csomagba. Használja a csomagban lévő fuvarlevélcímkét. Ha a címke sérült vagy elveszett, töltse le a portálról. Lépjen az **Áttekintés** oldalra, és kattintson a **Levélcímke letöltése** parancsra a parancssávon.
2. Adja fel a lezárt csomagot a futárszolgálatnál.  

A Data Box Disk szolgáltatás egy e-mail-értesítést küld, és frissíti a rendelés állapotát az Azure Portalon.

## <a name="verify-your-data"></a>Az adatok ellenőrzése

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. A Data Box-meghajtók az Azure-adatközpont hálózatához való csatlakoztatásakor az adatok az Azure-ba való feltöltése automatikusan megkezdődik.
2. Az Azure Data Box szolgáltatás az Azure Portalon értesíti, ha az adatok másolása befejeződött.
    
    1. Ellenőrizze a hibákat a hibanaplókban, és tegye meg a szükséges intézkedéseket.
    2. Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a lépés 2–3 percet vehet igénybe.

Az erőforrások eltávolításához visszavonhatja a Data Box-rendelést, és törölheti azt.

- A Data Box-rendelést a feldolgozása előtt bármikor visszavonhatja az Azure Portalon. A rendelést a teljesítése után már nem lehet visszavonni. A rendelés halad a maga útján, amíg el nem éri a teljesített állapotot.

    A rendelés visszavonásához lépjen az **Áttekintés** oldalra, és kattintson a **Megszakítás** parancsra a parancssávon.  

- A rendelés akkor törölhető, ha a **Teljesítve** vagy a **Megszakítva** állapot jelenik meg az Azure Portalon.

    A rendelés törléséhez lépjen az **Áttekintés** oldalra, és kattintson a **Törlés** parancsra a parancssávon.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Azure Data Box Disk-meghajtót helyezett üzembe az adatok az Azure-ba való importálásához. Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Data Box Disk kezeléséről:

> [!div class="nextstepaction"]
> [A Data Box Disk az Azure Portal használatával történő kezelése](data-box-portal-ui-admin.md)

::: zone-end
