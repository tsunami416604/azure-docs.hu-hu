---
title: Meglévő Azure-tárolók ajánlatának frissítése | Azure piactér
description: Meglévő Container-ajánlat frissítése az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 40cd375b11eb5b7ad5943fea9839b6339a7d002f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823086"
---
# <a name="update-an-existing-container-offer"></a>Meglévő tároló-ajánlat frissítése

Ez a cikk a tárolói ajánlat frissítésének különböző szempontjait ismerteti a [Cloud Partner Portalban](https://cloudpartner.azure.com/).

Számos oka lehet annak, hogy az ajánlatát szeretné frissíteni, például:

-  Új tároló rendszerkép-verziójának hozzáadása a meglévő SKU-hoz.
-  Új SKU-ket ad hozzá.
-  A piactér metaadatainak frissítése az ajánlathoz vagy az egyedi SKU-hoz.

A módosítások elősegítéséhez a portálon elérhetők az **összehasonlítás** és az **Előzmények** funkció.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Tároló-ajánlat vagy SKU nem engedélyezett módosításai

A tároló ajánlat vagy SKU attribútuma nem módosítható, miután az ajánlat az Azure piactéren elérhető. A következő beállítások nem módosíthatók:

-  Az ajánlat **azonosítója** és **közzétevő-azonosítója**
-  Meglévő SKU-i **SKU-azonosító**
-  Verziók címkéi, például: `1.0.1`
-  Számlázási/licencelési modell módosítása a meglévő SKU-ra

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-container-image-version-for-a-sku"></a>SKU-rendszerkép verziójának frissítése

Ez gyakran előfordul, hogy egy tároló lemezképét rendszeresen frissíteni kell a biztonsági javításokkal, további funkciókkal és így tovább. Ebben az esetben a következő lépések végrehajtásával szeretné frissíteni az SKU-ra hivatkozó tároló-rendszerképet:

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.
3. A **SKU** -i lapon válassza ki a frissíteni kívánt tároló-rendszerképhez társított SKU-t.
4. A **tároló képe**területen válassza az **+ új rendszerkép verziója** lehetőséget új tároló-rendszerkép hozzáadásához.
5. Adja meg az új tároló **rendszerképének verzióit**. A rendszerkép verziójának ugyanazokat a címkéket kell követnie, mint a korábbi verziókat. A verziószámnak X. Y. Z formátumúnak kell lennie, ahol az X, az Y és a Z egész számok. Ellenőrizze, hogy a megadott új verzió nagyobb-e az összes korábbi verziónál.
6. Válassza a **Közzététel** lehetőséget, hogy elindítsa a munkafolyamatot az új tároló rendszerkép-verziójának az Azure Marketplace-en való közzétételéhez.

### <a name="add-a-new-sku"></a>Új SKU hozzáadása

Az alábbi lépéseket követve új SKU-t hozhat elérhetővé az ajánlatához:

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.
3. A **SKU** -i lapon válassza az **új SKU hozzáadása** lehetőséget, és adjon meg egy **SKU azonosítót** az előugró ablakban.
4. Tegye közzé újra a tárolót a következő témakörben ismertetett lépések végrehajtásával: [Container-ajánlat közzététele](./cpp-publish-offer.md).
5. Válassza a **Közzététel** lehetőséget, hogy elindítsa a munkafolyamatot az új SKU közzétételéhez.

### <a name="update-offer-marketplace-metadata"></a>Ajánlat Marketplace-metaadatok frissítése

Az alábbi lépésekkel frissítheti az ajánlathoz tartozó Piactéri metaadatokat. (Például: cégnév, emblémák stb.)

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.
3. Lépjen a **Marketplace (piactér** ) lapra. a metaadatok módosításainak közzétételéhez használja a [Container ajánlat közzététele](./cpp-publish-offer.md) ajánlata című cikk utasításait.
4. Válassza a **Közzététel** lehetőséget a munkafolyamat elindításához a módosítások közzétételéhez.

## <a name="compare-feature"></a>Szolgáltatás összehasonlítása

Amikor módosításokat végez egy közzétett ajánlaton, az **összehasonlítás** funkcióval naplózhatja a végrehajtott módosításokat.

### <a name="to-use-the-compare-feature"></a>Az összehasonlítás funkció használata:

1. A szerkesztési folyamat bármely pontján válassza az összehasonlítás lehetőséget az ajánlathoz.
2. Tekintse meg a marketing eszközök és metaadatok egymás melletti verzióit.


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

A korábbi közzétételi tevékenységek megtekintéséhez válassza az **Előzmények** fület a bal oldali navigációs menüsorban Cloud Partner Portal. Az Azure Marketplace-ajánlatok élettartama alatt megtekintheti az időbélyegzővel ellátott műveleteket.