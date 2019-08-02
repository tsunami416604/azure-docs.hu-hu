---
title: Microsoft Customer Agreement-számla megismerése az Azure-ban
description: Ismerje meg, hogyan olvashatja és értelmezheti az Azure-beli Microsoft Customer Agreement-számlát
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383540"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Feltételek a Microsoft Customer Agreement-számlán

Ez a cikk egy Microsoft-ügyfél szerződéshez tartozó Azure számlázási fiókra vonatkozik. [Ellenőrizze, hogy van-e hozzáférése Microsoft-ügyfél szerződéshez](#check-access-to-a-microsoft-customer-agreement).

A számla összefoglalást biztosít a díjakról és a fizetési utasításokról. Letölthető a hordozható dokumentum formátumában (. pdf) a [Azure Portal](https://portal.azure.com/) , vagy e-mailben is elküldhető. További információ: [Microsoft Azure számla megtekintése és letöltése](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Elszámolási időszak

A számlázás havi rendszerességgel történik. Megtudhatja, hogy a hónap mely napján kapja meg a számlákat a számlázási profil *tulajdonságai területen a* Azure Portalban. [](https://portal.azure.com/) A számlázási időszak vége és a számla dátuma között felmerülő díjak a következő havi számlán szerepelnek, mivel azok a következő számlázási időszakban vannak. Az egyes számlák számlázási időszakának kezdési és befejezési dátumai a **Számlázási összefoglaló**számla PDF-fájlja lapon jelennek meg.

## <a name="invoice-terms-and-descriptions"></a>Számlázási feltételek és leírások

A következő szakaszokban a számlán látható fontos kifejezések szerepelnek, és az egyes feltételek leírását is megadhatja.

### <a name="invoice-summary"></a>Számla összegzése

A **számla összegzése** az első oldal tetején található, és a számlázási profiljával és a fizetés módjával kapcsolatos információkat jeleníti meg.

![Számla összegzése szakasz](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Kifejezés | Leírás |
| --- | --- |
| Sold to (Vevő) |A Számlázási fiók tulajdonságai között található jogi személy címe|
| Bill to (Számlázási cím) |A számlát fogadó számlázási profil számlázási címe, amely a számlázási profil tulajdonságai között található.|
| Számlázási profil |A számlát fogadó számlázási profil neve |
| P.O. szám |A nyomon követéshez hozzárendelt, nem kötelező beszerzési rendelés száma |
| Számla száma |Egy egyedi, a Microsoft által létrehozott, nyomon követési célokra használt számlaszám |
| Invoice Date (Számla dátuma) |A számla előállításának dátuma, általában a számlázási ciklus végét követő öt – 12 nap. A számlázási profil tulajdonságainál megtekintheti a számla dátumát.|
| Fizetési feltételek |Hogyan fizet a Microsoft-számlán. A *nettó 30 nap* azt jelenti, hogy a számla dátumától számított 30 napon belül fizetnie kell. |

### <a name="billing-summary"></a>Számlázási összefoglalás

A **Számlázási összefoglalás** a számlázási profilra vonatkozó díjakat mutatja az előző számlázási időszak, az alkalmazott kreditek, az adó és az esedékes teljes összeg alapján.

![Számlázási összefoglalás szakasz](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Kifejezés | Leírás |
| --- | --- |
| Díjak|A számlázási profilhoz tartozó Microsoft-díjak száma az utolsó számlázási időszak óta |
| Stáblisták |A visszáruból kapott kreditek |
| Alkalmazott Azure-kreditek | Azure-kreditek, amelyek automatikusan érvényesek az Azure-díjakra az egyes számlázási időszakokban |
| Részösszeg |Az adó előtti összeg esedékes |
| Adó |A számlázási profil országának/régiójától függően fizetendő adó típusa és mennyisége. Ha nem kell adót fizetnie, akkor nem jelenik meg a számlán szereplő adó. |
| Becsült teljes megtakarítás |A tényleges kedvezményekből mentett becsült teljes összeg. Ha alkalmazható, a tényleges árengedmények listája a részletek számla alapján szakaszban található beszerzési sor elemek alatt szerepel. |

### <a name="invoice-sections"></a>Számlaszakaszok

A számlázási profilban szereplő minden egyes számla szakasznál láthatja a díjakat, az alkalmazott Azure-kreditek mennyiségét, az adót és a teljes összeget.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Részletek a számla alapján szakasz

A részletek megmutatják az egyes számlák szakaszának költségeit a termék szerinti bontásban. Az egyes termékekhez tartozó rendeléseken belül a költségeket a szolgáltatás típusa szerinti bontásban kell megadni. A termékekkel és szolgáltatásokkal kapcsolatos napi díjakat a Azure Portal és az Azure-használat és a CSV-díjak között találja. További információ: a [Microsoft-ügyfél szerződése számláján](billing-mca-understand-your-bill.md)felszámított díjak megismerése.

Az egyes szolgáltatási családokhoz tartozó teljes összeg kiszámítása az *Azure* -kreditek *kreditek/díjakból* való kivonásával és az *adó*hozzáadásával történik:


![Részletek a számla alapján szakasz](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Kifejezés |Leírás |
| --- | --- |
| Egységár | A szolgáltatás (díjszabási pénznemben) érvényes egységára, amely a használat mértékére szolgál. Ez egyedi a termék, a szolgáltatás családja, a mérő és az ajánlat számára. |
| Mennyiség | A számlázási időszak során megvásárolt vagy felhasznált mennyiség |
| Díjak/kreditek | A kreditek/visszatérítések után felszámított díjak nettó összege |
| Azure-kredit | A díjakra/jóváírásokra alkalmazott Azure-kreditek mennyisége|
| Adókulcs | Az adókulcs (ok) az országtól/régiótól függően |
| Adó összege | A vásárlásra alkalmazott adó mennyisége az adókulcs alapján |
| Összes | A vásárlás miatti teljes összeg |

### <a name="how-to-pay"></a>Fizetés

A számla alján találhatók utasítások a számla kifizetéséhez. Az ellenőrzés, a drót vagy a hálózat is megfizethető. Ha online fizet, hitelkártyát vagy Azure-kreditet is használhat, ha van ilyen.

### <a name="publisher-information"></a>Közzétevő adatai

Ha a számlán harmadik féltől származó szolgáltatásokkal rendelkezik, az egyes közzétevők neve és címe megjelenik a számla alján.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft ügyfél-szerződéshez való hozzáférés engedélyezése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A számlázási profil számláján szereplő díjak megismerése](billing-mca-understand-your-bill.md)
- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A szervezet Azure-díjszabásának megtekintése](billing-ea-pricing.md)
- [A számlázási profilhoz tartozó adózási dokumentumok megtekintése](billing-mca-download-tax-document.md)
