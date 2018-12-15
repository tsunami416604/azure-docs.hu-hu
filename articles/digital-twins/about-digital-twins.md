---
title: Az Azure Digital Twins áttekintése | Microsoft Docs
description: További információ a térbeli intelligenciát megvalósító Azure IoT-megoldásról, az Azure Digital Twinsről.
author: julieseto
ms.author: jseto
ms.date: 12/14/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 2848f9ce97c2bdad59d86031c5894219875b6059
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437112"
---
# <a name="overview-of-azure-digital-twins"></a>Az Azure Digital Twins áttekintése

Az Azure Digital Twins egy Azure IoT-szolgáltatás, amellyel a fizikai környezet átfogó modelljei hozhatók létre. Térbeli üzletiintelligencia-grafikonokkal modell, kapcsolatokat és a személyek, szóközöket és eszközök közötti interakciókat tud létrehozni.

Az Azure digitális Twins lekérdezheti, ha egy fizikai helyet próbálják számos különböző érzékelőktől származó adatok. A szolgáltatás segít a streamelési adatok digitális és a fizikai világszerte hivatkozó újrafelhasználható, rugalmasan méretezhető, kijelölése figyelembe felhasználói környezetek készíthetők. Az alkalmazások egyedi vonatkozó környezetfüggő funkciókhoz tovább növeli. Az Azure digitális Twins is használható a következő példa feladatokat:

- Előrejelezheti a karbantartási igényeket Factory.
- Valós idejű energia követelményei az elektromos rács elemzése.
- Optimalizálja az Office használatát, rendelkezésre álló területet.

Az Azure digitális Twins környezetek minden típusú vonatkozik. Csak néhány példa adattárházakhoz, iroda, iskolák, a kórházak és bankok. Akkor is használható stadionokban, gyárak, ideiglenes sok, parkot, az intelligens hálózatok és városokat. Az alábbi példák az Azure digitális Twins használható:

- Napi hőmérséklet több állapotok nyomon követésére.
- Foglalt drónos elérési utak figyelésére.
- Autonóm járművek azonosításához.
- Épület foglaltsága szintjeinek elemzése.
- A legforgalmasabb pénztárgép található a tárolóban.

Akármi legyen is a valós üzleti forgatókönyv, az Azure Digital Twins segítségével készíthető hozzá megfelelő digitális példány.

Az alábbi videó az Azure digitális Twins közelebbről vesz igénybe.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Főbb képességek

Az Azure digitális Twins rendelkezik az alábbi főbb képességeket.

### <a name="spatial-intelligence-graph"></a>Térbeliintelligencia-diagramok

A [ *térbeli intelligencia graph*](./concepts-objectmodel-spatialgraph.md), vagy *térbeli graph*, a fizikai környezet virtuális ábrázolásai. Használhatja a személyek, helyek és eszközök közötti kapcsolatok modellezésére.

Fontolja meg egy intelligens segédprogram alkalmazást, amely magában foglalja a több használati fogyasztásmérők csatlakoztatva a helyek között. Az intelligens segédprogram vállalati pontosan kell figyelni és előre jelezni az elektromos áram használati és számlázási. Egyes eszközök és érzékelők a hely és az ügyfél, amely, számlázunk kontextusú modellezni kell. A térbeli intelligencia graph segítségével az ilyen típusú összetett kapcsolatok modellezésére.

### <a name="digital-twin-object-models"></a>Digitális ikerobjektum-modellek

[Digitális két objektummodellt](./concepts-objectmodel-spatialgraph.md) előre definiált eszköz-protokollok és a sémát. A megoldás tartomány-specifikus igényeihez, leegyszerűsítheti a fejlesztést, és rendezve.

Ilyen például, egy hely foglaltsága alkalmazás használhat előre definiált típusok, például a telephelyi, épület, emelet és hely.

### <a name="multiple-and-nested-tenants"></a>Többszörös és beágyazott bérlők

Több bérlő számára hozhat létre megoldásokat, amelyek a méretezhető, biztonságos és használható fel újra. Elkülönített és biztonságos módon használt és elérhető több subtenants is létrehozhat.

Ilyen például, egy hely kihasználtsági alkalmazást, amely konfigurálva van egy egyetlen épületen belül más bérlők adatait egy bérlő adatait elszigetelni egy. Vagy az alkalmazás adatokat kombinálja egy adott bérlő számos épületek szolgál.

### <a name="advanced-compute-capabilities"></a>Fejlett számítási képességek

A [felhasználó által definiált függvények](./concepts-user-defined-functions.md), határozza meg, és felhasználói függvények futtatása ellen bejövő [eszközadatok](./concepts-device-ingress.md) az előre definiált végpontjainak küldhet. A speciális funkció javítja a testreszabási és eszköz feladatok automatizálása.

