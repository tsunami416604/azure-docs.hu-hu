---
title: Az Azure IoT Edge-modul létező ajánlat frissítése |} A Microsoft Docs
description: Hogyan frissíthető egy meglévő IoT Edge modul ajánlatot az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 071837aac9fce405fcc5ef92e044a0265e0e5969
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431092"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Egy meglévő IoT Edge-modul ajánlat frissítése

Ez a cikk végigvezeti a frissítés az IoT Edge-modul ajánlat a különböző aspektusait a [Cloud Partner Portalon](https://cloudpartner.azure.com/) és majd újbóli közzététele az ajánlatot.

Miért érdemes az ajánlatot, mint például frissítése több oka is van:

-  Meglévő SKU-k hozzáadása egy új IoT Edge-modul lemezkép verziója.
-  Hozzáadás, új termékváltozatokra.
-  Az ajánlat vagy egyes termékváltozatok marketplace metaadatainak frissítésekor.

A portál kínál segítséget nyújt a ezeket a módosításokat, a **összehasonlítása** és **előzmények** funkciókat.  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>IoT Edge-modul ajánlat és az SKU engedett módosítása

Nincsenek a IoT Edge-modul ajánlat és a Termékváltozat, amely nem módosítható, miután az Azure piactéren az ajánlat élő attribútumainak. A következő beállítások nem módosíthatók:

-  **Ajánlat azonosítója** és **közzétevő-azonosító** az ajánlat
-  **SKU-azonosítója** a meglévő termékváltozatok
-  Verzió címkék, például: `1.0.1`
-  Számlázási és licencelési modell módosítja a meglévő termékváltozatok

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>A Termékváltozat az IoT Edge-modul lemezkép verziója frissítés

Szokás IoT Edge-modul lemezkép rendszeresen frissíteni kell a biztonsági javítások, további funkciók és így tovább. Ebben a forgatókönyvben szeretné frissíteni az IoT Edge-modul rendszerképének, amely a Termékváltozat hivatkozik az alábbi lépések segítségével:

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  A **minden ajánlat**, keresse meg a frissíteni kívánt ajánlat.

3.  Az a **termékváltozatok** lapra, válassza ki a hozzárendelt Termékváltozat az IoT Edge-modul rendszerképének frissítése.

4.  A **Edge-modul rendszerképének**válassza **+ új lemezkép verziója** hozzáadása egy új IoT Edge-modul rendszerképének.

5.  Adja meg az új IoT Edge-modul **verziók kép**. A rendszerkép verziószámát van szüksége, mint a korábbi verziók ugyanez a címkék az útmutató követéséhez. Verzió címkék az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lenniük. Ellenőrizze, hogy nagyobb, mint a korábbi verziók, adja meg az új verzióra.

6.  Válassza ki **közzététel** az új IoT Edge-modul verzió közzététele az Azure piactér a munkafolyamat elindításához.

### <a name="add-a-new-sku"></a>Adjon hozzá egy új Termékváltozatot

Használja az alábbi lépéseket, hogy egy új Termékváltozatban érhető el az ajánlatot: 

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  A **minden ajánlat**, keresse meg a frissíteni kívánt ajánlat.

3.  Alatt a **termékváltozatok** lapon jelölje be **adja hozzá az új Termékváltozat** , és adja meg egy **SKU-azonosítója** a felugró ablakban.

4.  Tegye közzé újra az IoT Edge-modul az ismertetett lépéseket követve [közzététele egy IoT Edge-modul az Azure piactéren](./cpp-publish-offer.md).

5.  Válassza ki **közzététel** közzététele az új Termékváltozat a munkafolyamat elindításához.


### <a name="update-offer-marketplace-metadata"></a>Az ajánlat marketplace metaadatok frissítése

A következő lépések segítségével frissíteni az ajánlatot társított marketplace metaadatait. (Például: cég neve, emblémák, stb.)

1.  Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

2.  A **minden ajánlat**, keresse meg az ajánlat, amelyet szeretne frissíteni.

3.  Nyissa meg a **Marketplace** fülre. Útmutatásait a [közzététele egy IoT Edge-modul az Azure piactéren](./cpp-publish-offer.md) cikk metaadatok módosításokat.

4.  Válassza ki **közzététel** a módosítások közzétételéhez a munkafolyamat elindításához.

## <a name="compare-feature"></a>A szolgáltatás összehasonlítása

Ha módosít egy közzétett kínálnak, használhatja a **összehasonlítása** funkció végrehajtott módosítások naplózását. 

**Az összehasonlítás funkció használatához:**

1.  Válassza ki a szerkesztési folyamat bármely mozzanata **összehasonlítása** az ajánlatban.

    ![Hasonlítsa össze a szolgáltatás gomb](./media/iot-edge-module-compare.png)


2.  Tekintse meg a marketing eszközök és metaadatok verzióinak egymás mellett.


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

Közzétételi végzett tevékenység előzményeit, jelölje ki a **előzmények** lap meg a bal oldali navigációs menü sáv a Cloud Partner portálra. Láthatja, hogy az Azure Marketplace-ajánlat teljes élettartama során végrehajtott időbélyegzővel műveletek.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
