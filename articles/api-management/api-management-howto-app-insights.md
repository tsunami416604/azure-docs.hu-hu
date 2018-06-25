---
title: Azure API Management integrálása Azure Application Insights |} Microsoft Docs
description: Útmutató a naplózása, és tekintse meg az eseményeket az Azure API Management Azure Application insights szolgáltatással.
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
ms.openlocfilehash: 7740da505f7635944536252d60ec2c2039295975
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320582"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure API Management integrálása az Azure Application insights szolgáltatással

Az Azure API Management lehetővé teszi, hogy az egyszerű integráció az Azure Application Insights - bővíthető szolgáltatásnak webfejlesztőknek felépítését és kezelését az alkalmazások különböző platformokon. Ez az útmutató végigvezeti az ilyen integráció minden lépés, és csökkenti a teljesítményre gyakorolt hatás az API Management szolgáltatáspéldányon kapcsolatos olyan stratégiák ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Kövesse a jelen útmutató, meg kell rendelkeznie az Azure API Management-példány. Ha még nincs fiókja, hajtsa végre a [oktatóanyag](get-started-create-service-instance.md) első.

## <a name="create-an-azure-application-insights-instance"></a>Hozzon létre egy Azure Application Insights-példányt

Azure Application Insights használata előtt először a szolgáltatás egy példányának létrehozásakor.

