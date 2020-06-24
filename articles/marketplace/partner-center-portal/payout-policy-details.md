---
title: Kifizetési szabályzat részletei – Azure Marketplace
description: A kifizetési szabályzatokkal kapcsolatos részletek, beleértve az ütemterveket és a visszakapcsolást.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: cb939d223d0aa91b6da62d3045ccad919f1bd277
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84887923"
---
# <a name="payout-policy-details"></a>Fizetési szabályzat részletei

Ez a cikk a Microsoft kifizetési folyamatát, a kifizetési ütemtervet ismerteti, ahol megtalálhatja a kifizetés és a visszakapcsolási szabályzat állapotát.

## <a name="where-to-find-upcoming-payouts"></a>Hol találhatja meg a közelgő kifizetéseket

A partner Centerben válassza a **kifizetés** lehetőséget a portál jobb felső részén:

![A partner Center portál jobb felső részén található kifizetési ikon illusztrálása.](./media/payout-overview.png)

> [!TIP]
> Nem minden fiók szerepkör fér hozzá a kifizetési adatokhoz. Részletekért lásd: [szerepkörök és engedélyek a kifizetési jelentés eléréséhez](./payout-summary-overview.md#roles-and-permissions).

## <a name="payment-schedules"></a>Fizetési ütemtervek

A következő szakaszok ismertetik a kifizetési folyamatokat.

### <a name="enterprise-agreement-transactions-after-may-1-2020"></a>Tranzakciók Nagyvállalati Szerződés május 1-től 2020

#### <a name="update-to-our-commercial-marketplace-publisher-payout-model"></a>Frissítés a kereskedelmi piactér kiadójának kifizetési modelljére

2020. május 1-től frissítjük az Azure Marketplace-en vásárolt termékekkel kapcsolatos kifizetési szabályzatot, vagy Microsoft-Nagyvállalati Szerződés AppSource ügyfeleinknek. Ha egy ügyfél az Azure Marketplace-ről vagy AppSource-ből vásárol egy terméket a meglévő Microsoft-Nagyvállalati Szerződés a 2020. május 1. után, akkor a következő kifizetési ciklusban megjelenő kifizetési ciklusban megkezdjük a kifizetések kiadását. Azok a tranzakciók, amelyekben az ügyfél hitelkártyát használ, változatlanok maradnak, és a nyereményt megelőzően 30 napos időszakot is megtartanak. Ez a táblázat a kifizetési ütemterv részleteit jeleníti meg.

> [!NOTE]
> Ha az ügyfél nem fizet, de már kiállítottunk Önnek egy kifizetést, tekintse meg az [ügyfél nem fizetési folyamatát](#process-for-customer-non-payment) alább.

| Esemény  | Dátum  | Partner láthatósága: partneri központ – kifizetési jelentés  |  Partner láthatósága: a partner Center Analytics\* |
| --- | --- | --- | --- |
| Tranzakció vagy havi használat | 8/1/2020 – 8/31/2020 | N/A | **Használati jelentés**: új felhasználás látható (négy óránként frissül)<br>**Rendelési jelentés**: N/A |
| Befejezési időszak (hónap) | 8/31/2020 | N/A | **Használati jelentés**: hónap végi felhasználás látható<br>**Rendelési jelentés**: N/A |
| Megrendelés létrehozva | 9/3/2020 – 9/7/2020 | N/A | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Kifizetési kereset kiszámítása | 9/4/2020 – 9/10/2020 | A kifizetési irányítópult tranzakciós előzményeiben **feldolgozatlan** megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Havi kifizetés | 10/5/2020 | A kifizetési irányítópulton a tranzakciós előzményekben **közelgőként** van megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Kifizetés dátuma | 10/15/2020 | A tranzakció **Sent** előzményeiben és a kifizetési irányítópult fizetések szakaszában ellátottként van megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Vevői számla összegyűjtve | 12/1/2020 | A tranzakció **Sent** előzményeiben és a kifizetési irányítópult fizetések szakaszában ellátottként van megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések  |
|  |  |  |  |

\*A használati és rendelési jelentések a partner Center elemzés szakaszában érhetők el.

### <a name="customers-who-pay-using-credit-card-or-invoice"></a>Bankkártyát vagy számlát használó ügyfelek

Az összes bankkártyával vagy havi számlával történő vásárlás 30 napos időszakot biztosít, amely biztosítja, hogy a rendszer kiürítse az alapokat, és nincsenek jóváírások vagy csalás gyanúja.

| Esemény  | Dátum  | Partner láthatósága: partneri központ – kifizetési jelentés  |  Partner láthatósága: a partner Center Analytics\*  |
| --- | --- | --- | --- |
| Tranzakció vagy havi használat | 8/1/2019 - 8/31/2019 | N/A | **Használati jelentés**: új felhasználás látható (négy óránként frissül)<br>**Rendelési jelentés**: N/A |
| Befejezési időszak (hónap) | 8/31/2019 | N/A | **Használati jelentés**: hónap végi felhasználás látható<br>**Rendelési jelentés**: N/A |
| Megrendelés létrehozva | 9/3/2019 – 9/7/2019 | N/A | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Vevői számla összegyűjtve | 9/7/2019 – 9/10/2019 | N/A | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Kifizetés kiszámítása | 9/8/2019 -9/12/2019 | A kifizetési irányítópult tranzakciós előzményeiben **feldolgozatlan** megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Havi kifizetés | 11/5/2019\* | A kifizetési irányítópulton a tranzakciós előzményekben **közelgőként** megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Kifizetés dátuma | 11/15/2019 | A tranzakció előzményeiben és a kifizetési irányítópult fizetések szakaszában **eljuttatott** jelöléssel ellátottként van megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
|  |  |  |  |

\*A használati és rendelési jelentések a partner Center elemzés szakaszában érhetők el.

### <a name="enterprise-agreement-transactions-prior-to-may-1-2020"></a>A tranzakciók Nagyvállalati Szerződés május 1. előtt, 2020

Az ezen dátum előtt bekövetkezett összes vásárlás feldolgozása és kifizetése az alábbi ütemterv szerint történik, miután a Microsoft begyűjtötte az ügyfelektől érkező fizetést, és feldolgozta a piactér díját.

| Esemény  | Dátum  | Partner láthatósága: partneri központ – kifizetési jelentés  |  Partner láthatósága: a partner Center Analytics\*  |
| --- | --- | --- | --- |
| Tranzakció vagy havi használat | 8/1/2019 – 8/31/2019 | N/A | **Használati jelentés**: új felhasználás látható (négy óránként frissül)<br>**Rendelési jelentés**: N/A |
| Befejezési időszak (hónap) | 8/31/2019 | N/A | **Használati jelentés**: hónap végi felhasználás látható<br>**Rendelési jelentés**: N/A |
| Megrendelés létrehozva | 9/3/2019 – 9/7/2019 | N/A | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Vevői számla összegyűjtve | 12/1/2019 | N/A | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Kifizetés kiszámítása | 12/5/2019 – 12/7/2019 | A kifizetési irányítópult tranzakciós előzményeiben **feldolgozatlan** megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Havi kifizetés | 1/5/2019 | A kifizetési irányítópulton a tranzakciós előzményekben **közelgőként** megjelölve | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
| Kifizetés dátuma | 1/15/2019 | **A tranzakció** előzményeiben és a kifizetési irányítópult fizetések szakaszában ellátottként megjelölt | **Használati jelentés**: a Rendeléskód/OrderLineItemID által megjelenített felhasználás<br>**Rendelési jelentés**: aktívként megjelenített Vevői megrendelések |
|  |  |  |  |

\*A használati és rendelési jelentések a partner Center elemzés szakaszában érhetők el.

## <a name="process-for-customer-non-payment"></a>Az ügyfél nem teljesített fizetési folyamata

Ritkán fordul elő, hogy a Microsoft nem tud befizetni az ügyfelektől a kereskedelmi Marketplace-vásárlásokhoz. Amikor egy ügyfél a számlázási ütemterv szerint nem fizet a Microsoftnak, kezdjük a gyűjtemények folyamatát. Ez a folyamat körülbelül négy hónapot vesz igénybe, és a Microsoft állandó kommunikációját is magában foglalja. Ha a folyamat végén nem érkezik fizetés, a Microsoft a nem begyűjthető módon írja le az alapokat.

Az itt megfogalmazott kifizetési folyamat alapján a Microsoft már kifizette a forrásokat olyan közzétevőknek (Ön), amelyek végső soron nem gyűjthetők. Ezért az ilyen összegek összeegyeztetése folyamatban van. Annak ellenőrzéséhez, hogy a (már fogadott) fizetést egyeztetni lehet-e, értesítést kap, ha az ügyfél a gyűjtemények folyamatában van, és a vásárlások valószínűleg ki lesznek írva.

A Microsoft a következő módszerek egyikének használatával visszakapcsolja az Ön számára már kifizetett kifizetéseket: (1) a Microsoft kivonja a jövőbeli kifizetésekből kifizetett összegeket. Ha például a kifizetések $1 000-es értéke nem gyűjthető és nem írható, a jövőbeli kifizetések visszatartásra kerülnek, amíg a $1 000 vissza nem áll, vagy (2) a Microsoft visszatérítési vagy számlázási közzétevőket igényelhet a nem gyűjtött összegekért.

A következő példa egy ütemezett ütemtervet mutat be:

| Esemény | Hozzávetőleges dátum | Partner láthatósága |
| --- | --- | --- |
| Példa a kifizetés dátumára | 10/15/2020 | A kifizetési **irányítópulton a** tranzakciós előzmények és a fizetések részben ellátottként van megjelölve |
| <font color="red">Ha az ügyfél nem fizet a Microsoftnak</font> | 12/2/2020 – 12/5/2020 | Nincs változás, ugyanaz, mint fent |
| Az ügyfél megkapja az első késedelmes fizetési értesítő e-mailt | 12/6/2020 | Nincs |
| Az ügyfél rendszeresen kap e-maileket a megnövekedett sürgősségről | 12/7/2020 – 1/31/2021 | Nincs |
| Valószínűleg a közzétevő értesítést kap a kiírásról | 1/7/2021 | A közzétevőnek küldött e-mail-értesítés, amelyet az ügyfele még nem küldött el. A tranzakció-azonosító és a dollár összege is szerepel. |
| Az ügyfél lemondási értesítést kap | 2/1/2021 | Nincs |
| A gyűjtési folyamat vége/az alapok le vannak írva | 2/15/2021 | A közzétevőnek elküldött e-mail-értesítés, amelyből a rendszer kiírta a forrásokat. A tranzakció-azonosító és a dollár összege is szerepel. |
| A kifizetés le van vonva | 3/1/2021 | A közzétevő negatív tranzakciót fog látni a partner Center kifizetési utasításban |
| A kifizetés megtagadva | 3/15/2021 | A jövőbeli kifizetések a partner Center kifizetési nyilatkozatában jelennek meg. A közzétevő nem kap fizetést, amíg az egyenleg már nem negatív.  |
|||

## <a name="next-step"></a>Következő lépés

- [Adó részletei](./tax-details-paid-transactions.md)
