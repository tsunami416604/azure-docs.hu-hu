---
title: Meglévő Azure IoT Edge modul-ajánlat frissítése | Azure piactér
description: Meglévő IoT Edge modul frissítése az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: cd0167e1af5bf8ef667df88237d83e9f33ed41f9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813396"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Meglévő IoT Edge modul-ajánlat frissítése

Ez a cikk a IoT Edge modul ajánlatának a [Cloud Partner Portalban](https://cloudpartner.azure.com/) történő frissítésének különböző szempontjait ismerteti, majd újból közzéteszi az ajánlatot.

Számos oka lehet annak, hogy az ajánlatát szeretné frissíteni, például:

-  Új IoT Edge modul rendszerkép-verziójának hozzáadása meglévő SKU-hoz.
-  Új SKU-ket ad hozzá.
-  A piactér metaadatainak frissítése az ajánlathoz vagy az egyedi SKU-hoz.

A fenti módosításokkal kapcsolatban a portál az **összehasonlítási** és **előzményi** funkciókat kínálja.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>IoT Edge modul ajánlatának vagy SKU-nak nem engedélyezett módosításai

A IoT Edge modul ajánlata vagy SKU attribútuma nem módosítható, miután az ajánlat az Azure Marketplace-en üzemel. A következő beállítások nem módosíthatók:

-  Az ajánlat **azonosítója** és **közzétevő-azonosítója**
-  Meglévő SKU-i **SKU-azonosító**
-  Verziók címkéi, például: `1.0.1`
-  Számlázási/licencelési modell módosítása a meglévő SKU-ra

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>SKU-hoz tartozó IoT Edge-modul rendszerkép-verziójának frissítése

Gyakori, hogy egy IoT Edge modul rendszerképét rendszeresen frissíteni kell a biztonsági javításokkal, a további funkciókkal és így tovább. Ebben a forgatókönyvben frissíteni szeretné az SKU-ra hivatkozó IoT Edge-modul rendszerképét az alábbi lépések végrehajtásával:

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

2.  A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.

3.  A **SKU** -i lapon válassza ki a frissíteni kívánt IoT Edgei modulhoz társított SKU-t.

4.  Az **Edge-modul képe**alatt válassza az **+ új rendszerkép verziója** lehetőséget új IoT Edge-modul rendszerképének hozzáadásához.

5.  Adja meg az új IoT Edge modul **rendszerképének verzióját**. A rendszerkép verziójának ugyanazokat a címkéket kell követnie, mint a korábbi verziókat. A verziószámnak X. Y. Z formátumúnak kell lennie, ahol az X, az Y és a Z egész számok. Ellenőrizze, hogy a megadott új verzió nagyobb-e az összes korábbi verziónál.

6.  Válassza a **Közzététel** lehetőséget, hogy elindítsa a munkafolyamatot az új IoT Edge modul verziójának az Azure Marketplace-en való közzétételéhez.

### <a name="add-a-new-sku"></a>Új SKU hozzáadása

Az alábbi lépéseket követve új SKU-t hozhat elérhetővé az ajánlatához: 

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

2.  A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.

3.  A **SKU** -i lapon válassza az **új SKU hozzáadása** lehetőséget, és adjon meg egy **SKU azonosítót** az előugró ablakban.

4.  Tegye közzé újból a IoT Edge modult a [IoT Edge modul közzététele az Azure Marketplace](./cpp-publish-offer.md)-en című témakörben ismertetett lépések segítségével.

5.  Válassza a **Közzététel** lehetőséget, hogy elindítsa a munkafolyamatot az új SKU közzétételéhez.


### <a name="update-offer-marketplace-metadata"></a>Ajánlat Marketplace-metaadatok frissítése

Az alábbi lépésekkel frissítheti az ajánlathoz tartozó Piactéri metaadatokat. (Például: vállalat neve, emblémák stb.)

1.  Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).

2.  A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.

3.  Lépjen a **piactér** lapra. a metaadatok módosításához kövesse az [IoT Edge-modul közzététele az Azure piactéren](./cpp-publish-offer.md) című cikket.

4.  Válassza a **Közzététel** lehetőséget a munkafolyamat elindításához a módosítások közzétételéhez.

## <a name="compare-feature"></a>Szolgáltatás összehasonlítása

Amikor módosításokat végez egy közzétett ajánlaton, az **összehasonlítás** funkcióval naplózhatja a végrehajtott módosításokat. 

**Az összehasonlítás funkció használata:**

1.  A szerkesztési folyamat bármely pontján válassza az **összehasonlítás** lehetőséget az ajánlathoz.

    ![Funkció összehasonlítása gomb](./media/iot-edge-module-compare.png)


2.  Tekintse meg a marketing eszközök és metaadatok egymás melletti verzióit.


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

A korábbi közzétételi tevékenységek megtekintéséhez válassza az **Előzmények** fület a bal oldali navigációs menüsorban Cloud Partner Portal. Az Azure Marketplace-ajánlatok élettartama alatt megtekintheti az időbélyegzővel ellátott műveleteket.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