Ilyen például, egy felhasználó által definiált függvény felmérni talajjal nedvesség érzékelőinek adatai és az időjárás-előrejelzést tartalmazó intelligens mezőgazdaság alkalmazás. Az alkalmazás ezután elküldi a jelek kapcsolatos öntözés igényeinek.

### <a name="built-in-access-control"></a>Beépített hozzáférés-vezérlés

Hozzáférés és identitáskezelési funkciókat használatával [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) és [Azure Active Directory](./security-authenticating-apis.md), biztonságosan szabályozhatja a hozzáférést a felhasználók és eszközök.

Például egy létesítmények felügyeleti alkalmazás, amely engedélyezi a lakók egy helyet, állítsa be a hőmérséklet egy megadott tartományon belül. Létesítményekben kezelők beállítása a hőmérséklet bármely hely bármilyen érték az engedélyezett.

### <a name="ecosystem"></a>Ökoszisztéma

Az Azure digitális Twins példány csatlakozhat számos hatékony Azure-szolgáltatások. Ezen szolgáltatások közé tartoznak az Azure Stream Analytics, Azure mesterséges Intelligencia és az Azure Storage. Is, az Azure Maps, a Microsoft vegyes valóságot, a Dynamics 365 vagy az Office 365.

Ilyen például, egy intelligens office, a teams és sok emeleteken található eszközök az Azure digitális Twins használó alkalmazás létrehozását. Eszközök élő adatok streamelése az a kiosztott digitális Ikereszköz-példányt, mert a Stream Analytics főbb gyakorlatban hasznosítható elemzéseket biztosít adatokat dolgozza fel. Az adatok Azure Storage-ban tárolt és megosztható fájlformátumba konvertálni. A fájl az Office 365 használatával legyen elosztva a teljes szervezet.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Az Azure Digital Twins előnyeit kihasználó megoldások

Az Azure digitális Twins hasznos a fizikai világ és a sok kapcsolatait. Egyszerűbbé téve a IoT modellezés, adatfeldolgozási, eseménykezelés eszköz- és nyomon követése. Vegye figyelembe a következő forgatókönyvek közül számos iparágak. Részesülnek történő használatát:

* A vállalat megjelenítése az szóközzel foglaltsága szintek a legjobb módszer annak irodaépület konferenciatermére konfigurálása információival glean az idő függvényében.
* Az eseményindító work order jegyek egy mobilalkalmazás. Ezzel a biztonsági őrök és ütemezés Házfelügyelői és más szolgáltatások egy kiskereskedelmi szóközt vagy sportesemények helyszín tart elküldeni.
* Egy épület bent megjelenítése, mely termek tartózkodnak az épület valós időben. Ezt követően segítenek munkahelyi szóközöket, a saját igényeinek megfelelően foglaljon le a személyre.
* Nyomon követheti, ahol eszközök találhatók belül adhatja.
* Díjszabási által a felhasználói beállítások és energia-rács megkötések modellezési electric jármű optimalizálása.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Az Azure digitális Twins egyéb IoT-szolgáltatások keretében

Az Azure Digital Twins az Azure IoT Hub segítségével csatlakozik az IoT-eszközökhöz és -érzékelőkhöz, amelyek naprakész adatokat szolgáltatnak a fizikai világról. Az alábbi ábrán látható, hogyan kapcsolódik az Azure digitális Twins más Azure-IoT-szolgáltatások.

![Az Azure Digital Twins egy, az Azure IoT Hubra épülő szolgáltatás][1]

IoT kapcsolatos további információkért lásd: [Azure IoT-technológiák és -megoldások](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies).

## <a name="next-steps"></a>További lépések

Nyissa meg egy rövid bemutatót az Azure digitális Twins kapcsolatos:

>[!div class="nextstepaction"]
>[Gyors útmutató: Elérhető az Azure digitális Twins teremkeresés](./quickstart-view-occupancy-dotnet.md)

Példakódot egy létesítmények projektmenedzsment-alkalmazás az Azure digitális Twins használatával:

>[!div class="nextstepaction"]
>[Oktatóanyag: Az Azure digitális Twins telepítheti és konfigurálhatja a térbeli grafikon](./tutorial-facilities-setup.md)

Az Azure Digital Twins alapvető fogalmainak ismertetése:

>[!div class="nextstepaction"]
>[A digitális Twins hálózatiobjektum-modellt és a térbeli intelligencia graph ismertetése](./concepts-objectmodel-spatialgraph.md)

<!-- Images -->
[1]: media/overview/azure-digital-twins-in-iot-ecosystem.png