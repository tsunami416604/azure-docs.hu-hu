---
title: Az Azure számla ismertetése
description: Az Azure-előfizetés használatának és számlájának értelmezése
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 38126e4539719ba56e6e5eac5e860cea9b49d446
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>A Microsoft Azure számlán feltételek megismerése

A számla összefoglalja a költségek, és fizetésre vonatkozó utasításokat tartalmazza. A Portable Document Format (.pdf) a letölthető érhető el a [Azure-portálon](https://portal.azure.com/) vagy e-mailben küldhetők el. További információkért lásd: [beszerzéséről a számla és a napi használati adatok számlázási Azure](billing-download-azure-invoice-daily-usage-date.md).

Néhány dolgot figyelembe venni:

-   Ha ingyenes próba-előfizetést használ, a részletes használati adatokat kaphat az Azure-portálon, de nincs számla.

-   Akár 24 órányi használati az előző számlázott időszak végén az aktuális számla előfordulhat, hogy megjelennek.

-   A nemzetközi ügyfelek számlázási utasítás szerepel költségek olyan jelleggel becslése. Előfordulhat, hogy a bankokkal átváltási különböző költségeit.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Részletes feltételeket és a számla leírása
Az alábbi szakaszok tartalmazzák a fontos fogalmakat tartalmazza, megtekintheti a számla és leírások minden kifejezéshez.

### <a name="account-information"></a>Fiók adatai

A fiók információk szakasza a számla az első lap tetején, és a profil és az előfizetés információkat jeleníti meg.

![Fiók információk szakasza számla](./media/billing-understand-your-invoice/1.png)

| Időtartam | Leírás |
| --- | --- |
| Ügyfél PO nem. |Egy nem kötelező megrendelőlap számát, nyomon követési Ön által hozzárendelt |
| Invoice No. (Számlaszám) |Egy egyedi, létrehozott Microsoft követési célból használt számla száma |
| Billing Cycle (Számlázási ciklus) |Dátumtartomány, amely lefedi a számla |
| Invoice Date (Számla dátuma) |A számla jött létre, általában a számlázási ciklus vége után naponta dátuma |
| Payment Method (Fizetési mód) |A fiókban használt fizetési típus (számla vagy hitelkártya) |
| Bill to (Számlázási cím) |Számlázási címét, amely a fiók szerepel |
| Előfizetés kínálnak ("használatalapú fizetés") |Előfizetés ajánlat vásárolt (használatalapú fizetés, BizSpark plusz, Azure fázisban, stb.) típusú. További információkért lásd: [Azure típusú](https://azure.microsoft.com/support/legal/offer-details/). |
| Account Owner Email (Fióktulajdonos e-mail-címe) | Az e-mail cím, amellyel a Microsoft Azure-fiókot regisztrálták. <br /><br />Az e-mail cím módosításához lásd [profiladatait, például kapcsolattartási e-mail címet vagy telefonszámot Azure-fiókja módosítása](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>A számla összesítésének magyarázata
A **számla összegzés** a számla a szakasz a teljes tranzakció összegek az utolsó számlázási időszakban, és a jelenlegi használati díjak óta.

![Számla összefoglaló szakasz](./media/billing-understand-your-invoice/2.png)

Az előfizetés neve ("éles tároló") az előfizetés a számla esetén.

#### <a name="understand-the-previous-charges"></a>Az előző költségek ismertetése
Az előző egyenleg kifizetések és függőben lévő egyenleg a szakasz a számla tranzakciók foglalja a legutóbbi számlázási időszak óta.

| Időtartam | Leírás |
| --- | --- |
| Previous balance (Előző egyenleg) |A teljes összeg miatt az utolsó számlázási időszakba eső |
| Payments (Befizetések) |Teljes kifizetések és az utolsó számlázási időszakban alkalmazott kreditek |
| Outstanding balance (from previous billing cycle) (Fennálló egyenleg (az előző számlázási ciklusból)) |Bármely kreditek vagy fiókjában óta az utolsó számlázási időszakban fennmaradó egyenleg |

#### <a name="understand-the-current-charges"></a>Az esedékes díjak magyarázata
A jelenlegi díjak szakasz a számla a havi költségeket részleteit jeleníti meg, az aktuális elszámolási időszak.

| Időtartam | Leírás |
| --- | --- |
| Használati díjak |Használati díjak meg az aktuális elszámolási időszak előfizetés teljes havi díjak|
| Discounts (Engedmények) |Az aktuális elszámolási időszak alkalmazott szolgáltatás kedvezményeket|
| Adjustments (Módosító tételek) |Vegyes kreditek (szabad használati, kreditek stb.) vagy az aktuális elszámolási időszak alkalmazott díjhátralékot.<br/><br/>Például ha MSDN-ajánlat a Visual Studio Enterprise, megjelenik egy havi keretet. Ha megszünteti az előfizetést, tekintse meg a havi használati díjakat, amelyek mérete meghaladja a havi keretet, az előfizetés ajánlat kapott. A díjak csak az előfizetés törlési dátumot a aktuális elszámolási időszak kezdetekor fel Önnek. |

#### <a name="sold-to-and-payment-instructions"></a>Eladott és fizetési utasítások

A következő táblázat ismerteti a eladott a és a fizetési utasítások a számla második oldalán látható.

| Időtartam |Leírás |
| --- | --- |
| Sold to (Vevő) |A fiók profil címet. <br/><br/>Ha a cím módosításához szüksége, tekintse meg [profiladatait, például kapcsolattartási e-mail címet vagy telefonszámot Azure-fiókja módosítása](billing-how-to-change-azure-account-profile.md).|
| Payment Instructions (Fizetési útmutató) |Útmutatást után kell fizetnie, attól függően, hogy a fizetési módot (például által jóváírás kártyák vagy számlán). |

#### <a name="usage-charges"></a>A használati díjakat részletező Usage Charges szakasz magyarázata

A használati díjak szakasz a számla mérő szintre vonatkozó információ a költségek jeleníti meg.

![Használati díjak szakasz](./media/billing-understand-your-invoice/3.png)

A következő táblázat ismerteti a használati díjak oszlopfejlécek a számla látható.

| Időtartam |Leírás |
| --- | --- |
| Name (Név) |A legfelső szintű szolgáltatást a használati azonosító |
| Típus |Az Azure szolgáltatás típusa, amelyek hatással lehetnek a sebesség meghatározása |
| Erőforrás |A mérő feldolgozottként mértékegysége azonosítja. |
| Régió |Az egyes szolgáltatások árú datacenter helye alapján adatközpont helye |
| Consumed (Felhasznált mennyiség) |A számlázott időszak során használt mérő mennyisége |
| Tartalmazza |A mérő az aktuális elszámolási időszak díjmentesen részét képező mennyisége |
| Billable (Számlázandó) |A felhasznált és a szereplő mennyiséggel közötti különbséget szemlélteti. Díjon számlázzuk, az ezt a mennyiséget. Használatalapú fizetés ajánlatokhoz, nem tartalmazza az ajánlat keretében az összesített megegyezik a felhasznált mennyiség |
| Rate (Egységár) |Most számítjuk fel számlázható egységenként száma másodpercenként |
| Érték |A Túlhasználati mennyiség oszlop arány oszlop eredménye látható. Ha a felhasznált mennyiség nem haladhatja meg a szereplő mennyiséget, akkor nem kell fizetni ebben az oszlopban. |
| Rész összesen |Minden a díjak előtti adót a számlázott időszak összege |
| Végösszeg |Az adót a számlázott időszak után a díjak összege |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Hogyan tehetem meg arról, hogy helyesen-e a költségeket a számla?
Ha a számlán, hogy milyen további részleteket a járnak, [a Microsoft Azure a számlázási ismertetése.](billing-understand-your-bill.md)

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
