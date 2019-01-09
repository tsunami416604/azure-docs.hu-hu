---
title: Az Azure Maps-fiók és kulcsok kezelése |} A Microsoft Docs
description: Az Azure portal segítségével az Azure Maps-fiók kezeléséhez, és kezelheti a hozzáférési kulcsait.
author: walsehgal
ms.author: v-musehg
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 81746279e935f9fa5b6ef00bc8d31e75f4d3bd20
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103044"
---
# <a name="manage-your-azure-maps-account-and-keys"></a>Az Azure Maps-fiók és kulcsok kezelése

Az Azure Maps-fiók és kulcsok az Azure Portalon kezelheti. Miután egy fiókot és a egy kulcsot, az API-k valósítható meg a webhely vagy mobilalkalmazás.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-new-account"></a>Új fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki **erőforrás létrehozása** az Azure portal bal felső sarkában.

3. Keresse meg és válassza **Maps**. Ezután kattintson a **Létrehozás** elemre.

4. Írja be az új fiók adatait.

![Írja be a fiók adatait a portálon](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>A fiók lapon kulcsok kezelése

Miután létrehozott egy fiókot, kap két véletlenszerűen létrehozott kulcsot. Térképadatok lekérése, vagy hozzon létre egy új JavaScript map-példány, a kulcsok használata az Azure Maps API-k hitelesítése.

A kulcsok az Azure Portalon találhatja meg. Keresse meg a fiókjához. Válassza ki **kulcsok** a menüből.

![Kezelheti a fiókkulcsokat a portálon](./media/how-to-manage-account-keys/account-keys-portal.png)

Ezen az oldalon másolja ki a kulcsokat, vagy hozzon létre újakat.

## <a name="delete-an-account"></a>-Fiók törlése

Egy fiók törölheti az Azure Portalról. Keresse meg a fiók áttekintése lapra, és válassza ki **törlése**.

![A portálon a fiók törlése](./media/how-to-manage-account-keys/account-delete-portal.png)

Ekkor megjelenik egy megerősítő oldal. A fiók törlése a név beírásával ellenőrizheti.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan kezelheti az Azure Maps-fiók tarifacsomagját:
    > [!div class="nextstepaction"] 
    > [Tarifacsomag kezelése](./how-to-manage-pricing-tier.md)

* Megtudhatja, hogyan tekintheti meg az API-használati metrikák az Azure Maps-fiók:
    > [!div class="nextstepaction"] 
    > [Használati metrikák megtekintése](./how-to-view-api-usage.md)
