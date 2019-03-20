---
title: A Microsoft Ügyfélszerződéséhez számla ismertetése |} A Microsoft Docs
description: Ismerje meg, és a számlázással kapcsolatos MCA olvasása
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: e5dc8ac0716e194dd0949e2e270346ee338a792a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839729"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>A Microsoft Ügyfélszerződéséhez számla feltételeinek értelmezése

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

A számla a költségeket és a fizetési utasításokat összegzését tartalmazza. Elérhető a Portable Document Format (.pdf) a letölthető a [az Azure portal](https://portal.azure.com/) vagy elküldhetők e-mailen keresztül. További információkért lásd: [megtekintése és letöltése a Microsoft Azure számlázása](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Számla használati és -leírások

Az alábbi szakaszok tartalmazzák a fontos fogalmakat tartalmazza, akkor jelenik meg a számlán és a leírások minden kifejezéshez meg.

### <a name="invoice-summary"></a>Számla összegzése

A **számla Invoice Summary** az első lap tetején, és a számlázási profilja és a fizetés módjának vonatkozó információkat jeleníti meg.

![Számla összefoglalás szakasza](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Időtartam | Leírás |
| --- | --- |
| Sold to (Vevő) |A jogi személy, található a fióktulajdonságok számlázási címe|
| Bill to (Számlázási cím) |Számlázási cím a számlázási profil, a számla számlázási profiltulajdonságok található fogadása|
| Számlázási profil |A számla fogadása a számlázási profil neve |
| P.O. szám |Egy nem kötelező beszerzési rendelésszámukra, nyomon követése az Ön által hozzárendelt |
| A számla száma |Követési célból használt egyedi, a Microsoft által létrehozott számla szám |
| Invoice Date (Számla dátuma) |A dátum, a számla általában 5 – 12 nap vége a számlázási ciklus után jön létre. A számla dátuma a számlázási profiltulajdonságok ellenőrizheti.|
| Fizetési feltételek |Fizetés módjának a Microsoft szolgáltatásaiért. *30 nap* azt jelenti, hogy ellenőrzésével kell fizetnie, illetve a átvitel vezetékes számla időpontjától számított 30 napon belül. |

### <a name="billing-summary"></a>A számlázás összegzése

A **számlázási összefoglalás** bemutatja a számlázási profilja alapján kell fizetni az előző számlázási időszak, minden alkalmazott kreditek, adózási és a teljes összeg óta, esedékes.

![A számlázás összefoglalás szakasza](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Időtartam | Leírás |
| --- | --- |
| Díjak|A Microsoft a díjat a számlázási profil óta az utolsó számlázási időszakban száma összesen |
| Stáblisták |Értéket ad eredményül kapott kreditek |
| Alkalmazza az Azure-kreditek | Azure-kreditet, amely automatikusan érvénybe lépnek az Azure díjat minden elszámolási időszak |
| Részösszeg |Az üzem előtti adó összege miatt |
| Adó |A típus és szoftverhasználatért, attól függően, az ország, a számlázási profilja adó összegét. Ha nem rendelkezik adót megfizetni, akkor a számla adója nem jelenik meg. |
| Becsült teljes megtakarítás |A becsült teljes összeget, hatékony kedvezmény mentette. Ha van ilyen, hatékony kedvezményes díjszabás alapján vannak listázva alatt a vásárlás részletei elemek számla szakaszban. |

### <a name="invoice-sections"></a>Számlaszakaszok

A számlázási profiljában minden számla területen láthatja a költségek, a alkalmazni az Azure-kreditek mennyiségét, adózási és fizetendő összeget.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Számla szakasz részletei

A részletek megjelenítése a minden termék sorrend szerinti bontásban számla szakasz költsége. Belül minden egyes termék sorrend költsége oszlanak meg szolgáltatás típusa. A termékek és szolgáltatások tartozó napi költségek a az Azure portal és az Azure-használat és költségek CSV találja meg. További tudnivalókért tekintse meg [a díjait a számlán megismerheti a Microsoft vevői szerződés](billing-mca-understand-your-bill.md).

A fizetendő összeg esetében minden egyes szolgáltatás család különbsége *az Azure-kreditek* a *kreditek/díjak* , és vegye fel *adó*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Számla szakasz részletei](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Időtartam |Leírás |
| --- | --- |
| Egységár | A szolgáltatás (a pénznem díjszabása), amely hatékony egység árát a használat gyakorisága használt. Ez egy termék, szolgáltatás család, mérőszámok és ajánlat esetében egyedi legyen. |
| Mennyiség | Vásárolt, vagy az elszámolási időszakban felhasznált mennyiség |
| Díjak/kreditek | Nettó összeg díjak kreditek/visszatérítések alkalmazása után |
| Azure-Kredit | A díjak kreditek alkalmazza az Azure-kreditek mennyiségét|
| Adó mértéke | Attól függően, ország OK |
| Adó összege | Adó mértéke alapján meg kell vásárolnia a alkalmazni adó összege |
| Összes | A fizetendő összeg vásárlására |

### <a name="how-to-pay"></a>Fizetési

A számla alján vannak a számla kifizetésére vonatkozó utasításokat. Fizethet a szolgáltatásért ellenőrzésével átviteli, vagy az interneten. Ha online fizetés esetén használhatja a kredit/bankkártyás fizetéssel vagy az Azure-kreditek, ha van ilyen.

### <a name="publisher-information"></a>Közzétevői adatokkal

Ha külső szolgáltatások szerepel a számlán, nevét és címét, mindegyik közzétevő a számla alján megjelenik.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A számlázási profilja számlán a költségek ismertetése](billing-mca-understand-your-bill.md)
- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A szervezet az Azure díjszabásának megtekintése](billing-ea-pricing.md)
- [A számlázási profiljához adó dokumentumok megtekintése](billing-mca-download-tax-document.md)
