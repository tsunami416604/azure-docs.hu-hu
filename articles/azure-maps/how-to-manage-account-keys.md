---
title: Az Azure a Maps-fiókot és a kulcsok kezelése |} Microsoft Docs
description: Az Azure portál segítségével kezelheti Azure Maps fiókját, és a elérési kulcsok kezelése.
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 153fb87b0e2b576fd67a24bb833165f2a1c93c56
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599660"
---
# <a name="how-to-manage-your-azure-maps-account-and-keys"></a>Az Azure a Maps-fiókot és a kulcsok kezelése

Az Azure Maps-fiókot és a kulcsok az Azure portálon keresztül kezelheti. Miután egy fiókot és egy kulcs, a webhely-vagy mobilalkalmazás az API-kat is létrehozható.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-new-account"></a>Új fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Keresse meg és jelölje ki **Maps**, majd kattintson a **létrehozása**.

3. Írja be az új fiók adatait. 

![Adja meg a fiók adatait a portálon](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>A fiók lapon kulcsok kezelése

Miután létrehozott egy olyan fiók, két véletlenszerűen létrehozott kulcs kap. A kulcsokat a Maps API-k hitelesítése, amikor adatainak beolvasása, vagy hozzon létre egy új JavaScript térkép példányt használja. 

A kulcsok az Azure-portálon találja meg. Keresse meg a fiókját, majd válasszon **kulcsok** a menüből.

![A portál fiók kulcsok kezelése](./media/how-to-manage-account-keys/account-keys-portal.png)

Ezen a lapon a kulcsok másolása, vagy újakat létrehozni. 

## <a name="delete-an-account"></a>-Fiók törlése

Törölheti a fiók az Azure portálról. Nyissa meg a fiók áttekintése lapra, és válassza ki **törlése**.

![A portál a fiók törlése](./media/how-to-manage-account-keys/account-delete-portal.png)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan használható a [képezi le a felügyeleti API](https://docs.microsoft.com/rest/api/maps-management/accounts) létrehozása, frissítése és a Maps törlését. 