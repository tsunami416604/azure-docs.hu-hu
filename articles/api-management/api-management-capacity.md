---
title: Egy Azure API Management példány kapacitása | Microsoft dokumentumok
description: Ez a cikk ismerteti, mi a kapacitás metrika, és hogyan hozhat megalapozott döntéseket arról, hogy egy Azure API-felügyeleti példány méretezése.
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
ms.custom: fasttrack-edit
ms.openlocfilehash: b6d949b50be348e72cedfc3710383308b04de106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336008"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Az Azure API Management-példányok kapacitása

**A kapacitás** a legfontosabb [Azure Monitor-metrika,](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) amely megalapozott döntéseket hoz arról, hogy egy API Management-példányt skáláz-e a nagyobb terhelés hez. Építése összetett és bizonyos viselkedést ír elő.

Ez a cikk ismerteti, hogy mi a **kapacitás,** és hogyan viselkedik. Bemutatja, hogyan **capacity** érheti el a kapacitásmetrikákat az Azure Portalon, és azt javasolja, hogy mikor vegye figyelembe az API Management-példány méretezését vagy frissítését.

> [!IMPORTANT]
> Ez a cikk bemutatja, hogyan figyelheti és skálázhatja az Azure API Management-példány kapacitásmetrikája alapján. Ugyanakkor ugyanilyen fontos megérteni, hogy mi történik, ha egy adott API Management-példány ténylegesen *elérte* a kapacitását. Az Azure API Management nem alkalmaz semmilyen szolgáltatásszintű szabályozást a példányok fizikai túlterhelésének megelőzése érdekében. Amikor egy példány eléri a fizikai kapacitását, hasonlóan fog elhasználódni minden olyan túlterhelt webkiszolgálóhoz, amely nem tudja feldolgozni a bejövő kérelmeket: a késés növekedni fog, a kapcsolatok megszakadnak, időtúllépési hibák lépnek fel stb. Ez azt jelenti, hogy az API-ügyfeleknek fel kell készülniük arra, hogy ezt a lehetőséget ugyanúgy kezeljék, mint bármely más külső szolgáltatást (pl. újrapróbálkozási házirendek alkalmazásával).

## <a name="prerequisites"></a>Előfeltételek

