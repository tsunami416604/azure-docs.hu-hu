---
title: Hogyan integrálható az Azure API Management az Azure Application insights segítségével |} A Microsoft Docs
description: Útmutató a bejelentkezéshez és az Azure API Management események megtekintése az Azure Application insights szolgáltatásban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 69f36773b702d9f0059e0cd27dbb864ccd7f7b2b
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262761"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Hogyan integrálható az Azure API Management az Azure Application insights segítségével

Könnyen integrálható az Azure Application Insights - egy bővíthető szolgáltatás webfejlesztőknek, több platformon működő alkalmazások létrehozását és kezelését az Azure API Management lehetővé teszi. Ez az útmutató végigvezeti azon, minden lépés ilyen az integráció, és az API Management szolgáltatáspéldányhoz a teljesítményre gyakorolt hatás csökkentésére vonatkozó stratégiákat ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató követéséhez szüksége lesz az Azure API Management-példány. Ha még nincs fiókja, hajtsa végre a [oktatóanyag](get-started-create-service-instance.md) első.

## <a name="create-an-azure-application-insights-instance"></a>Az Azure Application Insights-példány létrehozása

Az Azure Application Insights használata előtt először a szolgáltatás egy példányának létrehozásához.

1. Nyissa meg a **az Azure portal** , és keresse meg **Application Insights**.  
    ![Az App Insights létrehozása](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Kattintson a **+Hozzáadás** gombra.  
    ![Az App Insights létrehozása](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Töltse ki az űrlapot. Válassza ki **általános** , a **alkalmazástípus**.
4. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Az Azure Application Insights és az Azure API Management szolgáltatáspéldány létrehozása

1. Keresse meg a **Azure API Management-szolgáltatáspéldány** a a **az Azure portal**.
2. Válassza ki **Application Insights** a bal oldali menüből.
3. Kattintson a **+Hozzáadás** gombra.  
    ![App Insights naplózót](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Válassza ki a korábban létrehozott **Application Insights** példányra, és adjon meg egy rövid leírást.
5. Kattintson a **Create** (Létrehozás) gombra.
6. Az Azure Application Insights-naplózó csak egy kialakítási kulcsot hozott létre. Ekkor megjelenik a listában.  
    ![App Insights naplózót](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> A helyszín mögött egy [naplózó](https://docs.microsoft.com/rest/api/apimanagement/logger/createorupdate) entitás jön létre az API Management példánya, amely tartalmazza a kialakítási kulcsot az Application Insights-példány.

## <a name="enable-application-insights-logging-for-your-api"></a>Az API-hoz az Application Insights-naplózás engedélyezése

1. Keresse meg a **Azure API Management-szolgáltatáspéldány** a a **az Azure portal**.
2. A bal oldali menüből válassza ki az **API-k** elemet.
3. Ebben az esetben kattintson az API-t, a **Demo Conference API**.
4. Nyissa meg a **beállítások** a felső sávon fülre.
5. Görgessen le a **diagnosztikai naplók** szakaszban.  
    ![App Insights naplózót](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Ellenőrizze a **engedélyezése** mezőbe.
7. Válassza ki a csatolt naplózó a a **cél** legördülő listából.
8. Bemeneti **100** , **mintavételi (%)** osztásjelek és a **mindig hibák naplózása** jelölőnégyzetet.
9. Kattintson a **Save** (Mentés) gombra.

> [!WARNING]
> Az alapértelmezett érték felülírva **0** a a **szervezet első bájtok** mező jelentősen csökkentheti az API-k teljesítményét.

> [!NOTE]
> A helyszín mögött egy [diagnosztikai](https://docs.microsoft.com/rest/api/apimanagement/diagnostic/createorupdate) "applicationinsights" nevű entitás jön létre az API szintjén.

| Beállítás neve                        | Érték típusa                        | Leírás                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bekapcsolás                              | logikai                           | Meghatározza, hogy engedélyezett-e az API-t a naplózását.                                                                                                                                                                                                                                                                                                |
| Cél                         | Az Azure Application Insights-naplózót | Adja meg a használandó Azure Application Insights naplózót                                                                                                                                                                                                                                                                                           |
| Mintavételi (%)                        | tizedes tört                           | Értékek 0 és 100 (százalék). <br/> Itt adhatja meg, hány százaléka kéréseket az Azure Application Insights lesz naplózva. 0 %-os mintavételi azt jelenti, hogy a nulla kérelmeket naplózza, amíg a 100 %-os mintavételi azt jelenti, hogy naplózott összes kérelem. <br/> Ezzel a beállítással csökkenthető a teljesítményre gyakorolt hatása naplózási kérések az Azure Application Insights (lásd az alábbi szakaszt). |
| Mindig naplózni a hibákat                   | logikai                           | Ha ez a beállítás be van jelölve, az összes sikertelen lesz naplózva az Azure Application insights szolgáltatásba, függetlenül attól, hogy a **mintavételi** beállítás.                                                                                                                                                                                                                  |
| Alapszintű beállítások: Fejlécek              | lista                              | Meghatározza a fejléceket, amely a kérelmek és válaszok az Azure Application Insights lesz naplózva.  Alapértelmezés: nincs fejléceket a rendszer naplózza.                                                                                                                                                                                                             |
| Alapszintű beállítások: Szervezet első bájtok  | egész szám                           | Itt adhatja meg, hány első bájtok a szervezet Azure Application Insights naplózza a kérelmeket és válaszokat.  Alapértelmezett érték: a törzs nem naplózza.                                                                                                                                                                                              |
| Speciális beállítások: Előtér-kérelem  |                                   | Itt adhatja meg, hogyan *előtérbeli kérelmek* Azure Application insights lesz naplózva. *Előtérbeli kérelem* tulajdonképpen egy kérelem a bejövő és az Azure API Management szolgáltatást.                                                                                                                                                                        |
| Speciális beállítások: Előtér-válasz |                                   | Itt adhatja meg, hogyan *előtérbeli válaszok* az Azure Application Insights lesz naplózva. *Előtérbeli válasz* választ, az Azure API Management szolgáltatás kimenő.                                                                                                                                                                   |
| Speciális beállítások: Háttér-kérelem   |                                   | Itt adhatja meg, hogyan *háttérrendszer kérelmek* Azure Application insights lesz naplózva. *Háttér-kérelem* egy kérelmet, az Azure API Management szolgáltatás kimenő.                                                                                                                                                                        |
| Speciális beállítások: Háttér-válasz  |                                   | Itt adhatja meg, hogyan *háttérrendszer válaszait* az Azure Application Insights lesz naplózva. *Háttér-válasz* van az Azure API Management szolgáltatás a bejövő válasz.                                                                                                                                                                       |

> [!NOTE]
> Különböző szinteken – egyetlen API-naplózót vagy az összes API-k egy naplózó másolása is megadhat.
>  
> Ha mindkét adja meg:
> + Ha másik másolása, akkor mindkettő lesz használva (multiplexálási naplók),
> + azok az azonos másolása, de a különböző beállítások tartoznak, akkor egy egyetlen API-hoz (a részletes szint) egy, az összes API-k felülbírálják.

## <a name="what-data-is-added-to-azure-application-insights"></a>Milyen adatok kerülnek be az Azure Application Insights

Az Azure Application Insights megkapja:

+ *Kérelem* szereplő telemetriai elem, minden bejövő kérelem (*előtérbeli kérelem*, *előtérbeli válasz*),
+ *Függőségi* telemetriai elem háttérszolgáltatás továbbított minden egyes kérés esetében (*háttérrendszer kérelem*, *háttérrendszer válasz*),
+ *Kivétel* szereplő telemetriai elem, az összes sikertelen kérelem.

A sikertelen kérelmek tulajdonképpen egy kérelem, amely:

+ nem sikerült, mert lezárt ügyfélkapcsolat, vagy
+ aktivált egy *– hiba* szakaszában az API-házirendek vagy
+ válaszkód HTTP állapota: egyező 4xx vagy 5xx rendelkezik.

## <a name="performance-implications-and-log-sampling"></a>Teljesítményre gyakorolt hatása és naplófájl-mintavétel

> [!WARNING]
> Az összes esemény naplózása, előfordulhat, hogy komoly teljesítményre gyakorolt hatása, attól függően, bejövő kérelmek aránya.

Belső tesztek alapján, a funkció engedélyezése az okozza, 40 %-os – 50 %-os csökkenést átviteli sebesség kérelmek arányának túllépésekor 1000 vonatkozó kérelmek másodpercenkénti száma. Az Azure Application Insights készült alkalmazás előadások értékelésére statisztikai elemzésekhez használja. Ez nem célja, hogy egy naplózási a rendszer, és nem alkalmas a nagy mennyiségű API-k minden egyes kérés naplózás.

Fokozottabban naplózott kérések száma is módosíthatja a **mintavételi** beállítás (lásd a fenti lépéseket). Tulajdonság értéke 100 %-os azt jelenti, hogy az összes kérés naplózásra kerül, míg a 0 %-os tükrözi, hogy egyáltalán nincs naplózás. **Mintavételi** telemetriát, hatékonyan meggátolja, hogy jelentős teljesítménycsökkenéshez naplózásának előnyei továbbra is végrehajtása közben a kötet csökken.

A rendszer kihagyja a fejlécek és törzs kérelmek és válaszok naplózását is pozitív hatással lehet a teljesítménnyel kapcsolatos problémák enyhítése.

## <a name="video"></a>Videó

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>További lépések

+ Tudjon meg többet [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Érdemes lehet [az Azure Event Hubs-naplózás](api-management-howto-log-event-hubs.md).
