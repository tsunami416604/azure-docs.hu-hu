---
title: Az értékesítői ismeretek – GYIK
description: Gyakori kérdések a Cloud Partner Portal eladói bepillantást vevő szolgáltatásával kapcsolatban.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285384"
---
<a name="seller-insights-faq"></a>Az értékesítői ismeretek – GYIK
===================

Ez a cikk útmutatást nyújt a gyakori felhasználói eljárásokról, valamint az eladói információkra vonatkozó kérdésekről.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>A letöltött tranzakciós fájlban lévő értékek definícióinak megkeresése
------------------------------------------------------------------

A tranzakciós fájlban szereplő metrikai értékek definíciói a cikk [eladói bepillantások definíciójában](./si-insights-definitions-v4.md)találhatók.


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Megtekintheti a megfizetett tranzakciók vásárlói adatait
-------------------------------------------------------------

Miután letöltötte a tranzakciókat a kifizetési modulból, keresse meg a **kifizetési állapot**feliratú oszlopot, és alkalmazza a szűrőt úgy, hogy csak a "kifizetve" értéket jelenítse meg. A következő oszlopok jelennek meg, amelyek az ügyfél adatait tartalmazzák: **Cégnév**, **vásárlói E-mail**, **ügyfél országa**, **vásárlói állapot**és **ügyfél postai irányítószáma**.


<a name="calculate-my-open-accounts-receivable"></a>Saját nyitott fiókok számítása
-------------------------------------

Miután letöltötte a tranzakciókat a kifizetési modulból, keresse meg a **kifizetési állapot**feliratú oszlopot, és alkalmazza a szűrőt úgy, hogy csak a "közelgő kifizetés" és "nem készen áll a kifizetés" értéket jelenítse meg. Ezután adja meg a **kinyerési összeg (számítógép)** címkével ellátott oszlop összegét.


<a name="calculate-revenue-by-customer-usage-period"></a>Bevétel kiszámítása az ügyfél használati időtartama alapján
------------------------------------------

Miután letöltötte a tranzakciókat a kifizetési modulból, keresse meg a **tranzakció állapota**feliratú oszlopot, és szűrje a "fizetős" értéket.   Minden felsorolt tranzakció esetében a **kifizetési mennyiség (számítógép)** feliratú oszlop a kifizetett összeget jelöli.  A tranzakcióhoz társított használati időszak kiszámításához használja az oszlop **díjszabási dátumát**, amely a tranzakció érvényességi időszakához közeledik a használat utolsó napjának.


<a name="calculate-your-bad-debt"></a>A helytelen tartozás kiszámítása
---------------------

Miután letöltötte a tranzakciókat a kifizetési modulból, keresse meg a **végső gyűjtemény állapotát**jelölő oszlopot, és alkalmazza a szűrőt úgy, hogy csak a "Write off" értéket jelenítse meg. Ezután adja meg a **kinyerési összeg (számítógép)** címkével ellátott oszlop összegét.


<a name="view-payout-or-customer-contact-information"></a>Kifizetés vagy ügyfél elérhetőségi adatainak megtekintése
-------------------------------------------

Jelentkezzen be felhasználóként a "tulajdonos" szerepkörrel, és ne a "közreműködő" szerepkört. Csak a tulajdonosi szerepkör fogja látni a kifizetés és az ügyfél adatait. A felhasználói szerepkörökkel kapcsolatos további információkért tekintse meg a [felhasználók kezelése](./cloud-partner-portal-manage-users.md)című cikket.


<a name="calculate-my-advance-payouts"></a>Saját előfizetések kiszámítása
----------------------------

Miután letöltötte a tranzakciókat a kifizetési modulból, keresse meg a **Tranzakciótípus**nevű oszlopot, és alkalmazza a szűrőt úgy, hogy csak a "díj" értéket jelenítse meg. Ezután keresse meg a **végső gyűjtemény állapotát**jelölő oszlopot, és alkalmazza a szűrőt úgy, hogy csak a "folyamatban" értéket jelenítse meg. Végül a **kifizetés mennyisége (PC)** oszlopban adja meg az ügyféltől a gyűjtemény előtt fizetett összes előleg kiszámításának összegét.


