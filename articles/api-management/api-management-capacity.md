---
title: Az Azure API Management-példány kapacitása |} A Microsoft Docs
description: Ez a cikk bemutatja, mit jelent a kapacitás-metrika, és hogyan lehet tájékozott döntést e skálázásra Azure API Management-példány.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: 31959cc1bef6b6434f6d3f586052a845837aa438
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442591"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Az Azure API Management példányok kapacitását

**Kapacitás** van a közös, legfontosabb [Azure Monitor metrika](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) az e skálázásra az API Management példányok több terhelés kezelésére, így megalapozott döntéseket hozhasson. A konstrukció összetett, és bizonyos viselkedés ír elő.

Ez a cikk leírja, mi a **kapacitás** van, és hogyan viselkedik. Azt szemlélteti, hogyan lehet hozzáférni **kapacitás** mérőszámok az Azure Portalon, és mikor érdemes figyelembe venni a méretezés, vagy az API Management-példány frissítése javasol.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a lépések követéséhez rendelkeznie:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM-példány. További információkért lásd: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Mit jelent a kapacitás

![Kapacitási mérőszám](./media/api-management-capacity/capacity-ingredients.png)

**Kapacitás** azt jelzi, hogy a terhelés az APIM-példány van. Erőforrás-használat (CPU, memória) és a hálózati várólista-hosszúságok tükrözi. Processzor-és memória erőforrások fogyasztásának tárja fel:

+ APIM-szolgáltatások, például a felügyeleti műveletek vagy a kérelem feldolgozása, amelyek magukban foglalhatják továbbítaná a kérelmeket, vagy fut egy házirend
+ a kiválasztott operációs rendszer folyamataihoz, beleértve az SSL-kézfogások új kapcsolatokat, költség érintő folyamatokat.

Teljes **kapacitás** átlagosan az API Management-példány egység a saját értékeit.

## <a name="capacity-metric-behavior"></a>A kapacitás metrikákkal kapcsolatos viselkedés

A valós életben kialakításuk miatt **kapacitás** is negatív hatással lehet számos változók, például:

+ (új kapcsolat a kérés és a meglévő kapcsolat újbóli használata) kapcsolat minták
+ egy kérelem-válasz mérete
+ minden API-t vagy a kérésekkel ügyfelek számát a konfigurált szabályzatok.

A kérelmeket az összetettebb műveleteket is, a magasabb a **kapacitás** használat lesz. Összetett átalakítási szabályzatok például sokkal több CPU mint egy egyszerű kérelem továbbítási használnak fel. Lassú háttérrendszer szolgáltatás válaszok túl megnöveli azt.

> [!IMPORTANT]
> **Kapacitás** nem közvetlen méri, feldolgozás alatt álló kérelmek száma.

![A kapacitás metrika ugrásszerűen](./media/api-management-capacity/capacity-spikes.png)

**Kapacitás** is időszakosan hirtelen, vagy csak nullánál nagyobb, akkor sem, ha nincs a kérelem feldolgozása folyamatban. Rendszer - vagy a platform-specifikus műveletek miatt történik, és a meg nem figyelembe kell venni, amikor eldönti, hogy skálázni egy példányt.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Vizsgálja meg a kapacitást az Azure Portal használatával
  
![Kapacitási mérőszám](./media/api-management-capacity/capacity-metric.png)  

1. Keresse meg az APIM-példányra, az a [az Azure portal](https://portal.azure.com/).
2. Válassza ki **metrikák (előnézet)**.
3. A lila területen válassza ki **kapacitás** mérőszám a rendelkezésre álló metrikák és megtarthatja az alapértelmezett **átlagos** összesítést.

    > [!TIP]
    > Akkor mindig tekintse meg a **kapacitás** metrika lebontása helye nem megfelelő értelmezések elkerülése érdekében.

4. A zöld területen válassza ki **hely** vágását meghatározó a mérőszám a dimenzió által.
5. Válassza ki a felső sávon a szakasz egy kívánt időkeretet.

    Metrikariasztás értesíti, amikor valami váratlan történik állíthatja be. Ha például értesítések küldése, ha az APIM-példányra lett túllépi a hívásdíjkorlátját több mint 20 percig a várható maximális kapacitást.

    >[!TIP]
    > Lehetővé teszi, hogy értesíti, amikor a szolgáltatás kevés a kapacitás, vagy az Azure Monitor automatikus skálázási funkció használatával automatikusan adja hozzá az Azure API Management egységek riasztásokat lehet konfigurálni. Skálázási művelet is igénybe vehet körülbelül 30 percet, hogy ennek megfelelően meg kell terveznie a szabályok.  
    > Csak a fő helye Méretezés engedélyezve van.

## <a name="use-capacity-for-scaling-decisions"></a>Használja a kapacitás méretezését döntéseket hozhat

**Kapacitás** a metrika a döntéseket, e skálázásra API Management-példány, a további terhelés kezelésére. Vegye figyelembe:

+ A hosszú távú trendek és az átlag Hibaoldal.
+ A terhelés növelése figyelmen kívül hagyja a hirtelen ugrásszerűen legtöbbször nem kapcsolódik (lásd "A metrikákkal kapcsolatos viselkedést kapacitás" ismertetése).
+ Frissítést vagy a példány skálázása során **kapacitás**a érték meghaladja a 60 %-os, vagy 70 %-os egy hosszabb ideig (például 30 percig). Eltérő értékek jobban működnek, szolgáltatás vagy helyzetnek megfelel.

>[!TIP]  
> Ha tudja a forgalmat előre megbecsülni, tesztelje az APIM-példányra a számítási feladatok várt. A kérelmek által generált terhelést fokozatosan növelje a bérlő, és figyelheti a kapacitás mérőszám milyen érték megfelel a csúcsterhelés között. Kövesse az előző szakaszban az Azure portál használatával megismerheti, hogy mekkora kapacitást szolgál egy adott időpontban.

## <a name="next-steps"></a>További lépések

[Méretezhető, vagy egy Azure API Management-szolgáltatáspéldány frissítése](upgrade-and-scale.md)