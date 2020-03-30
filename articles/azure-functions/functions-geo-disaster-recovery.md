---
title: Az Azure Functions geokatasztrófa-helyreállításés magas rendelkezésre állás
description: Földrajzi régiók használata redundancia és feladatátvétel az Azure Functionsben.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080232"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Az Azure Functions geokatasztrófa-helyreállítása

Ha teljes Azure-régiók vagy adatközpontok leállást tapasztalnak, a számítási folyamat egy másik régióban történő feldolgozás folytatása érdekében elengedhetetlen.  Ez a cikk ismerteti néhány, a stratégiák, amelyek segítségével a funkciók üzembe helyezését, hogy a vész-helyreállítási.

## <a name="basic-concepts"></a>Alapfogalmak

Az Azure Functions egy adott régióban fut.  A magasabb rendelkezésre állás érdekében ugyanazokat a függvényeket több régióra is telepítheti.  Ha több régióban lehet, hogy a függvények fut az *aktív /aktív* minta vagy *aktív/passzív* minta.  

* Aktív/aktív. Mindkét régió aktív és eseményekfogadása (ismétlődő vagy rotációs). Aktív/aktív ajánlott HTTPS-függvények az Azure bejárati ajtajával együtt.
* Aktív/passzív. Az egyik régió aktív és események fogadása, míg a másodlagos tétlen.  Ha feladatátvételre van szükség, a másodlagos régió aktiválódik, és átveszi a feldolgozást.  Ez nem HTTP-funkciók, például a Service Bus és az Event Hubs esetén ajánlott.

Olvassa el, hogyan [futtathat alkalmazásokat több régióban](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) a több régióra kiterjedő telepítésekkel kapcsolatos további információkért.

## <a name="activeactive-for-https-functions"></a>Aktív/aktív HTTPS-függvényekhez

A függvények aktív/aktív üzembe helyezéséhez olyan összetevőre van szükség, amely képes koordinálni az eseményeket a két régió között.  A HTTPS-függvények esetében ez a koordináció az [Azure Bejárati ajtajával](../frontdoor/front-door-overview.md)történik.  Az Azure Front Door több regionális függvény között is irányíthat és ciklikus multiplexeléses HTTPS-kérelmeket irányíthat.  Emellett rendszeresen ellenőrzi az egyes végpontok állapotát.  Ha egy regionális függvény nem válaszol az állapot-ellenőrzésekre, az Azure Front Door kiveszi a rotációból, és csak a forgalmat továbbítja a kifogástalan állapotú függvények.  

![Architektúra az Azure bejárati ajtajához és funkciójához](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Aktív/aktív nem HTTPS-függvényekhez

Továbbra is elérheti az aktív/aktív központi telepítések nem HTTPS-függvények.  Meg kell azonban vizsgálnia, hogy a két régió hogyan fog együttműködni vagy egyeztetni egymással.  Ha ugyanazt a függvényalkalmazást két régióra telepítette, amelyek mindegyike ugyanazon a Service Bus-várólistán aktiválódik, akkor a várólistán való várólistán kívül helyezésekor versengő fogyasztókként működnek.  Bár ez azt jelenti, hogy minden egyes üzenetet csak az egyik példány dolgoz fel, ez azt is jelenti, hogy még mindig egyetlen hibapont van az egyetlen Service Bus-on.  Ha két Service Bus-várólistát telepít (egyet egy elsődleges régióban, egyet egy másodlagos régióban), és a két függvényalkalmazás a régióvárólistájukra mutat, a kihívás most a várólista-üzenetek két régió közötti elosztásának módjában jelentkezik.  Ez gyakran azt jelenti, hogy minden közzétevő megkísérli közzétenni az üzenetet *mindkét* régióban, és minden üzenetet mindkét aktív függvényalkalmazás feldolgoz.  Bár ez létrehoz egy aktív/aktív mintát, más kihívásokat hoz létre a számítási adatok megkettőzése és az adatok összevonásának időpontjával és módjával kapcsolatban.  Ezen okok miatt ajánlott a nem HTTPS-eseményindítók az aktív/passzív minta használata.

## <a name="activepassive-for-non-https-functions"></a>Aktív/passzív nem HTTPS-függvényekhez

Az aktív/passzív csak egyetlen függvény számára biztosítja az egyes üzenetek feldolgozását, de egy mechanizmust biztosít egy másodlagos régióba katasztrófa esetén a feladatok átvételéhez.  Az Azure Functions az [Azure Service Bus geo-helyreállítási](../service-bus-messaging/service-bus-geo-dr.md) és [az Azure Event Hubs geo-helyreállítási](../event-hubs/event-hubs-geo-dr.md)funkcióival együtt működik.

Az Azure Event Hubs eseményindítók példaként, az aktív/passzív minta a következőket foglalja magában:

* Az Azure Event Hub egy elsődleges és másodlagos régióban egyaránt telepítve van.
* Geo-katasztrófa engedélyezve az elsődleges és másodlagos Event Hub párosítása.  Ez egy "aliast" is létrehoz, amelynek segítségével az eseményközpontokhoz csatlakozhat, és a kapcsolatadatainak módosítása nélkül válthat az elsődlegesről a másodlagosra.
* Az elsődleges és másodlagos régióban egyaránt telepített függvényalkalmazások.
* A függvényalkalmazások a megfelelő eseményközpont *közvetlen* (nem alias) kapcsolati karakterláncán aktiválódnak. 
* Az eseményközpont közzétevőinek közzé kell tenniük az alias kapcsolati karakterláncban. 

![Aktív-passzív példaarchitektúra](media/functions-geo-dr/active-passive.png)

A feladatátvétel előtt a megosztott aliasra küldő közzétevők az elsődleges eseményközpontba kerülnek.  Az elsődleges függvényalkalmazás kizárólag az elsődleges eseményközpontot figyeli.  A másodlagos függvényalkalmazás passzív és tétlen lesz.  Afeladat-átvétel megkezdéseután a megosztott aliasra küldő közzétevők most átirányítanak a másodlagos eseményközpontba.  A másodlagos függvényalkalmazás most aktívvá válik, és automatikusan elindul.  A másodlagos régió hatékony feladatátvétele teljes mértékben az eseményközpontból hajtható le, és a függvények csak akkor válnak aktívvá, ha az adott eseményközpont aktív.

További információ a [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) és az eseményközpontok feladatátvételével kapcsolatos információkról és [szempontokról.](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>További lépések

* [Az Azure bejárati ajtajának létrehozása](../frontdoor/quickstart-create-front-door.md)
* [Az Eseményközpontok feladatátvételi szempontjai](../event-hubs/event-hubs-geo-dr.md#considerations)
