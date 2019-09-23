---
title: Díjak elkerülése az ingyenes Azure-fiókkal
description: Tudja meg, miért merülnek fel díjak ingyenes Azure-fiókja használatakor. Ismerje meg, hogyan lehet elkerülni ezeket a díjakat.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ac5bcf0a4dd9c7ee593c70a63509a21f28ba1456
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491482"
---
# <a name="avoid-charges-with-your-azure-free-account"></a>Díjak elkerülése az ingyenes Azure-fiókkal

Az ingyenes Azure-fiókhoz 200 dollár értékű, 30 napig felhasználható Azure-kredit jár, valamint 12 hónapos, mennyiségileg korlátozott hozzáférés ingyenes szolgáltatásokhoz. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. A kreditje állapotától függően felhasználhatja azt, vagy fizetnie kell az ingyenes szolgáltatásokon és mennyiségeken felüli használatért.

## <a name="azure-free-account-might-use-account-credit"></a>Az ingyenes Azure-fiók felhasználhatja a fiókkreditet
Ha még rendelkezik Azure-kredittel, amely még nem járt le, az Azure a kreditjét használja fel az ingyenes szolgáltatásokon és mennyiségeken felüli használatért való fizetéshez.

## <a name="your-credit-runs-out-or-is-expired"></a>Ha a kreditje elfogyott vagy lejárt
Ha a kreditje elfogyott vagy a 30 nap elteltével lejárt, az Azure letiltja az előfizetést. Az Azure-szolgáltatások további használatához egyéni előfizetésre kell frissítenie, használatalapú fizetési díjszabással. További információkat az [ingyenes próbaverziós Azure-előfizetés frissítésénél](billing-upgrade-azure-subscription.md) olvashat. A frissítést követően 12 hónapig továbbra is hozzáférhet az ingyenes szolgáltatásokhoz. Csak az ingyenes szolgáltatásokon és mennyiségeken felüli használatért kell fizetnie.

Vizsgáljunk meg néhány indokot, amelyek miatt költségek merülhetnek fel az ingyenes Azure-fiókja használatakor.

### <a name="usage-exceeds-the-limits-of-free-services"></a>Ha a használat meghaladja az ingyenes szolgáltatások korlátait

Az ingyenes Azure-fiókhoz minden hónapban korlátozott mennyiségű ingyenes szolgáltatás jár. Az ingyenes mennyiség a hónap végén lejár, és nem vihető át a következő hónapra. Tegyük fel például, hogy havonta 5 GB File Storage-tárterületet kap. Ha egy hónapban csak 2 GB-ot használ fel, a fennmaradó 3 GB nem használható fel a következő hónapban. A felszámított díjak elkerülése érdekében ügyeljen rá, hogy a használata ne haladja meg a korlátokat. Az ingyenes szolgáltatások korlátainak megismeréséhez tekintse meg [az ingyenes Azure-fiókkal kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/free/free-account-faq/). Az ingyenes szolgáltatások használatának ellenőrzéséhez tekintse meg [az ingyenes Azure-fiókhoz kapcsolódó ingyenes szolgáltatások használatának ellenőrzését](billing-check-free-service-usage.md) ismertető szakaszt.

### <a name="some-services-are-not-free"></a>Egyes szolgáltatások nem ingyenesek

Az olyan szolgáltatások használatáért, amelyek nem tartoznak az ingyenes Azure-fiókhoz mellékelt ingyenes szolgáltatások közé, használatalapú díjszabással kell fizetni. Az ingyenes fiókhoz mellékelt szolgáltatások megismeréséhez tekintse meg [az ingyenes Azure-fiókkal kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/free/free-account-faq/). A szolgáltatások használatának ellenőrzését az Azure Portalon vagy az Azure-használati fájlja segítségével végezheti el. További információért tekintse meg [a költséglebontás és a keretfelhasználási ráta a Portalon történő rendszeres ellenőrzését](billing-getting-started.md#costs) és a [használati adatok a Fiókközpontból történő letöltését](billing-download-azure-invoice-daily-usage-date.md).

### <a name="you-reached-the-end-of-your-free-12-months"></a>Ha letelt az ingyenes 12 hónap

Az ingyenes szolgáltatások és mennyiségek igénybevételi joga a 12 hónap elteltével lejár. Az Azure Portalon megtekintheti az ingyenes szolgáltatások lejáratának dátumát.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A navigációs terület bal oldalán válassza ki a **Minden szolgáltatás** elemet.

3.  Válassza az **Előfizetések** lehetőséget.

4.  Válassza ki azt az előfizetést, amelyet az ingyenes fiók regisztrációjakor hozott létre.

5.  Görgessen le, és keresse meg az ingyenes szolgáltatások rácsát. Kattintson a rács bal felső részén található eszköztippre.

![Képernyőkép, amely megmutatja, hol tekintheti meg az ingyenes fiók előnyeinek lejárati idejét](./media/billing-avoid-charges-free-account/freeaccount-benefits-expiration-date.png)


Az ingyenes szolgáltatások és a mennyiségek lejárta után az Azure az esetlegesen használt szolgáltatások díját használatalapú díjszabással számítja fel. Az Azure Portal használatával törölheti a nem használt szolgáltatások erőforrásait. Ha nem szándékozik Azure-szolgáltatásokat használni, [megszüntetheti az előfizetését](billing-how-to-cancel-azure-subscription.md).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Ingyenes próbaverziós Azure-előfizetés frissítése](billing-upgrade-azure-subscription.md)
