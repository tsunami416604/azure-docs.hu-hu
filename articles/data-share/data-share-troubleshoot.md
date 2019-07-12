---
title: Hibaelhárítás Azure-beli adat-megosztás előzetes verzió
description: Ismerje meg az Azure Data megosztás előzetes verziójával kapcsolatos problémák elhárítása
services: data-share
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: c02f72d6a327c4dcb94ac8844005613cfe316986
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838383"
---
# <a name="troubleshoot-common-issues-in-azure-data-share-preview"></a>Az Azure-adatok megosztása Előzetesében gyakori problémáinak elhárítása

Ez a cikk bemutatja, hogyan gyakori hibáinak elhárítása az Azure Data megosztás előzetes verzióra. 

## <a name="azure-data-share-invitations"></a>Az Azure az adatok megosztása meghívások 

Bizonyos esetekben, amikor egy új felhasználó **meghívó elfogadása** az elküldött e-mailben kapott, az azok lehet benyújtani a meghívások listája üres. 

![Nincsenek meghívói](media/no-invites.png)

A fenti hiba a szolgáltatás egy ismert probléma, és jelenleg javítása folyamatban van. A probléma megoldásához kövesse az alábbi lépéseket. 

1. Az Azure Portalon lépjen a **előfizetések**
1. Válassza ki az előfizetést, amely a használni kívánt Azure-adatok megosztása
1. Kattintson a **erőforrás-szolgáltatók**
1. Microsoft.DataShare keresése
1. Kattintson a **regisztrálása**

Rendelkeznie kell a [Azure RBAC-közreműködői szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) a lépések elvégzéséhez. 

Ha még nem látja a meghívó megosztani adatokat, kérje meg a data-szolgáltatót, és győződjön meg arról, hogy a meghívót az Azure-azonosító e-mail-címre elküldött és *nem* az e-mail-alias. 

> [!IMPORTANT]
> Ha már elfogadta a meghívást az Azure az adatok megosztása, és a tárolás konfigurálása előtt a szolgáltatás kilépett, kövesse a részletes utasításokat a [egy adatkészlet-leképezés konfigurálása](how-to-configure-mapping.md) konfigurálásának befejezéséhez további információt az útmutató a fogadott adatok megosztása, és indítsa el az adatok fogadása. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Hiba történt a létrehozásakor vagy fogad adatokat új megosztás

"Hiba: A művelet adott vissza egy érvénytelen állapotkód: "BadRequest" "

"Hiba: AuthorizationFailed"

"Hiba: a tárfiók szerepkör-hozzárendelés"

![Jogosultság hiba](media/error-write-privilege.png)

Ha bármelyik a fenti hibákat, amikor létrehozza az új adatok megosztása, vagy fogad egy új adatok megosztása, mert nincs elegendő jogosultsága a storage-fiókot. Az engedély szükséges *Microsoft.Authorization/role hozzárendelések írható*, amely a tárolási tulajdonosi szerepkör létezik, vagy egy egyéni biztonsági szerepkört kell hozzárendelni. Akkor is, ha a tárfiókban hozta létre, ezért nem automatikusan is végez, a tárfiók tulajdonosa. Kövesse az alábbi lépéseket a tárfiók tulajdonosa adhat saját magának. Egyéni szerepkör azt is megteheti, ezzel az engedéllyel, amelyeket hozzáadhat saját magának a hozható létre.  

1. Lépjen a tárfiókhoz az Azure Portalon
1. Válassza ki **hozzáférés-vezérlés (IAM)**
1. Kattintson a **hozzáadása**
1. Adjon hozzá tulajdonosként a saját maga.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan indítsa el az adatok megosztása, folytassa a [az adatok megosztása](share-your-data.md) oktatóanyag.

