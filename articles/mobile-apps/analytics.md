---
title: A mobilalkalmazások használatának és felhasználói viselkedésének megértése a Visual Studio App Center és az Azure-szolgáltatások segítségével
description: Ismerje meg az olyan szolgáltatásokat, mint például az App Center, amely intelligens üzleti döntéseket segít, ha tisztában van azzal, hogy a felhasználók hogyan használják a mobilalkalmazást.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241081"
---
# <a name="analyze-and-understand-mobile-application-use"></a>A mobilalkalmazások használatának elemzése és megértése
Mennyire érti, hogy a felhasználók hogyan használják az alkalmazásokat? Hány aktív felhasználóval rendelkezik az alkalmazás, és hogyan változik a használat az idő múlásával? Milyen funkciókat használnak, és melyek et használják a leggyakrabban? Hol vannak ezek a felhasználók alapján? Hány felhasználó használja az alkalmazás legújabb verzióját? Mindezek a kérdések fontos megérteni, hogy kapcsolja be az alkalmazást egy sikeres üzlet. Az ilyen típusú használatelemzési kérdések megválaszolásához használati adatokat kell gyűjtenie az alkalmazásokból.

Minél jobban lenéz az adatokra, annál inkább megtalálhatja az alkalmazás fejlesztésének és a felhasználók boldogságának megőrzésének módjait. Fontos, hogy az adatok segítségével találhatja meg a használható elemzéseket, és tartsa a felhasználók at elégedett.

## <a name="importance-of-analytics"></a>Az analitika fontossága
- Ismerje meg a felhasználókat, hogyan használják az alkalmazást, és mi hozza őket vissza az alkalmazás finomhangolására, és nagyszerű élményt nyújt a vállalkozás növekedéséhez.
- Kövesse nyomon a használati mutatókat, hogy megalapozott döntéseket hozhasson az alkalmazás forgalmazásáról és az ügyfelek jobb kiszolgálásáról.
- Mérje meg az alkalmazás teljesítményét.
- Ismerje meg, hogy az alkalmazás meghajtójának mely részei és teljesítménye.
- Adatalapú betekintést nyerhet a lemorzsolódást és megőrzést érintő problémákba.

A mobilalkalmazás-elemzés engedélyezéséhez használja az alábbi szolgáltatásokat.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Az App Center Analytics](/appcenter/analytics/) lehetővé teszi, hogy a fontos dolgokra összpontosítva növelje közönségét. Mély jelentéskészítést és elemzési adatokat kínál a felhasználói munkamenetekről, a legnépszerűbb eszközökről, az operációs rendszer verzióiról és a viselkedéselemzésről. Egyszerűen hozhat létre egyéni eseményeket, hogy bármit nyomon követhessen kiterjedt alkalmazáselemzéssel.

   **A legfontosabb jellemzők**
   - Kövesse nyomon a használati mintákat, a felhasználók elfogadását és más elköteleződési mutatókat ingyenesen.
   - Azonosítsa a trendeket, a felhasználói viselkedést és az egyéni eseményeken keresztüli elköteleződést.
   - A részletes metrikák és az alkalmazáshasználat (napi, heti, havi), munkamenetek, eszköztulajdonságok és felhasználói demográfiai adatok egyetlen irányítópulton történő részletes elemzése.
   - A korlátlan adatmegőrzéshez folyamatosan exportálhatja az Összes App Center Analytics-adatot az Azure-ba. Az App Center Analytics támogatja az Azure Blob storage és az Azure Application Insights exportálását.
   - Az Azure Application Insights szolgáltatással még mélyebb elemzéseket, például megőrzési, csatornaelemzést és kohorszokat is integrálhat.
   - Perceken belül egysoros SDK-integrációval kezdheti el a kezdést.
   - Platformtámogatás az iOS, Android, macOS, tvOS, Xamarin, React Native, Unity és Cordova rendszerhez.

   **Referencia**
   - [Regisztráció az App Center alkalmazásközpontjában](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [Az App Center Analytics – első lépések](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Az Azure Monitor tartalmazza [az Application Insights](/azure/azure-monitor/app/app-insights-overview)szolgáltatást, amely a telemetriai adatok gyűjtésére és elemzésére szolgáló eszközöket biztosít a teljesítmény maximalizálása és a mobilalkalmazás figyelése érdekében. Az Application Insights előnyeit az App Center Analytics segítségével állíthatja be az Application Insights exportálásának beállításához. Az Application Insights lekérdezheti, szegmentálhatja, szűrheti és elemezheti az alkalmazások egyéni eseménytelemetriai adatait az App Center által biztosított elemzési eszközökön túl.

**A legfontosabb jellemzők**
   - Kérdezze le az egyéniesemény-telemetriát.
   - Hatékony szegmentálási képességekkel szűrheti az eseménytelemetriai adatokat.
   - Elemezze a konverziós, megőrzési és navigációs mintákat az alkalmazásban. Az alábbi eszközöket használhatja:
     - Csatornák a konverziós arányok elemzéséhez és nyomon követéséhez.
     - Megőrzési annak elemzéséhez, hogy az alkalmazás mennyire tartja meg a felhasználókat az idő múlásával.
     - Munkafüzetek a vizualizációk és a szöveg megosztható jelentéssé történő egyesítéséhez.
     - Kohorszok, hogy nevezzenek el és mentsenek bizonyos felhasználói csoportokat vagy eseményeket, hogy könnyen hivatkozhassanak más elemzési eszközökből.

**Referencia**
- [Azure-portál](https://portal.azure.com/)
- [A mobilalkalmazás elemzése az App Center rel és az Application Insights segítségével](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Az App Center-elemzés használata az Application Insights szolgáltatással](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Az Azure PlayFab](https://playfab.com/) teljes háttérplatformot kínál játékszolgáltatásokkal, valós idejű elemzésekkel és LiveOps-szal, amelyekre világszínvonalú, felhőhöz kapcsolódó játékokat hozhat létre. Ezek a szolgáltatások csökkentik a játékfejlesztők számára indított akadályokat. Kínálnak mind a nagy és kis stúdiók költséghatékony fejlesztési megoldások, amelyek a skála a játékokat. A szolgáltatások segítségével stúdiók vegyenek részt, megtartása, és pénzt keresni a játékosok. A PlayFab segítségével a fejlesztők az intelligens felhő segítségével játékokat hozhatnak létre és működtethetnek, elemezhetik a játékadatokat, és javíthatják az általános játékélményt.

**A legfontosabb jellemzők**
   - Valós idejű irányítópultok figyelése.
   - A játék teljesítményének értékelése a legnépszerűbb mutatók kal.
   - Tekintse át a játék napi és havi teljesítményének összefoglalóit az automatikusan generált jelentéseken keresztül. A jelentéseket megtekintheti a Game Manager alkalmazásban, és naponta letöltheti vagy kézbesítheti a beérkezett üzenetek mappájába.
   - Az A/B tesztelés sel kísérletezéseket futtathat, és meghatározhatja egy adott változó optimális beállítását.
   - Használja szegmentálás a játékosok, hogy meghatározza az automatizált csoportosítása játékosok.
    
**Referencia**
- [PlayFab portál](https://developer.playfab.com/en-US/sign-up)
- [Elemzés](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Rövid útmutatók](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
