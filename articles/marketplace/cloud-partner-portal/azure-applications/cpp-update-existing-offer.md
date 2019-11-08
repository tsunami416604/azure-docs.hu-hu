---
title: Meglévő Azure-alkalmazási ajánlat frissítése | Azure piactér
description: Meglévő Azure-alkalmazási ajánlat frissítése az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pabutler
ms.openlocfilehash: 6854e445ea63639866e9e39a6afc725237bbc8fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826109"
---
# <a name="update-an-existing-azure-application-offer"></a>Meglévő Azure-alkalmazási ajánlat frissítése

A közzétett és az élő verzióban számos különböző típusú frissítést lehet elvégezni az ajánlaton. Az ajánlat új verziójának módosításait el kell menteni, és újra közzé kell tenni, hogy az tükrözze a piactéren. Ez a cikk a felügyelt alkalmazások [Cloud Partner Portalban](https://cloudpartner.azure.com/)való frissítésének különböző szempontjait ismerteti.

Számos oka lehet annak, hogy az ajánlatát szeretné frissíteni, például:

- Új rendszerkép-verzió hozzáadása a meglévő SKU-hoz.
- Új SKU-ket ad hozzá.
- A piactér metaadatainak frissítése az ajánlathoz vagy az egyedi SKU-hoz.

A módosítások elősegítéséhez a portálon elérhetők az **összehasonlítás** és az **Előzmények** funkció.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Azure-alkalmazási ajánlat vagy SKU nem engedélyezett módosításai

A tároló ajánlat vagy SKU attribútuma nem módosítható, miután az ajánlat az Azure piactéren elérhető. A következő beállítások nem módosíthatók:

- Az ajánlat azonosítója és közzétevő-azonosítója
- Meglévő SKU-i SKU-azonosító
- Verziók címkéi, például: `1.0.1`
- Számlázási/licencelési modell módosítása a meglévő SKU-ra

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-image-version-for-a-sku"></a>SKU-rendszerkép verziójának frissítése

Gyakori, hogy egy rendszerképet rendszeresen frissíteni kell a biztonsági javításokkal, további funkciókkal és így tovább. Ebben az esetben az alábbi lépések végrehajtásával szeretné frissíteni az SKU-ra hivatkozó rendszerképet:

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.
3. A **SKU** -i lapon válassza ki a frissíteni kívánt rendszerképhez társított SKU-t.
4. Új rendszerkép hozzáadásához válassza az **+ új rendszerkép verziója** lehetőséget.
5. Adja meg az új rendszerkép-verziókat. A rendszerkép verziójának ugyanazokat a címkéket kell követnie, mint a korábbi verziókat. A verziószámnak X. Y. Z formátumúnak kell lennie, ahol az X, az Y és a Z egész számok. Ellenőrizze, hogy a megadott új verzió nagyobb-e az összes korábbi verziónál.
6. Válassza a **Közzététel** lehetőséget, hogy elindítsa a munkafolyamatot az új tároló rendszerkép-verziójának az Azure Marketplace-en való közzétételéhez.

### <a name="add-a-new-sku"></a>Új SKU hozzáadása

Az alábbi lépéseket követve új SKU-t hozhat elérhetővé az ajánlatához:

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.
3. A **SKU** -i lapon válassza az **új SKU hozzáadása** lehetőséget, és adjon meg egy **SKU azonosítót** az előugró ablakban.
4. Tegye közzé újból az ajánlatot az [Azure-alkalmazás közzététele](./cpp-publish-offer.md)című témakörben ismertetett lépések alapján.
5. Válassza a **Közzététel** lehetőséget, hogy elindítsa a munkafolyamatot az új SKU közzétételéhez.

### <a name="update-offer-marketplace-metadata"></a>Ajánlat Marketplace-metaadatok frissítése

Az alábbi lépésekkel frissítheti az ajánlathoz tartozó Piactéri metaadatokat. (Például: vállalat neve, emblémák stb.)

1. Jelentkezzen be a [Cloud Partner Portalba](https://cloudpartner.azure.com/).
2. A **minden ajánlat**területen keresse meg a frissíteni kívánt ajánlatot.
3. Lépjen a **Marketplace (piactér** ) lapra. a metaadatok módosításához használja az [Azure-alkalmazás közzétételére vonatkozó ajánlat közzététele](./cpp-publish-offer.md) című témakör utasításait.
4. Válassza a **Közzététel** lehetőséget a munkafolyamat elindításához a módosítások közzétételéhez.
 
>[!Note]
>A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](../../cloud-solution-providers.md) című témakört.

## <a name="deleting-an-existing-offer"></a>Meglévő ajánlat törlése

Dönthet úgy, hogy eltávolítja az ajánlatot a piactéren. Az ajánlat törlése nem befolyásolja az ajánlat aktuális vásárlásait. A vásárlói vásárlások továbbra is ugyanúgy működnek, mint korábban. A törlés befejezése után azonban az ajánlat nem lesz elérhető az új vásárlásokhoz.

Az ajánlat megszüntetése a szolgáltatás és/vagy az Ön és ügyfelei közötti licencszerződés megszüntetését jelenti.
Az ajánlat eltávolításához és megszüntetéséhez kapcsolódó útmutatást és szabályzatokat Microsoft Marketplace kiadói szerződés szabályozza (lásd a 7. szakaszt) és a részvételi szabályzatokat (lásd: 6,2. szakasz).

További információ: [delete és Offer/SKU az Azure Marketplace-ről](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Szolgáltatás összehasonlítása

Amikor módosításokat végez egy közzétett ajánlaton, az összehasonlítás funkcióval naplózhatja a végrehajtott módosításokat.

Az összehasonlítás funkció használata:

1. A szerkesztési folyamat bármely pontján válassza az összehasonlítás lehetőséget az ajánlathoz.
2. Tekintse meg a marketing eszközök és metaadatok egymás melletti verzióit.

## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

A korábbi közzétételi tevékenységek megtekintéséhez válassza az **Előzmények** fület a bal oldali navigációs menüsorban Cloud Partner Portal. Az Azure Marketplace-ajánlatok élettartama alatt megtekintheti az időbélyegzővel ellátott műveleteket.

## <a name="next-steps"></a>További lépések

[Azure-alkalmazásajánlat](./cpp-azure-app-offer.md)