---
title: Frissítés és az Azure API Management példány méretezése |} Microsoft Docs
description: Ez a témakör ismerteti, hogyan frissítheti, és az Azure API Management példány méretezése.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: ca32c72b1582b2a09f9f1754ad778cf1b682a1c2
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293312"
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Frissítés és az API Management példány méretezése  

Ügyfelek hozzáadása és eltávolítása egységek által méretezhető API Management (APIM) példánya. A **egység** dedikált Azure-erőforrások össze, és egy bizonyos terhelést hordozó API számos meghívja a havi kifejezett kapacitás. Ez az érték nem felel meg a hívás korlátozása, de ahelyett, hogy a maximális átviteli sebesség az értéket a nyers kapacitástervezés. Tényleges átviteli sebesség és a késleltetés függenek körben száma és az egyidejű kapcsolatok sebessége tényezők, a konfigurált házirendek, a kérelem-válasz méretek és a háttérkiszolgáló késleltetés száma és típusa.

Kapacitás és az egyes egységek ára függ a **réteg** a egység létezik. Négy szintek közül választhat: **fejlesztői**, **alapvető**, **szabványos**, **prémium**. Ha egy réteget egy kézbesítési kapacitásbővítés van szüksége, adja hozzá egy egység. A jelenleg kiválasztott APIM példány réteg nem engedélyezi a további egységeket, ha szüksége, váltson egy magasabb szintű csomagra.

Az ár tárolóegységekhez és a rendelkezésre álló funkciók (például több területi telepítési) a réteg a APIM példány választott függ. A [díjszabása](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) cikk, egység és funkciónak, amelyeket az egyes rétegekbe ára ismerteti. 

>[!NOTE]
>A [díjszabása](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a cikk bemutatja egység kapacitás hozzávetőleges számát az egyes rétegek. Ahhoz, hogy pontosabb számok, tekintse meg az API-jainak valós forgatókönyv kell. Tekintse meg a [az Azure API Management példány kapacitása](api-management-capacity.md) cikk.

## <a name="prerequisites"></a>Előfeltételek

Kövesse az ebben a cikkben, a következőket kell tennie:

+ Aktív Azure-előfizetéssel rendelkezik.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Rendelkezik egy APIM példányával. További információkért lásd: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

+ [Kapacitás az Azure API Management-példány] koncepció ismertetése (api-felügyeleti-capacity.md).

## <a name="upgrade-and-scale"></a>Frissítés és skálázás  

Négy szintek közül választhat: **fejlesztői**, **alapvető**, **szabványos** és **prémium**. A **fejlesztői** réteg értékelni a szolgáltatást kell használni; ez nem használható termelési környezetben. A **fejlesztői** réteg nem rendelkezik SLA-t, és a réteg (hozzáadása egységek) nem lehet méretezni. 

**Alapszintű**, **szabványos** és **prémium** éles rétegek, amelyek SLA-t, és megadhat vannak. A **alapvető** réteg a legolcsóbb szint, amely rendelkezik SLA-t, és azok méretezett legfeljebb 2 egység **szabványos** kiterjeszthető legfeljebb négy egységeire. Tetszőleges számú egységeket is hozzáadhat a **prémium** réteg.

A **prémium** réteg lehetővé teszi a kívánt Azure-régiók tetszőleges számú API management egypéldányos szét. Kezdeti létrehozásakor az API Management service, a példány csak egy egységet tartalmaz, és egyetlen Azure-régiót találhatók. A kezdeti régió van kijelölve a **elsődleges** régióban. További régiókban könnyen lehet hozzáadni. Egy régiót hozzáadásakor meg kell adnia a lefoglalni kívánt egységek számát. Például lehet egy egységet a **elsődleges** régió és néhány más régióban öt egység. Könnyebben igazíthatja a forgalomra, minden régióban van egységek száma. További információkért lásd: [üzembe helyezése az Azure API Management szolgáltatáspéldány több Azure-régiók](api-management-howto-deploy-multi-region.md).

Frissítse, és bármely réteg érkező vagy oda irányuló visszaminősítését. Vegye figyelembe, hogy frissítése vagy alacsonyabb verziójúra változtatása távolíthatja el bizonyos szolgáltatásokat – például a Vnetek vagy több régió központi telepítését, ha a prémium szintű rétegtől alapszintű vagy Standard visszaminősítése.

>[!NOTE]
>A frissítés vagy skálája eltarthat 15 45 percig alkalmazni. Ha elkészült értesítést kap.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Az Azure portál segítségével frissítése és méretezése

![Skála APIM Azure-portálon](./media/upgrade-and-scale/portal-scale.png)

1. Keresse meg a APIM példányhoz, illetve a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **és az árképzés** a menüből.
3. Válassza ki a kívánt réteget.
4. Adja meg, hány **egységek** hozzá szeretné adni. A csúszka, vagy írja be a egységek számát.  
    Ha úgy dönt, a **prémium** réteg, először válasszon ki egy régiót.
5. Nyomja le az **mentése**

## <a name="next-steps"></a>További lépések

[Az Azure API Management szolgáltatáspéldány üzembe helyezése több Azure-régiók](api-management-howto-deploy-multi-region.md)