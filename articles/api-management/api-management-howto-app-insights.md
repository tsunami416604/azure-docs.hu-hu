---
title: Az Azure API-kezelés integrálása az Azure Application Insights szolgáltatással
titleSuffix: Azure API Management
description: Megtudhatja, hogyan naplózhatja és tekintheti meg az Azure API Management eseményeit az Azure Application Insightsban.
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
ms.openlocfilehash: 48a83fad3395f6ecf06fb1f1ba95aa1b06a53431
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259136"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Az Azure API Management integrációja az Azure Application Insightsszal

Az Azure API Management egyszerű integrációt tesz lehetővé az Azure Application Insights szolgáltatással – egy bővíthető szolgáltatás a több platformon alkalmazásokat fejlesztő és kezelő webfejlesztők számára. Ez az útmutató végigvezeti az ilyen integráció minden lépését, és ismerteti az API Management szolgáltatáspéldányteljesítmény-hatással kapcsolatos hatás csökkentésére irányuló stratégiákat.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követéséhez rendelkeznie kell egy Azure API Management-példány. Ha még nem rendelkezik ilyen, töltse ki az [oktatóanyag](get-started-create-service-instance.md) ot először.

## <a name="create-an-azure-application-insights-instance"></a>Azure Application Insights-példány létrehozása

Az Azure Application Insights használata előtt először létre kell hoznia a szolgáltatás egy példányát.

