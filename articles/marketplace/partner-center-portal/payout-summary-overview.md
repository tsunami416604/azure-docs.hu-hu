---
title: A nyeremények összegzésének áttekintése – Azure Marketplace
description: A nyeremények összegzése az ajánlatával kapcsolatban szerzett pénz részleteit jeleníti meg. Azt is megtudhatja, hogy mikor kapja meg a befizetéseket és mennyit fizet.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: a872331238946de0d57e6d42164f1ce7fb1c7357
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83746204"
---
# <a name="payout-summary-overview"></a>Fizetési összegzés áttekintése

A [kifizetési összefoglaló](./payout-summary.md) a Microsoft által szerzett pénz részleteit jeleníti meg. Azt is megtudhatja, hogy mikor kapja meg a befizetéseket és mennyit fizet.

Ha az Azure Marketplace-en értékesít ajánlatokat, akkor a sikeres kifizetésekről is tájékozódhat a kifizetési összefoglalóban. Az Azure Marketplace-fizetéssel kapcsolatos további információkért lásd: [Azure Marketplace részvételi szabályzatok](https://docs.microsoft.com/legal/marketplace/participation-policy) és [Microsoft Azure Marketplace kiadói szerződés](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Ahhoz, hogy jogosult legyen a kifizetésre, a bevételnek el kell érnie a $50-es [fizetési küszöbértéket](./payment-thresholds-methods-timeframes.md) . A fizetési küszöbértékre vonatkozó részletekért tekintse meg a [Microsoft Azure Marketplace kiadói szerződést](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

A Transact Publishing beállítás összes jelentéskészítési és elemzési funkciója elérhető a partner Center Analytics szakaszában, amely a portál jobb felső sarkában található ikon használatával érhető el:

![A partner Center portál jobb felső sarkában található kifizetési ikon illusztrálása.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Szerepkörök és engedélyek

Ezek a kifizetési jelentés eléréséhez szükséges szerepkörök és engedélyek:

| Jelentések/lapok | Fióktulajdonos | Manager | Fejlesztő | Üzleti közreműködő | Pénzügyi közreműködő | Marketingszakértő |
| --- | --- | --- | --- | --- | --- | --- |
| Beszerzési jelentés (beleértve a közel valós idejű adatfeldolgozást) | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés | Megtekinthető | Nincs hozzáférés |
| Visszajelzési jelentés/válaszok | Megtekintheti és elküldheti a visszajelzést | Megtekintheti és elküldheti a visszajelzést | Megtekintheti és elküldheti a visszajelzést | Nincs hozzáférés | Nincs hozzáférés | Megtekintheti és elküldheti a visszajelzést |
| --- | --- | --- | --- | --- | --- | --- |
| Állapotjelentés (a közel valós idejű adatfeldolgozást is beleértve) | Megtekinthető | Megtekinthető | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés |
| Használat jelentés | Megtekinthető | Megtekinthető | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés |
| Kifizetési fiók | Frissíthet | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Frissíthet | Nincs hozzáférés |
| Adózási profil | Frissíthet | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Frissíthet | Nincs hozzáférés |
| Kifizetés összegzése | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Megtekinthető | Nincs hozzáférés  |
| | | | | | | |

## <a name="payout-report-differences"></a>A kifizetési jelentések eltérései

A Cloud Partner Portal (régi) és a partner Center (új) közötti kifizetési jelentés különbségei:

| Felhőpartnerportál | Partneri központ |
| --- | --- |
| **Hivatkozás**:https://cloudpartner.azure.com/ | **Hivatkozás**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory éshttps://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigáció**: a bejelentések kifizetésében megadott kifizetési jelentések | **Navigáció**: a partner Centerben megadott kifizetési jelentés – kifizetés ikon |
| **Hatókör**:<ul><li>A tranzakció/sorban lévő elemek láthatók a folyamatban lévő, begyűjtött és fizetős gyűjteményekhez.</li><li>Jelentéskészítés – az összes sor elemet megjeleníti a beszerzési sorrend létrehozása után, beleértve a folyamatban lévő gyűjteményt és a számlázást, valamint a gyűjtemény állapotát és a sorokra vonatkozó olyan elemeket, amelyek még nem jogosultak a kifizetésre.</li></ul> | **Hatókör**:<ul><li>Megjeleníti a sorban lévő elemeket, miután a rendszer jogosult bevételnek tekinti őket.</li><li>Az ügyfelek először fizetnek a Microsoftnak, majd az ISV-ket láthatják a kifizetési jelentés megkezdéséről.</li><li>A kifizetési jelentés nem jeleníti meg a folyamatban lévő gyűjteményt, és a számlázás folyamatban van.</li></ul> |
| A **tranzakció nem áll készen a kifizetésre**: a számlázás folyamatban van | A **tranzakció nem áll készen a kifizetésre**: következő becsült fizetés: a kifizetés állapota a feldolgozatlan állapotban van. |
| **Kifizetés állapota**: n/a | **Kifizetés állapota**:<ul><li>Feldolgozatlan: a kereset fizetésre jogosult.</li><li>Közelgő: a befizetést a következő havi kifizetés keretében küldi el a rendszer a közzétevőnek.</li><li>Elküldve: a rendszer elküldje a fizetést a banknak.</li></ul> |
| | |

## <a name="payment-schedules"></a>Fizetési ütemtervek

A fizetési ütemtervek megvitatására, beleértve a tartási időszakokat, a partnerek láthatóságát, valamint azt, hogy az ügyfél milyen bankkártyát vagy számlát használ, tekintse meg a kifizetés **részletei** témakör [fizetési ütemtervek](./payout-policy-details.md#payment-schedules) című szakaszát.

## <a name="transaction-history-download-export"></a>Tranzakciós előzmények letöltésének exportálása

Ez a beállítás a tranzakció előzményei lapon megjelenő egyes letételi sorok letöltését teszi lehetővé. Ez magában foglalja a beszerzési típust, a dátumot, a kapcsolódó tranzakciók mennyiségét, az ügyfél, a termék és az ösztönző programhoz kapcsolódó egyéb tranzakciós adatokat.

| Oszlop neve | Description |
| --- | --- |
| earningId | Az egyes keresések egyedi azonosítója |
| participantId | A program keretében keresett partner elsődleges identitása |
| participantIdType | Program azonosítója az ösztönző programokhoz és az értékesítőhöz, ha a program az áruházi programokhoz és az Azure Marketplace-hez |
| participantName | A jövedelemszerzési partner neve |
| partnerCountryCode | A jövedelemszerzési partner helye/országa/régiója |
| Programnév | Ösztönző/tároló program neve |
| Tranzakcióazonosító | A tranzakció egyedi azonosítója |
| transactionCurrency | A pénznem, amelyben az eredeti ügyfél-tranzakció történt (nem a partner helyének pénzneme) |
| transactionDate | A tranzakció dátuma. Olyan programok esetében hasznos, amelyekben sok tranzakció járul hozzá egy adott keresethez |
| transactionExchangeRate | A megfelelő tranzakció USD-értékének megjelenítésére használt árfolyam |
| transactionAmount | A tranzakció összege az eredeti tranzakció pénznemében, amely alapján létrejött a kereset |
| transactionAmountUSD | Tranzakciós összeg USA-dollárban (USD) |
| szintje | Üzleti szabály a keresőképességhez |
| earningRate | Tranzakciós mennyiségre alkalmazott ösztönző arány a kereset létrehozásához |
| quantity | Tranzakciós programok számlázott mennyisége. A programtól függően változik |
| quantityType | A mennyiség típusa, például: számlázott mennyiség, havi aktív felhasználó (MAU) |
| earningType | Azt jelzi, hogy díj, árengedmény, együttműködés, értékesítés stb. |
| earningAmount | Jövedelemszerzési összeg az eredeti tranzakció pénznemében |
| earningAmountUSD | Jövedelemszerzési összeg USD-ben |
| earningDate | A kereset dátuma |
| calculationDate | A keresett adat kiszámításának dátuma a rendszeren |
| earningExchangeRate | A megfelelő USD összeg megjelenítéséhez használt árfolyam |
| exchangeRateDate | Az EarningAmount USD kiszámításához használt Exchange-árfolyam dátuma |
| paymentAmountWOTax | Jövedelemszerzési összeg (ÁFA nélkül) a &quot; csak elküldéses &quot; fizetések pénznemében |
| paymentCurrency | Fizessen a partner által a fizetési profilban kiválasztott pénznemre. Csak az eljuttatott kifizetések esetében látható |
| paymentExchangeRate | A paymentAmountWOTax a fizetési pénznemben való kiszámításához használt Exchange-árfolyam a ExchangeRateDate használatával |
| paymentId | A fizetés egyedi azonosítója. Ez a szám látható a banki utasításban |
| paymentStatus | Fizetési állapot |
| paymentStatusDescription | A fizetési állapot leírása |
| customerId | Mindig üres lesz |
| Customername ( | Mindig üres lesz |
| partNumber | Mindig üres lesz |
| productName | A tranzakcióhoz társított terméknév |
| productId | Egyedi termékazonosító |
| parentProductId | Egyedi szülő termék azonosítója. Ha nincs fölérendelt termék a tranzakcióhoz, akkor a szülő termék azonosítója = termékazonosító. |
| parentProductName | A szülő termék neve. Ha nincs fölérendelt termék a tranzakcióhoz, akkor a szülő terméknév = terméknév nevet adja meg. |
| productType | A termék típusa (például alkalmazás, bővítmény és játék) |
| invoiceNumber | Számla száma (csak nagyvállalati szerződések esetén) |
| resellerId | Viszonteladó azonosítója |
| Viszonteladó neve | Viszonteladó neve |
| transactionType | A tranzakció típusa (például vásárlás, visszatérítés, sztornírozás és jóváírás) |
| localProviderSeller | Helyi szolgáltató/a rekord eladója |
| taxRemitted | Átutalt adó (értékesítési, használati, ÁFA/GST-adók) mennyisége. |
| taxRemitModel | Az adók (értékesítési, használati, ÁFA/GST-adók) átutalásával megbízott fél. |
| storeFee | A Microsoft által a kereskedelmi piactéren elérhetővé tett alkalmazások vagy bővítmények díját megőrzött összeg. |
| transactionPaymentMethod | A tranzakcióhoz használt ügyfél-fizetési eszköz (például kártya, mobil Carrier számlázás és PayPal) |
| tpan | Harmadik féltől származó ad-hálózat |
| customerCountry | Ügyfél országa/régiója |
| customerCity | Customer City |
| customerState | Ügyfél állapota |
| customerZip | Ügyfél irányítószáma/irányítószám |
| TenantID | A bérlő azonosítója |
| externalReferenceId | A program egyedi azonosítója |
| externalReferenceIdLabel | Egyedi azonosító felirata |
| transactionCountryCode | Az ország/régió kódja, amelyben a tranzakció történt |
| taxCountry | Ügyfél országa/régiója |
| taxState | Ügyfél állapota |
| taxCity | Ügyfél városa |
| taxZipCode | Az ügyfél irányítószáma |
| LicensingProgramName | A licencelési program neve |
| Programkód | A program nevével leképezhető karakterlánc |
| earningAmountInLastPaymentCurrency | Az utolsó fizetési pénznemben befizetett összeg (a mező üresen marad, ha nem fizettek ki előzetes fizetést) |
| lastPaymentCurrency | Utolsó fizetési pénznem (a mező üresen marad, ha nem fizettek ki előzetes fizetést) |
| AssetId | A Piactéri szolgáltatáshoz tartozó ügyfelek rendeléseinek egyedi azonosítója. Ez a beszerzési sor elemeit jelöli. Több eszköz is lehet. |
| OrderId | Az ügyfél számlára vonatkozik |
| LineItemId | Egyéni sor az ügyfél számláján |
| Ügyfél országa/régiója | Az ügyfél által megadott ország/régió neve. Ez eltérő lehet az ügyfél Azure-előfizetésében lévő ország/régió esetében. |
| Ügyfél EmailAddress | Az ügyfél által megadott e-mail-cím. Ez eltérő lehet az ügyfél Azure-előfizetésében szereplő e-mail-címről. |
| SkuId | A közzététel során definiált SKU-azonosító. Egy ajánlat több SKU-val is rendelkezhet, de egy SKU csak egyetlen ajánlattal társítható. |

> [!NOTE]
> A Transact Publishing beállítás összes jelentéskészítési és elemzési funkciója a partner Center Analytics szakaszában található.

## <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás
Számlázási támogatásért forduljon a kereskedelmi piactér [közzétevő támogatásához](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="next-step"></a>Következő lépés

- [Fizetési összegzések](./payout-summary.md)
