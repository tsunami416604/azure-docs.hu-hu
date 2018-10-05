---
title: Alkalmazás-közzétételi lépéseket |} A Microsoft Docs
description: .
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 0fc82229e158ed35b97203b11d08841c683c45ce
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809918"
---
<a name="app-publishing-steps"></a>Alkalmazás közzétételének lépései
====================

A közzétételi folyamat indításához "Közzététel" a szerkesztő lapon kell kattintani.

![CPP közzététele alkalmazás gomb](./media/d365-financials/image014.jpg)


Az állapot lapon látni fogja a közzétételi lépéseket, amely azt jelzi, ahol az ajánlatot a közzétételi folyamat. A közzétételi folyamat bármely mozzanata is is jelentkezzen be és a minden kínál fülre kattintva bármely az ajánlatok a legfrissebb állapotának megtekintéséhez. Az ajánlat közvetlenül az állapot kattintson, és a részletek megtekintéséhez, ahol az ajánlatot a közzétételi folyamat.

Vegyük végig a közzétételi lépéseket minden egyes, ismertetjük, hogy mi történik, ha az egyes lépések, és mennyi ideig kell becsülni az egyes lépések vesz igénybe.

![Közzétételi folyamat ábrája](./media/d365-financials/image017.png)


**Előfeltételek ellenőrzése**

Ha "Közzététel" gombra kattint, egy automatikus ellenőrzést annak érdekében, hogy, miután feltöltötte az ajánlat az összes kötelező mezőt vesz igénybe. Ha mezőket nincsenek feltöltve adattal, egy figyelmeztetés fog megjelenni a mező mellett, és töltheti fel azt pontosan kell majd kattintson a "Közzététel" újra.

Miután végzett ezzel a lépéssel megfelelően, egy előugró ablak jelenik meg azzal a kérdéssel, közzététele az értesítések küldéséhez használandó e-mail cím. Miután az e-mail-címét, ez a lépés befejeződött.


**Automatikus alkalmazásnak érvényesítése**

Ebben a lépésben az automatizált hitelesítő szolgáltatás ellenőrzi a rendszerképre szűrjük, amelyek a tartalmak igazodik a megadott alkalmazás-bővítmények ajánlat metaadatok. Mindig győződjön meg arról, hogy az alkalmazás nevét, verzió, közzétevő és azonosítója megegyezik a megadott nevű bővítmény jegyzékfájljának `app.json`.


**Teszt meghajtó ellenőrzése**

Ha úgy döntött, Test Drive beállítása, ebben a fázisban, ahol a Test Drive-beállítások érvényesítése.


**Felügyeleti érvényesítési és-regisztráció**

Ebben a szakaszban az Érdeklődők generálása szolgáltatás konfiguráltság Microsoft ellenőrzi, hogy működik-e a CRM-integráció tesztelése az érdeklődő küld a CRM. Ez a lépés befejezése után töltse fel a CRM vagy az Azure Table hamis adatokat rekord jelenik meg. Érdeklődők generálása minden dokumentációja is található itt.


**Az AppSource-csomagolás**

Az áruház oldala részletes összetevőkhöz ellenőrzés alatt, és az appsource-on előzetes csomag létrehozása folyamatban van.


**Közzétevő jelentkezzen ki**

Ebben a szakaszban a **élő** gomb mostantól aktívvá válik. Most is kell az előzetes verzióra az ajánlat (az a hidekey) hivatkozás. Ha már elégedett az előzetes verzió megjelenése, kattintson az élő gombra.
Ne feledje, a kérelem nem teszi az alkalmazás live, az alkalmazás forrás, de ehelyett a belső érvényesítési folyamat elindítja.


**Marketinges és technikai alkalmazás ellenőrzése**

Ez a lépés nem, ahol párhuzamos marketinges és technikai ellenőrzés gyakorisággal. Tekintse meg a [ellenőrzőlista az alkalmazás beküldése az](https://aka.ms/CheckBeforeYouSubmit) és [alkalmazások fejlesztése a Dynamics 365 for Finance and Operations tanulmány](https://go.microsoft.com/fwlink/?linkid=841518) útmutatást dokumentációja kötelező követelményeket és javaslatokat. Az érvényesítési folyamat során a következő történik:
-  együttműködés Önnel bármely szálankénti függőben lévő kérdések és problémák.  
- biztosít egy alkalmazás-közzététel dátuma, és értesíti, amint az alkalmazás közzé van téve. 
- Adja meg a műszaki és a marketing érvényesítése 5 – 7 munkanapon belül vonatkozó első visszajelzés.

Ezeket a lépéseket általában eltarthat egy adott héten, és nem szükséges, hogy folyamatosan jelentkezve, a Cloud Partner portálra.


**A szolgáltatás az alkalmazás közzététele**

Az ajánlat rajta a végső feldolgozási keresztül történik. Az alkalmazás megfelelt a marketing és a műszaki ellenőrzési, de most már készen áll az alkalmazás forrás legyen rajta a végső feldolgozási keresztül kell haladnia.


**Élő**

Az ajánlat már élő az appsource-ban, és ügyfelek megtekinthetik, és helyezze üzembe az alkalmazást a Microsoft Dynamics 365 Business Central-előfizetésekben is. A kapcsolatot a arról tájékoztatja, hogy az alkalmazás közzétett alkalmazás forrás, kapni fog egy e-mailt. Bármikor minden ajánlatok lapján kattintson, és megnézheti az állapotukat minden az ajánlat, a jobb oldali oszlopban található. Kattintson az állapotadatokra kattintva részletesen ismertetjük az ajánlat közzétételi folyamat állapotát a.


<a name="error-handling"></a>Hibakezelés
--------------

A közzétételi folyamat során hiba merülhetnek fel. Ha hiba lép fel, amely tájékoztatja, hogy hiba történt a következő lépésekről utasításokat tartalmazó értesítő e-mailt kap. Hiba a folyamat során bármikor az állapot fülre kattintva is megjelenik. Amely mutasson, a folyamat a hiba történt a mellett megtudhatja, mit kell feloldani egy hibaüzenet jelenik meg.

A közzétételi folyamat során hibákat észlel, ha akkor szükségesek, javítsa ki a hibákat, majd kattintson a **közzététel** kezdenie a folyamatot. El kell indítania a közzétételi lépéseket elején **Előfeltételek ellenőrzése** során bármilyen hiba javítása után újbóli közzétételét.

Ha a hiba elhárítása problémák merülnek fel, meg kell nyisson egy támogatási kérelmet, kérjen segítséget a támogatási.


<a name="canceling-the-publishing-request"></a>A közzétételi kérelem visszavonása
--------------------------------

Előfordulhat, hogy közzététel megkezdéséhez, és hogy a kérés visszavonása. A közzétételi kérelem csak vonhatja vissza a közzétételi kérelem eléri a közzétevő jóváhagyás lépés után. Megszakítja a műveletet, kattintson a **Közzététel megszakítása**. A közzététel állapotának alaphelyzetbe állítja az 1. lépéshez, és újra közzé, meg kell kattintson a közzététel lehetőségre, majd kövesse az állapot.

![Mégse közzététel gomb](./media/d365-financials/image013.png)