<a name="calculate-customer-refunds"></a>Ügyfél-visszatérítések kiszámítása
--------------------------

Miután letöltötte a tranzakciókat a kifizetési modulból, keresse meg a **végső gyűjtemény állapotát**jelölő oszlopot, és alkalmazza a szűrőt úgy, hogy csak a "visszatérítés" értéket jelenítse meg. Az ügyfelek számára feldolgozott összes visszatérítés kiszámításához adja meg a **díj összege (PC)** oszlopot.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>A Microsoft Channel partner által érintett tranzakciók azonosítása
----------------------------------------------------------------

Az **Azure-licenccel** rendelkező oszlop összes olyan tranzakciója, amely szűrve van a "nagyvállalati szintű viszonteladó" és a "felhőalapú megoldás-szolgáltató" értékének megjelenítésére, egy Microsoft Channel partnert is magában foglal. A partnerrel kapcsolatos további részletekért tekintse meg a **viszonteladói nevét** és a **viszonteladói e-mail-címét** a kifizetési modul letöltése és az ügyfél modul letöltése területen.


<a name="identify-trial-usage-and-trial-conversions"></a>Próbaverziós és próbaverziós konverziók azonosítása
------------------------------------------

A megrendelés, a használat és a kifizetési modul letöltése mostantól tartalmazza a **próbaidőszakot** , amely segít megérteni, hogy mikor fejeződött be a próbaidőszak a megadott sorrendben, ahol alkalmazható. Ha meg szeretné tekinteni a próbaverzió használatát és a rendeléseket, keresse meg az **SKU számlázási típus** oszlopot a letöltésekben, és alkalmazza a szűrőt úgy, hogy csak a "próbaverzió" értéket jelenítse meg. A próbaverziók konverziójának megtekintéséhez keresse meg a **próbaverzió befejezési dátum** oszlopát a letöltések között, és alkalmazza a szűrőt úgy, hogy csak a megrendeléseket jelenítse meg, ha a **próbaidőszak** korábbi a mai dátum és a **Törlés dátuma** oszlop üres vagy későbbi, mint a **próbaverzió befejezési dátuma**.


<a name="when-is-my-monthly-payout-calculated"></a>Mikor számítják ki a havi kifizetés kiszámítását
------------------------------------

A kifizetéseket minden hónap 15. napján kell kiadni az előző hónap utolsó naptári napjában a kifizetésre kész összes összegért. A hónap harmadik napján a Microsoft kiszámítja az előző hónap kifizetési összegét, és frissíti a letöltésben szereplő összes díjköteles tranzakciót a "közelgő kifizetés" értékkel a **kifizetés állapota** oszlopban. Ezek a tranzakciók addig maradnak ebben az állapotban, amíg meg nem történik a fizetési kérelem elküldése a bankszámlájára, ekkor a **kifizetési állapotuk** a "kifizetve" értékre kerül, és a "kifizetés dátuma" frissül, hogy megjelenjen a fizetési kérelemnek a bankba való beküldésének dátuma.


<a name="calculate-customer-acquisition-and-loss"></a>Ügyfelek beszerzésének és elvesztésének kiszámítása
---------------------------------------

Megtekintheti azt a dátumot, amikor az ügyfél először vásárolta meg az egyik ajánlatot úgy, hogy megkeresi a **Date beszerzett** oszlopot az ügyfél letöltésében. Hasonlóképpen megtekintheti azt a dátumot, amely után már nem volt olyan ajánlat, amelyet az ügyfél letöltéséhez tartozó **elveszett** oszlop megkeresésével adott meg.


<a name="finding-more-help"></a>További segítség keresése
-----------------

- Az [értékesítői elemzések definíciói](./si-insights-definitions-v4.md) – a mérőszámok és az adatok definícióinak megkeresése

- [Ismerkedés az értékesítői betekintéssel](./si-getting-started.md) – bevezetés az eladói betekintési szolgáltatás használatába.

