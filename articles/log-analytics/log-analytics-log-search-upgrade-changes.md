---
title: "Az Azure Naplóelemzés változások? | Microsoft Docs"
description: "Ez a cikk ismerteti a frissítés vonatkozó gyakran ismételt kérdések új lekérdezés nyelvének a Naplóelemzési."
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
ms.date: 11/29/2017
ms.author: bwren
ms.openlocfilehash: 017a1da233827f19489a99b234ee9009fd9f6fe3
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>Az Azure Naplóelemzés változások?
A lekérdezési nyelv magát, kívül több fejlesztései és változtatásokat, hogy meg kell ügyelnie, ha van a Naplóelemzési munkaterület [frissítve az új lekérdezési nyelv](log-analytics-log-search-new.md).  Ez a cikk röviden között az egyes részletes tartalomra mutató hivatkozásokat tartalmazó örökölt és frissített munkaterület változásait ismerteti. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Whats-changed-in-Azure-Log-Analytics/player]

Lásd: [új Naplóelemzési jelentkezzen keresési – gyakori kérdések és ismert problémákat](log-analytics-log-search-faq.md) a frissítés és a gyakori kérdésekre adott válaszok kapcsolatos ismert problémák leírását.  

## <a name="query-language"></a>Lekérdezés nyelve
A Naplóelemzési frissítése az elsődleges módosítása az új lekérdező nyelv, amely rendelkezik-e az előző nyelvi keresztül végrehajtott jelentős fejlesztések többek.  

Az örökölt nyelv és az új nyelv közötti gyakori műveleteinek közvetlen összehasonlítása kaphat [Azure Naplóelemzés új lekérdezési nyelv való váltás](log-analytics-log-search-transition.md).  Új nyelvi teljes dokumentáció áll rendelkezésre, [Azure napló Analytics lekérdezési nyelv](https://docs.loganalytics.io).


## <a name="computer-groups"></a>Számítógépcsoportok
Számítógépcsoport létrehozásához a metódus nem változott, bár most adjon meg egy egyedi alias az egyes.  Napló keresések alapján számítógépcsoportokat az új nyelv szintaxisának kell használnia.

Nincs új szintaxisának számítógépcsoportok használata a napló keresése.  A $ComputerGroups függvény helyett számítógépcsoportok áll minden, a függvény egy egyedi alias a megvalósítva.  A napló-keresés, mint bármely más függvény használja az aliast.  

Részletek [számítógépcsoportokat a Log Analyticshez jelentkezzen keresések](log-analytics-computer-groups.md).


## <a name="log-search-portals"></a>Naplókeresési portálok
A naplófájl-keresési portálon hozhat létre és futtathat a napló keresések mellett most már használhatja a jelentősen nagyobb szerkesztési funkciókat biztosító Advanced Analytics portál.

A két portál rövid leírása megtalálható [portálok a létrehozása és módosítása az Azure Naplóelemzés napló lekérdezések](log-analytics-log-search-portals.md).  Az új portálon, a oktatóanyag végigvezetheti [az Analytics portál első lépések](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal).

## <a name="alerts"></a>Riasztások
A frissített munkaterületek működnek a riasztások, de a lekérdezés, amely futtatja az új nyelven kell megírni.  Bármely létező riasztási szabályok, a frissítés előtt volt automatikusan konvertálja az új nyelvet.  További részletekért szerezhet be [Naplóelemzési ismertetése riasztások](log-analytics-alerts.md).

A runbookok és webhookokkal küldött riasztások adattartalom formátuma megváltozott.  Az új adattartalom formátuma beolvasása – részletek [műveletek hozzáadni a Naplóelemzési riasztási szabályok](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Irányítópultok
[Irányítópultok](log-analytics-dashboards.md) elavulttá végezzük.  Továbbra is használnak a csempéket hozzáadott az irányítópulton, mielőtt a munkaterület lett frissítve, de nem szerkesztheti azokat a csempéket, vagy újakat vehet fel.  Adatforrásnézet-tervezőből segítségével hozzon létre egyéni nézeteket, amelyek egy gazdagabb szolgáltatáskészletet mint irányítópultok rendelkezik.

Az adatforrásnézet-tervezőből részletek [használata adatforrásnézet-tervezőből egyéni nézetek létrehozása a Naplóelemzési](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
A folyamat az Naplóelemzési adatexportálási Power bi-bA a frissített munkaterületek megváltozott, és minden meglévő ütemezések közül a frissítés előtt létrehozott lehetetlenné válik.  

Részletek [Naplóelemzési exportálása adatokat a Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
A napló keresések futtatásához a PowerShell Get-AzureRmOperationalInsightsSearchResults egy frissített munkaterület nem fog működni.  Ez a funkció egy frissített munkaterület Invoke-LogAnalyticsQuery használja.

Részletek [Azure napló Analytics REST API - PowerShell-parancsmagok](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets).

## <a name="log-search-api"></a>Naplófájl-keresési API
A naplófájl-keresési REST API-t, és még a régebbi verziót használó megoldások frissítenie kell az API-t új verzióját használja.   

A új az API-verzión részletek [Azure napló Analytics REST API](https://dev.loganalytics.io/).

## <a name="next-steps"></a>Következő lépések

- Lásd: [új Naplóelemzési jelentkezzen keresési – gyakori kérdések és ismert problémákat](log-analytics-log-search-faq.md) a frissítés és a gyakori kérdésekre adott válaszok kapcsolatos ismert problémák leírását.