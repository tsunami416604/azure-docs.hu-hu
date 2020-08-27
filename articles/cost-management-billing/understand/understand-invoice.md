---
title: Az Azure-számlák ismertetése
description: Az Azure-előfizetés használatának és számlájának értelmezése
author: bandersmsft
ms.reviewer: jureid
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b7b115f909c57470bfbdab9cc44e6a258f85b8f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685918"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla kifejezéseinek értelmezése

A számla tartalmazza a díjak összesítését, valamint a fizetésre vonatkozó utasításokat. A számlát letöltheti Portable Document Format (.pdf) formátumban az [Azure Portalról](https://portal.azure.com/), vagy kérheti, hogy e-mailben legyen elküldve. További információ: [Az Azure számlázási és napi használati adatainak beszerzése](../manage/download-azure-invoice-daily-usage-date.md).

Ügyeljen a következőkre:

-   Ha ingyenes próbaverziót használ, a használatra vonatkozó részletes információkat lekérheti az Azure Portalról, számlát azonban nem kap.

-   Az előző számlázási időszak végét követően legfeljebb 24 órányi használat megjelenhet az aktuális számlán.

-   A nemzetközi ügyfeleknek szóló számlázási kimutatásokon szereplő díjak csak becslések. Előfordulhat, hogy a bankok eltérő díjakat számítanak fel az átváltási árfolyamokhoz.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>A számla részletes kifejezései és leírásaik
Az alábbi szakaszok a számlán szereplő fontos kifejezéseket és azok leírásait tartalmazzák.

### <a name="account-information"></a>Fiók adatai

A számla fiókadatokat tartalmazó szakasza az első oldal tetején található, és a profiljával és előfizetésével kapcsolatos adatokat mutatja.

![A számla fiókadatokkal kapcsolatos szakasza](./media/understand-invoice/account-information-section.png)

| Időszak | Leírás |
| --- | --- |
| Ügyfél rendelési száma |Egy választható rendelési szám, amelyet Ön rendel hozzá nyomkövetés céljából |
| Invoice No. (Számlaszám) |A Microsoft által létrehozott egyedi, nyomkövetési célra használt számlaszám. |
| Billing Cycle (Számlázási ciklus) |Az a dátumtartomány, amelyre a számla vonatkozik. |
| Invoice Date (Számla dátuma) |A számla létrehozásának dátuma, amely általában a számlázási ciklus végét követő nap. |
| Payment Method (Fizetési mód) |A fiókban használt fizetési típus (számla vagy hitelkártya) |
| Bill to (Számlázási cím) |A fiókhoz tartozó számlázási cím |
| Előfizetési ajánlat (használatalapú fizetés) |A megvásárolt előfizetési ajánlat típusa (használatalapú fizetés, BizSpark Plus, Azure Pass stb.). További információkért tekintse meg [az Azure-ajánlatok típusait](https://azure.microsoft.com/support/legal/offer-details/). |
| Account Owner Email (Fióktulajdonos e-mail-címe) | A fiókhoz tartozó e-mail-cím, amellyel a Microsoft Azure-fiókot regisztrálta. <br /><br />Az e-mail-cím módosításával kapcsolatban lásd: [az Azure-fiók profiladatainak (pl. kapcsolattartási e-mail-cím, cím, telefonszám) módosítását](../manage/change-azure-account-profile.md) ismertető témakört. |

### <a name="understand-the-invoice-summary"></a>A számla összesítésének magyarázata
A számla **Számlaösszesítő** szakasza a legutóbbi számlázási időszak óta végrehajtott tranzakciók teljes összegét és az aktuális használati díjakat sorolja fel.

![Számlaösszesítő szakasz](./media/understand-invoice/invoice-summary-section.png)

Az Előfizetés neve („Production Storage”) a számlán szereplő előfizetés neve.

#### <a name="understand-the-previous-charges"></a>Az előző díjak értelmezése
A számla előző egyenlegét, kifizetéseit és fennálló tartozásait tartalmazó szakasz a legutóbbi számlázási időszak óta végrehajtott tranzakciókat összegzi.

| Időszak | Leírás |
| --- | --- |
| Previous balance (Előző egyenleg) |A legutóbbi számlázási időszakból fennmaradó tartozás teljes összege. |
| Payments (Befizetések) |A legutóbbi számlázási időszakban történt kifizetések és jóváírások teljes összege. |
| Outstanding balance (from previous billing cycle) (Fennálló egyenleg (az előző számlázási ciklusból)) |A számlán a legutóbbi számlázási időszak óta keletkezett jóváírások vagy a fennmaradó egyenleg. |

#### <a name="understand-the-current-charges"></a>Az esedékes díjak magyarázata
A számla Jelenlegi díjak szakasza az aktuális számlázási időszak havi díjainak részleteit ismerteti.

| Időszak | Leírás |
| --- | --- |
| Használati díjak |A használati díjak az előfizetés teljes havi díjai az aktuális számlázási időszakban.|
| Discounts (Engedmények) |Az aktuális számlázási időszakban érvényes szolgáltatási kedvezmények.|
| Adjustments (Módosító tételek) |Az aktuális számlázási időszakban történt egyéb jóváírások (Ingyenes használat, Jóváírások stb.) vagy a kiegyenlítetlen díjak.<br/><br/>Ha például a Visual Studio Enterprise with MSDN ajánlattal rendelkezik, itt havi jóváírást lát. Ha lemondja az előfizetését, akkor azokat a havi használati díjakat látja, amelyek meghaladják az előfizetési ajánlathoz járó havi jóváírást. A költségek számlázása az aktuális számlázási időszak kezdetétől az előfizetés lemondási dátumáig történik. |

#### <a name="sold-to-and-payment-instructions"></a>Vevői és fizetési útmutatás

Az alábbi táblázat a számla második oldalán található vevői és fizetési útmutatást ismerteti.

| Időszak |Leírás |
| --- | --- |
| Sold to (Vevő) |A számlához tartozó, profiladatoknál megadott cím. <br/><br/>A cím módosításával kapcsolatban lásd: [az Azure-fiók profiladatainak (pl. kapcsolattartási e-mail-cím, cím, telefonszám) módosítását](../manage/change-azure-account-profile.md) ismertető cikket.|
| Payment Instructions (Fizetési útmutató) |Útmutatás a fizetéssel kapcsolatban a fizetési módtól függően (pl. hitelkártyával vagy számlával). |

#### <a name="usage-charges"></a>Használati díjak

A számla Használati díjak szakasza a fogyasztásmérő szintjének költségekkel kapcsolatos információit jeleníti meg.

![Használati díjak szakasz](./media/understand-invoice/usage-charges-section.png)

Az alábbi táblázat a használati díjak számlán szereplő oszlopfejléceit ismerteti.

| Időszak |Leírás |
| --- | --- |
| Name (Név) |Megadja a legfelső szintű szolgáltatást a használat esetében. |
| Típus |Megadja azon Azure-szolgáltatás típusát, amely befolyásolhatja a díjszabást. |
| Erőforrás |Megadja a használt fogyasztásmérő mértékegységét. |
| Régió |Megadja az adatközpont helyét azon szolgáltatások esetén, amelyek díjszabása az adatközpont helyétől függ. |
| Consumed (Felhasznált mennyiség) |A fogyasztásmérő számlázási időszak során felhasznált mennyisége. |
| Tartalmazza |Az aktuális számlázási időszakban a fogyasztásmérőn ingyenesen felhasználható mennyiség. |
| Billable (Számlázandó) |A Felhasznált mennyiség és a Szolgáltatási keret közötti különbséget jeleníti meg. Ön ezért a mennyiségért fizet. Az ilyen mennyiséget nem tartalmazó, használatalapú fizetéses ajánlatok esetében ez ugyanaz, mint a Felhasznált mennyiség. |
| Rate (Egységár) |A számlázható egységenként számlázott díj. |
| Érték |A Keretet meghaladó mennyiség oszlop és a Díj oszlop szorzatának eredményét jeleníti meg. Ha a Felhasznált mennyiség nem haladja meg a Szolgáltatási keretet, akkor ebben az oszlopban nem szerepel semmilyen díj. |
| Részösszeg |A teljes fizetendő díj adók nélkül a jelenlegi számlázási időszakra vonatkozóan. |
| Végösszeg |A teljes fizetendő díj adókkal együtt a jelenlegi számlázási időszakra vonatkozóan. |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Hogyan ellenőrizhetem, hogy a számlán szereplő díjak helyesek-e?
Ha olyan díjakat lát a számlán, amellyel kapcsolatban többet szeretne megtudni, tekintse meg a [Microsoft Azure-számlák értelmezését](review-individual-bill.md) ismertető cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
