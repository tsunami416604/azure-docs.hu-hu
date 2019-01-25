---
title: Az Azure-számlák ismertetése |} A Microsoft Docs
description: Az Azure-előfizetés használatának és számlájának értelmezése
services: ''
documentationcenter: ''
author: bandersmsft
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 506910ffa46cb3fbd4be9dcecf9df0f9fdd291e5
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901020"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla feltételeinek értelmezése

A számla összefoglalja a díjakról, és fizetési vonatkozó utasításokat tartalmazza. Elérhető a Portable Document Format (.pdf) a letölthető a [az Azure portal](https://portal.azure.com/) vagy elküldhetők e-mailen keresztül. További információkért lásd: [beszerzése az Azure számlázási és napi használati adatok](billing-download-azure-invoice-daily-usage-date.md).

Vegye figyelembe, hogy néhány dolgot:

-   Ha egy ingyenes próba-előfizetést használ, a részletes információt kaphat az Azure Portalról, de nincs számlát.

-   Akár 24 óra az előző számlázási időszak végén a előfordulhat, hogy az aktuális számlán jelennek meg.

-   A nemzetközi ügyfelek számlázási kimutatások a felsorolt díjak csak költségbecslési célokat is. A bankok különböző költségeket a pénznemváltás is.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Részletes használati és a számla leírása
Az alábbi szakaszok tartalmazzák a fontos fogalmakat, amely akkor jelenik meg a számlán és a leírások minden kifejezéshez meg.

### <a name="account-information"></a>Fiók adatai

A számla a fiókadatok szakasza az első lap tetején, és a profil és az előfizetés adatait jeleníti meg.

![Fiókadatok szakasza számla](./media/billing-understand-your-invoice/1.png)

| Időtartam | Leírás |
| --- | --- |
| Vevői beszerzési rendelés száma |Egy nem kötelező beszerzési rendelésszámukra, nyomon követése az Ön által hozzárendelt |
| Invoice No. (Számlaszám) |Követési célból használt egyedi, Microsoft generált számla szám |
| Billing Cycle (Számlázási ciklus) |Dátumtartomány, amely lefedi a számla |
| Invoice Date (Számla dátuma) |A számlán, általában egy nap vége a számlázási ciklus után létrehozott dátuma |
| Payment Method (Fizetési mód) |A fiókban használt fizetési típus (számla vagy hitelkártya) |
| Bill to (Számlázási cím) |Számlázási cím, amely szerepel a listán a fiók |
| Előfizetési ajánlat ("használatalapú fizetés") |(Használatalapú fizetés, BizSpark Plus, Azure Pass stb.) vásárolt előfizetési csomag típusa. További információkért lásd: [Azure ajánlattípusokról](https://azure.microsoft.com/support/legal/offer-details/). |
| Account Owner Email (Fióktulajdonos e-mail-címe) | Az e-mail cím, amellyel a Microsoft Azure-fiókot regisztrálták. <br /><br />Ha módosítani szeretné az e-mail-cím, lásd: [profil adatait az Azure-fiók például a kapcsolattartási e-mail cím és telefonszám módosítása](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>A számla összesítésének magyarázata
A **számla Invoice Summary** számla szakasz felsorolja a tranzakció teljes mennyiségeket óta az utolsó elszámolási időszakban érvényes, és a jelenlegi használati terheléseit.

![Számla összefoglalás szakasza](./media/billing-understand-your-invoice/2.png)

Az előfizetés neve ("éles tároló") az a név az előfizetés az adott számlához.

#### <a name="understand-the-previous-charges"></a>Az előző költségek ismertetése
Az előző egyenleget, befizetéseket és a fennmaradó egyenleget a számla szakasza összefoglalja az előző elszámolási időszakban érvényes óta történt tranzakciókat.

| Időtartam | Leírás |
| --- | --- |
| Previous balance (Előző egyenleg) |A fizetendő összeg, az utolsó elszámolási időszakban érvényes |
| Payments (Befizetések) |Teljes kifizetések és az utolsó elszámolási időszakban érvényes alkalmazott kreditek |
| Outstanding balance (from previous billing cycle) (Fennálló egyenleg (az előző számlázási ciklusból)) |Bármely kreditjei és a fennmaradó egyenleget, a fiókban, az utolsó elszámolási időszakban érvényes óta |

#### <a name="understand-the-current-charges"></a>Az esedékes díjak magyarázata
A számla Current Charges szakasza az elszámolási időszakban felszámított díjakat kapcsolatos részleteket az aktuális elszámolási időszakban jeleníti meg.

| Időtartam | Leírás |
| --- | --- |
| Használati díjak |A használati díjak a teljes havi díjak egy adott előfizetés az aktuális elszámolási időszakban felszámított|
| Discounts (Engedmények) |Az aktuális elszámolási időszakban érvényes szolgáltatási engedmények|
| Adjustments (Módosító tételek) |Egyéb jóváírások (ingyenes használatát, kreditek stb.) és az aktuális elszámolási időszakban érvényes adjustments.<br/><br/>Például ha a Visual Studio Enterprise with MSDN-ajánlat, látni havi kredittel. Ha az előfizetés megszüntetése esetén a havi használati díjakat, amelyek túllépik a havi kredit, az előfizetési ajánlatok között a jelenik meg. A díjakat számítunk fel az előfizetés megszűnésének-ig a jelenlegi számlázási időszak elején. |

#### <a name="sold-to-and-payment-instructions"></a>Eladott és a fizetési útmutató

A következő táblázat ismerteti az értékesített és a fizetési útmutató a számla a második oldalán látható.

| Időtartam |Leírás |
| --- | --- |
| Sold to (Vevő) |Profil címével, amely a fiókhoz. <br/><br/>Ha a cím van szüksége, tekintse meg [profil adatait az Azure-fiók például a kapcsolattartási e-mail cím és telefonszám módosítása](billing-how-to-change-azure-account-profile.md).|
| Payment Instructions (Fizetési útmutató) |Útmutatást függően fizetési módot kell fizetnie (például által jóváírás kártyák vagy számla ellenében). |

#### <a name="usage-charges"></a>A használati díjakat részletező Usage Charges szakasz magyarázata

A használati díjak szakasz a számla szint mérési adatok a költségek jeleníti meg.

![Használati díjak szakasz](./media/billing-understand-your-invoice/3.png)

A következő táblázat ismerteti a használati díjak oszlopfejlécek jelenik meg a számlán.

| Időtartam |Leírás |
| --- | --- |
| Name (Név) |A felső szintű szolgáltatásnak a használati azonosítja. |
| Típus |Azure-szolgáltatás, amely a díjszámítást is érintheti típusát határozza meg. |
| Erőforrás |A rendszer, elérheti az érték mértékegységét azonosítja. |
| Régió |Az adatközpontok elhelyezkedésétől is adatközpont elhelyezkedése alapján igénybe vett |
| Consumed (Felhasznált mennyiség) |Az elszámolási időszakban használt mérőszám mennyisége |
| Tartalmazza |A mérőszám, amely tartalmazza az aktuális elszámolási időszakban érvényes díjmentesen mennyisége |
| Billable (Számlázandó) |A felhasznált mennyiség és a foglalt mennyiség közötti különbséget mutatja. Ez a mennyiség díjköteles. A használatalapú fizetés, amelyek nem tartalmaz az ajánlat keretében elérhető ez a mennyiség megegyezik a felhasznált mennyiség |
| Rate (Egységár) |A sebesség díját a számlázható egység |
| Érték |A Rate oszlop Kerettúllépés mennyiség oszlop szorzása eredménye látható. A felhasznált mennyiség nem haladja meg a foglalt mennyiséget, ha ott nem költségekkel ebben az oszlopban. |
| Rész összesen |Minden a költségek előtti adózási a számlázási időszak összege |
| Teljes összeg százalékában |Adózás a számlázási időszak után a díjak összege |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Hogyan ellenőrizhetem, hogy, hogy helyesek-e a költségek, a számlán?
Ha a számlán, adja meg további részleteket a díj, lásd: [Microsoft Azure-hoz kapcsolódó számlák magyarázata.](billing-understand-your-bill.md)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
