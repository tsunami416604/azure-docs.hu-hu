---
title: Kifizetési jelentések – Microsoft kereskedelmi piactér
description: A kifizetési jelentések részletesen ismertetik az ajánlattal kapcsolatban szerzett pénz részleteit, beleértve a fizetés összegét és a fizetését.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 09/09/2020
ms.openlocfilehash: 62de9380578ca5a97f7b72f9939d5c2dd7392c0d
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004221"
---
# <a name="payout-summaries"></a>Fizetési összegzések

A kifizetési összefoglaló a Microsoft által szerzett pénz részleteit jeleníti meg. Azt is megtudhatja, hogy mikor kapja meg a befizetéseket és mennyit fizet.

Ha termékeket értékesít az Azure Marketplace-en, a nyeremények összefoglalójában a sikeres kifizetésekről is tájékozódhat. További információ az Azure Marketplace-ről: befizetések [a kereskedelmi piactéren](./get-paid.md) és a [Microsoft kiadói szerződésben](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Ahhoz, hogy jogosult legyen a kifizetésre, a bevételnek el kell érnie a $50-es fizetési küszöbértéket. Részletekért tekintse meg a [Microsoft kiadói szerződését](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx).

> [!NOTE]
> Ha támogatásra [van](https://developer.microsoft.com/windows/support)szüksége a kifizetésekkel kapcsolatban, beleértve a kifizetési fiókok konfigurálását, a hiányzó kifizetéseket, a befizetések tárolását, vagy bármi mást, forduljon az ügyfélszolgálathoz.

## <a name="access-the-payout-summary-pages"></a>Hozzáférés a kifizetési összefoglaló lapokhoz

A kifizetési összefoglaló lapok egyikének megnyitása:

1. Válassza ki a kifizetés ikont a jobb felső sarokban.
2. Válassza ki a **tranzakciók előzményeit**, a **befizetéseket**és **az adatexportálást**.

## <a name="payments-page"></a>Fizetési oldal

Az ezen a lapon található összegek az összes, a programban részt vevő programot képviselik. Résztvevő-azonosító, program, fizetési azonosító és a bevétel típusa alapján is szűrhet. Az összegek amerikai dollárban vannak feltüntetve. A kifizetett érték szintén a fizetési pénznemben jelenik meg.

| Terület  | Description |
| --- | --- |
| Az év teljes kifizetése   | Az egész évben az USA dollárban fizetett összesített összeg az összes program esetében       |
| Következő becsült fizetés | Az egyszeri következő fizetés Önhöz közeledik (még akkor is, ha hamarosan mások is érkeznek), USA dollárban |
| Utolsó fizetés           | A legutóbbi fizetés összegét (USA-dollárban), a program nevét és programját           |
| Kifizetések forrás szerint     | Az elmúlt 12 hónap során a program által képviselt kifizetések mennyisége (USA-dollárban)           |
| Payments (Befizetések)               | Válassza a **kifizetett** vagy **függőben lévő** lehetőséget, majd a kívánt rendezést. Egy adott fizetésre vonatkozó további részletekért válassza a **nézet**lehetőséget. A fizetési átutalási utasítás másolatának letöltéséhez válassza a **Letöltés**lehetőséget. Mivel a tranzakciós előzmények adatai akár 24 órát is igénybe vehetnek, előfordulhat, hogy a kapcsolódó bevétel azonnal nem jelenik meg. |
|||

Az ezen a lapon lévő összes érték exportálásához válassza az **Exportálás** lehetőséget, és kövesse az adatexportálás lapon található utasításokat.

## <a name="transaction-history-page"></a>Tranzakció előzményei lap

Ez az oldal az összes egyéni bevételt jeleníti meg, beleértve a dátumot, a típust és a keresőt is. Kiválaszthatja a megtekinteni kívánt időszakot, és a beléptetési azonosító, a program, a fizetési azonosító, a kereső típusa, a kar és az állapot alapján szűrhet. Az aktuális pénzügyi évre (július 1 – június 30.) és az előző két pénzügyi évre vonatkozó adatszolgáltatások érhetők el.

Ha további részletekre kíváncsi, kattintson a lap jobb oldalán található lefelé mutató nyílra. Ekkor megjelenik a kar, a bevétel mennyisége és a termék. Ha az ilyen típusú adatmennyiség nem érhető el, de hozzá kell férnie, forduljon az [ügyfélszolgálathoz](https://developer.microsoft.com/windows/support). Ha a kereset egy beállítás eredménye, és nem tranzakció, a termék mezői nem jelennek meg.

Ezen az oldalon az összes tranzakciós adatmennyiség exportálásához válassza az **Exportálás** lehetőséget, és kövesse az adatexportálás lapon található utasításokat. A tranzakció előzményei lapról exportált fájlok megjelenítik az adatok tranzakció pénznemben történő megjelenítését, a tranzakciók pénznemében és az USA-dollárban felhalmozott összegeket, valamint a díjköteles díjat a pénznemben.

## <a name="payment-status"></a>Fizetési állapot

| Jövedelemszerzési állapot | Ok | Partneri beavatkozás szükséges? |
| --- | --- | --- |
| Feldolgozatlan              | A kereset jogosult a fizetésre. Ebben az állapotban marad az ösztönző programhoz tartozó program útmutatójában meghatározott hűtési időszakra vonatkozóan. | No                                                         |
| Közelgő                 | Függőben lévő belső felülvizsgálatok által létrehozott fizetési rendelés a kifizetés feldolgozása előtt                                                               | No                                                         |
| Függőben lévő adó számla      | Az adó számla nem teljes vagy érvénytelen                                                                                                  | A fizetés előtt frissítenie kell az áfát |
| Elutasította a felülvizsgálat során   | A rendszer elutasította a fizetést a felülvizsgálat során                                                                                                     | Részletekért forduljon a [Microsoft ügyfélszolgálatához](https://developer.microsoft.com/windows/support)                      |
| Sikertelen                   | A fizetés Microsoft rendszerhiba miatt meghiúsult                                                                                         | Részletekért forduljon a [Microsoft ügyfélszolgálatához](https://developer.microsoft.com/windows/support)                      |
| Folyamatban              | A fizetés folyamatban van                                                                                                                 | No                                                         |
| Helytelen fizetés        | A fizetési Újracsatlakozás folyamatban van                                                                                                       | No                                                         |
| Elküldött                     | A program elküldje a fizetést a banknak                                                                                                     | No                                                         |
| Újrafeldolgozás             | A fizetés Microsoft rendszerhibát észlelt, és folyamatban van az újrafeldolgozása                                                                  | No                                                         |
| Reversed                 | A bank megfordította a fizetést, és a következő fizetési ciklusban ismét el lesz küldve.                                                     | No                                                         |
| Számlaösszeg elutasítva     | Az adó számla elutasítása a felülvizsgálat során megtörtént. Az összes függőben lévő kifizetés addig tart, amíg az adó számla felülvizsgálata be nem fejeződik.                 | Részletekért forduljon a [Microsoft ügyfélszolgálatához](https://developer.microsoft.com/windows/support)                      |
| Adózási számla a felülvizsgálat alatt | Az adó számla felülvizsgálata folyamatban van. A kifizetését az adó-számla jóváhagyása után bocsátja ki a rendszer.                                   | No                                                         |
| Elutasítva                 | A Bank elutasította a fizetést                                                                                                      | Részletekért forduljon a bankhoz.                             |
|||

## <a name="export-data-page"></a>Adatexportálási oldal

Az adatexportáláshoz kövesse az alábbi utasításokat.

Az adatexportálás lap nem frissül a sajátján. Előfordulhat, hogy manuálisan kell frissítenie a lapot a legfrissebb információk megtekintéséhez.

Előfordulhat, hogy a szűrő **nem érhető** el az Adathiba miatt. Ez valószínűleg azt jelenti, hogy a kiválasztott alapértelmezett időszakot három hónapra hagyta, majd kiválasztott egy fizetési azonosítót az adott időszakon kívüli jövedelemszerzési szolgáltatásból. Bontsa ki az időtartamot, és próbálkozzon újra.

## <a name="payments"></a>Payments (Befizetések)

![Kifizetések exportálása](./media/pc-export-payments.png)

Ez a lehetőség egy adott program, a kapcsolódó adó és az összesített kereset összegének a bankban kapott fizetések letöltését biztosítja. Ez a jelentés számos partner Center-programhoz használatos, ezért előfordulhat, hogy egyes oszlopok nem alkalmazhatók a jelentésre. Ezek az oszlopok az alábbi jelöléssel vannak megjelölve.

| Oszlop neve | Description |
| --- | --- |
| participantID            | A program keretében keresett partner elsődleges identitása                                                                             |
| participantIDType        | Az áruházi programok esetében általában a program azonosítója az ösztönző programokhoz és az értékesítői AZONOSÍTÓhoz                                                                |
| participantName          | A jövedelemszerzési partner neve                                                                                                               |
| Programnév              | Ösztönző/tároló program neve                                                                                                              |
| szerzett                   | Az adott programhoz/participantID tartozó fizetési pénznemben szerzett összeg                                                                       |
| earnedUSD                | A program/résztvevő azonosítója számára elért összeg USD-ben                                                                                      |
| withheldTax              | A program-vagy participantID fizetési pénznemében visszatartott adó összege                                                               |
| salesTax                 | A program-vagy participantID tartozó fizetési díj pénznemben kifejezett teljes forgalmi adó összege (csak ösztönző programok esetén alkalmazható)                   |
| serviceFeeTax            | A program/participantID (csak az áruházi programokra és az Azure Marketplace-re vonatkozó) pénznemben fizetendő serviceFeeTax teljes mennyisége |
| totalPayment             | Teljes kifizetés a helyi pénznemben, kivéve a visszatartási adót, valamint a program-vagy participantID vonatkozó forgalmi adót (ha van ilyen).   |
| currencyCode             | Fizetés a pénznemkóddal                                                                                                                      |
| paymentMethod            | A partner fizetéséhez használt módszer, például az elektronikus banki átutalás, a jóváírási Megjegyzés                                                     |
| paymentID                | A fizetés egyedi azonosítója. Ez a szám általában a banki utasításban látható (csak az SAP-fizetésekre vonatkozik).              |
| paymentStatus            | Fizetési állapot                                                                                                                            |
| paymentStatusDescription | A fizetési állapot rövid leírása                                                                                                    |
| paymentDate              | A Microsofttól érkezett fizetési határidő                                                                                                      |
|||

## <a name="transaction-history"></a>Tranzakciók előzményei

![Tranzakciók előzményeinek exportálása](./media/pc-export-transaction.png)

Ez a beállítás a tranzakciós Előzmények lapon, a kereset típusa, a dátum, a társított tranzakció összege, az ügyfél, a termék és a programokra vonatkozó egyéb tranzakciós adatok letöltését teszi lehetővé.

| Oszlop neve | Description | Ösztönzők/tárolás/Azure Marketplace alkalmazhatósága |
| --- | --- | --- |
| earningId                      | Az egyes keresések egyedi azonosítója                                                                                                       | Mind                                                            |
| participantId                  | A program keretében keresett partner elsődleges identitása                                                                            | Mind                                                            |
| participantIdType              | Az ösztönző programokhoz és az értékesítőhöz általában az áruházbeli programok és az Azure Marketplace program azonosítója                                          | Mind                                                            |
| participantName                | A jövedelemszerzési partner neve                                                                                                              | Mind                                                            |
| partnerCountryCode             | A jövedelemszerzési partner helye/országa/régiója                                                                                                  | Mind                                                            |
| Programnév                    | Ösztönző/tároló program neve                                                                                                             | Mind                                                            |
| Tranzakcióazonosító                  | A tranzakció egyedi azonosítója                                                                                                    | Mind                                                            |
| transactionCurrency            | A pénznem, amelyben az eredeti ügyfél-tranzakció bekövetkezett (ez nem a partneri hely pénzneme)                                     | Mind                                                            |
| transactionDate                | A tranzakció dátuma. Olyan programok esetében hasznos, amelyekben sok tranzakció járul hozzá egy adott keresethez                                           | Mind                                                            |
| transactionExchangeRate        | A megfelelő tranzakció USD-értékének megjelenítésére használt árfolyam dátuma                                                                 | Mind                                                            |
| transactionAmount              | A tranzakció összege az eredeti tranzakció pénznemében, amely alapján létrejött a kereset                                              | Mind                                                            |
| transactionAmountUSD           | Tranzakció összege USD-ben                                                                                                                | Mind                                                            |
| szintje                          | Üzleti szabályt jelez a beszerzéshez                                                                                                  | Mind                                                            |
| earningRate                    | Tranzakciós mennyiségre alkalmazott ösztönző arány a kereset létrehozásához                                                                      | Mind                                                            |
| quantity                       | A programtól függően változhat. A tranzakciós programok számlázott mennyiségét jelzi.                                                            | Mind                                                            |
| quantityType                   | A mennyiség típusát jelzi, például: számlázott mennyiség, MAU                                                                             | Mind                                                            |
| earningType                    | Azt jelzi, hogy díj, árengedmény, Co-op, értékesítés stb.                                                                                          | Mind                                                            |
| earningAmount                  | Jövedelemszerzési összeg az eredeti tranzakció pénznemében                                                                                      | Mind                                                            |
| earningAmountUSD               | Jövedelemszerzési összeg USD-ben                                                                                                                    | Mind                                                            |
| earningDate                    | A kereset dátuma                                                                                                                      | Mind                                                            |
| calculationDate                | A keresett adat kiszámításának dátuma a rendszeren                                                                                            | Mind                                                            |
| earningExchangeRate            | A megfelelő USD összeg megjelenítéséhez használt árfolyam                                                                                  | Mind                                                            |
| exchangeRateDate               | Az EarningAmount USD kiszámításához használt Exchange-árfolyam dátuma                                                                                   | Mind                                                            |
| paymentAmountWOTax             | Fizetési összeg (ÁFA nélkül) a csak az "eljuttatott" kifizetésekért fizetendő pénznemért                                                                 | Mind                                                            |
| paymentCurrency                | Fizessen a partner által a fizetési profilban kiválasztott pénznemre. Csak az eljuttatott kifizetések esetében látható                                                   | Mind                                                            |
| paymentExchangeRate            | A paymentAmountWOTax a fizetési pénznemben való kiszámításához használt Exchange-árfolyam a ExchangeRateDate használatával                                            | Mind                                                            |
| claimId                        | Jogcím egyedi azonosítója                                                                                                              | Ösztönzők – csak néhány program                                |
| planId                         | Csomag egyedi azonosítója                                                                                                               | Ösztönzők – csak néhány program                                |
| paymentId                      | A fizetés egyedi azonosítója. Ez a szám általában a banki utasításban látható                                                 | Csak SAP-fizetések                                              |
| paymentStatus                  | Fizetési állapot                                                                                                                           | Mind                                                            |
| paymentStatusDescription       | A fizetési állapot rövid leírása                                                                                                   | Mind                                                            |
| customerId                     | Mindig üres lesz                                                                                                                     | Csak ösztönző programok (kivétel: OEM) és Azure Marketplace |
| Customername (                   | Mindig üres lesz                                                                                                                     | Csak ösztönző programok (kivétel: OEM) és Azure Marketplace |
| partNumber                     | Mindig üres lesz                                                                                                                     | Néhány ösztönző és tárolási program és az Azure Marketplace        |
| productName                    | A tranzakcióhoz csatolt terméknév                                                                                                       | Mind                                                            |
| productId                      | Egyedi termékazonosító                                                                                                                | Az áruház és az Azure Marketplace                                    |
| parentProductId                | Egyedi szülő termék azonosítója. Ha nincs fölérendelt termék a tranzakcióhoz, akkor a szülő termék azonosítója = termékazonosító. | Az áruház és az Azure Marketplace                                    |
| parentProductName              | A szülő termék neve. Ha nincs fölérendelt termék a tranzakcióhoz, akkor a szülő terméknév = terméknév nevet adja meg.   | Az áruház és az Azure Marketplace                                    |
| productType                    | A termék típusa, például alkalmazás, bővítmény vagy játék                                                                                        | Az áruház és az Azure Marketplace                                    |
| invoiceNumber                  | Számla száma (csak az EA esetében érvényes)                                                                                                  | Ösztönző és Azure Marketplace – csak néhány program           |
| subscriptionId                 | Az ügyfélhez társított előfizetés-azonosító                                                                                         | Ösztönző – csak néhány program                                 |
| subscriptionStartDate          | Előfizetés kezdő dátuma                                                                                                                  | Ösztönző – csak néhány program                                 |
| subscriptionEndDate            | Előfizetés befejezési dátuma                                                                                                                    | Ösztönző – csak néhány program                                 |
| resellerId                     | Viszonteladó azonosítója                                                                                                                      | Ösztönző – csak néhány program                                 |
| Viszonteladó neve                   | Viszonteladó neve                                                                                                                            | Ösztönző – csak néhány program                                 |
| distributorId                  | Terjesztő azonosítója                                                                                                                   | Ösztönző – csak néhány program                                 |
| distributorName                | Terjesztő neve                                                                                                                         | Ösztönző – csak néhány program                                 |
| agreementNumber                | Szerződés száma                                                                                                                         | Ösztönző – csak néhány program                                 |
| agreementStartDate             | Szerződés kezdő dátuma                                                                                                                     | Ösztönző – csak néhány program                                 |
| agreementEndDate               | Szerződés befejezési dátuma                                                                                                                       | Ösztönző – csak néhány program                                 |
| számítási feladat                       | Számítási feladat                                                                                                                                 | Ösztönző – csak néhány program                                 |
| transactionType                | A tranzakció típusa, mint például a vásárlás, a visszatérítés, a sztornírozás vagy a jóváírás                                                               | Az áruház és az Azure Marketplace                                    |
| localProviderSeller            | Helyi szolgáltató/a rekord eladója                                                                                                          | Csak tárolás                                                     |
| taxRemitted                    | Átutalt adó mennyisége (értékesítés, felhasználás vagy ÁFA/GST-adó)                                                                                   | Az áruház és az Azure Marketplace                                    |
| taxRemitModel                  | Az adók (értékesítési, használati, ÁFA/GST-adók) átutalásával megbízott fél                                                                    | Csak tárolás                                                     |
| storeFee                       | A Microsoft által a tárolóban az alkalmazás vagy a bővítmény elérhetővé tételének díjaként megőrzött összeg                                           | Csak tárolás                                                     |
| transactionPaymentMethod       | A tranzakcióhoz használt ügyfél-fizetési eszköz, például kártya, mobil szolgáltatói számlázás vagy PayPal                                | Az áruház és az Azure Marketplace                                    |
| tpan                           | A harmadik féltől származó ad-hálózatot jelzi                                                                                                     | Áruház – csak hirdetések                                               |
| customerCountry                | Ügyfél országa/régiója                                                                                                                         | Az áruház és az Azure Marketplace                                    |
| customerCity                   | Customer City                                                                                                                            | Az áruház és az Azure Marketplace                                    |
| customerState                  | Ügyfél állapota                                                                                                                           | Az áruház és az Azure Marketplace                                    |
| customerZip                    | Ügyfél irányítószáma/irányítószám                                                                                                                 | Az áruház és az Azure Marketplace                                    |
| purchaseTypeCode               | Mindig üres lesz                                                                                                                     | Ösztönző program – ICC                                        |
| purchaseOrderType              | Mindig üres lesz                                                                                                                     | Ösztönző program – ICC                                        |
| purchaseOrderCoverageStartDate | Mindig üres lesz                                                                                                                     | Ösztönző program – ICC                                        |
| purchaseOrderCoverageEndDate   | Mindig üres lesz                                                                                                                     | Ösztönző program – ICC                                        |
| programOfferingLevel           |                                                                                                                                          | Ösztönző program – ICC                                        |
| TenantID                       |                                                                                                                                          | Ösztönző programok                                             |
| externalReferenceId            | A program egyedi azonosítója                                                                                                        | Közvetlen fizetési programok (ösztönző és tároló)                      |
| externalReferenceIdLabel       | Egyedi azonosító felirata                                                                                                                  | Közvetlen fizetési programok (ösztönző és tároló)                      |
|||

## <a name="historical-statements"></a>Korábbi utasítások

![Korábbi utasítások exportálása](./media/pc-export-statements.png)

A 1 2019 júliusa előtti tranzakciós előzmények külön vannak kezelve. Az utasítások az aktuálisak helyett a következő mezőket fogják használni.

> [!NOTE]
> A korábbi tranzakciós előzmények egy "fenntartott" nevű oszlopot tartalmaznak, amely a modern előzmények "bevétel" oszlopának felel meg, azzal a különbséggel, hogy kizárja az összes bevételt az állapot = "fizetés elküldése" értékkel.

> [!NOTE]
> Az olyan szűrők, mint a 3M, 6M vagy 12M, nem lesznek érvényesek a **korábbi utasítások** szakaszra.

| Mező neve | Description |
| --- | --- |
| Bevétel forrása          | A bevétel forrása, amely alapján a tranzakció bekövetkezett, például Microsoft Store, Windows Phone-telefon áruház, Windows áruház 8 vagy reklám                  |
| Rendelésazonosító                | Egyedi megrendelési azonosító. Ez az azonosító lehetővé teszi a vásárlási tranzakciók azonosítását a megfelelő nem vásárlási tranzakciókkal, például a visszatérítéssel vagy a jóváírásokkal kapcsolatban. Mindkettő ugyanazzal a megrendelési AZONOSÍTÓval fog rendelkezni. Továbbá, ha egy megosztott díj esetében több fizetési módszert használtak egyetlen vásárláshoz, akkor lehetővé teszi a vásárlási tranzakciók összekapcsolását. |
| Tranzakcióazonosító          | Egyedi tranzakció-azonosító.                                                                                                                                          |
| Tranzakció dátuma és időpontja   | A tranzakció dátuma és időpontja (UTC).                                                                                                                       |
| Szülő termék azonosítója       | Egyedi szülő termék azonosítója. Ha nincs fölérendelt termék a tranzakcióhoz, akkor a szülő termék azonosítója = termékazonosító.                                |
| Termék azonosítója              | Egyedi termékazonosító.                                                                                                                                              |
| Szülő terméknév     | A szülő termék neve. Ha nincs fölérendelt termék a tranzakcióhoz, akkor a szülő terméknév = terméknév nevet adja meg.                                  |
| Terméknév            | A termék neve                                                                                                                                                    |
| Termék típusa            | A termék típusa, például alkalmazás, bővítmény vagy játék                                                                                                                       |
| Mennyiség                | Ha a bevételi forrás Microsoft Store for Business, a mennyiség a megvásárolt licencek számát jelöli. Az összes többi bevételi forrás esetében a mennyiség mindig 1 lesz. Még akkor is, ha egy tranzakció két sorra van felosztva, mert két különböző fizetési módszert használtak, az egyes sorok egy 1. mennyiséget jelenítenek meg. |
| Transaction Type (Tranzakció típusa)        | A tranzakció típusa, mint például a vásárlás, a visszatérítés, a sztornírozás vagy a jóváírás                                                                                              |
| Fizetési mód          | A tranzakcióhoz használt ügyfél-fizetési eszköz, például kártya, mobil szolgáltatói számlázás vagy PayPal                                                               |
| Ország/régió        | Az ország/régió, ahol a tranzakció történt                                                                                                                          |
| Helyi szolgáltató/eladó | Helyi szolgáltató/a rekord eladója                                                                                                                                        |
| Tranzakció pénzneme    | A tranzakció pénzneme                                                                                                                                            |
| Tranzakció mennyisége      | A tranzakció mennyisége                                                                                                                                              |
| Átutalt adó            | Átutalt adó mennyisége (értékesítés, felhasználás vagy ÁFA/GST-adó)                                                                                                                  |
| Nettó visszaigazolások            | Tranzakciós összeg, amely kevesebb adót átutalt                                                                                                                                   |
| Tárolási díj               | A Microsoft által a tárolóban az alkalmazás vagy a bővítmény elérhetővé tételének díjaként megőrzött nettó bevételezések százalékos aránya                                                      |
| Alkalmazás folytatása            | Nettó visszaigazolások mínusz a tárolási díj                                                                                                                                       |
| Visszatartott adók          | Visszatartott jövedelemadó-mennyiség (a **fenntartott** CSV-fájlban található)                                                                                                |
| Payment                 | Az alkalmazás a vonatkozó jövedelemadó-forrásadót is csökkenti (a tranzakció pénznemében megjelenő mennyiség). Nem szerepel a **fenntartott** CSV-fájlban.                               |
| FX-díj                 | A tranzakció pénznemének fizetési pénznemre való átalakításához használt deviza-árfolyam                                                                                         |
| Kifizetés pénzneme        | Az a pénznem, amelyben a fizetést elvégezték                                                                                                                                       |
| Átváltott fizetés       | Fizetési pénznemre átváltott fizetési összeg az FX-díj használatával                                                                                                         |
| Adó-kiutalás modell         | Az adók (értékesítési, használati, ÁFA/GST-adók) átutalásával megbízott fél                                                                                                   |
| Jogosultság dátuma és időpontja   | Az a dátum és idő, amikor a tranzakció bevétele jogosulttá válik a kifizetésre (UTC). A kifizetések létrehozásakor a tranzakció a kifizetés létrehozási dátuma előtt (csak a **fenntartott** CSV-fájlban foglalt) jogosultsági dátummal folytatódik. |
| Díjak                 | Megjeleníti a tranzakció mennyisége oszlopban összesített összes díj részletezését (csak az Azure Marketplace-en, nem tartalmazza a **fenntartott** CSV-fájl részét). |
|||

## <a name="next-step"></a>Következő lépés

- [Fizetési szabályzat részletei](./payout-policy-details.md)
