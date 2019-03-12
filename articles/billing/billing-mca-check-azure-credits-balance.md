---
title: Nyomon követheti az Azure kreditegyenlegét Microsoft Ügyfélszerződéséhez |} A Microsoft Docs
description: Ismerkedjen meg az Azure-kredit egyenleg keresése a Microsoft ügyfél-szerződés.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: amberb
ms.openlocfilehash: 1e8c3e6863b9cd8f2f5ced18a57918c32c865e75
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544596"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Nyomon követheti az Azure kreditegyenlegét Microsoft Ügyfélszerződéséhez

Az Azure Portalon ellenőrizheti az Azure-kredit egyenleg a Microsoft ügyfél-szerződés. Kreditek használatával, amelyek a kreditek hatálya termékek kell fizetnie.

Ha olyan termékhez, nem terjed ki a kreditek használhatja, vagy a felhasználás mértéke elérné a kreditegyenlegét számítunk fel. További információkért lásd: [termékhez, az Azure-kreditek nem terjed ki.](#products-that-arent-covered-by-azure-credits)

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-credit-balance-in-the-azure-portal"></a>Az Azure Portalon kreditegyenlegét ellenőrzése

1. Jelentkezzen be az [Azure Portalra]( https://portal.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Képernyőkép a Keresés a portál a Költségkezelés + számlázás](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Nyissa meg a számlázási profilja. A hozzáférést, attól függően szükség lehet egy számlázási fiók kiválasztása. Válassza ki a számlázási fiók **profilok számlázási** és a egy számlázási profilja majd.

4. Válassza ki **az Azure-kreditek**.

5. Az Azure-kreditek lap az alábbi információkat jeleníti meg:

   ![Képernyőkép a kreditegyenlegét és a egy számlázási profil tranzakciók](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Időtartam               | Meghatározás                           |
   |--------------------|--------------------------------------------------------|
   | Becsült egyenleg  | Becsült összeg után a számlázás a mérlegeli az összes kreditjét, és a függőben lévő tranzakciók |
   | Aktuális egyenleg    | A legutóbbi számla kezdődően kreditek mennyiségét. Függőben lévő tranzakciók nem tartalmazza |
   | Tranzakciók       | Az Azure-kredit egyenleg érintett összes számlázási tranzakció |

   A becsült egyenlege 0-ra csökken, amikor díjkötelesek minden a használatot, beleértve a termékek kreditek alá esnek.

6. Válassza ki **kreditek lista** , a számlázási profil kreditek listájának megtekintése. A kreditek listája a következő információkat biztosítja:

   ![Képernyőkép a kreditek sorolja fel a számlázási profil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Időtartam                 | Meghatározás                           |
   |----------------------|--------------------------------------------------------|
   | Forrás               | A kreditek megszerzése forrása |
   | Kezdő dátum           | A dátum, amikor Ön szerezte be, a kredit |
   | Lejárati dátum      | A dátum, amikor a kreditek érvényessége lejár |
   | Egyenleg              | Az egyenleg a legutóbbi számla kezdődően |
   | Az eredeti összeg      | Kredit az eredeti összeg |
   | status               | Kredit aktuális állapotát. Állapot lehet aktív, használt, lejárt vagy hamarosan lejár |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>A Microsoft vevői szerződés kreditek használata

A Microsoft vevői szerződés számlázási fiók számlázási profilok használhatja a számlákat és a fizetési módok kezeléséhez. A havi számla jön létre minden egyes számlázási profilja, és a fizetési módok használatával a számla.

Az Azure-kreditek a fizetési módok tartoznak. Kredit a Microsoft, például a kedvezményes és szolgáltatási szint követel kap. A kreditek számlázási profilja vannak hozzárendelve. A számlázási profilja számla alapján jön létre, amikor kreditek a rendszer automatikusan alkalmazza a teljes számlázott összeg számításához, akkor meg kell fizetni. Fizetnie, a fennmaradó például ellenőrzése egy másik fizetési móddal, vagy átviteli kábelezést.

## <a name="products-that-arent-covered-by-azure-credits"></a>Az Azure-kreditek nem vonatkozik termékek

 A következő termékeket az Azure-kreditek nem terjed ki. Használja ezeket a termékeket a kreditegyenlegét függetlenül díjkötelesek:

- Canonical
- Citrix XenApp Essentials
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

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, kell [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [Számlázási fiók megismerheti a Microsoft ügyfél-szerződés](billing-mca-overview.md)
- [A Microsoft Ügyfélszerződéséhez számla feltételeinek értelmezése](billing-mca-understand-your-invoice.md)