---
title: Eladó insights GYIK
description: Gyakori kérdések a Cloud Partner Portal Eladói elemzési funkciójával kapcsolatban.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285384"
---
<a name="seller-insights-faq"></a>Eladó insights GYIK
===================

Ez a cikk útmutatást nyújt az Eladói elemzésekkel kapcsolatos gyakori felhasználói eljárásokhoz és kérdésekhez.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>A letöltött tranzakciófájlban lévő értékek definícióinak megkeresése
------------------------------------------------------------------

A metrikus értékek definíciói a tranzakciós fájlban találhatók a [cikkBen Eladó Insights definíciók](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Azon tranzakciók vevői adatainak megtekintése, amelyekért kifizettek
-------------------------------------------------------------

Miután letöltötte a tranzakciókat a Kifizetési modulból, keresse meg a **Kifizetési állapot**feliratú oszlopot, és alkalmazza a szűrőt, hogy csak a "Kifizetett" értéket jelenítse meg. A következő oszlopok jelennek meg, amelyek a vevő adatait tartalmazzák: **Cégnév**, **Vevőe-mail**, **Vevőország**, **Vevői állam**és **Vevői irányítószám**.


<a name="calculate-my-open-accounts-receivable"></a>Nyitott kinnlevőségem kiszámítása
-------------------------------------

Miután letöltötte a tranzakciókat a Kifizetési modulból, keresse meg a **Kifizetési állapot**feliratú oszlopot, és alkalmazza a szűrőt, hogy csak a "Közelgő kifizetés" és a "Nem áll készen a kifizetésre" értéket jelenítse meg. Ezután összegezze a **kifizetési összeg (PC)** feliratú oszlopot.


<a name="calculate-revenue-by-customer-usage-period"></a>Bevétel számítása vevői használati időszak szerint
------------------------------------------

Miután letöltötte a tranzakciókat a Kifizetési modulból, keresse meg a **Tranzakció állapota**című oszlopot, és szűrje a "Fizetett" értéket.   Minden felsorolt tranzakció esetében a **Kifizetési összeg (PC)** feliratú oszlop a kifizetett összeget jelöli.  A tranzakcióhoz társított használati időszak becsléséhez használja a **Költségdátum**oszlopot, amely a tranzakció a tranzakció alapjául szolgáló időszak utolsó napjának szoros közelítése.


<a name="calculate-your-bad-debt"></a>Számolja ki a rossz adósság
---------------------

Miután letöltötte a tranzakciókat a Kifizetési modulból, keresse meg a **Final Collection Status**feliratú oszlopot, és alkalmazza a szűrőt, hogy csak a "Leírás" értéket jelenítse meg. Ezután összegezze a **kifizetési összeg (PC)** feliratú oszlopot.


<a name="view-payout-or-customer-contact-information"></a>Kifizetési vagy vevői kapcsolattartási adatok megtekintése
-------------------------------------------

Jelentkezzen be felhasználóként a "tulajdonos" szerepkörrel, és ne a "közreműködő" szerepkörrel. Csak a tulajdonosi szerepkör fogja látni a kifizetést és az ügyféladatokat. A felhasználói szerepkörökről a [Felhasználók kezelése](./cloud-partner-portal-manage-users.md)című cikkben olvashat bővebben.


<a name="calculate-my-advance-payouts"></a>Számítsa ki az előlegkifizetéseimet
----------------------------

Miután letöltötte a tranzakciókat a Kifizetés modulból, keresse meg a **Tranzakciótípus**feliratú oszlopot, és alkalmazza a szűrőt, hogy csak a "Költség" értéket jelenítse meg. Ezután keresse meg a **Záró gyűjtemény állapota**feliratú oszlopot, és alkalmazza a szűrőt, hogy csak a "Folyamatban" értéket jelenítse meg. Végül összegezd a **Kifizetési összeg (PC)** oszlopot, hogy kiszámítsd az ügyféltől történő beszedés előtt kifizetett előlegeket.


<a name="calculate-customer-refunds"></a>Vevői visszatérítések számítása
--------------------------

Miután letöltötte a tranzakciókat a Kifizetés modulból, keresse meg a **Final Collection Status**feliratú oszlopot, és alkalmazza a szűrőt, hogy csak a "Visszatérítés" értéket jelenítse meg. A **Díj összege (PC)** oszlop összegzése az ügyfelek számára feldolgozott összes visszatérítés kiszámításához.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>A Microsoft Channel-partnert érintő tranzakciók azonosítása
----------------------------------------------------------------

Az **Azure licenctípus** oszlopában lévő összes olyan tranzakció, amely a "Nagyvállalati viszonteladón keresztül" és a "Felhőszolgáltatón keresztül" értékek megjelenítéséhez szűrt, microsoft csatornapartnert foglal magában. A partnerrel kapcsolatos további részletekért keresse meg **a viszonteladói nevét** és **a viszonteladói e-mail-címét** a Kifizetésmodul letöltési moduljában és az Ügyfél modul letöltésében.


<a name="identify-trial-usage-and-trial-conversions"></a>A próbaverziók használatának és a próbaverziók konverzióinak azonosítása
------------------------------------------

A Rendelés, a Használati és a Kifizetés modul letöltése immár tartalmazza **a próbaidőszak befejezési dátumát,** így adott esetben könnyebben megértheti, hogy a próbaidőszak mikor értvéget az adott megrendelésre. A próbaverziók használatának és a rendelések megtekintéséhez keresse meg a **Termékváltozat számlázási típusa** oszlopot a letöltésekben, és alkalmazza a szűrőt, hogy csak a "Próbaverzió" értéket jelenítse meg. A próbakonverziók megtekintéséhez keresse meg a **próbaidőszak befejezési dátuma** oszlopot a letöltésekben, és alkalmazza a szűrőt csak azokra a megjelenítendő rendelésekre, amelyeknél a **próbaidőszak záró dátuma** már elmúlt a mai dátumnál, és a **Dátum törlése** oszlop üres vagy későbbi, mint a **Próbaidőszak záró dátuma**.


<a name="when-is-my-monthly-payout-calculated"></a>Mikor számítják ki a havi kifizetést?
------------------------------------

A kifizetéseket minden hónap 15-ig bocsátjuk ki Önnek minden kifizetésre kész összegre az előző hónap utolsó naptári napjáig. A hónap harmadik napján a Microsoft kiszámítja az előző havi kifizetés összegét, és frissíti a letöltésösszes vonatkozó terhelési tranzakcióját a **"Közelgő** kifizetés" oszlopban. Ezek a tranzakciók abban az államban maradnak mindaddig, amíg a kifizetési kérelmet el nem küldik a bankszámlájára, amikor a **kifizetési állapotuk** "Kifizetett" lesz, és a "Kifizetés dátuma" frissítésre kerül, hogy megjelenítse a kifizetési kérelmet a bankjának.


<a name="calculate-customer-acquisition-and-loss"></a>Vevőszerzés és -veszteség kiszámítása
---------------------------------------

Láthatja azt a dátumot, amikor az ügyfél először vásárolta meg az egyik ajánlatot, ha megkeresi a **Megszerzett dátum oszlopot** az ügyfél letöltésében. Hasonlóképpen láthatja azt a dátumot, amely után már nem volt ön által közzétett ajánlatuk, ha megkeresi a **Date Lost** oszlopot az ügyfél letöltésében.


<a name="finding-more-help"></a>További segítség keresése
-----------------

- [Seller Insights-definíciók](./si-insights-definitions-v4.md) – Metrikák és adatok definícióinak keresése

- [Első lépések az Eladói elemzések szolgáltatással](./si-getting-started.md) – Bevezetés az Eladói elemzések funkcióba.