1. Nyissa meg az **Azure Portalt,** és keresse meg az Application Insights .open the Azure portal and navigate to **Application Insights**.  
    ![Az App Insights létrehozása](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Kattintson a **+Hozzáadás** gombra.  
    ![Az App Insights létrehozása](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Töltse ki az űrlapot. Válassza az **Általános** lehetőséget **alkalmazástípusként**.
4. Kattintson **a Létrehozás gombra.**

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Kapcsolat létrehozása az Azure Application Insights és az Azure API Management szolgáltatáspéldány között

1. Keresse meg az **Azure API Management szolgáltatáspéldányát** az **Azure Portalon.**
2. Válassza ki az **Application Insights** a bal oldali menüben.
3. Kattintson a **+Hozzáadás** gombra.  
    ![Az App Insights naplózója](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Válassza ki a korábban létrehozott Application Insights-példányt, és adjon meg egy rövid leírást. **Application Insights**
5. Kattintson **a Létrehozás gombra.**
6. Nemrég létrehozott egy Azure Application Insights-naplózót egy instrumentation kulccsal. Most meg kell jelennie a listában.  
    ![Az App Insights naplózója](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> A jelenet mögött egy [logger](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger/createorupdate) entitás jön létre az API Management-példányban, amely az Application Insights-példány Instrumentation kulcsa.

## <a name="enable-application-insights-logging-for-your-api"></a>Az Application Insights-naplózás engedélyezése az API-hoz

1. Keresse meg az **Azure API Management szolgáltatáspéldányát** az **Azure Portalon.**
2. A bal oldali menüből válassza ki az **API-k** elemet.
3. Kattintson az API-ra, ebben az esetben **a Demo Conference API-ra.**
4. Lépjen a felső sáv **Beállítások** fülére.
5. Görgessen le a **Diagnosztikai naplók** szakaszhoz.  
    ![Az App Insights naplózója](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Jelölje be az **Engedélyezés** jelölőnégyzetet.
7. Válassza ki a csatolt naplózót a **Cél** legördülő menüben.
8. Adjon meg **100-at** **mintavételként (%)** és jelölje be a **Mindig naplózza** a hibákat jelölőnégyzetet.
9. Kattintson a **Mentés** gombra.

> [!WARNING]
> A **testmező első bájtjában** az alapértelmezett **0** érték felülbírálása jelentősen csökkentheti az API-k teljesítményét.

> [!NOTE]
> A jelenet mögött egy "applicationinsights" nevű [diagnosztikai](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate) entitás jön létre az API szintjén.

| Beállítás neve                        | Érték típusa                        | Leírás                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bekapcsolás                              | logikai                           | Itt adható meg, hogy az API naplózása engedélyezve van-e.                                                                                                                                                                                                                                                                                                |
| Cél                         | Azure Application Insights-naplózó | Megadja az Azure Application Insights-naplózót, amelyet használni kell                                                                                                                                                                                                                                                                                           |
| Mintavétel (%)                        | tizedes tört                           | 0 és 100 (százalék) értékig. <br/> Itt adható meg, hogy a kérelmek hány százaléka kerül-e az Azure Application Insights ba. A 0%-os mintavétel azt jelenti, hogy nulla kérés van naplózva, míg a 100%-os mintavételaz összes kérelem naplózott. <br/> Ez a beállítás az Azure Application Insights naplózási kérelmek naplózási kérelmeinek teljesítménybeli következményeinek csökkentésére szolgál (lásd az alábbi szakaszt). |
| Mindig naplózza a hibákat                   | logikai                           | Ha ez a beállítás van kiválasztva, az összes hiba lesz naplózva az Azure Application Insights, függetlenül a **mintavételi** beállítás.                                                                                                                                                                                                                  |
| Alapbeállítások: Fejlécek              | lista                              | Megadja azokat a fejléceket, amelyeket az Azure Application Insights kérésekre és válaszokra naplózni fog.  Alapértelmezett: a rendszer nem naplóz fejléceket.                                                                                                                                                                                                             |
| Alapvető beállítások: A törzs első bájtjai  | egész szám                           | Itt adható meg, hogy a törzs első bájtjai hány bájtot naplózzanak az Azure Application Insights kérésekhez és válaszokhoz.  Alapértelmezett: a törzs nincs naplózva.                                                                                                                                                                                                    |
| Speciális beállítások: Részletesség         |                                   | A részletességi szintet adja meg. A rendszer csak a magasabb súlyossági szinttel rendelkező egyéni nyomkövetéseket naplózza. Alapértelmezett: Információ.                                                                                                                                                                                                                               |
| Speciális beállítások: Előtér-kérelem  |                                   | Itt adható meg, hogy az *előtér-kérelmek* naplózása az Azure Application Insightsban legyen-e, és ha annak módját. *Az előtér-kérelem* az Azure API Management szolgáltatásba érkező kérelem.                                                                                                                                                                        |
| Speciális beállítások: Előtér-válasz |                                   | Itt adható meg, hogy a rendszer naplózza-e *az előtér-válaszokat* az Azure Application Insightsba, és ha az okat. *Frontend válasz* egy válasz kimenő az Azure API Management szolgáltatás.                                                                                                                                                                   |
| Speciális beállítások: Háttérrendszer-kérelem   |                                   | Itt adható meg, hogy a rendszer naplózza-e *a háttérkérelmeket* az Azure Application Insightsban, és ha annak módját. *Háttérrendszer-kérelem* egy kérelem kimenő az Azure API Management szolgáltatás.                                                                                                                                                                        |
| Speciális beállítások: Háttérrendszer reválasza  |                                   | Itt adható meg, hogy a rendszer naplózza-e *a háttérválaszokat* az Azure Application Insightsba, és ha annak módját. *Háttér-válasz* az Azure API Management szolgáltatás bejövő válasz.                                                                                                                                                                       |

> [!NOTE]
> A naplózókat különböző szinteken adhatja meg – egyetlen API-naplózót vagy az összes API-hoz naplózót.
>  
> Ha mindkettőt megadja:
> + ha ezek különböző favágók, akkor mindkettőt használják (multiplex naplók),
> + ha ugyanazok a naplózók, de különböző beállításokkal rendelkeznek, akkor az egyetlen API-hoz (részletesebb szint) az összes API-hoz való beállítást felülírja.

## <a name="what-data-is-added-to-azure-application-insights"></a>Milyen adatok kerülnek az Azure Application Insightsba

Az Azure Application Insights a következőket kapja:

+ *Telemetriai* elem kérése minden bejövő kérelemhez (*előtér-kérelem,* *előtér-válasz*),
+ *Függőségi* telemetriai elem, minden háttérszolgáltatásnak továbbított kérelemhez (*háttérrendszer-kérelem*, *háttérválasz*),
+ *Kivétel* telemetriai elem, minden sikertelen kéréshez.

A sikertelen kérelem olyan kérés, amely:

+ zárt ügyfélkapcsolat miatt nem sikerült, vagy
+ aktiválta *az API-szabályzatok hiba közbeni* szakaszát, vagy
+ van egy válasz HTTP állapotkód megfelelő 4xx vagy 5xx.

## <a name="performance-implications-and-log-sampling"></a>Teljesítményvonzatés naplómintavétel

> [!WARNING]
> Az összes esemény naplózása komoly teljesítménybeli következményekkel járhat a bejövő kérelmek arányától függően.

A belső terheléstesztek alapján a funkció engedélyezése 40–50%-kal csökkentette az átviteli sebességet, ha a kérelmek száma meghaladta az 1000 kérést másodpercenként. Az Azure Application Insights statisztikai elemzéseket használ az alkalmazások teljesítményének értékeléséhez. Nem tekinthető naplózási rendszernek, és nem alkalmas a nagy mennyiségű API-k minden egyes kérésének naplózására.

A **mintavételi** beállítás módosításával módosíthatja a naplózott kérelmek számát (lásd a fenti lépéseket). A 100%-os érték azt jelenti, hogy a rendszer minden kérést naplóz, míg a 0% egyáltalán nem tükröz naplózást. **A mintavételezés** segít csökkenteni a telemetriai adatok mennyiségét, hatékonyan megakadályozva a jelentős teljesítménycsökkenést, miközben továbbra is a naplózás előnyeit hordozza.

A fejlécek naplózásának, valamint a kérelmek és válaszok törzsének kihagyása szintén pozitív hatással lesz a teljesítményproblémák enyhítésére.

## <a name="video"></a>Videó

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>További lépések

+ További információ az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/)ról.
+ Fontolja meg [az Azure Event Hubs szolgáltatással való naplózást.](api-management-howto-log-event-hubs.md)
