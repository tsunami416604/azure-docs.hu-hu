---
title: Azure-végpont előfizetés kezelése |} Microsoft Docs
description: Ebben a cikkben a LUIS fiókját adja meg a végpont a következő fizetési terv korlátlan forgalmat mért végpont kulcsot létrehozni.
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 3526871f126ac975f323fe84b14883b361b684ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349795"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Azure-végpont előfizetés kulcsok kezelése

A teszteléshez és prototípus csak a szabad (F0) csomagot használja. Éles rendszerek esetén használja a [fizetett](https://aka.ms/luis-price-tier) réteg. 

> [!NOTE]
> Ne használja a [kulcs szerzői](luis-concept-keys.md#authoring-key) végpont lekérdezések éles környezetben.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>LUIS végpont-kulcs létrehozása

1. Jelentkezzen be  **[Microsoft Azure](https://ms.portal.azure.com/)** 
2. Kattintson a zöld **+** jelentkezzen be a felső bal oldali panelen, és keressen a "LUIS" a piactéren, majd kattintson a **nyelvi ismertetése** , és kövesse a **környezetet**  LUIS előfizetés-fiók létrehozásához. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. Konfigurálja az előfizetés beállításokkal fiók nevét, például árképzési rétegek stb. 

    ![Az Azure API kiválasztása](./media/luis-azure-subscription/azure-api-choice.png) 

4. Miután létrehozta a LUIS szolgáltatást, megtekintheti a hívóbetűk hozott létre **erőforrás-kezelés -> kulcsok**.  

    ![Az Azure-kulcsok](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > * Jelentkezzen be a régió [LUIS](luis-reference-regions.md) webhely és [adja hozzá az új LUIS végpontkulcs](Manage-Keys.md#assign-endpoint-key). 
    > * Jegyezze meg a nevet kell ahhoz, hogy válassza ki azt a régió létrehozása az Azure szolgáltatás [LUIS](luis-reference-regions.md) közzéteszi a lapot.  

## <a name="change-luis-pricing-tier"></a>Változás LUIS tarifacsomag kiválasztása

1.  A [Azure](https://portal.azure.com), LUIS előfizetése található. Kattintson a LUIS előfizetésre.
    ![Az LUIS előfizetés keresése](./media/luis-usage-tiers/find.png)
2.  Kattintson a **tarifacsomag** láthatók az elérhető árképzési szinteket. 
    ![Tarifacsomagok megtekintése](./media/luis-usage-tiers/subscription.png)
3.  Kattintson a tarifacsomagot, majd **válasszon** menteni a módosítást. 
    ![A LUIS fizetési szint módosítása](./media/luis-usage-tiers/plans.png)
4.  Ha befejeződött a tarifacsomag módosítása, egy előugró ablak ellenőrzi az új árképzési szint. 
    ![Ellenőrizze a LUIS fizetési réteg](./media/luis-usage-tiers/updated.png)
5. Ne felejtse el [rendelje hozzá a végpont kulcs](manage-keys.md#assign-endpoint-key) a a **közzététel** lapon, és annak összes végpont lekérdezés használata. 

## <a name="exceed-pricing-tier-usage"></a>Haladhatja meg az árképzési szint kihasználtsága
Minden egyes réteg meghatározott arányban lehetővé teszi a végpont kérelmek LUIS fiókjába. Ha kérelmek száma nagyobb, mint a megengedett aránya a díjköteles fiók / perc vagy havonta, kérelmek kapnak egy HTTP-hiba a "429: túl sok kérelmet."

Minden egyes réteg lehetővé teszi a havi adódnak össze kérelmek. Ha a kérelmek teljes száma a magasabb, mint az engedélyezett sebességét, a kérelmeket a HTTP hibaüzenetet kap "403: tiltott".  

## <a name="viewing-summary-usage"></a>Összegző használati megtekintése
Az Azure-ban LUIS használati információk is megtekinthetők. A **áttekintése** lap többek között a hívások és hibák legutóbbi összegző információit jeleníti meg. Ha elvégezte a LUIS végpont kérelmet, majd azonnal tekintse meg a **– áttekintés oldalra**, akár öt percet megjeleníti őket a használatának engedélyezése.

![Összegző használati megtekintése](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Használati diagramok testreszabása
Metrikák révén részletes képet kaphat az adatokat.

![Alapértelmezett metrikák](./media/luis-usage-tiers/metrics-default.png)

A metrikák diagramok konfigurálása időszakot és a típust. 

![Egyéni metrikák](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Teljes tranzakciók küszöbértékének riasztási
Ha szeretné tudni, hogy amikor elérte az egy bizonyos tranzakció küszöböt, például 10 000 tranzakciók, létrehozhat egy riasztást. 

![Alapértelmezett riasztások](./media/luis-usage-tiers/alert-default.png)

A metrika értesítés hozzáadása a **összes hívás** metrika egy adott időszakra vonatkozóan. Adja hozzá, amelyek megkapják a riasztás összes személy e-mail címét. Adja hozzá a webhookok kell kapnia a riasztás összes rendszerek számára. A riasztás aktiválása is futtathatja egy logikai alkalmazást. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható [verziók](luis-how-to-manage-versions.md) a LUIS alkalmazás kezelése.