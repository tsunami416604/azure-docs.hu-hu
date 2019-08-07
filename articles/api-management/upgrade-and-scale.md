---
title: Azure API Management-példány frissítése és méretezése | Microsoft Docs
description: Ez a témakör az Azure API Management-példányok frissítését és méretezését ismerteti.
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
ms.openlocfilehash: 6bafd5ed5f2d7080b0f2a2db71ac96e4f97a1f76
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774941"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Azure API Management-példány frissítése és méretezése  

Az Azure API Management-(APIM-) példányok az egységek hozzáadásával és eltávolításával méretezhetők. Az **egységek** dedikált Azure-erőforrásokból állnak, és egy bizonyos terhelési kapacitással rendelkeznek, amely havonta több API-hívással van kifejezve. Ez a szám nem jelent hívási korlátot, hanem a maximális átviteli sebességet, amely lehetővé teszi a durva kapacitás megtervezését. A tényleges átviteli sebesség és a késés a különböző tényezőktől függ, például az egyidejű kapcsolatok száma és sebessége, a konfigurált szabályzatok típusa és száma, a kérelmek és a válaszok mérete, valamint a háttérbeli késés.

Az egyes egységek kapacitása és ára attól függ, hogy az egység milyen szinten található. Négy szintet is választhat: **Fejlesztői**,alapszintű, **standard**, **prémium**. Ha a kapacitást egy adott szinten szeretné bővíteni, vegyen fel egy egységet. Ha a APIM-példányban jelenleg kiválasztott réteg nem teszi lehetővé több egység hozzáadását, akkor magasabb szintű szintre kell frissítenie.

Az egyes egységek és a rendelkezésre álló funkciók (például a többrégiós telepítés) díja a APIM-példányra kiválasztott szinttől függ. A [díjszabás részleteit ismertető](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) cikk az egyes csomagokban kapott egységenkénti és-funkciók árát ismerteti. 

>[!NOTE]
>A [díjszabási részletekről](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) szóló cikk az egység kapacitásainak hozzávetőleges számát mutatja az egyes csomagokban. A pontosabb számok megszerzéséhez az API-k reális forgatókönyvét kell megvizsgálnia. Tekintse meg az [Azure API Management-példány kapacitását](api-management-capacity.md) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek követéséhez a következőket kell tennie:

+ Aktív Azure-előfizetéssel rendelkezik.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Rendelkeznie kell APIM-példánnyal. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

+ Ismerje meg az [Azure API Management-példány kapacitásának](api-management-capacity.md)koncepcióját.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Frissítés és skálázás  

Négy szintet is választhat: **Fejlesztői**,alapszintű, **standard** és **prémium**. A **fejlesztői** szintet a szolgáltatás kiértékeléséhez kell használni; nem használható éles környezetben. A **fejlesztői** szint nem rendelkezik SLA-val, és nem méretezheti ezt a szintet (egység hozzáadása/eltávolítása). 

Az alapszintű, a **standard** és a **prémium** a SLA-val rendelkező, és méretezhető. Az alapszintű **csomag** az SLA-val rendelkező legolcsóbb szint, amely akár 2 egységig bővíthető, a **standard** szint pedig akár négy egységig is méretezhető. A **prémium** szinthez tetszőleges számú egységet adhat hozzá.

A **prémium** szint lehetővé teszi egyetlen Azure API Management-példány terjesztését tetszőleges számú, a kívánt Azure-régióban. Amikor először hoz létre egy Azure API Management-szolgáltatást, a példány csak egy egységet tartalmaz, és egyetlen Azure-régióban helyezkedik el. A kezdeti régió **elsődleges** régióként van kijelölve. A további régiók egyszerűen hozzáadhatók. Régió hozzáadásakor meg kell adnia, hogy hány egységet szeretne lefoglalni. Rendelkezhet például egy egységgel az **elsődleges** régióban és öt egységben egy másik régióban. Az egységek számát az egyes régiókban található forgalomhoz igazíthatja. További információ: [azure API Management Service-példány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md).

Bármely szintre frissítheti és visszaléphet. Vegye figyelembe, hogy a verziófrissítés vagy a visszalépési művelettel eltávolíthat bizonyos funkciókat – például a virtuális hálózatok vagy a többrégiós telepítést, ha a prémium szintről standard vagy alapszintű értékre vált.

>[!NOTE]
>A frissítés vagy a méretezési csoport folyamat is igénybe vehet a 15-45 percre a alkalmazni. Értesítést kap, ha elkészült.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>A Azure Portal használata a frissítéshez és a méretezéshez

![APIM méretezése Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. Navigáljon a [Azure Portal](https://portal.azure.com/)APIM-példányához.
2. Válassza a **Méretezés és díjszabás** lehetőséget a menüből.
3. Válassza ki a kívánt szintet.
4. Adja meg a hozzáadni kívánt **egységek** számát. Használhatja a csúszkát, vagy megadhatja az egységek számát.  
    Ha a **prémium** szintet választja, először ki kell választania egy régiót.
5. Kattintson a **Mentés** gombra.

## <a name="downtime-during-scaling-up-and-down"></a>Leállás a felfelé és lefelé skálázás során
Ha a fejlesztői szinten vagy a-ból végez méretezést, a rendszer leállást eredményez. Ellenkező esetben nincs leállás. 


## <a name="next-steps"></a>További lépések

- [Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md)
- [Azure API Management Service-példány automatikus méretezése](api-management-howto-autoscale.md)
