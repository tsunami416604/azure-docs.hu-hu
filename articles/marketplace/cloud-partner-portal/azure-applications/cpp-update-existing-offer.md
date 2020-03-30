---
title: Meglévő Azure-alkalmazásajánlat frissítése | Azure Piactér
description: Meglévő Azure-alkalmazásajánlat frissítése az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275951"
---
# <a name="update-an-existing-azure-application-offer"></a>Meglévő Azure-alkalmazásajánlat frissítése

Vannak különböző típusú frissítéseket, hogy érdemes lehet csinálni, hogy az ajánlat közzététele után, és él. Az ajánlat új verzióján tett módosításokat menteni kell, és újra közzé kell tenni, hogy az tükröződjön a Marketplace-en. Ez a cikk a felügyelt alkalmazásajánlat [felhőpartnerportálon](https://cloudpartner.azure.com/)történő frissítésének különböző szempontjait ismerteti.

Számos oka lehet annak, hogy miért szeretné frissíteni az ajánlatot, például:

- Új lemezkép-verzió hozzáadása a meglévő változathoz.
- Új ska hozzáadása.
- Az ajánlat vagy az egyes sk-ek piactéri metaadatainak frissítése.

A módosítások segítése érdekében a portál az **Összehasonlítás** és az **Előzmények** funkciókat biztosítja.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Nem engedélyezett módosítások egy Azure-alkalmazásajánlatban vagy termékváltozatban

Vannak olyan tárolóajánlat vagy termékváltozat attribútumai, amelyek nem módosíthatók, miután az ajánlat az Azure Marketplace-en való élő ben. A következő beállítások nem módosíthatók:

- Az ajánlat ajánlatazonosítója és közzétevőazonosítója
- Meglévő termékváltozatok termékazonosítója
- Verziócímkék, például:`1.0.1`
- A meglévő sk-ek számlázási/licencmodell-módosításai

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-image-version-for-a-sku"></a>Termékváltozat képverziójának frissítése

Gyakori, hogy egy képet rendszeresen frissíteni kell a biztonsági javításokkal, további funkciókkal és így tovább. Ebben az esetben a termékváltozat által hivatkozott lemezképet a következő lépésekkel szeretné frissíteni:

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A **Minden ajánlat csoportban**keresse meg a frissíteni kívánt ajánlatot.
3. A **Termékváltozatok** lapon válassza ki a frissítandó lemezképhez társított termékváltozatot.
4. Új kép hozzáadásához válassza a **+ Új képverzió** lehetőséget.
5. Adja meg az új lemezkép-verziókat. A lemezkép-verziónak ugyanazokat a címkékre vonatkozó irányelveket kell követnie, mint a korábbi verzióknak. A verziócímkéknek X.Y.Z formátumúnak kell lenniük, ahol X, Y és Z egész számok. Ellenőrizze, hogy a megadott új verzió nagyobb-e, mint az összes korábbi verzió.
6. Válassza **a Közzététel** lehetőséget az új tárolólemezkép-verzió azure Piactérre való közzétételéhez.

### <a name="add-a-new-sku"></a>Új termékváltozat hozzáadása

Az alábbi lépésekkel elérhetővé tehet egy új termékváltozatot az ajánlathoz:

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A **Minden ajánlat csoportban**keresse meg a frissíteni kívánt ajánlatot.
3. A **Termékváltozatok** lapon válassza az **Új termékváltozat hozzáadása** lehetőséget, és adja meg a **termékváltozat-azonosítót** az előugró ablakban.
4. Tegye közzé újra az ajánlatot az [Azure-alkalmazásajánlat közzététele](./cpp-publish-offer.md)című könyvben leírt lépésekkel.
5. Válassza **a Közzététel** lehetőséget az új termékváltozat közzétételéhez a munkafolyamat elindításához.

### <a name="update-offer-marketplace-metadata"></a>Ajánlatpiactér metaadatainak frissítése

Az alábbi lépésekkel frissítheti az ajánlathoz társított piactér metaadatait. (Például: cégnév, logók stb.)

1. Jelentkezzen be a [Cloud Partner Portal ba.](https://cloudpartner.azure.com/)
2. A **Minden ajánlat területen**keresse meg a frissíteni kívánt ajánlatot.
3. Nyissa meg a **Piactér** lapot. [Publish Azure application offer](./cpp-publish-offer.md)
4. A **közzététel gombra** a munkafolyamat elindításához válassza a módosítások közzétételét.
 
>[!Note]
>A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](../../cloud-solution-providers.md) ban talál.

## <a name="deleting-an-existing-offer"></a>Meglévő ajánlat törlése

Dönthet úgy, hogy eltávolítja az ajánlatát a Piactérről. Az ajánlat törlése nincs hatással az ajánlat aktuális vásárlásaira. Ezek az ügyfelek vásárlásai a mai napnak megfelelően fognak működni. A törlés befejezése után azonban az ajánlat nem lesz elérhető új vásárlásokhoz.

Az ajánlat megszüntetése az Ön és meglévő ügyfelei közötti szolgáltatási és/vagy licencszerződés megszüntetésének folyamata.
Az ajánlat eltávolításával és megszüntetésével kapcsolatos útmutatásokra és irányelvekre a Microsoft Marketplace kiadói szerződése (lásd a 7. szakaszt) és a részvételi szabályzatok vonatkoznak (lásd a 6.2 szakaszt).

További információ: [Delete and offer/SKU from Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Jellemző összehasonlítása

Amikor módosítja a közzétett ajánlatot, az Összehasonlítás funkcióval naplózhatja a végrehajtott módosításokat.

Az Összehasonlítás funkció használata:

1. A szerkesztési folyamat bármely pontján válassza az Összehasonlítás az ajánlathoz lehetőséget.
2. Tekintse meg a marketingeszközök és metaadatok egymás melletti verzióit.

## <a name="history-of-publishing-actions"></a>A közzétételi műveletek előzményei

A korábbi közzétételi tevékenység megtekintéséhez válassza az **Előzmények** lapot a Cloud Partner Portal bal oldali navigációs menüsorában. Megtekintheti az Azure Marketplace-ajánlatok élettartama során végrehajtott időbélyeggel ellátott műveleteket.

## <a name="next-steps"></a>További lépések

[Azure alkalmazásajánlat](./cpp-azure-app-offer.md)