A cikk ben leírt lépések követéséhez a következőkre van szüksége:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Egy APIM-példány. További információ: [Create an Azure API Management instance](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Mi a kapacitás

![Kapacitási mérőszám](./media/api-management-capacity/capacity-ingredients.png)

**A kapacitás** egy API Management-példány terhelésének jelzője. Ez tükrözi az erőforrások használatát (CPU, memória) és a hálózati várólista hossza. A PROCESSZOR- és memóriahasználat a következők segítségével tárja fel az erőforrások felhasználását:

+ API-kezelés adatsík-szolgáltatások, például a kérelmek feldolgozása, amely magában foglalhatja a kérelmek továbbítása vagy a szabályzat futtatása.
+ Az API Management management síkszolgáltatások, például az Azure Portalon vagy az ARM-on keresztül alkalmazott felügyeleti műveletek, vagy a [fejlesztői portálról](api-management-howto-developer-portal.md)érkező terhelés.
+ A kiválasztott operációsrendszer-folyamatok, beleértve azokat a folyamatokat is, amelyek az új kapcsolatokra vonatkozó TLS-kézfogások költségét foglalják magukban.

A teljes **kapacitás** a saját értékeinek átlaga egy API Management-példány minden egységéből.

Bár a **kapacitásmetrika** úgy van kialakítva, hogy problémákat okozz az API Management-példánysal, vannak olyan esetek, amikor a problémák nem jelennek meg a **kapacitásmetrika**változásaiban.

## <a name="capacity-metric-behavior"></a>Kapacitásmérő viselkedése

Az építés miatt a valós **életkapacitásra** számos változó hatással lehet, például:

+ kapcsolati minták (új kapcsolat kérésre és a meglévő kapcsolat újrafelhasználása)
+ a kérelem és a válasz mérete
+ az egyes API-kon vagy a kéréseket küldő ügyfelek számán konfigurált házirendek.

Minél összetettebb műveletek a kérelmek, annál nagyobb a **kapacitás-fogyasztás** lesz. Például az összetett átalakítási házirendek sokkal több PROCESSZORt fogyasztanak, mint egy egyszerű kérelemtovábbítás. Lassú háttér szolgáltatás válasz akarat növekszik ez túl.

> [!IMPORTANT]
> **A kapacitás** nem a feldolgozott kérelmek számának közvetlen mérőszáma.

![Kapacitásmetrikus csúcsok](./media/api-management-capacity/capacity-spikes.png)

**A kapacitás** is kiugró szakaszosan, vagy nagyobb, mint nulla akkor is, ha nincsenek feldolgozás alatt nem kérelmek feldolgozása. Ez a rendszer- vagy platformspecifikus műveletek miatt történik, és nem szabad figyelembe venni annak eldöntésekor, hogy egy példányméretezése megtörtént-e.

Az alacsony **kapacitásmérő** nem feltétlenül jelenti azt, hogy az API Management-példány nem tapasztal semmilyen problémát.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Az Azure Portal használata a kapacitás vizsgálatához
  
![Kapacitási mérőszám](./media/api-management-capacity/capacity-metric.png)  

1. Nyissa meg az APIM-példányt az [Azure Portalon.](https://portal.azure.com/)
2. Válassza a **Metrika** lehetőséget.
3. A lila szakaszban válassza **kapacitás metrika** a rendelkezésre álló metrikák, és hagyja meg az alapértelmezett **avg** összesítés.

    > [!TIP]
    > Mindig tekintse meg a **kapacitás** metrikus bontáshely helyenként, hogy elkerüljék a téves értelmezések.

4. A zöld szakaszban válassza a **Hely lehetőséget** a metrika dimenzió szerint történő felosztásához.
5. Válassza ki a kívánt időkeretet a szakasz felső sávjából.

    Beállíthat egy metrikariasztást, amely értesíti, ha valami váratlan történik. Például értesítést kaphat, ha az APIM-példány több mint 20 percig túllépte a várható csúcskapacitást.

    >[!TIP]
    > Beállíthatja, hogy a riasztások, hogy tudassa, ha a szolgáltatás kapacitása alacsony, vagy használja az Azure Monitor automatikus skálázási funkció automatikusan hozzá egy Azure API Management egység automatikusan hozzá. A skálázási művelet körülbelül 30 percet is igénybe vehet, ezért ennek megfelelően kell megterveznie a szabályokat.  
    > Csak a főhely méretezése engedélyezett.

## <a name="use-capacity-for-scaling-decisions"></a>Kapacitás használata a döntések méretezése során

**A kapacitás** a metrika, amely eldönti, hogy egy API Management-példány méretezése több terhelés t. Megfontolandó szempontok:

+ Keresi a hosszú távú trend és az átlagos.
+ Figyelmen kívül hagyva a hirtelen kiugrásokat, amelyek valószínűleg nem kapcsolódnak a terhelés növekedéséhez (magyarázatért lásd a "Kapacitás metrika viselkedése" című szakaszt).
+ A példány frissítése vagy méretezése, ha a **kapacitás**értéke hosszabb ideig (például 30 perc) meghaladja a 60%-ot vagy a 70%-ot. A különböző értékek jobban működhetnek a szolgáltatás vagy a forgatókönyv számára.

>[!TIP]  
> Ha előre meg tudja becsülni a forgalmat, tesztelje az APIM-példányt a várt számítási feladatokon. Fokozatosan növelheti a bérlői kérelemterhelést, és figyelheti, hogy a kapacitásmetrika milyen értéke felel meg a csúcsterhelésnek. Kövesse az előző szakaszlépéseit az Azure Portal használatához, hogy megértse, mekkora kapacitást használ fel egy adott időpontban.

## <a name="next-steps"></a>További lépések

[Azure API Management szolgáltatáspéldány méretezése vagy frissítése](upgrade-and-scale.md)