1. Nyissa meg a **Azure-portálon** , és keresse meg **Application Insights**.  
    ![App Insights létrehozása](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Kattintson a **+Hozzáadás** gombra.  
    ![App Insights létrehozása](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Töltse ki az űrlapot. Válassza ki **általános** , a **alkalmazástípus**.
4. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Kapcsolat létrehozása az Azure Application insights szolgáltatással és az Azure API Management service-példány között

1. Keresse meg a **Azure API Management service-példány** a a **Azure-portálon**.
2. Válassza ki **Application Insights** a bal oldali menüből.
3. Kattintson a **+Hozzáadás** gombra.  
    ![App Insights naplózó](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Válassza ki a korábban létrehozott **Application Insights** példányt, és adjon meg egy rövid leírást.
5. Kattintson a **Create** (Létrehozás) gombra.
6. Egy Azure Application Insights-naplózó instrumentation kulccsal rendelkező előbb létrehozott. Az ekkor meg kell jelennie a listában.  
    ![App Insights naplózó](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

## <a name="enable-application-insights-logging-for-your-api"></a>Az API-Application Insights-naplózás engedélyezése

1. Keresse meg a **Azure API Management service-példány** a a **Azure-portálon**.
2. Válassza ki **API-k** a bal oldali menüből.
3. Ebben az esetben kattintson az API a **bemutató konferencia API**.
4. Lépjen a **beállítások** a felső sávon a lapon.
5. Görgessen le a **diagnosztikai naplók** szakasz.  
    ![App Insights naplózó](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Ellenőrizze a **engedélyezése** mezőbe.
7. Válassza ki a csatolt naplózó a a **cél** legördülő menüből.
8. Bemeneti **100** , **mintavételi (%)** osztásjelek és a **mindig hibák naplózása** jelölőnégyzetet.
9. Bemeneti **1024** a a **szervezet első bájt** mező.
10. Kattintson a **Save** (Mentés) gombra.

| Beállítás neve                        | Érték típusa                        | Leírás                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bekapcsolás                              | logikai                           | Határozza meg, hogy engedélyezve van-e ez az API naplózását.                                                                                                                                                                                                                                                                                                |
| Cél                         | Az Azure Application Insights-naplózó | Megadja a használandó Azure Application Insights-naplózó                                                                                                                                                                                                                                                                                           |
| A mintavételi (%)                        | Decimális                           | Értékek 0 és 100 (százalék). <br/> Itt adhatja meg, hány százaléka kérelmek Azure Application insights lesz naplózva. 0 % mintavételi azt jelenti, hogy a nulla kérelmeket naplózza, amíg a 100 %-os mintavételi azt jelenti, hogy az összes kérelmet a rendszer naplózza. <br/> Ezzel a beállítással a teljesítményre gyakorolt hatása naplózási kérelmek Azure Application insights csökkentésére (lásd a következő szakaszban). |
| Mindig hibák naplózása                   | logikai                           | Ha ezt a beállítást választja, az összes sikertelen lesz naplózva Azure Application insights részére, függetlenül attól, hogy a **mintavételi** beállítást.                                                                                                                                                                                                                  |
| Alapvető beállítások: fejlécek              | lista                              | Adja meg a fejlécek kerül a naplóba Azure Application insights kérelmeit és válaszait.  Alapértelmezés: nincs fejlécek naplózza.                                                                                                                                                                                                             |
| Alapvető beállítások: Szervezet első bájtja  | egész szám                           | Itt adhatja meg, hány első bájt a szervezet bekerülnek a naplóba Azure Application insights kérelmeit és válaszait.  Alapértelmezett: a törzs nem naplózza.                                                                                                                                                                                              |
| Speciális beállítások: Előtér-kérés  |                                   | Meghatározza, hogyan *előtér kérelmek* Azure Application insights lesz naplózva. *Előtérbeli kérelem* tulajdonképpen egy kérelem bejövő az Azure API Management szolgáltatáshoz.                                                                                                                                                                        |
| Speciális beállítások: Előtér-válasz |                                   | Meghatározza, hogyan *előtér válaszok* Azure Application insights lesz naplózva. *Előtérbeli válasz* választ, az Azure API Management szolgáltatás kimenő.                                                                                                                                                                   |
| Speciális beállítások: Háttér kérelem   |                                   | Meghatározza, hogyan *háttér kérelmek* Azure Application insights lesz naplózva. *Háttér-kérelem* kérelmet, az Azure API Management szolgáltatás kimenő.                                                                                                                                                                        |
| Speciális beállítások: Háttér-válasz  |                                   | Meghatározza, hogyan *háttér válaszok* Azure Application insights lesz naplózva. *Háttér-válasz* van a válasz bejövő az Azure API Management szolgáltatáshoz.                                                                                                                                                                       |

> [!NOTE]
> Különböző szinteken - egyetlen API-naplózó vagy egy naplózó minden API figyelő szoftverek is megadhat.
>  
> Ha mindkét adható meg:
> + Ha különböző figyelő szoftverek, akkor mindkettő használja a rendszer (multiplexáló naplók),
> + Ha a ugyanazon figyelő szoftverek, de a különböző beállítások tartoznak, majd egy, az egyetlen API-val (részletesebb felügyeletét) felülbírál egy, az összes API-k.

## <a name="what-data-is-added-to-azure-application-insights"></a>Milyen adatok kerülnek Azure Application insights szolgáltatással

Az Azure Application Insights kap:

+ *Kérelem* telemetriai eleme, a bejövő kérelmeket (*előtér kérelem*, *előtér válasz*),
+ *Függőség* telemetriai elem által a háttérszolgáltatáshoz továbbított összes kérés (*háttér kérelem*, *háttér válasz*),
+ *Kivétel* telemetriai eleme, a sikertelen kérelmek.

A sikertelen kérelmek a kérelmet, amely:

+ nem sikerült, mert lezárt ügyfélkapcsolat, vagy
+ elindul egy *hiba* az API-házirendek szakasza vagy
+ válasz HTTP állapot kód egyező 4xx vagy 5xx rendelkezik.

## <a name="performance-implications-and-log-sampling"></a>Teljesítményre gyakorolt hatása és napló mintavétel

> [!WARNING]
> Események naplózása, előfordulhat, hogy komoly teljesítményre gyakorolt hatása, attól függően, hogy a bejövő kérelmek aránya.

Belső tesztek alapján, a funkció engedélyezése, hogy 40 % - 50 %-os csökkenést átviteli sebességének amikor kérelmek aránya meghaladja a 1000 kérések száma másodpercenként. Az Azure Application Insights célja, hogy statisztikai elemzések használja az alkalmazás teljesítményének értékeléséhez. Egy naplózási a rendszer nem javasolt, és nem megfelelő a naplózás minden egyes kérelem nagy mennyiségű API-k esetében.

Kezelheti az beállításával naplózott kérelmek száma a **mintavételi** beállítás (lásd a fenti lépések). Értéke 100 %-os azt jelenti, hogy minden kérések naplózása, amíg 0 % tükrözi egyáltalán nem naplózása. **A mintavételi** csökken telemetriai adatot, hatékonyan akadályozza meg, hogy jelentős teljesítménycsökkenés, a naplózási előnyei továbbra is végrehajtása közben.

Fejlécek és a kérések és válaszok naplózása kihagyása teljesítményproblémák enyhítése pozitív hatása is rendelkezik majd.

## <a name="next-steps"></a>További lépések

+ További információ [Azure Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/).
+ Érdemes lehet [az Azure Event Hubs-naplózás](api-management-howto-log-event-hubs.md).
