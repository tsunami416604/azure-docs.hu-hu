---
title: A mobileszköz-használat és a felhasználói viselkedés megértése Visual Studio App Center és Azure-szolgáltatásokkal
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint a App Center, amelyek segítségével megtudhatja, hogyan használják a felhasználók a mobil alkalmazást.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ebe07ec76e5b852dbe7d030ad8859d59ce5cd074
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451059"
---
# <a name="analyze-and-understand-mobile-application-use"></a>A mobil alkalmazások használatának elemzése és értelmezése
Mennyire jól érti, hogyan használják a felhasználók az alkalmazásaikat? Hány aktív felhasználó működik az alkalmazásban, és hogyan változik a használat az idő múlásával? Milyen funkciókat használnak, és melyeket használják leginkább? Hol vannak ezek a felhasználók? Hány felhasználó használja az alkalmazás legújabb verzióját? Ezek a kérdések fontosak ahhoz, hogy megértse az alkalmazás sikeres üzleti környezetbe való bekapcsolását. Ezeknek a használati elemzési kérdéseknek a megválaszolásához a használati adatokat össze kell gyűjteni az alkalmazásaiból.

Minél jobban megtekintheti az adatait, annál több lehetőség van az alkalmazások fejlesztésére és a felhasználók megtartására. Fontos, hogy a gyakorlatban hasznosítható elemzéseket és a felhasználók elégedettségét is megkeresse.

## <a name="importance-of-analytics"></a>Az elemzés fontossága
- Ismerje meg a felhasználókat, hogy miként működnek együtt az alkalmazással, és mi teszi őket vissza az alkalmazás finomhangolásához, és nagyszerű élményt nyújtson az üzleti növekedéshez.
- Nyomon követheti a használati metrikákat, hogy megalapozott döntéseket hozhasson az alkalmazások értékesítésével és az ügyfelek jobb kiszolgálásával kapcsolatban.
- Mérje fel az alkalmazás teljesítményét.
- Megtudhatja, hogy az alkalmazás meghajtójának mely részeit és teljesítményét kell megismernie.
- Adatvezérelt betekintést nyerhet az adatvesztést és adatmegőrzést érintő problémákba.

A következő szolgáltatások segítségével engedélyezheti a Mobile Application Analytics szolgáltatást.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Analytics](/appcenter/analytics/) lehetővé teszi a célközönség növelését a fontos dolgokra összpontosítva. Részletes jelentéskészítési és elemzési információkat nyújt a felhasználói munkamenetekről, a legnépszerűbb eszközökről, az operációsrendszer-verziókról és a viselkedési elemzésekről. Egyszerűen hozhat létre egyéni eseményeket, amelyek részletesen nyomon követhetik az alkalmazások elemzését.

   **A legfontosabb jellemzők**
   - Nyomon követheti a használati mintákat, a felhasználók bevezetését és az egyéb elkötelezettségi mérőszámokat ingyenesen.
   - Azonosíthatja a trendeket, a felhasználói viselkedést és az egyéni eseményeken keresztüli részvételt.
   - Az alkalmazás-használat (naponta, hetente, havonta), a munkamenetek, az eszközök tulajdonságai és a felhasználói demográfiai adatok beszerzése egyetlen irányítópulton.
   - A App Center Analytics-adatok folyamatos exportálása az Azure-ba korlátlan megőrzés céljából. App Center Analytics támogatja az Azure Blob Storage-ba és az Azure Application Insightsba való exportálást.
   - Integrálhatja az Azure Application Insightst még mélyebb elemzésekhez, például a megőrzéshez, a tölcsérek elemzéséhez és a kohorszokhoz.
   - Percek alatt elsajátíthatja az egysoros SDK-integrációt.
   - Az iOS, az Android, a macOS, a tvOS, a Xamarin, a Native, az Unity és a Cordova platform támogatását veheti igénybe.

   **Referencia**
   - [Regisztráció App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Ismerkedés a App Center Analytics szolgáltatással](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
A Azure Monitor [Application Insights](/azure/azure-monitor/app/app-insights-overview)is tartalmaz, amely eszközöket biztosít a telemetria gyűjtéséhez és elemzéséhez a teljesítmény maximalizálása és a mobil alkalmazások figyelése érdekében. A App Center Analytics használatával kihasználhatja a Application Insights előnyeit a Application Insightsre való exportálás beállításához. Application Insights lekérdezheti, szegmentálhatja, szűrheti és elemezheti az alkalmazásokból származó egyéni Event telemetria, a App Center által biztosított elemzési eszközökön kívül is.

**A legfontosabb jellemzők**
   - Kérdezze le az egyéniesemény-telemetriát.
   - Az Event telemetria szűrése hatékony szegmentálási képességekkel.
   - Elemezheti az alkalmazás átalakítási, megőrzési és navigálási mintáit. Az alábbi eszközöket használhatja:
     - Tölcsérek a konverziós arányok elemzéséhez és figyeléséhez.
     - Megtarthatja, hogy az alkalmazás mennyi időt vesz igénybe a felhasználók számára.
     - A vizualizációkat és a szöveget egy megosztható jelentésbe egyesítő munkafüzetek.
     - A kohorszok segítségével a felhasználók vagy események adott csoportjait nevezheti és mentheti, így könnyen hivatkozhat más elemzési eszközökről.

**Referencia**
- [Azure Portal](https://portal.azure.com/)
- [A Mobile-alkalmazás elemzése App Center és Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [App Center Analytics használata a Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
Az [Azure PlayFab](https://playfab.com/) teljes körű háttér-platformot kínál a game Services, a valós idejű elemzési és a LiveOps, amelyekkel világszínvonalú felhőalapú játékok hozhatók létre. Ezek a szolgáltatások csökkentik a játék fejlesztői számára indított akadályokat. A nagyméretű és a kis stúdiók költséghatékony fejlesztési megoldásokat kínálnak, amelyek méretezhetők a játékokkal. A szolgáltatások segítséget nyújtanak a stúdióknak a játékosok bevonásában, megtartásában és értékesítésében. A PlayFab segítségével a fejlesztők az intelligens felhőben játékokat hozhatnak létre és kezelhetnek, elemezheti a játékok adataikat, és javíthatja az általános játékélményt.

**A legfontosabb jellemzők**
   - Valós idejű irányítópultok figyelése.
   - Értékelje ki a játék teljesítményét a legfontosabb mérőszámok segítségével.
   - A játék napi és havi teljesítményének összefoglalóit az automatikusan generált jelentésekben tekintheti át. A jelentéseket a game Managerben tekintheti meg, és naponta letöltheti vagy leküldheti azokat.
   - A/B tesztelés használatával kísérleteket futtathat, és meghatározhatja egy adott változó optimális beállítását.
   - A játékosok szegmentálása a játékosok automatizált csoportosításának definiálásához.
    
**Referencia**
- [PlayFab-portál](https://developer.playfab.com/en-US/sign-up)
- [Elemzés](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Rövid útmutatók](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
