---
title: Nyomon követheti az Azure kreditegyenlegét egy Microsoft Ügyfélszerződéséhez
description: Ismerje meg, hogyan ellenőrizheti az Azure-kredit egyenleg a Microsoft ügyfél-szerződés.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490967"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>A Microsoft vevői szerződés program Azure kreditegyenlegét nyomon követése

Az Azure Portalon ellenőrizheti az Azure-kredit egyenleg a Microsoft ügyfél-szerződés. Kreditek használatával fizetni a díjat a kreditek alá esnek.

Ha olyan termékhez, nem terjed ki a kreditek használhatja, vagy a felhasználás mértéke elérné a kreditegyenlegét számítunk fel. További információkért lásd: [termékhez, az Azure-kreditek nem terjed ki. () #products-that-aren't-covered-by-azure-credits).

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Ellenőrizze a kreditegyenlegét

1. Jelentkezzen be az [Azure Portalra]( https://portal.azure.com).

2. Keresse meg **Cost Management és számlázás**.

    ![Képernyőkép a Keresés a portál a Költségkezelés + számlázás](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Válassza ki **az Azure-kreditek** bal oldalán. A hozzáférést, attól függően szükség lehet egy számlázási fiók vagy számlázási-profilt válassza, majd **az Azure-kreditek**.

4. Az Azure-kreditek lap az alábbi információkat jeleníti meg:

   ![Képernyőkép a kreditegyenlegét és a egy számlázási profil tranzakciók](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Kifejezés               | Meghatározás                           |
   |--------------------|--------------------------------------------------------|
   | Becsült terheléselosztása  | Becsült összeg után a számlázás a mérlegeli az összes kreditjét, és a függőben lévő tranzakciók |
   | Aktuális egyenleg    | A legutóbbi számla kezdődően kreditek mennyiségét. Függőben lévő tranzakciók nem tartalmazza |
   | Tranzakciók       | Az Azure-kredit egyenleg érintett összes számlázási tranzakció |

   A becsült egyenlege 0-ra csökken, amikor díjkötelesek minden a használatot, beleértve a termékek kreditek alá esnek.

6. Válassza ki **kreditek lista** , a számlázási profil kreditek listájának megtekintése. A kreditek listája a következő információkat biztosítja:

   ![Képernyőkép a kreditek sorolja fel a számlázási profil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Kifejezés | Meghatározás |
   |---|---|
   | Becsült terheléselosztása | Nem számlázott jogosult költségek a jelenlegi kivonva levonása után rendelkezik Azure-kredit összege|
   | Aktuális egyenleg | Azure-kredit előtt figyelembe véve a nem számlázott kredit jogosult díjak összege. Ez összeadásával kapott új Azure-kreditek kreditegyenlegét a legutóbbi számla időpontjában|
   | source | A kreditek megszerzése forrása |
   | Kezdő dátum | A dátum, amikor Ön szerezte be, a kredit |
   | Lejárat dátuma | A dátum, amikor a kreditek érvényessége lejár |
   | Mérleg | Az egyenleg a legutóbbi számla kezdődően |
   | Az eredeti összeg | Kredit az eredeti összeg |
   | Állapot | Kredit aktuális állapotát. Állapot lehet aktív, használt, lejárt vagy hamarosan lejár |

## <a name="how-credits-are-used"></a>Hogyan használhatók a kreditek

A Microsoft vevői szerződés számlázási fiók számlázási profilok használhatja a számlákat és a fizetési módok kezeléséhez. A havi számla jön létre minden egyes számlázási profilja, és a fizetési módok használatával a számla.

Az Azure-kreditek a fizetési módok tartoznak. Kredit a Microsoft, például a kedvezményes és szolgáltatási szint követel kap. A kreditek számlázási profilja vannak hozzárendelve. A számlázási profilja számla alapján jön létre, amikor kreditek a rendszer automatikusan alkalmazza a teljes számlázott összeg számításához, akkor meg kell fizetni. Fizetnie, a fennmaradó például ellenőrzése egy másik fizetési móddal, vagy átviteli kábelezést.

## <a name="products-that-arent-covered-by-azure-credits"></a>Az Azure-kreditek nem vonatkozik termékek

 A következő termékeket az Azure-kreditek nem terjed ki. Használja ezeket a termékeket a kreditegyenlegét függetlenül díjkötelesek:

- Canonical
- Citrix XenApp Essentials rendszerbe
- A Citrix xendesktop-példányok
- Regisztrált felhasználó
- Openlogic
- Távoli hozzáférési jogok a XenApp Essentials regisztrált felhasználó
- Ubuntu Advantage
- A Visual Studio Enterprise (havi)
- Visual Studio Enterprise (Annual)
- A Visual Studio Professional (havonta)
- Visual Studio Professional (Annual)
- Azure Marketplace-termékekkel
- Azure-támogatás ügyfeleknek

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [Számlázási fiók megismerheti a Microsoft ügyfél-szerződés](billing-mca-overview.md)
- [A Microsoft Ügyfélszerződéséhez számla feltételeinek értelmezése](billing-mca-understand-your-invoice.md)
