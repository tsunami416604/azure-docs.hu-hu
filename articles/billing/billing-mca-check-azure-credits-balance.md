---
title: A Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegének nyomon követése
description: Megtudhatja, hogyan ellenőrizheti a Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegét.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ea3fc21891f1e4d4e744449032a4b2cfcdfbb2f0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177535"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>A Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegének nyomon követése

A Microsoft-ügyfélszerződéshez tartozó Azure-kreditek egyenlegét az Azure Portalon ellenőrizheti. A krediteket az általuk fedezett díjak kifizetésére használhatja.

Díjat számítunk fel, ha olyan terméket használ, amelyet nem fedeznek a kreditek, vagy ha a használata meghaladja a kreditegyenlegét. További információért lásd [az Azure-kreditek által nem fedezett termékeket](#products-that-arent-covered-by-azure-credits).

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel a Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>A kreditegyenleg ellenőrzése

1. Jelentkezzen be az [Azure Portalra]( https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  A bal oldalon válassza az **Azure-kredit** lehetőséget. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási fiók vagy számlázási profil kiválasztása után tudja kiválasztani az **Azure-kredit** lehetőséget.

4. Az Azure-kredit lap az alábbi információkat jeleníti meg:

   ![Képernyőkép a számlázási profil kreditegyenlegéről és tranzakcióiról](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Időtartam               | Meghatározás                           |
   |--------------------|--------------------------------------------------------|
   | Becsült egyenleg  | A meglévő kreditek becsült mennyisége az összes számlázott és függőben lévő tranzakció figyelembevételével |
   | Aktuális egyenleg    | A kreditek mennyisége a legutóbbi számla óta. Ez nem tartalmazza a függő tranzakciókat |
   | Tranzakciók       | Az Azure-kredit egyenlegét befolyásoló számlázási tranzakciók |

   Ha a becsült egyenleg 0-ra csökken, a teljes használatért fizetnie kell, beleértve a kreditek által fedezett termékeket is.

6. A számlázási profilhoz tartozó kreditek listájának megtekintéséhez válassza a **Kreditek listája** lehetőséget. A kreditek listája az alábbi információkat jeleníti meg:

   ![Képernyőkép a számlázási profilhoz tartozó kreditek listájáról](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Időtartam | Meghatározás |
   |---|---|
   | Becsült egyenleg | Azon Azure-kreditek mennyisége, amellyel a nem számlázott, kreditre jogosult díjak az aktuális egyenlegből történő kivonása után rendelkezik|
   | Aktuális egyenleg | Az Azure-kreditek azon mennyisége, amellyel a nem számlázott, kreditre jogosult díjak figyelembevétele előtt rendelkezik. Ez a mennyiséget a rendszer a legutóbbi számla időpontjában a kreditegyenlegére kapott új Azure-kreditek hozzáadásával számítja ki.|
   | Forrás | A kredit beszerzésének forrása |
   | Kezdő dátum | A kredit beszerzésének dátuma |
   | Lejárati dátum | A kredit lejáratának dátuma |
   | Egyenleg | A legutóbbi számla egyenlege |
   | Eredeti mennyiség | A kredit eredeti mennyisége |
   | status | A kredit aktuális állapota. Az Állapot lehet aktív, felhasznált, lejárt vagy lejáró |

## <a name="how-credits-are-used"></a>A kreditek felhasználása

A Microsoft-ügyfélszerződésekhez tartozó számlázási fiókokban a számlázási profilokkal kezelheti a számlákat és a fizetési módokat. A rendszer havonta létrehoz egy számlát minden számlázási profil számára, a fizetési módok használatával pedig kifizetheti a számlát.

Az egyik fizetési mód az Azure-kredit általi fizetés. A Microsofttól krediteket kaphat, például promóciós kreditet és szolgáltatási szintű kreditet. A rendszer ezeket a krediteket egy számlázási profilhoz rendeli. A számla számlázási profil számára történő létrehozásakor a rendszer automatikusan alkalmazza a krediteket a teljes számlázott összegre a fizetendő összeg kiszámításához. A fennmaradó összeget egy másik fizetési mód segítségével, például csekkel vagy átutalással fizetheti ki.

## <a name="products-that-arent-covered-by-azure-credits"></a>Az Azure-kreditek által nem fedezett termékek

 Az alábbi termékeket nem fedezi az Azure-kredit. E termékek használatáért a kreditegyenlegétől függetlenül fizetnie kell:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (havi)
- Visual Studio Enterprise (éves)
- Visual Studio Professional (havi)
- Visual Studio Professional (éves)
- Azure Marketplace-termékek
- Azure-támogatás ügyfeleknek

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- [A Microsoft-ügyfélszerződéshez tartozó számlázási fiók ismertetése](billing-mca-overview.md)
- [A Microsoft-ügyfélszerződéshez tartozó számlán szereplő kifejezések értelmezése](billing-mca-understand-your-invoice.md)
