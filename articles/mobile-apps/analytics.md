---
title: A mobileszköz-használat és a felhasználói viselkedés megértése Visual Studio App Center és Azure-szolgáltatásokkal
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint a App Center, amelyek segítségével megtudhatja, hogyan használják a felhasználók a mobil alkalmazást.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795795"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>A Mobile Applications használatának elemzése és megértése
Mennyire jól érti, hogyan használják a felhasználók az alkalmazásaikat? Hány aktív felhasználó működik az alkalmazásban, és hogyan változik a használat az idő múlásával? Milyen funkciókat használnak, és melyeket használják leginkább? Hol vannak ezek a felhasználók? Hány felhasználó használja az alkalmazás legújabb verzióját? Ezek a kérdések fontosak ahhoz, hogy megértse az alkalmazás sikeres üzleti környezetbe való bekapcsolását. Ezeknek a használati elemzési kérdéseknek a megválaszolásához a használati adatokat össze kell gyűjteni az alkalmazásaiból.

Minél jobban megtekintheti az adatait, annál jobban megtalálhatja az alkalmazás fejlesztését, és megtarthatja a felhasználókat. Fontos, hogy a gyakorlatban hasznosítható elemzéseket tudjon használni, és a felhasználók boldogok maradjanak.

## <a name="importance-of-analytics"></a>Az elemzés fontossága
- **Ismerje** meg a felhasználókat, hogy miként működnek együtt az alkalmazással, és mi teszi őket vissza az alkalmazás finomhangolásához, és nagyszerű élményt nyújtson az üzleti növekedéshez.
- **Nyomon követheti** a használati metrikákat, hogy megalapozott döntéseket hozhasson az alkalmazás értékesítésével és az ügyfelek jobb kiszolgálásával kapcsolatban.
- **Mérje** fel az alkalmazás teljesítményét.
- **Megtudhatja** , hogy az alkalmazás mely részeit és teljesítményét adja meg.
- **Adatvezérelt elemzések az adatvesztéssel** és-megőrzéssel kapcsolatos problémákkal kapcsolatban.

A következő szolgáltatások segítségével engedélyezheti a Mobile Application Analytics szolgáltatást.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Analytics](/appcenter/analytics/) lehetővé teszi a célközönség növelését a fontos dolgokra összpontosítva, a felhasználói munkamenetek, a legnépszerűbb eszközök, az operációsrendszer-verziók és a viselkedési elemzések részletes jelentéskészítésével és elemzésével. Egyszerűen hozhat létre egyéni eseményeket, amelyek részletesen nyomon követhetik az alkalmazások elemzését.

   **Főbb funkciók**
   - **Nyomon követheti** a használati mintákat, a felhasználók bevezetését és az egyéb elkötelezettségi mérőszámokat ingyenesen.
   - **Azonosíthatja** a trendeket, a felhasználói viselkedést és az egyéni eseményeken keresztüli részvételt.
   - A beépített **mérőszámok** és **részletes** információk az alkalmazás használatáról (napi, heti, havi), munkamenetek, eszköz tulajdonságai és felhasználói demográfiai adatok **egyetlen irányítópulton**.
   - Az **összes Analytics-adatok folyamatos exportálása az Azure-ba** korlátlan megőrzés céljából. Támogatja az Azure Blob Storage és az Azure Application Insights exportálását.
   - **Integrálhatja az Azure Application Insightst** még mélyebb elemzésekhez, például a megőrzéshez, a tölcsérek elemzéséhez és a kohorszokhoz
   - Az **egysoros SDK-integráció** perceken belül elérhetővé teheti az első lépéseket.
   - **Platform-támogatás** – iOS, Android, MacOS, TvOS, Xamarin, inaktív, Unity, Cordova.

   **Hivatkozik**
   - [Regisztráció App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Ismerkedés a App Center Analytics szolgáltatással](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
A Azure Monitor [Application Insights](/azure/azure-monitor/app/app-insights-overview) , amely eszközöket biztosít a telemetria begyűjtésére és elemzésére a teljesítmény maximalizálása és a mobil alkalmazások figyelése érdekében. A Application Insights kihasználhatja App Center Analytics használatával, és beállíthatja az exportálást a Application Insightsre. Application Insights lekérdezheti, szegmentálhatja, szűrheti és elemezheti az alkalmazásokból származó egyéni Event telemetria, a App Center által biztosított elemzési eszközökön kívül.

**Főbb funkciók**
   - **Kérdezze** le az egyéni Event telemetria.
   - Az Event telemetria **szűrése** hatékony szegmentálási képességekkel.
   - **Elemezheti** az alkalmazás átalakítási, megőrzési és navigálási mintáit.
     - **Tölcsérek** – a konverziós arányok elemzéséhez és figyelemmel kíséréséhez.
     - **Megtartja annak** elemzését, hogy az alkalmazás mennyi időt vesz igénybe a felhasználók számára.
     - **Munkafüzetek** – képi megjelenítések és szöveg megosztható jelentésbe való egyesítéséhez.
     - **Kohorszok** – az adott felhasználó- vagy eseménycsoportok elnevezéséhez és mentéséhez, hogy könnyen lehessen rájuk hivatkozni más elemzési eszközökből.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com/)
- [A Mobile-alkalmazás elemzése App Center és Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Használati Analitika Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure-PlayFab
Az [Azure PlayFab](https://playfab.com/) egy teljes körű háttér-platformot kínál a game Services, a valós idejű elemzési és a LiveOps, amelyekkel világszínvonalú felhőalapú játékok hozhatók létre. Ezek a szolgáltatások csökkentik a játék-fejlesztők számára indított akadályokat, és a nagy és kis stúdiók számára is költséghatékony fejlesztési megoldásokat kínálnak, amelyek méretezhetők a játékaikkal, és segítenek a játékosok bevonásában, megőrzésében és pénzbevételében. A PlayFab lehetővé teszi, hogy a fejlesztők az intelligens felhőben játékokat hozzanak létre és működtessenek, elemezzék a játékok mennyiségét, és javítják a teljes játékélményt.

**Főbb funkciók**
   - Valós idejű irányítópultok **figyelése** .
   - **Értékelje** ki a játék teljesítményét a legfontosabb mérőszámok segítségével.
   - **Jelentés** a játék napi és havi teljesítményének összefoglalóinak áttekintéséről a game Managerben megtekinthető automatikusan generált jelentéseken keresztül, valamint a Beérkezett üzenetek napi letöltéséhez.
   - **A/B tesztelés** a kísérletek futtatásához és egy adott változó optimális beállításának meghatározásához.
   - A játékosok **szegmentálása** lehetővé teszi a játékosok automatizált csoportosításának meghatározását.
    
**Hivatkozik**
- [PlayFab-portál](https://developer.playfab.com/en-US/sign-up)
- [Elemzés](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Rövid útmutatók](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
