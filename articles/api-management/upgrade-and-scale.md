---
title: Azure API Management-példány frissítése és méretezése | Microsoft dokumentumok
description: Ez a témakör ismerteti, hogyan frissítheti és skálázható egy Azure API Management-példány.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018239"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Azure API Management-példány frissítése és méretezése  

Az ügyfelek az Azure API Management (APIM) példányok méretezésével egységek hozzáadásával és eltávolításával skálázhatják. Egy **egység** dedikált Azure-erőforrásokból áll, és egy bizonyos teherbírású kapacitással rendelkezik, amelyhavonta több API-hívásban van kifejezve. Ez a szám nem egy hívási korlátot, hanem egy maximális átviteli értéket, amely lehetővé teszi a durva kapacitástervezést. A tényleges átviteli sebesség és a késés nagymértékben függ olyan tényezőktől, mint az egyidejű kapcsolatok száma és sebessége, a konfigurált házirendek fajtája és száma, a kérelem- és válaszméretek, valamint a háttérrendszer késése.

Az egyes egységek kapacitása és ára attól függ, hogy az egység **milyen szinten** van. Négy szint közül választhat: **Fejlesztő**, **Alap,** **Standard**, **Prémium**. Ha egy adott részlegen belüli szolgáltatás kapacitásának növelésére van szüksége, adjon hozzá egy egységet. Ha az APIM-példányban jelenleg kiválasztott szint nem teszi lehetővé további egységek hozzáadását, magasabb szintű szintre kell frissítenie.

Az egyes egységek ára és a rendelkezésre álló funkciók (például több régióközponti telepítés) attól függ, hogy a réteg, amely az APIM-példány választotta. A [díjszabás részleteit](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a cikk ismerteti az egységár és a funkciók kap az egyes szinteken. 

>[!NOTE]
>A [díjszabás részletei](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) az egyes rétegek egységkapacitásának hozzávetőleges számát jeleníti meg. Ahhoz, hogy pontosabb számokat kapjon, meg kell vizsgálnia az API-k reális forgatókönyvét. Tekintse meg az [Azure API Management-példány oklatait.](api-management-capacity.md)

## <a name="prerequisites"></a>Előfeltételek

A cikk ben leírt lépések követéséhez a következőket kell tennie:

+ Aktív Azure-előfizetéssel rendelkezik.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Van egy APIM-példány. További információ: [Create an Azure API Management instance](get-started-create-service-instance.md).

+ Ismerje meg az [Azure API Management-példány kapacitása fogalmát.](api-management-capacity.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Frissítés és skálázás  

Négy szint közül választhat: **Fejlesztő,** **Alap,** **Standard** és **Prémium.** A **fejlesztői** szint kell használni, hogy értékelje ki a szolgáltatást; nem használható fel termelésre. A **fejlesztői** szint nem rendelkezik SLA-val, és nem skálázhatja ezt a réteget (egységek hozzáadása/eltávolítása). 

**Alapszintű**, **Standard** és **prémium** termelési szintek, amelyek SLA-val rendelkeznek, és skálázhatók. Az **alapszintű** szint a legolcsóbb szint, amely SLA-val rendelkezik, és legfeljebb 2 egységre skálázható, **a standard** szint legfeljebb négy egységre méretezhető. Tetszőleges számú egységet adhat hozzá a **prémium** szinthez.

A **prémium** szint lehetővé teszi, hogy egyetlen Azure-API Management-példányt terjesszen tetszőleges számú kívánt Azure-régióban. Amikor először hoz létre egy Azure API Management-szolgáltatást, a példány csak egy egységet tartalmaz, és egyetlen Azure-régióban található. A kezdeti régió elsődleges **régióként** van megjelölve. További régiók könnyen hozzáadhatók. Régió hozzáadásakor meg kell adnia a lefoglalni kívánt egységek számát. Például lehet egy egység az **elsődleges** régióban és öt egység egy másik régióban. Az egységek számát az egyes régiókban lévő forgalomhoz igazíthatja. További információ: [Azure API Management szolgáltatáspéldány üzembe helyezése több Azure-régióban.](api-management-howto-deploy-multi-region.md)

Bármely szintre frissíthet és visszaválthat. Vegye figyelembe, hogy a frissítés vagy a visszaminősítés eltávolíthat bizonyos funkciókat – például vnet-eket vagy többrégiós telepítést, ha a prémium szintről standard vagy alapszintű reminősítésre való visszaminősítés.

> [!NOTE]
> A frissítési vagy méretezési folyamat alkalmazása 15 és 45 perc között is eltelhet. Értesítést kap, ha kész van.

> [!NOTE]
> API Management szolgáltatás a **felhasználási** szint skálák automatikusan a forgalom alapján.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Az Azure Portal használata frissítéshez és méretezéshez

![AZ APIM méretezése az Azure Portalon](./media/upgrade-and-scale/portal-scale.png)

1. Nyissa meg az APIM-példányt az [Azure Portalon.](https://portal.azure.com/)
2. Válassza a menü **Méretezés és árképzés parancsát.**
3. Válassza ki a kívánt szintet.
4. Adja meg a hozzáadni kívánt **egységek** számát. Használhatja a csúszkát, vagy beírhatja az egységek számát.  
    Ha a prémium szintet **választja,** először ki kell választania egy régiót.
5. Kattintson a **Mentés** gombra.

## <a name="downtime-during-scaling-up-and-down"></a>Állásidő fel- és leskálázás közben
Ha a fejlesztői szintről vagy a fejlesztői szintre skáláz, állásidő áll fenn. Ellenkező esetben nincs állásidő. 


## <a name="next-steps"></a>További lépések

- [Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md)
- [Azure API Management szolgáltatáspéldány automatikus méretezése](api-management-howto-autoscale.md)
