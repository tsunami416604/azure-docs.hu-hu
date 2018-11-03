---
title: Frissítse a meglévő Azure-tárolók ajánlat |} A Microsoft Docs
description: Hogyan frissíthető egy meglévő tároló ajánlatot az Azure Marketplace-en.
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
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 2b568717b6656fb9ae15e9a6dbd27441689c4372
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979763"
---
# <a name="update-an-existing-container-offer"></a>Meglévő tároló ajánlat frissítése

Ez a cikk végigvezeti a tároló ajánlatot frissítése különböző aspektusainak a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

Miért érdemes az ajánlatot, mint például frissítése több oka is van:

-  Új tároló lemezkép verzió hozzáadása meglévő SKU-k.
-  Hozzáadás, új termékváltozatokra.
-  Az ajánlat vagy egyes termékváltozatok marketplace metaadatainak frissítésekor.

Segítség ezeket a módosításokat, a portál biztosít a **összehasonlítása** és **előzmények** funkciókat.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Egy tároló ajánlat és az SKU engedett módosítása

Nincsenek a tároló ajánlat és a Termékváltozat, amely nem módosítható, miután az Azure piactéren az ajánlat élő attribútumainak. A következő beállítások nem módosíthatók:

-  **Ajánlat azonosítója** és **közzétevő-azonosító** az ajánlat
-  **SKU-azonosítója** a meglévő termékváltozatok
-  Verzió címkék, például: `1.0.1`
-  Számlázási és licencelési modell módosítja a meglévő termékváltozatok

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-container-image-version-for-a-sku"></a>A Termékváltozat-es frissítési tároló lemezkép verziója

Szokás a tárolórendszerkép rendszeresen frissíteni kell a biztonsági javítások, további funkciók és így tovább. Ebben a forgatókönyvben szeretné frissíteni a tárolórendszerképet, amely a Termékváltozat hivatkozik az alábbi lépések segítségével:

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A **minden ajánlat**, keresse meg a frissíteni kívánt ajánlat.
3. Az a **termékváltozatok** lapra, válassza ki a tároló rendszerképét hozzárendelt Termékváltozat frissítése.
4. Alatt **tárolórendszerkép**válassza **+ új lemezkép verziója** hozzáadása egy új tárolórendszerképet.
5. Adja meg az új tároló **verziók kép**. A rendszerkép verziószámát van szüksége, mint a korábbi verziók ugyanez a címkék az útmutató követéséhez. Verzió címkék az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lenniük. Ellenőrizze, hogy nagyobb, mint a korábbi verziók, adja meg az új verzióra.
6. Válassza ki **közzététel** az új tároló-lemezkép verziója az Azure piactéren közzétenni a munkafolyamat elindításához.

### <a name="add-a-new-sku"></a>Adjon hozzá egy új Termékváltozatot

Használja az alábbi lépéseket, hogy egy új Termékváltozatban érhető el az ajánlatot:

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A **minden ajánlat**, keresse meg a frissíteni kívánt ajánlat.
3. Alatt a **termékváltozatok** lapon jelölje be **adja hozzá az új Termékváltozat** , és adja meg egy **SKU-azonosítója** a felugró ablakban.
4. Tegye közzé újra a tárolót, az ismertetett lépéseket követve [közzététel tároló ajánlat](./cpp-publish-offer.md).
5. Válassza ki **közzététel** közzététele az új Termékváltozat a munkafolyamat elindításához.

### <a name="update-offer-marketplace-metadata"></a>Az ajánlat marketplace metaadatok frissítése

A következő lépések segítségével frissíteni az ajánlatot társított marketplace metaadatait. (Például: vállalati nevet, emblémát és stb.)

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A **minden ajánlat**, keresse meg az ajánlat, amelyet szeretne frissíteni.
3. Nyissa meg a **Marketplace** fülre. Útmutatásait a [közzététel tároló ajánlat](./cpp-publish-offer.md) kínálnak a cikket, hogy a metaadat-módosítások.
4. Válassza ki **közzététel** a módosítások közzétételéhez a munkafolyamat elindításához.

## <a name="compare-feature"></a>A szolgáltatás összehasonlítása

Ha módosít egy közzétett ajánlat, használhatja a **összehasonlítása** funkció végrehajtott módosítások naplózását.

### <a name="to-use-the-compare-feature"></a>Az összehasonlítás funkció használatához:

1. A szerkesztési folyamat bármely mozzanata válassza ki az ajánlatban összehasonlítása.
2. Tekintse meg a marketing eszközök és metaadatok verzióinak egymás mellett.


## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

Közzétételi végzett tevékenység előzményeit, jelölje ki a **előzmények** lap meg a bal oldali navigációs menü sáv a Cloud Partner portálra. Láthatja, hogy az Azure Marketplace-ajánlat teljes élettartama során végrehajtott időbélyegzővel műveletek.