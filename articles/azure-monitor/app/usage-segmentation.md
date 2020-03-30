---
title: Felhasználó-, munkamenet- és eseményelemzés az Azure Application Insightsban
description: A webalkalmazás felhasználóinak demográfiai elemzése.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 56059304026b060e2215ce73e0e94e3200573a14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670984"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Felhasználók, munkamenetek és események elemzése az Application Insightsban

Megtudhatja, hogy az emberek mikor használják a webalkalmazást, milyen oldalak érdeklik őket a legjobban, hol találhatók a felhasználók, és milyen böngészőket és operációs rendszereket használnak. Üzleti és használati telemetriai adatok elemzése az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)használatával.

![Képernyőkép az Application Insights felhasználóiról](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Bevezetés

Ha még nem látja az adatokat a felhasználók, munkamenetek vagy események panelaz Application Insights portálon, [megtudhatja, hogyan kezdheti el a használati eszközöket.](usage-overview.md)

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A Felhasználók, munkamenetek és események szegmentálása eszköz

A használati panelek közül három ugyanazt az eszközt használja a webalkalmazásból származó telemetriai adatok szeleteléséhez és kockázta három szempontból történő szeleteléséhez. Az adatok szűrésével és felosztásával betekintést nyerhet a különböző oldalak és funkciók relatív használatára vonatkozóan.

* **Felhasználók eszköz:** Hányan használták az alkalmazást és annak funkcióit.  A felhasználókat a böngészőcookie-kban tárolt névtelen azonosítók segítségével számoljuk. A különböző böngészőket vagy gépeket használó egyetlen személy egynél több felhasználónak számít.
* **Munkamenetek eszköz**: Hány felhasználói tevékenységi munkamenet tartalmazott az alkalmazás bizonyos oldalait és funkcióit. A munkamenetet a rendszer fél óra felhasználói inaktivitás vagy 24 órás folyamatos használat után számolja.
* **Események eszköz**: Milyen gyakran használják az alkalmazás bizonyos oldalait és funkcióit. Az oldalnézet akkor számít, amikor a böngésző betölt egy oldalt az alkalmazásból, feltéve, hogy ön [már műszerezte azt.](../../azure-monitor/app/javascript.md) 

    Az egyéni esemény az alkalmazásban történik valami nek egy előfordulását jelenti, gyakran felhasználói beavatkozást, például egy gombra kattintást vagy egy feladat befejezését. Kódot szúrhat be az alkalmazásba [egyéni események létrehozásához.](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)

## <a name="querying-for-certain-users"></a>Bizonyos felhasználók lekérdezése

A felhasználók különböző csoportjainak megismerése a Felhasználók eszköz tetején található lekérdezési beállítások módosításával:

* Megjelenítés: Válassza ki az elemzésre kipróbálandó felhasználók kohorszát.
* Ki használta: Egyéni események et és oldalmegtekintéseket választhat.
* Közben: Válasszon egy időtartományt.
* By: Válassza ki, hogyan szeretné az adatokat egy ideig vagy egy másik tulajdonság, például a böngésző vagy a város szerint.
* Felosztás: Válasszon olyan tulajdonságot, amellyel felosztandó vagy szegmentálni szeretné az adatokat. 
* Szűrők hozzáadása: Korlátozza a lekérdezést bizonyos felhasználókra, munkamenetekre vagy eseményekre a tulajdonságaik , például a böngésző vagy a város alapján. 
 
## <a name="saving-and-sharing-reports"></a>Jelentések mentése és megosztása 
A Felhasználók jelentéseit mentheti, akár csak Ön számára a Saját jelentések szakaszban, vagy megosztva mindenki mással, akik hozzáférnek ehhez az Application Insights-erőforráshoz a Megosztott jelentések szakaszban.

Felhasználó,munkamenetek vagy események jelentésre mutató hivatkozás megosztása; kattintson a **Megosztás** gombra az eszköztáron, majd másolja a hivatkozást.

Az adatok másolatának megosztása a Felhasználók, munkamenetek vagy események jelentésben; Kattintson a **Megosztás** gombra az eszköztáron, majd a **Word ikonra** kattintva hozzon létre egy Word-dokumentumot az adatokkal. Vagy kattintson a fődiagram feletti **Word ikonra.**

## <a name="meet-your-users"></a>Ismerje meg a felhasználókat

A **Felhasználók beismerése** szakasz az aktuális lekérdezéssel egyező öt mintafelhasználó adatait jeleníti meg. Az egyének viselkedésének mérlegelése és feltárása az aggregátumok mellett betekintést nyújthat arra vonatkozóan is, hogy az emberek valójában hogyan használják az alkalmazásodat.

## <a name="next-steps"></a>További lépések

- A használati élmény engedélyezéséhez kezdje el elküldeni [az egyéni eseményeket](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) vagy [oldalnézeteket.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Ha már küld egyéni eseményeket vagy oldalnézeteket, fedezze fel a Használati eszközöket, hogy megtudja, hogyan használják a felhasználók a szolgáltatást.
    - [Tölcsérek](usage-funnels.md)
    - [Megőrzés](usage-retention.md)
    - [Felhasználói folyamatok](usage-flows.md)
    - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
    - [Felhasználói környezet hozzáadása](usage-send-user-context.md)
