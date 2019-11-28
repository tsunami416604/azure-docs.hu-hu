---
title: A Microsoft-partnerszerződés Azure-beli számláinak ismertetése
description: Ismerje meg, hogyan olvashatók le és értelmezhetők a Microsoft-partnerszerződésre vonatkozó Azure-beli számlák
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 28eb65438507dc77233236e59d356df5a2806144
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226074"
---
# <a name="terms-in-your-microsoft-partner-agreement-invoice"></a>A Microsoft-partnerszerződés számláin szereplő kifejezések

Ez a cikk a Microsoft-partnerszerződéshez tartozó Azure-beli számlázási fiókra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-partnerszerződéshez](#check-access-to-a-microsoft-partner-agreement).

A számla tartalmazza a díjak összesítését, valamint a fizetésre vonatkozó utasításokat. A számlát letöltheti Portable Document Format (.pdf) formátumban az [Azure Portalról](https://portal.azure.com/), vagy kérheti, hogy e-mailben legyen elküldve. További információ: [A Microsoft Azure-számla megtekintése és letöltése](billing-download-azure-invoice.md).

A következő szakaszokban a számlán látható fontos kifejezések és azok leírásai szerepelnek.

## <a name="billing-period"></a>Számlázási időszak

A számlázás havi rendszerességgel történik. A számlázási időszak vége és a számla dátuma közötti időben felmerülő díjak a következő havi számlán szerepelnek, mivel azok a következő számlázási időszakra esnek. Az egyes számlák számlázási időszakának kezdési és záró dátumait a számlázási PDF-fájl tartalmazza az **Összesítő elszámolás** felett.

## <a name="invoice-summary"></a>Számlaösszesítő

A **Számlaösszesítő** az első oldal tetején található, és a számlázási profiljával és a fizetés módjával kapcsolatos információkat jeleníti meg.

<!-- add screenshot -->

| Időtartam | Leírás |
| --- | --- |
| Sold to (Vevő) |A jogi személy címe, amely a számlázási fiók tulajdonságaiban található|
| Bill to (Számlázási cím) |A számlát fogadó számlázási profil számlázási címe, amely a számlázási profil tulajdonságaiban található|
| Számlázási profil |A számlát fogadó számlázási profil neve |
| Rendelési szám |Egy választható rendelési szám, amelyet Ön rendel hozzá nyomkövetés céljából |
| Számlaszám |Egy egyedi, a Microsoft által létrehozott, nyomkövetési célokra használt számlaszám |
| Invoice Date (Számla dátuma) |A számla kiállításának dátuma, amely általában 5–12 nappal követi a számlázási ciklus végét. A számla dátumát a számlázási profil tulajdonságaiban ellenőrizheti.|
| Fizetési feltételek |A Microsoft-számla fizetési módja. A *60 nap* azt jelenti, hogy a számláját a számla dátumától számított 60 napon belül egyenlíti ki. |

## <a name="billing-summary"></a>Összesítő elszámolás

Az **Összesítő elszámolás** az előző számlázási időszak óta a számlázási profilra vonatkozó díjakat, az alkalmazott krediteket, az adót és az esedékes teljes összeget mutatja.

<!-- add screenshot -->

| Időtartam | Leírás |
| --- | --- |
| Díjak|Az utolsó számlázási időszak óta felmerült Microsoft-díjak száma összesen. |
| Kreditek |Visszatérítésekből kapott kreditek |
| Alkalmazott Azure-kreditek | Az Azure-díjakra az egyes számlázási időszakokban automatikusan alkalmazott Azure-kreditek |
| Részösszeg |Az adók nélküli esedékes összeg |
| Adó |A számlázási profil országától vagy régiójától függően fizetendő adó típusa és összege. Ha nem kell adót fizetnie, nem szerepel a számlán. |
| Becsült összes megtakarítás |Az érvényes kedvezményekből megtakarított becsült teljes összeg. Ha alkalmazhatók, az érvényes kedvezményes díjszabások a számlaszakasz-információk beszerzési sorainak elemei alatt találhatók. |


## <a name="billing-details-by-product"></a>Termékenként részletezett számlázási adatok

A **Termékenként részletezett számlázási adatok** szakasz felsorolja az ehhez a számlázási profilhoz tartozó termékek teljes díját. Megtekintheti az egyes termékek költségeinek napi bontását az Azure-beli használati adatokat és díjakat tartalmazó CSV-fájlban. A PDF-formátumú számla az Azure-beli használati adatokkal és díjakkal való egyeztetéséről a [ Microsoft-partnerszerződés számlái ismertető](billing-mpa-understand-your-bill.md) szakaszban tájékozódhat.

## <a name="how-to-pay"></a>Fizetési mód

A számla alján található, a számla kifizetésére szolgáló útmutató. Fizethet átutalással vagy csekkel a számla dátumát követő 60 napon belül.

## <a name="publisher-information"></a>Kibocsátó adatai

Ha a számlán harmadik féltől származó szolgáltatások szerepelnek, az egyes kibocsátók neve és címe a számla alján jelenik meg.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft-partnerszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A számlázási profil számláján szereplő díjak ismertetése](billing-mca-understand-your-bill.md)
- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A szervezet Azure-díjszabásásnak megtekintése](billing-ea-pricing.md)
- [A számlázási profil adózási dokumentumainak megtekintése](billing-mca-download-tax-document.md)
