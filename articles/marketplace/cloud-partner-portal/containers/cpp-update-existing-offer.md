---
title: Meglévő Azure-tárolók ajánlatának frissítése | Azure Piactér
description: Meglévő tárolóajánlat frissítése az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 74f97b082c07e17a59a1615c4b1245434c497ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279946"
---
# <a name="update-an-existing-container-offer"></a>Meglévő tárolóajánlat frissítése

Ez a cikk a felhőpartner-webhelyen a tárolóajánlat frissítésének különböző szempontjait [ismerteti.](https://cloudpartner.azure.com/)

Számos oka lehet annak, hogy miért szeretné frissíteni az ajánlatot, például:

-  Új tárolólemezkép-verzió hozzáadása a meglévő sk-ekhez.
-  Új ska hozzáadása.
-  Az ajánlat vagy az egyes sk-ek piactéri metaadatainak frissítése.

A módosítások segítése érdekében a portál az **Összehasonlítás** és az **Előzmények** funkciókat biztosítja.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Tárolóajánlat vagy termékváltozat nem engedélyezett módosításai

Vannak olyan tárolóajánlat vagy termékváltozat attribútumai, amelyek nem módosíthatók, miután az ajánlat az Azure Marketplace-en való élő ben. A következő beállítások nem módosíthatók:

-  Az ajánlat **ajánlatazonosítója** és **közzétevőazonosítója**
-  **Meglévő** termékváltozatok termékazonosítója
-  Verziócímkék, például:`1.0.1`
-  A meglévő sk-ek számlázási/licencmodell-módosításai

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-container-image-version-for-a-sku"></a>Tárolólemezkép verziójának frissítése termékváltozathoz

Gyakori, hogy egy tárolórendszerképet rendszeresen frissíteni kell a biztonsági javításokkal, további funkciókkal és így tovább. Ebben az esetben a következő lépésekkel szeretné frissíteni a tárolólemezképet, amelyet a termékváltozat hivatkozik:

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A **Minden ajánlat csoportban**keresse meg a frissíteni kívánt ajánlatot.
3. A **Termékváltozatok** lapon válassza ki a frissíteni kívánt tárolólemezképhez társított termékváltozatot.
4. A **Tárolólemezkép csoportban**válassza **a + Új lemezkép verzió lehetőséget** új tárolólemezkép hozzáadásához.
5. Adja meg az új **tárolórendszerkép-verziókat**. A lemezkép-verziónak ugyanazokat a címkékre vonatkozó irányelveket kell követnie, mint a korábbi verzióknak. A verziócímkéknek X.Y.Z formátumúnak kell lenniük, ahol X, Y és Z egész számok. Ellenőrizze, hogy a megadott új verzió nagyobb-e, mint az összes korábbi verzió.
6. Válassza **a Közzététel** lehetőséget az új tárolólemezkép-verzió azure Piactérre való közzétételéhez.

### <a name="add-a-new-sku"></a>Új termékváltozat hozzáadása

Az alábbi lépésekkel elérhetővé tehet egy új termékváltozatot az ajánlathoz:

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A **Minden ajánlat csoportban**keresse meg a frissíteni kívánt ajánlatot.
3. A **Termékváltozatok** lapon válassza az **Új termékváltozat hozzáadása** lehetőséget, és adja meg a **termékváltozat-azonosítót** az előugró ablakban.
4. Tegye közzé újra a tárolót a [Tároló ajánlat közzététele](./cpp-publish-offer.md)című részben ismertetett lépésekkel.
5. Válassza **a Közzététel** lehetőséget az új termékváltozat közzétételéhez a munkafolyamat elindításához.

### <a name="update-offer-marketplace-metadata"></a>Ajánlatpiactér metaadatainak frissítése

Az alábbi lépésekkel frissítheti az ajánlathoz társított piactér metaadatait. (Például: cégnév, logók stb.)

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A **Minden ajánlat területen**keresse meg a frissíteni kívánt ajánlatot.
3. Nyissa meg a **Piactér** lapot. [Publish container offer](./cpp-publish-offer.md)
4. A **közzététel gombra** a munkafolyamat elindításához válassza a módosítások közzétételét.

## <a name="compare-feature"></a>Jellemző összehasonlítása

Amikor módosítja a közzétett ajánlatot, az **Összehasonlítás** funkcióval naplózhatja a végrehajtott módosításokat.

### <a name="to-use-the-compare-feature"></a>Az Összehasonlítás funkció használata:

1. A szerkesztési folyamat bármely pontján válassza az Összehasonlítás az ajánlathoz lehetőséget.
2. Tekintse meg a marketingeszközök és metaadatok egymás melletti verzióit.


## <a name="history-of-publishing-actions"></a>A közzétételi műveletek előzményei

A korábbi közzétételi tevékenység megtekintéséhez válassza az **Előzmények** lapot a Cloud Partner Portal bal oldali navigációs menüsorában. Megtekintheti az Azure Marketplace-ajánlatok élettartama során végrehajtott időbélyeggel ellátott műveleteket.
