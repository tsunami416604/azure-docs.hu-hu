---
title: Kereskedelmi piactér – kifizetési összefoglalás | Azure piactér
description: A nyeremények összegzése az ajánlatával kapcsolatban szerzett pénz részleteit jeleníti meg. Azt is megtudhatja, hogy mikor kapja meg a befizetéseket és mennyit fizet.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980269"
---
# <a name="payout-reporting"></a>Kifizetési jelentések

A **kifizetési összefoglaló** a Microsoft által szerzett pénz részleteit jeleníti meg. Azt is megtudhatja, hogy mikor kapja meg a befizetéseket és mennyit fizet.

Ha az Azure Marketplace-en értékesít ajánlatokat, akkor a sikeres kifizetésekről is tájékozódhat a **kifizetési összefoglalóban**. Az Azure Marketplace fizetési szolgáltatásával kapcsolatos további információkért tekintse meg a [Microsoft Azure Marketplace részvételi szabályzatokat](https://go.microsoft.com/fwlink/p/?LinkId=722436) és a [Microsoft Azure Marketplace közzétevői szerződést](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Ahhoz, hogy jogosult legyen a kifizetésre, a bevételnek el kell érnie a $50-es [fizetési küszöbértéket](payment-thresholds-methods-timeframes.md) . A fizetési küszöbértékre vonatkozó részletekért tekintse meg ezt a lapot, és tekintse át a [Microsoft Azure Marketplace kiadói szerződést](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Kifizetési jelentés: a Cloud Partner Portal és a partneri központ közötti különbség](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Ügyfelek típusai](#customer-types)
- [A kifizetések és a használat közötti Corel](#corelation-between-payout-and-usage)
- [Tranzakciós előzmények letöltése](#transaction-history-download-export)
- [Számlázási kérdések és támogatás](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Kifizetési jelentés: a Cloud Partner Portal és a partneri központ közötti különbség

| | Felhőpartnerportál | Partnerközpont |
|---------|---------|---------|
| Hivatkozások | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory és https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
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

|Leírás    |    Dátum  | Megrendelések/használat  | Kifizetés |
|----------|----------|-----------|-------------|
|Rendelési időszak   | Augusztus 15., 2019-augusztus 30., 2019 | **Korrelációs attribútumok rendelései** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Használat** <br> <ul> <li>CustomerId </li> <li>Ügyfél neve</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Becsült kiterjesztett díj <br> Becsült kifizetés (számítógép) </li> </ul> |  |
|Befejezési időszak (hónap)   | Augusztus 30, 2019 | | |
|Számlázási dátum | Szeptember 1., 2019 | | |
|Ügyfél-fizetési dátum | Szeptember 1., 2019 | | |
|Letéti időszak (csak bankkártyák, 30 nap) | Szeptember 1., 2019 – szeptember 30, 2019 | | **Korrelációs attribútumok rendelései:** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> Ügyfél neve</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Ügyfél neve</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Kifizetés állapota:** Feldolgozatlan |
|Gyűjtési időszak kezdete | Szeptember 1., 2019 | | |
|Gyűjtési időszak vége (legfeljebb, 30 nap) | Szeptember 30., 2019 | | |
|Kifizetési számítás dátuma (havonta, 15-én) | Október 1, 2019 | | **Korrelációs attribútumok** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li>Ügyfél neve</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Ügyfél neve</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Kifizetés állapota:** Közelgő |
|Kifizetés dátuma | Október 15., 2019 | | **Korrelációs attribútumok** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> Ügyfél neve</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Ügyfél neve</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Kifizetés állapota:** Fizetés elküldése |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Nagyvállalati szerződés (negyedévente/havonta)

| Leírás |    Dátum  | Használat | Kifizetés |
|----------|----------|---------|-----------|
|Rendelési időszak | Augusztus 15., 2019-augusztus 30., 2019 | **Korrelációs attribútumok rendelései** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Használati jelentés** <br> <ul> <li>CustomerId </li> <li>Ügyfél neve</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Becsült kiterjesztett díj <br> Becsült kifizetés (számítógép) </li> </ul> | |
|Befejezési időszak (negyedév) | Szeptember 30., 2019 | | |
|Számlázási dátum | Október 15., 2019 | | |
|Letéti időszak (csak bankkártyák, 30 nap) | – | | |
|Gyűjtési időszak kezdete | Október 15., 2019 | | |
|Csak bankkártyák, 30 nap | November 1, 2019 – november 30, 2019 | | |
|Gyűjtési időszak vége (maximum, 90 nap) | Jan. január 15., 2020 | | |
|Ügyfél-fizetési dátum | Dec. 30, 2019 | | |
|Kifizetés kiszámítása | Jan. január 15., 2020 | | |
|Kifizetés dátuma | Február 15., 2020 | | **Negyedéves ügyfelek esetén** <br> <br> **Rendelések jelentés** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> Ügyfél neve</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Ügyfél neve</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Kifizetés állapota:** elküldve |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

## <a name="transaction-history-download-export"></a>Tranzakciós előzmények letöltésének exportálása

Ez a beállítás letölti a tranzakciós Előzmények lapon, a keresett típus, a dátum, a társított tranzakció összege, az ügyfél, a termék és az ösztönző programra vonatkozó egyéb tranzakciós elemek letöltését. 

| Oszlop neve     | Leírás    | 
|-------------|-------------------------------|
| earningId                      | Az egyes keresések egyedi azonosítója                                                                                                       |
| participantId                  | A program keretében keresett partner elsődleges identitása                                                                            | 
| participantIdType              | Az ösztönző programokhoz és az értékesítőhöz általában az áruházbeli programok és az Azure Marketplace program azonosítója                                          | 
| participantName                | A jövedelemszerzési partner neve                                                                                                              | 
| partnerCountryCode             | A kereső partner helye/országa                                                                                                  |
| Programnév                    | Ösztönző/tároló program neve                                                                                                             | 
| Tranzakcióazonosító                  | A tranzakció egyedi azonosítója                                                                                                    | 
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
| CustomerId                     | Mindig üres lesz                                                                                                                     |
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
| OrderId       | az ügyfél számlára vonatkozik                                                                                                                   |
| LineItemId       | Egyéni sor az ügyfél számláján                                                                                                                   |
| Ügyfél országa       | Az ügyfél által megadott ország neve.  Ez az ügyfél Azure-előfizetésében szereplő országtól eltérő lehet.                                                                                                                   |
| Ügyfél EmailAddress       | A végfelhasználó által megadott e-mail-cím.  Ez eltérő lehet az ügyfél Azure-előfizetésében szereplő e-mail-címről.                                                                                                                   |
| SkuId       | A közzététel során definiált SKU-azonosító. Egy ajánlat több SKU-val is rendelkezhet, de egy SKU csak egyetlen ajánlattal társítható.                                                                                                                   |

>[!Note]
>A Transact Publishing beállítás összes jelentéskészítési és elemzési funkciója a partner Center Cloud Partner Portal vagy Analytics szakaszának elemzések szakaszán keresztül érhető el.

## <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás

Ha segítséget szeretne kapni a számlázási kérdésekkel kapcsolatban, forduljon a [kereskedelmi piactér kiadója támogatási szolgálatához](https://aka.ms/marketplacepublishersupport).
