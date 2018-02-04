---
title: "Új naplófájl-keresési Azure Naplóelemzés frissítése |} Microsoft Docs"
description: "Az új Naplóelemzési lekérdezési nyelv szinte, és a nyilvános előzetes verziójában való részvételhez.  Ez a cikk ismerteti az új nyelv és a munkaterület konvertálása előnyeit."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 3bb54f7897876d656da6f1a4b349c9db202a142d
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Új naplófájl-keresési frissítsen az Azure Naplóelemzés

Az új Naplóelemzési lekérdező nyelv, és a munkaterület kihasználja frissíteni kell.  A munkaterület frissítése, saját magának, vagy várjon, amíg az automatikusan frissítendő késői októberben elindul, és az év végét végig kell vinnie a bevezetés időszaka alatt.  Ez a cikk ismerteti az új nyelv és a munkaterület konvertálása előnyeit.  

## <a name="why-the-new-language"></a>Miért érdemes az új nyelv?
Tudjuk, hogy minden átmenet problémás van, és azt nem az it visszatöltött lekérdezés nyelvének csak módosítása.  Több oka a módosítás jelentős értéket nyújt a Log Analytics-ügyfelek számára.

- **Egyszerű, mégis hatékony.** Az új nyelv a könnyebben érthetőek legyenek és a több mint természetes nyelvű, mint a hagyományos nyelvi szerkezetek SQL hasonló.
- **Teljes kimenetátirányításával nyelv.**  Az új nyelv szélesebb körű kimenetátirányításával képességekkel rendelkezik mint a hagyományos nyelvet.  Egy másik parancsba is, mint korábban volt összetettebb lekérdezések létrehozása átirányítható a szinte bármilyen kimenet.
- **Keresési kötött mező információkinyerés.**  Az új nyelv speciális számított futtatókörnyezeti mezőkben, mint a hagyományos nyelvet támogat.  Kiegészítő mezők összetett számítások használja, és ezután használja a számított mezők további parancsokat, beleértve az összekapcsolásokhoz és az aggregációhoz.
- **Speciális illesztéseket.**  Az új nyelv speciális csatlakozik, mint például a táblák több mező csatlakoztatást, a belső és külső illesztések használata és a kiterjesztett mezők Csatlakoztatás örökölt nyelv biztosít.
- **Dátum és idő funkciók.**  Az új nyelv rendelkezik fejlettebb dátum/idő funkciók, mint a hagyományos nyelv.
- **Intelligens elemzés.**  Az új nyelv speciális algoritmusok adatkészletek minták értékeli, és hasonlítsa össze az adatok különböző csoportjai számára.
- **Speciális Analytics-portálról.**  A speciális elemzés portál nem érhető el a Log Analytics-portálról, beleértve a többsoros elemzési funkciókat nyújtja a lekérdezéseket, a további képi megjelenítések és a speciális diagnosztika szerkesztési.
- **Más alkalmazásokkal konzisztencia.**  Az új nyelv és az Advanced Analytics portál már használja az Application Insightsban elemzéséhez.  A Naplóelemzési bevezetné, lehetővé teszi az konzisztencia Azure-szolgáltatásokhoz.
- **Jobb integráció a Power bi-ban.** Az új nyelven lekérdezések exportálhatja a Power BI Desktop, használhatja a részletes adatok átalakítása képességei.
- **Sok minden mást.** Tekintse meg a [Azure napló Analytics lekérdezési nyelv](https://docs.loganalytics.io) helyek további információkat és az új nyelv kapcsolatos bemutatók.


## <a name="when-can-i-upgrade"></a>Ha frissíthető?
A frissítés lesz állítva az összes Azure-régiók közötti ezért elérhető egyes régiókban többi előtt.  Ha a munkaterület frissítve lesz, amikor megjelenik egy szalagcím tetején találhatók, a munkaterület felkérte a frissítés érhető el tudja.

![1. frissítése](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Ha a munkaterületet automatikusan frissítve van, majd, megjelenik egy szalagcím jelzi, hogy a felmerülő esetleges problémákat azonosítása egy összefoglalás frissíti a rendszer.

 ![Automatikus frissítés](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>Mi történik, a frissítés után?
A következő változnak a munkaterület átalakítás után:

- A mentett keresések, a riasztási szabályok és a nézeteket, a nézet-tervezővel létrehozott automatikusan alakítja át az új nyelven.  Megoldások szereplő keresések nem automatikusan történik, de a azok még inkább parancsprogramok megnyitásakor.  
- [Saját irányítópult](log-analytics-dashboards.md) az alábbi szempontból is elavult [adatforrásnézet-tervezőből](log-analytics-view-designer.md) és [Azure irányítópultok](../azure-portal/azure-portal-dashboards.md).  Saját irányítópult hozzáadott csempék továbbra is elérhetők, de azok most csak olvasható.
- [A Power BI-integráció](log-analytics-powerbi.md) új folyamat helyére.  Minden létrehozott meglévő Power BI ütemezések le lesz tiltva, és ki kell cserélni őket az új folyamat során.
- A válaszok [műveletek riasztási](log-analytics-alerts-actions.md) webhookok és runbookok használatával új formátumuk, és ennek megfelelően frissíti a riasztási szabályok esetleg.
- Tekintse meg a következő a [naplófájl-keresési gyakran ismételt kérdések](log-analytics-log-search-faq.md) esetében a frissítés kapcsolatos gyakori kérdésekre.

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>Hogyan állapítható meg, hogy történtek-e a frissítés probléma merül fel?
A frissítés befejezése után nem lesznek egy **összefoglaló frissítése** szakasz a munkaterület beállításait.  Ez a szakasz információt a frissítés és a felmerülő problémákkal megtekintéséhez ellenőrizze.

 ![Frissítési összefoglalót](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>Hogyan manuálisan végezhető el a frissítést?
A munkaterület frissítheti, amikor a portál felső fejléc megjelenik.  

1.  A frissítési folyamat elindításához kattintson a szalagcím, amely szerint **további és frissítési**.

    ![2. frissítés](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Olvassa végig a további információt a frissítés a frissítési adatok lapon.

    ![2. frissítés](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Kattintson a **frissítés most** a frissítés elindításához.

    ![4. frissítés](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>A jobb felső sarkában található egy értesítési mezőt a állapotát jeleníti meg.
    
    ![5. frissítése](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  Készen is van.  A naplófájl-keresési oldalon tekintse meg a következő a frissített munkaterület ismerteti.

    ![6 frissítése](media/log-analytics-log-search-upgrade/upgrade-06.png)

Ha a frissítés nem sikerül okozó hibát észlel, nyissa meg a [vitafóruma](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) és tegye fel a kérdését vagy [hozzon létre egy támogatási kérést](../azure-supportability/how-to-create-azure-support-request.md) Azure-portálról.

## <a name="how-do-i-learn-the-new-language"></a>Hogyan ismerje meg az új nyelv?
Mivel több szolgáltatás használják létrehoztunk Önnek egy [külső webhely üzemeltetésére dokumentációjában](https://docs.loganalytics.io/) az új nyelven.  Ez magában foglalja, oktatóanyagok, példák és teljes hivatkozás segítségével gyorsan származnak. Az új nyelv oktatóanyag keresztül végigvezetheti [Ismerkedés a lekérdezések](https://go.microsoft.com/fwlink/?linkid=856078) és hozzáférni a nyelvi referencia: [Log Analytics lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856079).  

Ha azt szeretné, hogy próbálja ki az új nyelv, beleértve a mintaadatok álló, lemezcsoport bemutató környezetben, rá egy pillantást a [playground környezet](https://portal.loganalytics.io/demo#/discover/home).

Ha már ismeri a hagyományos Log Analytics lekérdezési nyelv a, ha, majd használhatja a nyelvi konverter felvett a munkaterületen a frissítés részeként.  A hagyományos lekérdezés megírása, és kattintson a **átalakítása** lefordított verziószámának megjelenítéséhez.  Majd vagy a Keresés gombra a keresést, vagy a példány futtatásához, és illessze be a konvertált lekérdezést használandó valahol máshol, például a riasztási szabályt is.  Azt is beállíthatja, hogy egy pillantást a [adatlap](log-analytics-log-search-transition.md) , amely közvetlenül összehasonlítja az örökölt nyelv közös lekérdezések.

![Nyelvi konverter](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>További lépések
- Tekintse meg a [oktatóanyag új nyelvi](https://go.microsoft.com/fwlink/?linkid=856078).
- Végezze el a [a naplófájl-keresési portál használata az oktatóanyag](log-analytics-log-search-log-search-portal.md) az új lekérdezés nyelven.
- Ismerkedjen meg az új [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587).
