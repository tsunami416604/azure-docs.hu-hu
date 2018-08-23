---
title: Frissítés és skálázás az Azure API Management-példány |} A Microsoft Docs
description: Ez a témakör ismerteti a frissítés és skálázás az Azure API Management-példány.
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
ms.openlocfilehash: 99848cf4ba1e6e65a8c41c682916ca391128eb21
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056912"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Frissítés és skálázás az Azure API Management-példány  

Ügyfeleink az Azure API Management (APIM) példány méretezhetők egységek hozzáadásával vagy eltávolításával. A **egység** szintből áll, dedikált Azure-erőforrások és a egy bizonyos terhelés-hatással kapacitás kifejezett API számos hívások / hó. Ez a szám nem hívás korlátozva, de ahelyett, hogy a nyers kapacitásának tervezéséhez maximális átviteli sebesség értéket képvisel. Tényleges átviteli sebességgel és késéssel széles körben tényezők, például száma és sebessége, az egyidejű kapcsolatok, típusa és száma konfigurált szabályzatok, a kérelem és a válaszok méretétől és a háttéralkalmazás késése függően változnak.

Kapacitás és az egyes egységek díj attól függ, a **szint** az egység létezik. Négy szinten közül választhat: **fejlesztői**, **alapszintű**, **Standard**, **prémium**. Ha növeli a kapacitást a szinten belüli szolgáltatásként van szüksége, hozzá kell adnia egy egységet. A jelenleg kiválasztott az APIM-példányra a réteg nem engedélyezi a több egység hozzáadása, a magasabb szintű csomagra frissíteni szeretne.

Minden egység és a rendelkezésre álló funkciók (például a többrégiós üzembe helyezés) ára attól függ, hogy a szint, az APIM-példányra számára is választott. A [díjszabás](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) cikk mellett, az ár / egység és funkciónak, az egyes szintek ismerteti. 

>[!NOTE]
>A [díjszabás](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a cikk bemutatja a egység kapacitás hozzávetőleges számát az egyes szintek. Lekérheti a pontosabb számokat, nézze meg API-k valószerűbb forgatókönyvet kell. Tekintse meg a [az Azure API Management-példány kapacitása](api-management-capacity.md) cikk.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a lépések követéséhez a következőket kell tennie:

+ Aktív Azure-előfizetéssel rendelkezik.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM-példány van. További információkért lásd: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

+ [Az Azure API Management példányok kapacitását] fogalma ismertetése (api-kezelés – capacity.md).

## <a name="upgrade-and-scale"></a>Frissítés és skálázás  

Négy szinten közül választhat: **fejlesztői**, **alapszintű**, **Standard** és **prémium**. A **fejlesztői** szinten a szolgáltatás értékeléséhez használandó; ez nem használható éles üzemi környezetek részei. A **fejlesztői** szint nem rendelkezik SLA-t, és nem skálázhatja a csomag (egység hozzáadása/eltávolítása). 

**Alapszintű**, **Standard** és **prémium** termelési szint, amelyek SLA-t és skálázhatók. A **alapszintű** szintje a legolcsóbb szint, amely rendelkezik SLA-t és a méretezett legfeljebb 2 egység, azok **Standard** számítógépréteg bővíthető legfeljebb négy. Tetszőleges számú egységeket is hozzáadhat a **prémium** szint.

A **prémium** szint lehetővé teszi, hogy egyetlen Azure API Management példány szét a kívánt Azure-régiók tetszőleges számú. Először létrehoz egy Azure API Management szolgáltatást, ha a példány csak egy egységet tartalmaz, és a egy adott Azure-régióban található. A kezdeti régió elsődlegesként lett megjelölve a **elsődleges** régióban. További régiók könnyedén adhat hozzá. Egy régióban hozzáadásakor meg kell adnia a hozzárendelni kívánt egységek számát. Például használhat egy egységet a **elsődleges** régió és más régióban öt egységek. Testre szabhatja a forgalomra, minden régióban van egységek száma. További információkért lásd: [egy Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md).

Is, majd, és bármely szintről. Vegye figyelembe, hogy a frissítés vagy alacsonyabb verziójúra változtatása távolíthatja el bizonyos funkciók – például a virtuális hálózatok vagy a több régióból álló üzemelő, ha alacsonyabb szolgáltatásszintre Standard vagy Alapszintűre vált.

>[!NOTE]
>A frissítés vagy a méretezési csoport folyamat is igénybe vehet a 15-45 percre a alkalmazni. Értesítést kaphat, amikor kész van.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Frissítés és skálázás az Azure portal használatával

![APIM méretezés az Azure Portalon](./media/upgrade-and-scale/portal-scale.png)

1. Keresse meg az APIM-példányra, az a [az Azure portal](https://portal.azure.com/).
2. Válassza ki **skálázás és díjszabás** a menüből.
3. Válassza ki a kívánt tarifacsomagot.
4. Adja meg, hány **egységek** szeretne hozzáadni. A csúszka, vagy írja be az egységek számát.  
    Ha úgy dönt, a **prémium** szint, először ki kell választania egy régiót.
5. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

*[Az Azure API Management-szolgáltatáspéldány üzembe helyezése több Azure-régióban](api-management-howto-deploy-multi-region.md)
*[automatikus méretezése egy Azure API Management-szolgáltatáspéldány](api-management-howto-autoscale.md)