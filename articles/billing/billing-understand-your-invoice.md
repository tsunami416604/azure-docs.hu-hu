---
title: Az Azure-számlák ismertetése | Microsoft Docs
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
ms.openlocfilehash: 77c1a85136b2117af7396b8eec2d8b92b335d61d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "60369970"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>A Microsoft Azure-számla kifejezéseinek értelmezése

A számla tartalmazza a díjak összesítését, valamint a fizetésre vonatkozó utasításokat. A számlát letöltheti Portable Document Format (.pdf) formátumban az [Azure Portalról](https://portal.azure.com/), vagy kérheti, hogy e-mailben legyen elküldve. További információ: [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md).

Ügyeljen a következőkre:

-   Ha ingyenes próbaverziót használ, a használatra vonatkozó részletes információkat lekérheti az Azure Portalról, számlát azonban nem kap.

-   Az előző számlázási időszak végét követően legfeljebb 24 órányi használat megjelenhet az aktuális számlán.

-   A nemzetközi ügyfeleknek szóló számlázási kimutatásokon szereplő díjak csak becslések. Előfordulhat, hogy a bankok eltérő díjakat számítanak fel az átváltási árfolyamokhoz.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>A számla részletes kifejezései és leírásaik
Az alábbi szakaszok a számlán szereplő fontos kifejezéseket és azok leírásait tartalmazzák.

### <a name="account-information"></a>Fiók adatai

A számla fiókadatokat tartalmazó szakasza az első oldal tetején található, és a profiljával és előfizetésével kapcsolatos adatokat mutatja.

![A számla fiókadatokkal kapcsolatos szakasza](./media/billing-understand-your-invoice/1.png)

| Kifejezés | Leírás |
| --- | --- |
| Ügyfél rendelési száma |Egy választható rendelési szám, amelyet Ön rendel hozzá nyomkövetés céljából. |
| Invoice No. (Számlaszám) |A Microsoft által létrehozott egyedi, nyomkövetési célra használt számlaszám. |
| Billing Cycle (Számlázási ciklus) |Az a dátumtartomány, amelyre a számla vonatkozik. |
| Invoice Date (Számla dátuma) |A számla létrehozásának dátuma, amely általában a számlázási ciklus végét követő nap. |
| Payment Method (Fizetési mód) |A fiókban használt fizetési típus (számla vagy hitelkártya) |
| Bill to (Számlázási cím) |A fiókhoz tartozó számlázási cím |
| Előfizetési ajánlat (használatalapú fizetés) |A megvásárolt előfizetési ajánlat típusa (használatalapú fizetés, BizSpark Plus, Azure Pass stb.). További információkért tekintse meg [az Azure-ajánlatok típusait](https://azure.microsoft.com/support/legal/offer-details/). |
| Account Owner Email (Fióktulajdonos e-mail-címe) | A fiókhoz tartozó e-mail-cím, amellyel a Microsoft Azure-fiókot regisztrálta. <br /><br />Az e-mail-cím módosításával kapcsolatban lásd: [az Azure-fiók profiladatainak (pl. kapcsolattartási e-mail-cím, cím, telefonszám) módosítását](billing-how-to-change-azure-account-profile.md) ismertető témakört. |

### <a name="understand-the-invoice-summary"></a>A számla összesítésének magyarázata
A számla **Számlaösszesítő** szakasza a legutóbbi számlázási időszak óta végrehajtott tranzakciók teljes összegét és az aktuális használati díjakat sorolja fel.

![Számlaösszesítő szakasz](./media/billing-understand-your-invoice/2.png)

Az Előfizetés neve („Production Storage”) a számlán szereplő előfizetés neve.

#### <a name="understand-the-previous-charges"></a>Az előző díjak értelmezése
A számla előző egyenlegét, kifizetéseit és fennálló tartozásait tartalmazó szakasz a legutóbbi számlázási időszak óta végrehajtott tranzakciókat összegzi.

| Időtartam | Leírás |
| --- | --- |
| Previous balance (Előző egyenleg) |A legutóbbi számlázási időszakból fennmaradó tartozás teljes összege. |
| Payments (Befizetések) |A legutóbbi számlázási időszakban történt kifizetések és jóváírások teljes összege. |
| Outstanding balance (from previous billing cycle) (Fennálló egyenleg (az előző számlázási ciklusból)) |A számlán a legutóbbi számlázási időszak óta keletkezett jóváírások vagy a fennmaradó egyenleg. |

#### <a name="understand-the-current-charges"></a>Az esedékes díjak magyarázata
A számla Jelenlegi díjak szakasza az aktuális számlázási időszak havi díjainak részleteit ismerteti.

| Időtartam | Leírás |
| --- | --- |
| Használati díjak |A használati díjak az előfizetés teljes havi díjai az aktuális számlázási időszakban.|
| Discounts (Engedmények) |Az aktuális számlázási időszakban érvényes szolgáltatási kedvezmények.|
| Adjustments (Módosító tételek) |Az aktuális számlázási időszakban történt egyéb jóváírások (Ingyenes használat, Jóváírások stb.) vagy a kiegyenlítetlen díjak.<br/><br/>Ha például a Visual Studio Enterprise with MSDN ajánlattal rendelkezik, itt havi jóváírást lát. Ha lemondja az előfizetését, akkor azokat a havi használati díjakat látja, amelyek meghaladják az előfizetési ajánlathoz járó havi jóváírást. A költségek számlázása az aktuális számlázási időszak kezdetétől az előfizetés lemondási dátumáig történik. |

#### <a name="sold-to-and-payment-instructions"></a>Vevői és fizetési útmutatás

Az alábbi táblázat a számla második oldalán található vevői és fizetési útmutatást ismerteti.

| Időtartam |Leírás |
| --- | --- |
| Sold to (Vevő) |A számlához tartozó, profiladatoknál megadott cím. <br/><br/>A cím módosításával kapcsolatban lásd: [az Azure-fiók profiladatainak (pl. kapcsolattartási e-mail-cím, cím, telefonszám) módosítását](billing-how-to-change-azure-account-profile.md) ismertető cikket.|
| Payment Instructions (Fizetési útmutató) |Útmutatás a fizetéssel kapcsolatban a fizetési módtól függően (pl. hitelkártyával vagy számlával). |

#### <a name="usage-charges"></a>Használati díjak

A számla Használati díjak szakasza a fogyasztásmérő szintjének költségekkel kapcsolatos információit jeleníti meg.

![Használati díjak szakasz](./media/billing-understand-your-invoice/3.png)

Az alábbi táblázat a használati díjak számlán szereplő oszlopfejléceit ismerteti.

| Időtartam |Leírás |
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
Ha olyan díjakat lát a számlán, amellyel kapcsolatban többet szeretne megtudni, tekintse meg a [Microsoft Azure-számlák értelmezését](billing-understand-your-bill.md) ismertető cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
