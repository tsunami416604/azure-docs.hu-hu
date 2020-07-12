---
title: Az Azure API Management integrálása az Azure-Application Insights
titleSuffix: Azure API Management
description: Megtudhatja, hogyan naplózhatja és tekintheti meg az Azure-API Management az Azure Application Insightsban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 8c9df3393a0554d2e65b3918c6760885f89e11ed
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86254744"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Az Azure API Management integrációja az Azure Application Insightsszal

Az Azure API Management lehetővé teszi az Azure Application Insights egyszerű integrálását – a webes fejlesztők számára bővíthető szolgáltatás több platformon futó alkalmazások létrehozásához és kezeléséhez. Ez az útmutató végigvezeti az integráció minden lépésén, és leírja azokat a stratégiákat, amelyek csökkentik a teljesítményre gyakorolt hatást a API Management Service-példányon.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követéséhez Azure API Management-példánnyal kell rendelkeznie. Ha még nem rendelkezik ilyennel, először hajtsa végre az [oktatóanyagot](get-started-create-service-instance.md) .

## <a name="create-an-azure-application-insights-instance"></a>Azure Application Insights-példány létrehozása

Az Azure Application Insights használatához először létre kell hoznia a szolgáltatás egy példányát.

1. Nyissa meg a **Azure Portal** , és navigáljon a **Application Insights**.  
    ![Alkalmazás-áttekintések létrehozása](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Kattintson a **+ Hozzáadás** gombra.  
    ![Alkalmazás-áttekintések létrehozása](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Töltse ki az űrlapot. Válassza az **általános** lehetőséget az **alkalmazás típusaként**.
4. Kattintson a **Létrehozás** lehetőségre.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Kapcsolat létrehozása az Azure Application Insights és az Azure API Management Service-példány között

1. Navigáljon az **Azure API Management Service-példányhoz** a **Azure Portal**.
2. A bal oldali menüben válassza a **Application Insights** lehetőséget.
3. Kattintson a **+ Hozzáadás** gombra.  
    ![App-bepillantások naplózó](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Válassza ki a korábban létrehozott **Application Insights** példányt, és adjon meg egy rövid leírást.
5. Kattintson a **Létrehozás** lehetőségre.
6. Most létrehozott egy rendszerállapot-kulccsal rendelkező Azure Application Insights naplózó eszközt. Ekkor meg kell jelennie a listában.  
    ![App-bepillantások naplózó](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> A jelenet mögött egy [naplózó](/rest/api/apimanagement/2019-12-01/logger/createorupdate) entitás jön létre a API Management-példányban, amely a Application Insights példány kialakítási kulcsát tartalmazza.

## <a name="enable-application-insights-logging-for-your-api"></a>Application Insights naplózás engedélyezése az API-hoz

1. Navigáljon az **Azure API Management Service-példányhoz** a **Azure Portal**.
2. A bal oldali menüből válassza ki az **API-k** elemet.
3. Kattintson az API-ra, ebben az esetben a **bemutató konferencia API**-ra.
4. Nyissa meg a **Beállítások** lapot a felső sávon.
5. Görgessen le a **diagnosztikai naplók** szakaszhoz.  
    ![App-bepillantások naplózó](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Jelölje be az **Engedélyezés** jelölőnégyzetet.
7. Válassza ki a csatolt naplózó elemet a **cél** legördülő menüben.
8. Adja meg a **100** bemenetet **mintavételezésként (%)** , és jelölje **be a mindig naplózott hibák** jelölőnégyzetet.
9. Kattintson a **Mentés** gombra.

> [!WARNING]
> Ha felülbírálja az alapértelmezett **0** értéket az **első bájtban, a törzs** mezőben jelentős mértékben csökkentheti az API-k teljesítményét.

> [!NOTE]
> A jelenet mögött egy "applicationinsights" nevű [diagnosztikai](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entitás jön létre az API szintjén.

| Beállítás neve                        | Érték típusa                        | Leírás                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bekapcsolás                              | logikai                           | Megadja, hogy engedélyezve van-e az API naplózása.                                                                                                                                                                                                                                                                                                |
| Destination (Cél)                         | Azure Application Insights Logger | A használandó Azure Application Insights-naplózó megadása                                                                                                                                                                                                                                                                                           |
| Mintavételezés (%)                        | decimal                           | 0 és 100 közötti értékek (százalék). <br/> Megadja, hogy a rendszer milyen százalékban naplózza a kérelmeket az Azure Application Insightsba. 0%-os mintavételezés: a rendszer a naplózott nulla kérelmeket jelenti, míg a 100% mintavételezés az összes naplózott kérelmet jelenti. <br/> Ezzel a beállítással csökkenthető a kérelmek Azure Application Insightsra való naplózásának teljesítményre gyakorolt hatása (lásd az alábbi szakaszt). |
| Hibák naplózása mindig                   | logikai                           | Ha ez a beállítás be van jelölve, a rendszer minden hibát naplóz az Azure Application Insightsba, a **mintavételezési** beállítástól függetlenül.                                                                                                                                                                                                                  |
| Alapszintű beállítások: fejlécek              | list                              | Azokat a fejléceket adja meg, amelyek az Azure Application Insightsba lesznek naplózva kérések és válaszok esetén.  Alapértelmezett: nincsenek naplózva fejlécek.                                                                                                                                                                                                             |
| Alapszintű beállítások: a törzs első bájtjai  | egész szám                           | Megadja, hogy a törzs hány első bájtjában legyen naplózva az Azure Application Insights a kérések és válaszok számára.  Alapértelmezett: a törzs nincs naplózva.                                                                                                                                                                                                    |
| Speciális beállítások: részletesség         |                                   | Megadja a részletességi szintet. A rendszer csak a magasabb súlyossági szintű egyéni nyomkövetéseket naplózza. Alapértelmezett: információ.                                                                                                                                                                                                                               |
| Speciális beállítások: előtér-kérelem  |                                   | Itt adhatja meg, hogy a rendszer hogyan naplózza a *frontend-kérelmeket* az Azure Application Insightsba. A *frontend-kérelem* az Azure API Management szolgáltatásba beérkező kérelem.                                                                                                                                                                        |
| Speciális beállítások: frontend-válasz |                                   | Megadja, hogy a rendszer hogyan naplózza az Azure-Application Insights a *felületi válaszokat* . A *frontend válasza* az Azure API Management szolgáltatásból kimenő válasz.                                                                                                                                                                   |
| Speciális beállítások: háttérbeli kérelem   |                                   | Megadja, hogy a rendszer naplózza-e a *háttérbeli kérelmeket* az Azure Application Insightsba. A *háttér-kérelem* az Azure API Management szolgáltatásból kimenő kérelem.                                                                                                                                                                        |
| Speciális beállítások: háttérbeli válasz  |                                   | Meghatározza, hogy a *háttérbeli válaszokat* a rendszer naplózza-e az Azure Application Insightsba. A *háttérbeli válasz* az Azure API Management szolgáltatásba érkező válasz.                                                                                                                                                                       |

> [!NOTE]
> Különböző szinteken is megadhatja a naplózók – egyetlen API-naplózó vagy az összes API-hoz tartozó naplózó.
>  
> Ha mindkettőt megadja:
> + Ha különböző naplózók, akkor mindkettőt használni fogjuk (multiplex naplók),
> + Ha ugyanazok a naplózók, de eltérő beállításokkal rendelkeznek, akkor az önálló API-k (több szemcsés szint) esetében az egyik az összes API-t felülbírálja.

## <a name="what-data-is-added-to-azure-application-insights"></a>Milyen adatbevitelt adnak az Azure Application Insights

Az Azure Application Insights fogadása:

+ Telemetria-elem *kérése* minden bejövő kérelemhez (előtér-*kérelem*, előtér- *Válasz*)
+ *Függőségi* telemetria elem, a háttér-szolgáltatásba továbbított minden kérelem esetében (*háttér-kérelem*, *háttérbeli válasz*),
+ *Kivétel* telemetria elem, minden sikertelen kérelem esetén.

A sikertelen kérelem egy kérelem, amely:

+ lezárt ügyfélkapcsolat miatt nem sikerült, vagy
+ az API *-házirendek on-Error* szakaszának elindítása, vagy
+ a válasz HTTP-állapotkódot megfelelő 4xx vagy 5xx.

## <a name="performance-implications-and-log-sampling"></a>Teljesítményre gyakorolt hatás és a napló mintavételezése

> [!WARNING]
> Az összes esemény naplózása súlyos teljesítménybeli következményekkel járhat, a bejövő kérések számától függően.

A belső terhelési tesztek alapján a funkció engedélyezése 40%-50%-os csökkenést eredményezett az átviteli sebességben, amikor a kérelmek száma túllépte az 1 000 kérelmek másodpercenkénti számát. Az Azure Application Insights az alkalmazások teljesítményének felmérésére szolgáló statisztikai elemzések használatára készült. Nem tekinthető naplózási rendszernek, és nem alkalmas arra, hogy a nagy mennyiségű API-ra vonatkozó egyedi kérelmeket naplózza.

A bejelentkezett kérelmek számát a **mintavételezési** beállítás módosításával módosíthatja (lásd a fenti lépéseket). A 100%-os érték azt jelenti, hogy az összes kérelem naplózva van, míg a 0%-os nincs naplózás. A **mintavétel** segít csökkenteni a telemetria mennyiségét, ami gyakorlatilag megakadályozza a teljesítmény jelentős romlását, miközben továbbra is a naplózás előnyeit hordozza.

A fejlécek és a kérelmek és válaszok naplózásának kihagyása pozitív hatással lesz a teljesítménnyel kapcsolatos problémák enyhítésére is.

## <a name="video"></a>Videó

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Következő lépések

+ További információ az [Azure Application Insightsról](/azure/application-insights/).
+ Vegye fontolóra [Az Azure Event Hubs való bejelentkezést](api-management-howto-log-event-hubs.md).
