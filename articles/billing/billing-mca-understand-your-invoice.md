---
title: Az Azure-ban a Microsoft Ügyfélszerződéséhez számla ismertetése
description: Ismerje meg, hogyan olvassa el, és a számlázással kapcsolatos tudnivalók a Microsoft ügyfél-szerződés az Azure-ban
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fed658d3f672d6116d7c2b0f3e2e9ad989dd67c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490639"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>A Microsoft vevői feltételeit számlázása

Ez a cikk a Microsoft vevői szerződés vonatkozik Azure-számlázási fiókba. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

A számla a költségeket és a fizetési utasításokat összegzését tartalmazza. Elérhető a Portable Document Format (.pdf) a letölthető a [az Azure portal](https://portal.azure.com/) vagy elküldhetők e-mailen keresztül. További információkért lásd: [megtekintése és letöltése a Microsoft Azure számlázása](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Billing period (Számlázási időszak)

A számlázása havi rendszerességgel történik. Megtudhatja, melyik nappal kap számlákat ellenőrzésével *számla dátuma* alatt a profiltulajdonságok számlázási a [az Azure portal](https://portal.azure.com/). A számlázási időszak végén, és a számla dátuma között felmerülő költségek megtalálhatók a következő havi számlán, mivel a következő elszámolási időszak vannak. A számlázási időszak kezdő és záró dátuma minden számlán a szerepel a számlán a fenti PDF **számlázási összefoglalás**.

## <a name="invoice-terms-and-descriptions"></a>Számla használati és -leírások

A következő szakaszok fontos kifejezések listája, tekintse meg a számlán, és minden kifejezéshez meg leírását tartalmazzák.

### <a name="invoice-summary"></a>Számla összegzése

A **számla Invoice Summary** az első lap tetején, és a számlázási profilja és a fizetés módjának vonatkozó információkat jeleníti meg.

![Számla összefoglalás szakasza](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Kifejezés | Leírás |
| --- | --- |
| Sold to (Vevő) |A jogi személy, található a fióktulajdonságok számlázási címe|
| Bill to (Számlázási cím) |Számlázási cím a számlázási profil, a számla számlázási profiltulajdonságok található fogadása|
| Számlázási profil |A számla fogadása a számlázási profil neve |
| P.O. szám |Egy nem kötelező beszerzési rendelésszámukra, nyomon követése az Ön által hozzárendelt |
| A számla száma |Követési célból használt egyedi, a Microsoft által létrehozott számla szám |
| Invoice Date (Számla dátuma) |A dátum, a számla általában 5 – 12 nap vége a számlázási ciklus után jön létre. A számla dátuma a számlázási profiltulajdonságok ellenőrizheti.|
| Fizetési feltételek |Fizetés módjának a Microsoft szolgáltatásaiért. *30 nap* azt jelenti, hogy a számla időpontjától számított 30 napon belül fizet. |

### <a name="billing-summary"></a>A számlázás összegzése

A **számlázási összefoglalás** bemutatja a számlázási profilja alapján kell fizetni az előző számlázási időszak, minden alkalmazott kreditek, adózási és a teljes összeg óta, esedékes.

![A számlázás összefoglalás szakasza](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Kifejezés | Leírás |
| --- | --- |
| Díjak|A Microsoft a díjat a számlázási profil óta az utolsó számlázási időszakban száma összesen |
| Stáblisták |Értéket ad eredményül kapott kreditek |
| Alkalmazza az Azure-kreditek | Azure-kreditet, amely automatikusan érvénybe lépnek az Azure díjat minden elszámolási időszak |
| Részösszeg |Az üzem előtti adó összege miatt |
| Adózási |A típus és szoftverhasználatért, attól függően, az országban vagy régióban, a számlázási profilja adó összegét. Ha nem rendelkezik adót megfizetni, akkor a számla adója nem jelenik meg. |
| Becsült teljes megtakarítás |A becsült teljes összeget, hatékony kedvezmény mentette. Ha van ilyen, hatékony kedvezményes díjszabás alapján vannak listázva alatt a vásárlás részletei elemek számla szakaszban. |

### <a name="invoice-sections"></a>Számla szakaszok

A számlázási profiljában minden számla területen láthatja a költségek, a alkalmazni az Azure-kreditek mennyiségét, adózási és fizetendő összeget.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Számla szakasz részletei

A részletek megjelenítése a minden termék sorrend szerinti bontásban számla szakasz költsége. Belül minden egyes termék sorrend költsége oszlanak meg szolgáltatás típusa. A termékek és szolgáltatások tartozó napi költségek a az Azure portal és az Azure-használat és költségek CSV találja meg. További tudnivalókért tekintse meg [a díjait a számlán megismerheti a Microsoft vevői szerződés](billing-mca-understand-your-bill.md).

A fizetendő összeg esetében minden egyes szolgáltatás család különbsége *az Azure-kreditek* a *kreditek/díjak* , és vegye fel *adó*:


![Számla szakasz részletei](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Kifejezés |Leírás |
| --- | --- |
| Egységár | A szolgáltatás (a pénznem díjszabása), amely hatékony egység árát a használat gyakorisága használt. Ez egy termék, szolgáltatás család, mérőszámok és ajánlat esetében egyedi legyen. |
| Mennyiség | Vásárolt, vagy az elszámolási időszakban felhasznált mennyiség |
| Díjak/kreditek | Nettó összeg díjak kreditek/visszatérítések alkalmazása után |
| Azure-Kredit | A díjak kreditek alkalmazza az Azure-kreditek mennyiségét|
| Adó mértéke | Ország/régió függően OK |
| Adó összege | Adó mértéke alapján meg kell vásárolnia a alkalmazni adó összege |
| Összes | A fizetendő összeg vásárlására |

### <a name="how-to-pay"></a>Fizetési

A számla alján vannak a számla kifizetésére vonatkozó utasításokat. Fizethet a szolgáltatásért ellenőrzésével átviteli, vagy az interneten. Ha online fizetés esetén használhatja a kredit/bankkártyás fizetéssel vagy az Azure-kreditek, ha van ilyen.

### <a name="publisher-information"></a>Közzétevői adatokkal

Ha külső szolgáltatások szerepel a számlán, nevét és címét, mindegyik közzétevő a számla alján megjelenik.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A számlázási profilja számlán a költségek ismertetése](billing-mca-understand-your-bill.md)
- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A szervezet az Azure díjszabásának megtekintése](billing-ea-pricing.md)
- [A számlázási profiljához adó dokumentumok megtekintése](billing-mca-download-tax-document.md)
