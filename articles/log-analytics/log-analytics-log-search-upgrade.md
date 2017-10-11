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
ms.date: 08/08/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 4a3ee3c4d1fa9b626a51f24997603adceed8311f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="upgrade-your-azure-log-analytics-workspace-to-new-log-search"></a>Új naplófájl-keresési Azure Naplóelemzési munkaterület frissítése

> [!NOTE]
> Az új frissítési Log Analytics lekérdezési nyelv lehetősége jelenleg választható jogosultságot ad [hatékonyságának növeléséhez az új nyelv](https://go.microsoft.com/fwlink/?linkid=856078).  

Az új Naplóelemzési lekérdező nyelv, és frissítenie kell a munkaterület azt előnyeit.  Ez a cikk ismerteti az új nyelv és a munkaterület konvertálása előnyeit.  Ha nem szeretné most frissíteni, majd a munkaterület továbbra is működik ugyanúgy, mint mindig volt, de azt a rendszer automatikusan konvertál egy későbbi időpontban.  Jelentős mennyiségű időt és értesítést fog kapni, a dátumnak beállításakor.

Ez a cikk részletesen az új nyelv és a frissítési folyamat.

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
A frissítés lesz állítva az összes Azure-régiók közötti ezért elérhető egyes régiókban többi előtt.  Tudni fogja, ha a munkaterület frissítve lesz, amikor megjelenik a lila szalagcím tetején találhatók, a munkaterület felkérte a frissítés érhető el.

![1. frissítése](media/log-analytics-log-search-upgrade/upgrade-01a.png)

## <a name="what-happens-when-i-upgrade"></a>Mi történik, ha frissítek?
Ha a munkaterületet, minden mentett keresések, a riasztási szabályok és a nézetek, a nézet-tervezővel létrehozott automatikusan alakulnak az új nyelven.  Megoldások szereplő keresések nem automatikusan történik, de a azok még inkább parancsprogramok megnyitásakor.  Ez az teljesen átlátható.

## <a name="can-i-go-back-after-i-upgrade"></a>Frissíthetem után is lépjen?
Amikor frissít, a munkaterület egy teljes biztonsági mentés használatban van, amely tartalmazza az összes mentett keresések, a riasztási szabály és a nézetek pillanatképet.  Ez lehetővé teszi, hogy a régi munkaterület visszaállítása, ha később kell felügyelni.

Az örökölt munkaterület visszaállítása, keresse fel **beállítások** a munkaterületen, majd **összefoglaló frissítése**.  Ezután kiválaszthatja az beállítással **örökölt munkaterület visszaállítása**.  

![Örökölt visszaállítása](media/log-analytics-log-search-upgrade/restore-legacy-b.png)

## <a name="how-do-i-perform-the-upgrade"></a>Hogyan végezhető el a frissítést?
A munkaterület frissítheti, amikor megjelenik a portál felső lila szalagcím.  

1.  A frissítési folyamat elindításához kattintson a lila szalagcím, amely szerint **további és frissítési**.<br>![2. frissítés](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>
2.  Olvassa végig a további információt a frissítés a frissítési adatok lapon.<br>![2. frissítés](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>
3.  Kattintson a **frissítés most** a frissítés elindításához.<br>![4. frissítés](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>A jobb felső sarkában található egy értesítési mezőt a állapotát jeleníti meg.<br>![5. frissítése](media/log-analytics-log-search-upgrade/upgrade-05.png)
4.  Készen is van.  A naplófájl-keresési oldalon tekintse meg a következő a frissített munkaterület ismerteti.<br>![6 frissítése](media/log-analytics-log-search-upgrade/upgrade-06.png)<br>

Ha a frissítés nem sikerül okozó hibát észlel, nyissa meg a [vitafóruma](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) és tegye fel a kérdését vagy [hozzon létre egy támogatási kérést](../azure-supportability/how-to-create-azure-support-request.md) Azure-portálról.

## <a name="how-do-i-learn-the-new-language"></a>Hogyan ismerje meg az új nyelv?
Mivel több szolgáltatás használják létrehoztunk Önnek egy [külső webhely üzemeltetésére dokumentációjában](https://docs.loganalytics.io/) az új nyelven.  Ez magában foglalja, oktatóanyagok, példák és teljes hivatkozás segítségével gyorsan származnak. Az új nyelv oktatóanyag keresztül végigvezetheti [Ismerkedés a lekérdezések](https://go.microsoft.com/fwlink/?linkid=856078) és hozzáférni a nyelvi referencia: [Log Analytics lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856079).  

Ha már ismeri a hagyományos Log Analytics lekérdezési nyelv a, ha, majd használhatja a nyelvi konverter felvett a munkaterületen a frissítés részeként.

A hagyományos lekérdezés megírása, és kattintson a **átalakítása** lefordított verziószámának megjelenítéséhez.  Majd vagy a Keresés gombra a keresést, vagy a példány futtatásához, és illessze be a konvertált lekérdezést használandó valahol máshol, például a riasztási szabályt is.

![Nyelvi konverter](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Következő lépések
- Tekintse meg a [oktatóanyag új nyelvi](https://go.microsoft.com/fwlink/?linkid=856078).
- Végezze el a [a naplófájl-keresési portál használata az oktatóanyag](log-analytics-log-search-log-search-portal.md) az új lekérdezés nyelven.
- Ismerkedjen meg az új [Advanced Analytics portál](https://go.microsoft.com/fwlink/?linkid=856587).
