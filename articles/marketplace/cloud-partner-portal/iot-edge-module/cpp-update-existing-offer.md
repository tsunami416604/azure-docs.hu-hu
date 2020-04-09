---
title: Meglévő Azure IoT Edge-modulajánlat frissítése | Azure Piactér
description: Meglévő IoT Edge-modulajánlat frissítése az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 019711f35d249e6684013feddb6b453d509f66d4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985354"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Meglévő IoT Edge-modulajánlat frissítése

>[!Important]
>2020. április 13-tól megkezdjük az IoT Edge modulajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [IoT Edge-modulajánlat létrehozása](https://aka.ms/AzureCreateIoT) az áttelepített ajánlatok kezeléséhez kövesse az utasításokat.

Ez a cikk az IoT Edge-modulajánlat frissítésének különböző szempontjait lépteti át a [Cloud Partner Portalon,](https://cloudpartner.azure.com/) majd az ajánlat újbóli közzétételét.

Számos oka lehet annak, hogy miért szeretné frissíteni az ajánlatot, például:

-  Új IoT Edge-modulkép-verzió hozzáadása a meglévő változathoz.
-  Új ska hozzáadása.
-  Az ajánlat vagy az egyes sk-ek piactéri metaadatainak frissítése.

A módosítások segítése érdekében a portál az **Összehasonlítás** és az **Előzmények** funkciókat kínálja.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Az IoT Edge modulajánlat vagy termékváltozat nem engedélyezett módosításai

Vannak olyan attribútumok, amelyek egy IoT Edge-modul ajánlat vagy termékváltozat, amely nem módosítható, miután az ajánlat él az Azure Marketplace-en. A következő beállítások nem módosíthatók:

-  Az ajánlat **ajánlatazonosítója** és **közzétevőazonosítója**
-  **Meglévő** termékváltozatok termékazonosítója
-  Verziócímkék, például:`1.0.1`
-  A meglévő sk-ek számlázási/licencmodell-módosításai

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Az IoT Edge modul képverziójának frissítése termékváltozathoz

Gyakori, hogy az IoT Edge-modul lemezképét rendszeresen frissítik a biztonsági javításokkal, a további funkciókkal és így tovább. Ebben az esetben a következő lépésekkel szeretné frissíteni az IoT Edge modullemezképét, amelyet az Termékváltozat hivatkozik:

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)

2.  A **Minden ajánlat csoportban**keresse meg a frissíteni kívánt ajánlatot.

3.  A **Termékváltozatok** lapon válassza ki a frissítéshez az IoT Edge modul lemezképéhez társított termékváltozatot.

4.  Az **Edge modul képalatt**válassza **a + Új képverzió** lehetőséget egy új IoT Edge modulkép hozzáadásához.

5.  Adja meg az új IoT Edge **modulkép-verziókat.** A lemezkép-verziónak ugyanazokat a címkékre vonatkozó irányelveket kell követnie, mint a korábbi verzióknak. A verziócímkéknek X.Y.Z formátumúnak kell lenniük, ahol X, Y és Z egész számok. Ellenőrizze, hogy a megadott új verzió nagyobb-e, mint az összes korábbi verzió.

6.  Válassza **a Közzététel** lehetőséget az új IoT Edge-modulverzió azure Piactérre való közzétételéhez.

### <a name="add-a-new-sku"></a>Új termékváltozat hozzáadása

Az alábbi lépésekkel elérhetővé tehet egy új termékváltozatot az ajánlathoz: 

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)

2.  A **Minden ajánlat csoportban**keresse meg a frissíteni kívánt ajánlatot.

3.  A **Termékváltozatok** lapon válassza az **Új termékváltozat hozzáadása** lehetőséget, és adja meg a **termékváltozat-azonosítót** az előugró ablakban.

4.  Tegye közzé újra az IoT Edge-modult az [IoT Edge-modul közzététele az Azure Marketplace-en](./cpp-publish-offer.md)című részben leírt lépésekkel.

5.  Válassza **a Közzététel** lehetőséget az új termékváltozat közzétételéhez a munkafolyamat elindításához.


### <a name="update-offer-marketplace-metadata"></a>Ajánlatpiactér metaadatainak frissítése

Az alábbi lépésekkel frissítheti az ajánlathoz társított piactér metaadatait. (Például: cégnév, logók stb.)

1.  Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)

2.  A **Minden ajánlat területen**keresse meg a frissíteni kívánt ajánlatot.

3.  Nyissa meg a **Piactér** lap. Az Utasításokat az IoT Edge modul közzététele az [Azure Marketplace-en](./cpp-publish-offer.md) cikk metaadatok módosításához.

4.  A **közzététel gombra** a munkafolyamat elindításához válassza a módosítások közzétételét.

## <a name="compare-feature"></a>Jellemző összehasonlítása

Ha módosítja a közzétett ajánlatot, az **Összehasonlítás** funkcióval naplózhatja a végrehajtott módosításokat. 

**Az Összehasonlítás funkció használata:**

1.  A szerkesztési folyamat bármely pontján válassza az Összehasonlítás az ajánlathoz **lehetőséget.**

    ![Jellemző összehasonlítása gomb](./media/iot-edge-module-compare.png)


2.  Tekintse meg a marketingeszközök és metaadatok egymás melletti verzióit.


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

A korábbi közzétételi tevékenység megtekintéséhez válassza az **Előzmények** lapot a Cloud Partner Portal bal oldali navigációs menüsorában. Megtekintheti az Azure Marketplace-ajánlatok élettartama során végrehajtott időbélyeggel ellátott műveleteket.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
