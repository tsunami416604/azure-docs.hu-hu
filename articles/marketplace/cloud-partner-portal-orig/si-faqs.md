---
title: Értékesítői Insights – gyakori kérdések |} A Microsoft Docs
description: Gyakori kérdések a Cloud Partner portálra az értékesítői Insights-funkciót.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: ec7fc3a7877cf0bffac0043a74c34d6f0f04826b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810893"
---
<a name="seller-insights-faq"></a>Értékesítői Insights – gyakori kérdések
===================

Ez a cikk útmutatást nyújt általános felhasználói eljárások belül és értékesítői Insights kapcsolatos kérdésekre.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>A letöltött tranzakció fájlban keresse meg az értékeket a definíciók
------------------------------------------------------------------

A cikk azon a metrikaértékek a tranzakció fájlban található [értékesítői Insights definíciók](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Ügyfél részletek megtekintése, amelyhez I korábban lett fizetett tranzakciók
-------------------------------------------------------------

A tranzakciók küldenie jóváhagyásra cége kifizetési modulból a letöltés után keresse meg a címkével ellátott oszlop **küldenie jóváhagyásra cége kifizetési állapot**, és alkalmazza a szűrőt, hogy csak a "Out fizetős." értéket jeleníti meg A következő oszlopok jelenik meg a vásárlói adatokat tartalmazó: **cégnév**, **ügyfél e-mailek**, **vevői ország**, **ügyfélállapota**, és **ügyfél irányítószám**.


<a name="calculate-my-open-accounts-receivable"></a>Saját nyílt Kinnlevőségek kiszámítása
-------------------------------------

A tranzakciók küldenie jóváhagyásra cége kifizetési modulból a letöltés után keresse meg a címkével ellátott oszlop **küldenie jóváhagyásra cége kifizetési állapot**, és a alkalmazni a szűrő csak megjelenítése az érték "Közelgő kifizetés" és "Nem áll készen a küldenie jóváhagyásra cége kifizetési." A címkével ellátott oszlop majd összeg **küldenie jóváhagyásra cége kifizetési összeg (számítógép)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Ügyfél használati időszak alapján számítja ki az bevétel
------------------------------------------

A tranzakciók küldenie jóváhagyásra cége kifizetési modulból a letöltés után keresse meg a címkével ellátott oszlop **tranzakció állapota**, és az érték "Fizetős" szűréséhez.   Minden tranzakció felsorolt, a címkével ellátott oszlop **küldenie jóváhagyásra cége kifizetési összeg (számítógép)** is fizetett összeg jelöli.  Becsülje meg a tranzakcióhoz tartozó használati időszak, használja az oszlop **terhelés dátuma**, azaz az időszak, amelyekre vonatkozik a tranzakció használatának utolsó napján egy Bezárás előállításához.


<a name="calculate-your-bad-debt"></a>A tapasztalta kiszámítása
---------------------

A tranzakciók küldenie jóváhagyásra cége kifizetési modulból a letöltés után keresse meg a címkével ellátott oszlop **utolsó adatgyűjtési állapot**, és alkalmazza a szűrőt a csak a megjelenített értéke "Write ki." A címkével ellátott oszlop majd összeg **küldenie jóváhagyásra cége kifizetési összeg (számítógép)**.


<a name="view-payout-or-customer-contact-information"></a>Küldenie jóváhagyásra cége kifizetési és az elérhetőségi adatok megtekintése
-------------------------------------------

Jelentkezzen be a "tulajdonos" szerepkör és a "közreműködő" szerepkör nem rendelkező felhasználóként. Csak a tulajdonos szerepkör küldenie jóváhagyásra cége kifizetési és ügyfelek adatai jelennek meg. További információk a felhasználói szerepköröket a cikkben találja [felhasználók kezelése](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Az előzetes kifizetések kiszámítása
----------------------------

A tranzakciók küldenie jóváhagyásra cége kifizetési modulból a letöltés után keresse meg a címkével ellátott oszlop **tranzakció típusa**, és alkalmazza a szűrőt, hogy csak a "Díj." értéket jeleníti meg Ezután keresse meg a címkével ellátott oszlop **utolsó adatgyűjtési állapot**, és alkalmazza a szűrőt, hogy csak a "Folyamatban" értéket jeleníti meg. Végül összeg a **küldenie jóváhagyásra cége kifizetési összeg (számítógép)** oszlopot, amely kiszámítja az összes tudnak fizetős Önnek előtti gyűjteményben az ügyféltől.


<a name="calculate-customer-refunds"></a>Ügyfél visszatérítések kiszámítása
--------------------------

A tranzakciók küldenie jóváhagyásra cége kifizetési modulból a letöltés után keresse meg a címkével ellátott oszlop **utolsó adatgyűjtési állapot**, és alkalmazza a szűrőt, hogy csak a "Visszatérítés." értéket jeleníti meg Sum a **díj összege PC-** oszlopot, amely kiszámítja az összes visszatérítések az ügyfelek érdekében dolgoz fel.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>A tranzakciók vesz részt a Microsoft Channel Partner azonosítása
----------------------------------------------------------------

Az oszlopban lévő összes tranzakció **Azure licenctípus** , amely szűr a rendszer az "Enterprise keresztül viszonteladó" értékek megjelenítése és a "Cloud Solution Provider" magában foglalja a Microsoft Channel Partner. A partner olvashat, annak a **viszonteladó neve** és **viszonteladó e-mail címét** a küldenie jóváhagyásra cége kifizetési modul letöltése és a vásárlói modul letöltése.


<a name="identify-trial-usage-and-trial-conversions"></a>Próbaverzió használati és próba átalakítások meghatározása
------------------------------------------

Mostantól tartalmazza a rendelési, használatának és küldenie jóváhagyásra cége kifizetési modul letöltések **próbaverziós Záródátum** annak megértéséhez, ha a próbaidőszak alatt befejeződik. adott sorrendben, ha vannak ilyenek. Próbaverzió használati és a rendelések megtekintéséhez keresse meg a **Termékváltozat számlázási típus** a letöltések oszlopában, és alkalmazza a szűrőt, hogy csak a "Próbaverzió." értéket jeleníti meg Próbaverziós átalakítások megtekintéséhez keresse meg a **próbaverziós befejezési dátum** a letöltések oszlopában és a alkalmazni a szűrő csak megjeleníthető rendelésekhez a **próbaverziós befejezési dátum** utáni dátumot és **dátummegszakítása** oszlop értéke üres vagy későbbi, mint a **próbaverzió Záródátum**.


<a name="when-is-my-monthly-payout-calculated"></a>Mikor történik a havi küldenie jóváhagyásra cége kifizetési kiszámítása
------------------------------------

A kifizetések adják ki, hogy minden hónap 15. minden készen áll a küldenie jóváhagyásra cége kifizetési mennyiségű szerint az előző hónap utolsó napjának naptárban. A hónap harmadik napját, a Microsoft alapján számítja ki az előző hónapra küldenie jóváhagyásra cége kifizetési összeget lesz, és a letöltés "Közelgő kifizetés" az összes megfelelő díj tranzakciók frissítését a a **küldenie jóváhagyásra cége kifizetési állapot** oszlop. Egyes tranzakciók marad az állapotban addig, amíg a fizetési irányuló kérést küld a fiók létrehozása, hogy mely azok **küldenie jóváhagyásra cége kifizetési állapot** frissülni fognak "Fizetős lejárt", és frissülni fog a "küldenie jóváhagyásra cége kifizetési dátum" elküldte azt a dátumot megjelenítéséhez a a bank fizetési kérelmet.


<a name="calculate-customer-acquisition-and-loss"></a>Ügyfelek megnyerésében és veszteség
---------------------------------------

Láthatja, hogy a dátum, amikor az ügyfél először az ajánlatok egyik által vásárolt megkeresése a **dátum megszerzett** az ügyfél letöltési oszlopa. Hasonlóképpen, láthatja a dátum, amely után már nem volt megkeresésével az Ön által közzétett bármely ajánlatot a **dátum elveszett** az ügyfél letöltési oszlopa.


<a name="finding-more-help"></a>További segítséget nyújtó
-----------------

- [Értékesítői Insights definíciók](./si-insights-definitions-v4.md) -definíciók keresése mérőszámokat és adatokat

- [Ismerkedés az értékesítői Insights](./si-getting-started.md) – az értékesítői elemzés szolgáltatás bemutatása.

