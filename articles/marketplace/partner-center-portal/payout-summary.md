---
title: Kereskedelmi piac kifizetésének összefoglalása | Azure Piactér
description: A Kifizetés összegzése részletesen bemutatja az ajánlattal szerzett pénzt. Azt is jelzi, hogy mikor kap kifizetéseket, és mennyit fog fizetni.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 5a930dfb38007349155581424d03ee7b3e7a6b46
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730208"
---
# <a name="payout-reporting"></a>Kifizetési jelentéskészítés

A [**Kifizetés összegzése**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) a Microsoftnál szerzett pénz részleteit jeleníti meg. Azt is jelzi, hogy mikor kap kifizetéseket, és mennyit fog fizetni.

Ha ajánlatokat értékesít az Azure Piactéren, a sikeres kifizetésekre vonatkozó információkat a **Kifizetésösszegzésben is láthatja.** Az Azure Marketplace-alapú fizetéssel kapcsolatos további információkért tekintse meg a [Microsoft Azure Piactér részvételi szabályzatait](https://go.microsoft.com/fwlink/p/?LinkId=722436) és a [Microsoft Azure Piactér kiadói szerződését.](https://go.microsoft.com/fwlink/p/?LinkID=699560)

> [!NOTE]
> Ahhoz, hogy jogosult legyen a kifizetésre, a bevételnek el kell érnie az 50 dolláros [kifizetési küszöböt.](payment-thresholds-methods-timeframes.md) A fizetési küszöbértékkel kapcsolatos részletekért tekintse meg ezt a lapot, és tekintse át a [Microsoft Azure Piactér kiadói szerződését.](https://go.microsoft.com/fwlink/p/?LinkID=699560)

- [Szerepkörök és a kifizetési jelentés elérésére való engedély](#roles-and-permission-to-access-the-payout-report)
- [Kifizetési jelentés: különbség a Cloud Partner Portal és a Partner Center között](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Vevőtípusok](#customer-types)
- [A kifizetés és a felhasználás közötti összefüggés](#corelation-between-payout-and-usage)
- [Tranzakcióelőzmények letöltése](#transaction-history-download-export)
- [Számlázási kérdések és támogatás](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Szerepkörök és a kifizetési jelentés elérésére való engedély

| Jelentések/oldalak    | Fióktulajdonos    | Manager  | Fejlesztői | Üzleti közreműködő |  Pénzügyi közreműködő | Marketingszakértő |
|------------------|------------------|----------|-----------|----|----|-----|
| Akvizíciós jelentés (beleértve a közelvalós idejű adatokat is) | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés | Megtekinthető | Nincs hozzáférés |
| Visszajelzési jelentés/válaszok | Megtekintheti és küldhet visszajelzést | Megtekintheti és küldhet visszajelzést | Megtekintheti és küldhet visszajelzést | Nincs hozzáférés | Nincs hozzáférés | Megtekintheti és küldhet visszajelzést |
| Egészségügyi jelentés (beleértve a közel valós idejű adatokat) | Megtekinthető | Megtekinthető | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés |
| Használat jelentés | Megtekinthető | Megtekinthető | Megtekinthető | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés |
| Kifizetési számla | Frissítheti | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Frissítheti | Nincs hozzáférés |
| Adózási profil | Frissítheti | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Frissítheti | Nincs hozzáférés |
| Kifizetés összegzése | Megtekinthető | Nincs hozzáférés | Nincs hozzáférés | Nincs hozzáférés | Megtekinthető | Nincs hozzáférés |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Kifizetési jelentés: különbség a Cloud Partner Portal és a Partner Center között

| | Felhőpartnerportál | Partnerközpont |
|---------|---------|---------|
| Hivatkozások | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)És[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navigáció | Az Insights-kifizetésben szereplő kifizetési jelentések | Kifizetési jelentés a Partnerközpontban – Kifizetésikon |
| Hatókör | <ul> <li>A soronkénti tranzakció látható a folyamatban lévő, összegyűjtött és kifizetett beszedéshez </li> <li>Jelentéskészítés – a beszerzési rendelés létrehozása után megjeleníti az összes sort, beleértve a folyamatban lévő beszedést és a folyamatban lévő számlázást, valamint a beszedés állapotát és azokat a sorokat, amelyek még nem jogosultak kifizetésre. </li> </ul> | <ul> <li>A sorokat jeleníti meg, amint jogosult bevételnek minősülnek.</li> <li>Az ügyfelek először a Microsoftnak fizetnek, majd a független szoftverszállítók láthatják a kifizetési jelentést.</li> <li>A kifizetési jelentés nem jeleníti meg a folyamatban lévő gyűjtést és a folyamatban lévő számlázást.  </li> </ul>  |
| A tranzakció nem áll készen a kifizetésre | Folyamatban lévő számlázás | Következő becsült kifizetés: A kifizetés állapota feldolgozatlan állapotban van.  |
| Kifizetési állapot |  | Feldolgozatlan: <br> A kereset jogosult a fizetésre. Ebben az állapotban marad az Ösztönző program programútmutatójában meghatározott hűtési időszakban. <br> <br> Közelgő: <br> A fizetési megbízás oka a fizetés feldolgozása előtt belső ellenőrzésre vár. <br> <br> Küldött: <br> A kifizetés elküldve a banknak. |

## <a name="customer-types"></a>Vevőtípusok

### <a name="enterprise-agreement"></a>Nagyvállalati szerződés

A nagyvállalati szerződéssel nem rendelkező ügyfelek nek havonta kell fizetniük a marketplace-alapú szoftverlicencekért. A nagyvállalati szerződéssel rendelkező ügyfelek számlázása havonta történik egy negyedévente bemutatott számlán keresztül.

### <a name="credit-cards-and-monthly-invoice"></a>Hitelkártyák és havi számla

Az ügyfelek hitelkártyával és havi számlával is fizethetnek. Ebben az esetben a szoftverlicenc-díjakat havonta számlázunk.

### <a name="csp-and-direct-pay-users"></a>KRIPT- és közvetlen fizetős felhasználók

Ha például a vevő hitelkártyával vásárol.

## <a name="corelation-between-payout-and-usage"></a>A kifizetés és a felhasználás közötti összefüggés

|Leírás    |    Dátum  | Rendelések/felhasználás  | Kifizetés |
|----------|----------|-----------|-------------|
|Rendelési időszak   | Aug 15, 2019 - Aug 30, 2019 | **Korrelációs attribútumok sorrende** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Használat** <br> <ul> <li>CustomerId </li> <li>Vevő neve</li> <li>(UsageReference) Beszerzésrekordazonosító/sorelemazonosító</li> <li> Becsült kiterjesztett díj <br> Becsült kifizetés (PC) </li> </ul> |  |
|Időszak vége (hónap)   | 2019. augusztus 30., 2019 | | |
|Számlázási dátum | 2019. szeptember 1. | | |
|Vevőfizetési dátum | 2019. szeptember 1. | | |
|Letéti időszak (csak hitelkártyák, 30 nap) | 2019. szeptember 1. | | **Korrelációs attribútumok Rendelései:** <br> <ul><li>Eszközazonosító</li> <li>Ügyfél azonosítója</li> <li> Vevő neve</li> </ul> <br> **Használat** <br> <ul> <li>Eszközazonosító</li> <li>CustomerId</li> <li>Vevő neve</li> <li>OrderId</li> <li>Sorelemazonosító</li> <li>tranzakcióAmount</li> <li>EarningAmountInLastPaymentCurrency (ÖsszeginLastPaymentCurrency)</li> </ul> <br> **Kifizetési állapot:** Feldolgozatlan |
|Begyűjtési időszak kezdete | 2019. szeptember 1. | | |
|A begyűjtési időszak vége (maximum 30 nap) | 2019. szeptember 30. | | |
|Kifizetés számítási dátuma (havonta 15-én) | 2019. október 1., | | **Korrelációs attribútumok** <br> <ul><li>Eszközazonosító</li> <li>Ügyfél azonosítója</li> <li>Vevő neve</li> </ul> <br> **Használat** <br> <ul> <li>Eszközazonosító</li> <li>CustomerId</li> <li>Vevő neve</li> <li>OrderId</li> <li>Sorelemazonosító</li> <li>tranzakcióAmount</li> <li>EarningAmountInLastPaymentCurrency (ÖsszeginLastPaymentCurrency)</li> </ul> <br> **Kifizetési állapot:** Közelgő |
|Kifizetés dátuma | 2019. október 15. | | **Korrelációs attribútumok** <br> <ul><li>Eszközazonosító</li> <li>Ügyfél azonosítója</li> <li> Vevő neve</li> </ul> <br> **Használat** <br> <ul> <li>Eszközazonosító</li> <li>CustomerId</li> <li>Vevő neve</li> <li>OrderId</li> <li>Sorelemazonosító</li> <li>tranzakcióAmount</li> <li>EarningAmountInLastPaymentCurrency (ÖsszeginLastPaymentCurrency)</li> </ul> <br> **Kifizetési állapot:** Elküldött kifizetés |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Nagyvállalati szerződés (negyedéves/havi ügyfelek)

| Leírás |    Dátum  | Használat | Kifizetés |
|----------|----------|---------|-----------|
|Rendelési időszak | Aug 15, 2019 - Aug 30, 2019 | **Korrelációs attribútumok sorrende** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Használati jelentés** <br> <ul> <li>CustomerId </li> <li>Vevő neve</li> <li>(UsageReference) Beszerzésrekordazonosító/sorelemazonosító</li> <li> Becsült kiterjesztett díj <br> Becsült kifizetés (PC) </li> </ul> | |
|Időszak vége (negyedév) | 2019. szeptember 30. | | |
|Számlázási dátum | 2019. október 15. | | |
|Letéti időszak (csak hitelkártyák, 30 nap) | n/a | | |
|Begyűjtési időszak kezdete | 2019. október 15. | | |
|Csak hitelkártyák, 30 nap | Nov 1, 2019 - Nov 30, 2019 | | |
|A begyűjtési időszak vége (maximum, 90 nap) | 2020. január 15. | | |
|Vevőfizetési dátum | 2019. december 30.Dec 30, 2019 | | |
|Kifizetés számítása | 2020. január 15. | | |
|Kifizetés dátuma | Február 15, 2020 | | **Negyedéves ügyfelek számára** <br> <br> **Rendelések jelentés** <br> <ul><li>Eszközazonosító</li> <li>Ügyfél azonosítója</li> <li> Vevő neve</li> </ul> <br> **Használat** <br> <ul> <li>Eszközazonosító</li> <li>CustomerId</li> <li>Vevő neve</li> <li>OrderId</li> <li>Sorelemazonosító</li> <li>tranzakcióAmount</li> <li>EarningAmountInLastPaymentCurrency (ÖsszeginLastPaymentCurrency)</li> </ul> <br> **Kifizetési állapot:** elküldve |

## <a name="transaction-history-download-export"></a>Tranzakcióelőzmények letöltésének exportálása

Ez a beállítás a Tranzakcióelőzmények oldalon látható minden egyes kereseti sor letöltését biztosítja, a kereset típusa, dátuma, a kapcsolódó tranzakció összege, az ügyfél, a termék és az Ösztönzők programra vonatkozó egyéb tranzakciós részletek.

| Oszlop neve     | Leírás    |
|-------------|-------------------------------|
| earningId                      | Egyedi azonosító minden egyes keresethez                                                                                                       |
| participantId                  | A program keretében kereső partner elsődleges identitása                                                                            |
| participantIdType típus              | Többnyire programazonosító ösztönző programok és eladó IF Áruházi programok és az Azure Marketplace                                          |
| participantName (résztvevőneve)                | A keresőpartner neve                                                                                                              |
| partnerOrszágkód             | A keresőpartner helye/országa                                                                                                  |
| programneve                    | Ösztönző/tároló program neve                                                                                                             |
| tranzakcióazonosító                  | A tranzakció egyedi azonosítója                                                                                                    |
| tranzakcióPénznem            | Az a pénznem, amelyben az eredeti vevőtranzakció történt (ez nem a partner hely pénzneme)                                     |
| transactionDate (tranzakciódátuma)                | A tranzakció dátuma. Hasznos olyan programokhoz, ahol sok tranzakció hozzájárul egy keresőképességhez                                           |
| tranzakcióExchangeRate        | A megfelelő tranzakció USD összegének megjelenítéséhez használt árfolyam                                                                 |
| tranzakcióAmount              | Tranzakció összege az eredeti tranzakció pénznemében, amely alapján a kereset keletkezik                                              |
| tranzakcióAmountUSD           | Tranzakció összege USD-ben                                                                                                                |
| Kar                          | A keresetüzleti szabályát jelzi.                                                                                                  |
| earningRate                    | A tranzakció összegére alkalmazott ösztönző ráta a keresőképesség létrehozásához                                                                      |
| quantity                       | A programtól függően változik. Tranzakciós programok számlázott mennyiségének jelzése                                                            |
| mennyiségtípusa                   | A mennyiség típusát jelzi, például: Számlázott mennyiség, MAU                                                                                     |
| earningType                    | Azt jelzi, ha ez díj, visszatérítés, coop, eladni stb.                                                                                          |
| earningAmount (összeg)                  | Kereseti összeg az eredeti tranzakció pénznemében                                                                                      |
| earningAmountUSD               | Kereseti összeg USD-ben                                                                                                                    |
| earningDate                    | A kereset dátuma                                                                                                                      |
| calculationDate (számítási dátum)                | A kereset számításának dátuma a rendszerben                                                                                            |
| keresőExchangeRate            | A megfelelő USD összeg megjelenítéséhez használt átváltási árfolyam                                                                                  |
| exchangeRateDate               | A keresőösszeg USD kiszámításához használt árfolyamdátum                                                                                   |
| paymentAmountWOTax             | Kereseti összeg (adó nélkül) csak "Elküldött" kifizetések esetén a fizetési pénznemben                                                                 |
| fizetési pénznem                | Fizetés a partner által a Fizetés profilban kiválasztott pénznemben. Csak az elküldött kifizetések esetén jelenik meg                                                   |
| fizetésExchangeRate            | A paymentAmountWOTax kiszámításához használt átváltási árfolyam az ExchangeRateDate használatával fizetési pénznemben                                            |
| fizetési azonosító            | A kifizetés egyedi azonosítója Ez a szám látható a bankszámlakivonaton                                            |
| paymentStatus (fizetési állapot)            | Kifizetés állapota                                            |
| paymentStatusDescription            | A fizetési állapot rövid leírása                                            |
| customerId                     | Mindig üres lesz                                                                                                                     |
| ügyfélneve                   | Mindig üres lesz                                                                                                                     |
| partNumber                     | Mindig üres lesz                                                                                                                     |
| Productname                    | Tranzakcióhoz kapcsolt terméknév                                                                                                       |
| productId                      | Egyedi termékazonosító                                                                                                                |
| parentProductId                | Egyedi szülőtermék-azonosító. Megjegyzés: ha nincs szülőtermék a tranzakcióhoz, akkor a szülőtermékazonosító = termékazonosító. |
| parentProductName              | A szülőtermék neve. Megjegyzés: ha nincs szülőtermék a tranzakcióhoz, akkor a szülőtermék neve = Terméknév.   |
| productType                    | A termék típusa (például alkalmazás, bővítmény, játék stb.)                                                                                        |
| invoiceNumber (számlaszáma)                  | Számlaszám (csak az EA-ra vonatkozik)                                                                                                  |
| viszonteladóId                     | Viszonteladóazonosítója                                                                                                                      |
| viszonteladóNeve                   | Viszonteladó neve                                                                                                                            |
| tranzakciótípusa                | A tranzakció típusa (például vásárlás, visszatérítés, sztornírozás, visszaterhelés stb.)                                                               |
| localProviderSeller            | Helyi szolgáltató/nyilvántartó                                                                                                          |
| adóVisszaadva                    | Az átutalt adó összege (értékesítés, felhasználás vagy ÁFA/GST adók).                                                                                   |
| taxRemitModell                  | Az adók átutalásáért felelős fél (értékesítés, felhasználás vagy ÁFA/GST adók).                                                                    |
| storeFee (ár- díj)                       | A Microsoft által az áruházban való elérhetővé tételért fizetendő díjként megtartott összeg.                                            |
| transactionPaymentMethod       | A tranzakcióhoz használt ügyfélfizetési eszköz (például kártya, mobilszolgáltatói számlázás, PayPal stb.)                                |
| tpan                           | A harmadik fél hirdetési hálózatát jelzi                                                                                                     |
| ügyfélOrszág                | Vevő országa                                                                                                                         |
| ügyfélváros                   | Ügyfél városa                                                                                                                            |
| customerState                  | Vevő állapota                                                                                                                           |
| customerZip                    | Vevő irányítószáma                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| külsőhivatkozás-azonosító            | A program egyedi azonosítója                                                                                                        |
| külsőReferenciaIdLabel       | Egyedi azonosító címkéje                                                                                                                  |
| transactionCountryCode       | Az az országkód, amelyben a tranzakció történt                                                                                                                  |
| adóOrszág       | Eladva a vevőországnak                                                                                                                  |
| adóÁllam       | Eladva a vevő államnak                                                                                                                  |
| taxCity       | Eladva az ügyfélvárosnak                                                                                                                  |
| taxZipCode       | Eladva az ügyfélzip-nek                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Programkód       | A program nevével leképezendő karakterlánc                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Kereseti összeg az utolsó kifizetés pénznemében (a mező üres lesz, ha nem fizettek ki előzetes kifizetéseket)                                                                                                                   |
| lastPaymentCurrency (lastPaymentCurrency)       | Utolsó kifizetés pénzneme (a mező üres lesz, ha nem fizetett ki előzetes kifizetést)                                                                                                                   |
| Eszközazonosító       | A piactéri szolgáltatás vevői rendeléseinek egyedi azonosítója.  A tranzakciós beszerzési sorokat jelöli. Több eszköz is lehet.                                                                                                                   |
| OrderId       | a vevő számlájára vonatkozik                                                                                                                   |
| Sorelemazonosító       | egyedi sor a vevő számláján                                                                                                                   |
| Vevő országa       | A vevő által megadott országnév.  Ez eltérhet az ügyfél Azure-előfizetésében szereplő országtól.                                                                                                                   |
| Ügyfél e-mail címe       | A végfelhasználó által megadott e-mail cím.  Ez eltérhet az ügyfél Azure-előfizetésében szereplő e-mail címtől.                                                                                                                   |
| SkuId között       | A közzététel során meghatározott termékváltozat-azonosító. Egy ajánlat sok termékváltozattal rendelkezhet, de egy termékváltozat csak egyetlen ajánlathoz társítható.                                                                                                                   |

>[!Note]
>A tranzakciós közzétételi lehetőségösszes jelentéskészítési és elemzési információja elérhető a Partnerközpont Felhőpartneri portál vagy Analytics szakaszának Elemzési részében.

## <a name="billing-questions-and-support"></a>Számlázási kérdések és támogatás

Ha segítségre van szüksége a számlázással kapcsolatos kérdésekkel kapcsolatban, forduljon a [kereskedelmi piactér kiadói támogatási csoportjához.](https://partner.microsoft.com/support/v2/?stage=1)
