---
title: Az Azure Maps-fiók és kulcsok kezelése |} A Microsoft Docs
description: Az Azure portal segítségével az Azure Maps-fiók kezeléséhez, és kezelheti a hozzáférési kulcsait.
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 38926cc42b44e61bd83f6b1e33946a9026cda06d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989401"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Az Azure Maps-fiók és kulcsok kezelése

Az Azure Maps-fiók és kulcsok az Azure Portalon kezelheti. Ha már rendelkezik fiókkal, és a egy kulcsot, az API-k valósítható meg a webhely vagy mobilalkalmazás.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-new-account"></a>Új fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Keresse meg és válassza **Maps**, majd kattintson a **létrehozás**.

3. Írja be az új fiók adatait. 

![Írja be a fiók adatait a portálon](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>A fiók lapon kulcsok kezelése

Miután létrehozott egy fiókot, kap két véletlenszerűen létrehozott kulcsot. A kulcsokat a Maps API-k hitelesítése, ha meg szeretné beolvasni a térképadatok, vagy hozzon létre egy új JavaScript map-példány használja. 

A kulcsok az Azure Portalon találhatja meg. Keresse meg a fiókot, majd válassza ki **kulcsok** a menüből.

![Kezelheti a fiókkulcsokat a portálon](./media/how-to-manage-account-keys/account-keys-portal.png)

Ezen a lapon másolja ki a kulcsokat, vagy hozzon létre újakat. 

## <a name="delete-an-account"></a>-Fiók törlése

Egy fiók törölheti az Azure Portalról. Keresse meg a fiók áttekintése lapra, és válassza ki **törlése**.

![A portálon a fiók törlése](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható a [leképezi az management API](https://docs.microsoft.com/rest/api/maps-management/accounts) létrehozása, frissítése és törlése a Maps-fiókok. 