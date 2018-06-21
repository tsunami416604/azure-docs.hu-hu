---
title: Az Azure API Management példány kapacitása |} Microsoft Docs
description: Ez a cikk azt ismerteti, mi a kapacitás metrika és kérdésekre vonatkozó döntések meghozatalában, hogy az Azure API Management-példányok méretezési módját.
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
ms.openlocfilehash: 4983854a14a6efe9214692dc677dedeada73933b
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296105"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Az Azure API Management-példány kapacitás

**Kapacitás** legfontosabb egy [Azure figyelő metrika](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) adja meg a megfelelő tájékozott döntéseket tudjon hozni, hogy méretezhető-e további terhelés kezelésére API Management példánya-e. A konstrukció bonyolult, és bizonyos viselkedést írja elő.

Ez a cikk azt ismerteti, mi a **kapacitás** van, és hogyan viselkedik. Azt mutatja, hogyan férhet hozzá **kapacitás** metrikák az Azure portálon, és mikor kell figyelembe venni a méretezés, vagy az API Management-példány frissítése javasol.

## <a name="prerequisites"></a>Előfeltételek

Kövesse az ebben a cikkben, kell rendelkeznie:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM példánya. További információkért lásd: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

## <a name="what-is-capacity"></a>Mi az a kapacitás

![A kapacitás metrika](./media/api-management-capacity/capacity-ingredients.png)

**Kapacitás** APIM példány terhelése mutatója. Erőforrás-használat (Processzor, memória) és hálózati várólista-hosszúságok meggátolják tükrözi. Processzor- és memóriahasználatról tárja fel az erőforrások használatát:

+ APIM szolgáltatások, például a felügyeleti műveleteket, vagy kérjen feldolgozási, amelyek magukban foglalhatják továbbítaná a kérelmeket, vagy fut egy házirend
+ a kiválasztott operációs rendszer folyamataihoz, beleértve a folyamatokat, például az új kapcsolatok használata esetén SSL-kézfogások költségét.

Teljes **kapacitás** saját API Management-példány minden egységből értékek átlaga.

## <a name="capacity-metric-behavior"></a>A kapacitás metrika viselkedése

A valós életben kialakításuk miatt **kapacitás** például számos tényező befolyásolhatja:

+ kapcsolat minták (egy kérelem vs újból felhasználja a meglévő kapcsolat az új kapcsolat)
+ a kérés- és mérete
+ minden API-t vagy az ügyfél által küldött kérelmek száma konfigurált házirendek.

A kérelemhez összetettebb műveletek esetében, a magasabb a **kapacitás** fog. Összetett átalakítási csoportházirendek például sokkal több CPU mint egy egyszerű kérelmet továbbító használja. Lassú háttér szolgáltatás válaszok növeli az túl.

> [!IMPORTANT]
> **Kapacitás** nincs közvetlen mértéke a feldolgozás alatt álló kérelmek száma.

![A kapacitás metrika igényeiben jelentkező](./media/api-management-capacity/capacity-spikes.png)

**Kapacitás** is időnként hirtelen megugró vagy akkor is, ha nincsenek feldolgozott kérelmek csak nullánál nagyobb lehet. Rendszer - vagy platform-specifikus műveletek miatt történik, és meg nem kell figyelembe veszi, amikor eldönti, hogy a példány méretezése.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Vizsgálja meg a kapacitás az Azure portál használatával
  
![A kapacitás metrika](./media/api-management-capacity/capacity-metric.png)  

1. Keresse meg a APIM példányhoz, illetve a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **metrikák (előzetes verzió)**.
3. A lila területen válassza ki **kapacitás** elérhető, és hagyja az alapértelmezett Átjárómetrika **Avg** összesítést.

    > [!TIP]
    > Azt mindig vizsgálja a **kapacitás** metrika állomásonkénti helye hibás értelmezéseket elkerülése érdekében.

4. A zöld területen válassza ki **hely** vágását meghatározó metrika dimenziónként.
5. Válassza ki a felső sávon a szakasz a kívánt időkereteket.

    Beállíthatja, hogy a mérték riasztások tájékoztatja a felhasználót, amikor valamilyen nem várt történik. Például kapott értesítések APIM példány lett exceededing a várható kapacitásához több mint 20 percig.

    >[!TIP]
    > Lehetővé teszi, hogy ha a szolgáltatás kevés a kapacitás vagy Azure figyelő automatikus skálázás funkció segítségével automatikusan adja hozzá az Azure API Management egység riasztások konfigurálása Skálázás műveletet is igénybe vehet körülbelül 30 percet, ennek megfelelően meg kell terveznie a szabályokat.  
    > A fő helye csak skálázás engedélyezett.

## <a name="use-capacity-for-scaling-decisions"></a>Használja a kapacitás méretezését döntések

**Kapacitás** a metrikát döntések, hogy méretezhető-e további terhelés kezelésére API Management példány van. Vegye figyelembe:

+ A hosszú távú trend és átlagos megnézi.
+ Figyelmen kívül hagyása hirtelen teljesítményt, amelyek valószínűleg nem kapcsolódik a terhelés növekedése (lásd a "Kapacitás metrika viselkedés" szakaszt ismertetése).
+ Frissítést vagy a példány méretezése amikor **kapacitás**tartozó értéke több, mint 60 % vagy 70 % (például 30 perces) hosszabb időtartamra. A szolgáltatás-vagy forgatókönyv jobban működnek eltérő értékek tartoznak.

>[!TIP]  
> Ha a forgalom becslése előzetesen, tesztelje a tervezett munkaterhelési típusoktól APIM példánya. A kérelem terhelés fokozatosan növelje a tenant, és milyen értéket a kapacitás mérőszám felel meg a csúcsterhelés figyelésére. Kövesse a fenti szakaszban leírt Azure-portálon használható megértéséhez, mekkora kapacitást szolgál az adott időpontban.

## <a name="next-steps"></a>További lépések

[Méretezhető, vagy egy Azure API Management service-példány frissítése](upgrade-and-scale.md)