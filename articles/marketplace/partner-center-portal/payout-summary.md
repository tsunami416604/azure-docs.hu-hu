---
title: Kereskedelmi piactér – kifizetési összefoglalás | Azure piactér
description: A nyeremények összegzése az ajánlatával kapcsolatban szerzett pénz részleteit jeleníti meg. Azt is megtudhatja, hogy mikor kapja meg a befizetéseket és mennyit fizet.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 6ee6b6f325ba58ecaa3c3acb5d5ded173262bafb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715430"
---
# <a name="payout-reporting"></a>Kifizetési jelentések

A [**kifizetési összefoglaló**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) a Microsoft által szerzett pénz részleteit jeleníti meg. Azt is megtudhatja, hogy mikor kapja meg a befizetéseket és mennyit fizet.

Ha az Azure Marketplace-en értékesít ajánlatokat, akkor a sikeres kifizetésekről is tájékozódhat a **kifizetési összefoglalóban**. Az Azure Marketplace fizetési szolgáltatásával kapcsolatos további információkért tekintse meg a [Microsoft Azure Marketplace részvételi szabályzatokat](https://go.microsoft.com/fwlink/p/?LinkId=722436) és a [Microsoft Azure Marketplace közzétevői szerződést](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Ahhoz, hogy jogosult legyen a kifizetésre, a bevételnek el kell érnie a $50-es [fizetési küszöbértéket](payment-thresholds-methods-timeframes.md) . A fizetési küszöbértékre vonatkozó részletekért tekintse meg ezt a lapot, és tekintse át a [Microsoft Azure Marketplace kiadói szerződést](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [A kifizetési jelentés eléréséhez szükséges szerepkörök és engedélyek](#roles-and-permission-to-access-the-payout-report)
- [Kifizetési jelentés: a Cloud Partner Portal és a partneri központ közötti különbség](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Ügyfelek típusai](#customer-types)
- [A kifizetések és a használat közötti Corel](#corelation-between-payout-and-usage)
- [Tranzakciós előzmények letöltése](#transaction-history-download-export)
- [Számlázási kérdések és támogatás](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>A kifizetési jelentés eléréséhez szükséges szerepkörök és engedélyek

| Jelentések/lapok    | Fióktulajdonos    | Manager  | Fejlesztő | Üzleti közreműködő |  Pénzügyi közreműködő | Marketingszakértő |
|------------------|------------------|----------|-----------|----|----|-----|
| Beszerzési jelentés (beleértve a közel valós idejű adatfeldolgozást) | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés | Megtekinthető | Nincs hozzáférés |
| Visszajelzési jelentés/válaszok | Megtekintheti és elküldheti a visszajelzést | Megtekintheti és elküldheti a visszajelzést | Megtekintheti és elküldheti a visszajelzést | Nincs hozzáférés | Nincs hozzáférés | Megtekintheti és elküldheti a visszajelzést |
| Állapotjelentés (a közel valós idejű adatfeldolgozást is beleértve) | Megtekinthető | Megtekinthető | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés |
| Használati jelentés | Megtekinthető | Megtekinthető | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés |
| Kifizetési fiók | Frissíthet | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Frissíthet | Nincs hozzáférés |
| Adózási profil | Frissíthet | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Frissíthet | Nincs hozzáférés |
| Kifizetés összegzése | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Megtekinthető | Nincs hozzáférés |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Kifizetési jelentés: a Cloud Partner Portal és a partneri központ közötti különbség

| | Felhőpartnerportál | Partneri központ |
|---------|---------|---------|
| Hivatkozások | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory) és [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navigáció | A bejelentések kifizetésében megadott kifizetési jelentések | A partner Centerben megadott kifizetési jelentés – kifizetési ikon |
| Hatókör | <ul> <li>A tranzakció/sorban lévő elemek láthatók a folyamatban lévő, begyűjtött és fizetős gyűjteményekhez. </li> <li>Jelentéskészítés – az összes sor elemet megjeleníti a beszerzési sorrend létrehozása után, beleértve a folyamatban lévő gyűjteményt és a számlázást, valamint a gyűjtemény állapotát és a sorokra vonatkozó olyan elemeket, amelyek még nem jogosultak a kifizetésre. </li> </ul> | <ul> <li>Megjeleníti a sorok elemeit, amint azok jogosult bevételnek minősülnek.</li> <li>Az ügyfelek először fizetnek a Microsoftnak, majd az ISV-ket láthatják a kifizetési jelentés megkezdéséről.</li> <li>A kifizetési jelentés nem jeleníti meg a folyamatban lévő gyűjteményt és a számlázást.  </li> </ul>  |
| A tranzakció nem áll készen a kifizetésre | Számlázás folyamatban | Következő becsült fizetés: a kifizetés állapota feldolgozatlan állapotban van.  |
| Kifizetés állapota |  | Feldolgozatlan <br> A kereset jogosult a fizetésre. Ebben az állapotban marad az ösztönző programhoz tartozó program útmutatójában meghatározott hűtési időszakra vonatkozóan. <br> <br> Közelgő <br> Fizetési rendelés – függőben lévő belső felülvizsgálatok a kifizetés feldolgozása előtt. <br> <br> Küldött <br> A rendszer elküldje a fizetést a banknak. |

## <a name="customer-types"></a>Ügyfelek típusai

### <a name="enterprise-agreement"></a>Nagyvállalati szerződés

A Nagyvállalati Szerződés nélküli ügyfelek számlázása havonta történik a piactér szoftverlicenc esetében. A Nagyvállalati Szerződés rendelkező ügyfelekért havonta kell fizetnie egy negyedévente bemutatott számlán keresztül.

### <a name="credit-cards-and-monthly-invoice"></a>Bankkártyák és havi számla

Az ügyfelek bankkártyával és havi számlával is fizethetnek. Ebben az esetben a szoftverlicenc-díjakat havonta számlázjuk.

### <a name="csp-and-direct-pay-users"></a>CSP és közvetlen fizetésű felhasználók

Például, ha az ügyfél bankkártyával vásárol.

## <a name="corelation-between-payout-and-usage"></a>A kifizetések és a használat közötti Corel

|Leírás    |    Dátum  | Megrendelések/használat  | Kifizetési |
|----------|----------|-----------|-------------|
|Rendelési időszak   | Augusztus 15., 2019-augusztus 30., 2019 | **Korrelációs attribútumok rendelései** <br> <ul> <li>Rendeléskód</li> <li>Vevőkód</li> </ul> <br> **Használat** <br> <ul> <li>Vevőkód </li> <li>Ügyfél neve</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Becsült kiterjesztett díj <br> Becsült kifizetés (számítógép) </li> </ul> |  |
|Befejezési időszak (hónap)   | Augusztus 30, 2019 | | |
|Számlázási dátum | Szeptember 1., 2019 | | |
|Ügyfél-fizetési dátum | Szeptember 1., 2019 | | |
|Letéti időszak (csak bankkártyák, 30 nap) | Szeptember 1., 2019 – szeptember 30, 2019 | | **Korrelációs attribútumok rendelései:** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> Ügyfél neve</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>Vevőkód</li> <li>Ügyfél neve</li> <li>Rendeléskód</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Kifizetés állapota:** Feldolgozatlan |
|Gyűjtési időszak kezdete | Szeptember 1., 2019 | | |
|Gyűjtési időszak vége (legfeljebb, 30 nap) | Szeptember 30., 2019 | | |
|Kifizetési számítás dátuma (havonta, 15-én) | Október 1, 2019 | | **Korrelációs attribútumok** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li>Ügyfél neve</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>Vevőkód</li> <li>Ügyfél neve</li> <li>Rendeléskód</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Kifizetés állapota:** Közelgő |
|Kifizetés dátuma | Október 15., 2019 | | **Korrelációs attribútumok** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> Ügyfél neve</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>Vevőkód</li> <li>Ügyfél neve</li> <li>Rendeléskód</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Kifizetés állapota:** Fizetés elküldése |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Nagyvállalati szerződés (negyedévente/havonta)

| Leírás |    Dátum  | Használat | Kifizetési |
|----------|----------|---------|-----------|
|Rendelési időszak | Augusztus 15., 2019-augusztus 30., 2019 | **Korrelációs attribútumok rendelései** <br> <ul> <li>Rendeléskód</li> <li>Vevőkód</li> </ul> <br> **Használati jelentés** <br> <ul> <li>Vevőkód </li> <li>Ügyfél neve</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Becsült kiterjesztett díj <br> Becsült kifizetés (számítógép) </li> </ul> | |
|Befejezési időszak (negyedév) | Szeptember 30., 2019 | | |
|Számlázási dátum | Október 15., 2019 | | |
|Letéti időszak (csak bankkártyák, 30 nap) | n/a | | |
|Gyűjtési időszak kezdete | Október 15., 2019 | | |
|Csak bankkártyák, 30 nap | November 1, 2019 – november 30, 2019 | | |
|Gyűjtési időszak vége (maximum, 90 nap) | Jan. január 15., 2020 | | |
|Ügyfél-fizetési dátum | Dec. 30, 2019 | | |
|Kifizetés kiszámítása | Jan. január 15., 2020 | | |
|Kifizetés dátuma | Február 15., 2020 | | **Negyedéves ügyfelek esetén** <br> <br> **Rendelések jelentés** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> Ügyfél neve</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>Vevőkód</li> <li>Ügyfél neve</li> <li>Rendeléskód</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Kifizetés állapota:** elküldve |

## <a name="transaction-history-download-export"></a>Tranzakciós előzmények letöltésének exportálása

Ez a beállítás letölti a tranzakciós Előzmények lapon, a keresett típus, a dátum, a társított tranzakció összege, az ügyfél, a termék és az ösztönző programra vonatkozó egyéb tranzakciós elemek letöltését.

| oszlop neve     | Leírás    |
|-------------|-------------------------------|
| earningId                      | Az egyes keresések egyedi azonosítója                                                                                                       |
| participantId                  | A program keretében keresett partner elsődleges identitása                                                                            |
| participantIdType              | Az ösztönző programokhoz és az értékesítőhöz általában az áruházbeli programok és az Azure Marketplace program azonosítója                                          |
| participantName                | A jövedelemszerzési partner neve                                                                                                              |
| partnerCountryCode             | A kereső partner helye/országa                                                                                                  |
| Programnév                    | Ösztönző/tároló program neve                                                                                                             |
| transactionId                  | A tranzakció egyedi azonosítója                                                                                                    |
| transactionCurrency            | A pénznem, amelyben az eredeti ügyfél-tranzakció történt (nem a partner helyének pénzneme)                                     |
| transactionDate                | A tranzakció dátuma. Olyan programok esetében hasznos, amelyekben sok tranzakció járul hozzá egy adott keresethez                                           |
| transactionExchangeRate        | A megfelelő tranzakció USD-értékének megjelenítésére használt árfolyam                                                                 |
| transactionAmount              | A tranzakció összege az eredeti tranzakció pénznemében, amely alapján létrejött a kereset                                              |
| transactionAmountUSD           | Tranzakció összege USD-ben                                                                                                                |
| szintje                          | Üzleti szabályt jelez a beszerzéshez                                                                                                  |
| earningRate                    | Tranzakciós mennyiségre alkalmazott ösztönző arány a kereset létrehozásához                                                                      |
| quantity                       | A programtól függően változhat. A tranzakciós programok számlázott mennyiségét jelzi.                                                            |
| quantityType                   | A mennyiség típusát jelzi, például: számlázott mennyiség, MAU                                                                                     |
| earningType                    | Azt jelzi, hogy díj, árengedmény, együttműködés, eladás stb.                                                                                          |
| earningAmount                  | Jövedelemszerzési összeg az eredeti tranzakció pénznemében                                                                                      |
| earningAmountUSD               | Jövedelemszerzési összeg USD-ben                                                                                                                    |
| earningDate                    | A kereset dátuma                                                                                                                      |
| calculationDate                | A keresett adat kiszámításának dátuma a rendszeren                                                                                            |
| earningExchangeRate            | A megfelelő USD összeg megjelenítéséhez használt árfolyam                                                                                  |
| exchangeRateDate               | Az EarningAmount USD kiszámításához használt Exchange-árfolyam dátuma                                                                                   |
| paymentAmountWOTax             | Fizetési összeg (ÁFA nélkül) a csak az "eljuttatott" kifizetésekért fizetendő pénznemért                                                                 |
| paymentCurrency                | Fizessen a partner által a fizetési profilban kiválasztott pénznemre. Csak az eljuttatott kifizetések esetében látható                                                   |
| paymentExchangeRate            | A paymentAmountWOTax a fizetési pénznemben való kiszámításához használt Exchange-árfolyam a ExchangeRateDate használatával                                            |
| paymentId            | A fizetés egyedi azonosítója. Ez a szám látható a banki utasításban                                            |
| paymentStatus            | Fizetési állapot                                            |
| paymentStatusDescription            | A fizetési állapot rövid leírása                                            |
| Vevőkód                     | Mindig üres lesz                                                                                                                     |
| Customername (                   | Mindig üres lesz                                                                                                                     |
| partNumber                     | Mindig üres lesz                                                                                                                     |
| productName                    | A tranzakcióhoz csatolt terméknév                                                                                                       |
| productId                      | Egyedi termékazonosító                                                                                                                |
| parentProductId                | Egyedi szülő termék azonosítója. Megjegyzés: Ha nincs fölérendelt termék a tranzakcióhoz, akkor a szülő termék azonosítója = termékazonosító. |
| parentProductName              | A szülő termék neve. Megjegyzés: Ha nincs fölérendelt termék a tranzakcióhoz, akkor a szülő terméknév = terméknév nevet adja meg.   |
| productType                    | A termék típusa (például alkalmazás, bővítmény, játék stb.)                                                                                        |
| invoiceNumber                  | Számla száma (csak az EA esetében érvényes)                                                                                                  |
| resellerId                     | Viszonteladó azonosítója                                                                                                                      |
| Viszonteladó neve                   | Viszonteladó neve                                                                                                                            |
| transactionType                | A tranzakció típusa (például vásárlás, visszatérítés, megfordítás, jóváírás stb.)                                                               |
| localProviderSeller            | Helyi szolgáltató/a rekord eladója                                                                                                          |
| taxRemitted                    | Átutalt adó (értékesítési, használati, ÁFA/GST-adók) mennyisége.                                                                                   |
| taxRemitModel                  | Az adók (értékesítési, használati, ÁFA/GST-adók) átutalásával megbízott fél.                                                                    |
| storeFee                       | A Microsoft által a tárolóban az alkalmazás vagy a bővítmény elérhetővé tételének díjaként megőrzött összeg.                                            |
| transactionPaymentMethod       | A tranzakcióhoz használt ügyfél-fizetési eszköz (például kártya, mobil szolgáltatói számlázás, PayPal stb.)                                |
| tpan                           | A harmadik féltől származó ad-hálózatot jelzi                                                                                                     |
| customerCountry                | Ügyfél országa                                                                                                                         |
| customerCity                   | Customer City                                                                                                                            |
| customerState                  | Ügyfél állapota                                                                                                                           |
| customerZip                    | Ügyfél irányítószáma/irányítószám                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | A program egyedi azonosítója                                                                                                        |
| externalReferenceIdLabel       | Egyedi azonosító felirata                                                                                                                  |
| transactionCountryCode       | Az országkód, amelyben a tranzakció történt                                                                                                                  |
| taxCountry       | Eladva az ügyfél országának                                                                                                                  |
| taxState       | Értékesítés a vásárlói állapotba                                                                                                                  |
| taxCity       | Eladva a Customer City-nek                                                                                                                  |
| taxZipCode       | Eladva a Customer zip-be                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Programkód       | A program nevével leképezhető karakterlánc                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Az utolsó fizetési pénznemben befizetett összeg (a mező üres lesz, ha nem fizettek ki előzetes fizetést)                                                                                                                   |
| lastPaymentCurrency       | Utolsó fizetési pénznem (a mező üres lesz, ha nem fizettek ki előzetes fizetést)                                                                                                                   |
| AssetId       | A Piactéri szolgáltatáshoz tartozó ügyfelek rendeléseinek egyedi azonosítója.  Ez a megvásárolt beszerzési sorokhoz tartozó elemeket jelöli. Több eszköz is lehet.                                                                                                                   |
| Rendeléskód       | az ügyfél számlára vonatkozik                                                                                                                   |
| LineItemId       | Egyéni sor az ügyfél számláján                                                                                                                   |
| Ügyfél országa       | Az ügyfél által megadott ország neve.  Ez az ügyfél Azure-előfizetésében szereplő országtól eltérő lehet.                                                                                                                   |
| Ügyfél EmailAddress       | A végfelhasználó által megadott e-mail-cím.  Ez eltérő lehet az ügyfél Azure-előfizetésében szereplő e-mail-címről.                                                                                                                   |
| SkuId       | A közzététel során definiált SKU-azonosító. Egy ajánlat több SKU-val is rendelkezhet, de egy SKU csak egyetlen ajánlattal társítható.                                                                                                                   |

>[!Note]
>A Transact Publishing beállítás összes jelentéskészítési és elemzési funkciója a partner Center Cloud Partner Portal vagy Analytics szakaszának elemzések szakaszán keresztül érhető el.

## <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás

Ha segítséget szeretne kapni a számlázási kérdésekkel kapcsolatban, forduljon a [kereskedelmi piactér kiadója támogatási szolgálatához](https://aka.ms/marketplacepublishersupport).
