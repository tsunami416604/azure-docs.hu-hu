---
title: Áttekintés - Azure Digital Twins | Microsoft dokumentumok
description: További információ a térbeli intelligenciát megvalósító Azure IoT-megoldásról, az Azure Digital Twinsről.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.date: 12/30/2019
ms.topic: overview
ms.service: digital-twins
services: digital-twins
ms.custom: mvc
ms.openlocfilehash: 91386eaf7753f2aefada2d1423a11b366c3ee924
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370899"
---
# <a name="overview-of-azure-digital-twins-preview"></a>Az Azure Digital Twins előzetes verzió áttekintése

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Az Azure Digital Twins Preview egy Azure IoT-szolgáltatás, amely átfogó modelleket hoz létre a fizikai környezetből. Térbeli intelligencia grafikonokat hozhat létre az emberek, terek és eszközök közötti kapcsolatok és interakciók modellezésére.

Az Azure Digital Twins segítségével lekérdezheti az adatokat a fizikai térből, nem pedig sok különböző érzékelőről. Ez a szolgáltatás segít újrafelhasználható, jól skálázható, térbelileg tudatos élményeket építeni, amelyek összekapcsolják a streamelési adatokat a digitális és fizikai világban. Alkalmazásait ezek az egyedülállóan releváns környezetfüggő funkciók fokozzák. 

Az Azure Digital Twins minden típusú környezetre vonatkozik, például raktárakra, irodákra, iskolákra, kórházakra és bankokra. Azt is fel lehet használni a stadionok, gyárak, parkolók, parkok, intelligens hálózatok, és a városok. Az alábbiakban néhány olyan forgatókönyvet, ahol az Azure Digital Twins hasznos lehet:

- Jósolja meg a gyár karbantartási igényeit.
- Valós idejű energiaszükséglet elemzése elektromos hálózathoz.
- Optimalizálja a rendelkezésre álló hely egy irodában.
- Kövesse nyomon a napi hőmérsékletet több államban.
- A forgalmas drónútvonalak figyelése.
- Azonosítsa az autonóm járműveket.
- Elemezze egy épület kihasználtsági szintjét.
- Keresse meg a legforgalmasabb pénztárgépet a boltjában.

Bármi legyen is a valós üzleti forgatókönyv, valószínű, hogy egy megfelelő digitális példány azure digital twins keresztül kiépíthető.

Az alábbi videó közelebbről is szemügyre veszi az Azure Digital Twins programot.

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>Főbb képességek

Az Azure Digital Twins a következő kulcsfontosságú képességekkel rendelkezik.

### <a name="spatial-intelligence-graph"></a>Térbeliintelligencia-diagramok

A [*térbeli intelligencia grafikon*](./concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph), vagy *térbeli grafikon*, egy virtuális ábrázolása a fizikai környezet. Segítségével modellezheti a személyek, helyek és eszközök közötti kapcsolatokat.

Vegyünk egy intelligens segédprogram ot, amely több villamosenergia-használati mérőt foglal magában a környéken. Az intelligens közüzemi vállalatnak pontosan figyelemmel kell kísérnie és előre kell jeleznie a villamosenergia-felhasználást és a számlázást. Minden eszközt és érzékelőt a hely és a számlázandó ügyfél kontextusával kell modellezve modellezni. A térbeli intelligencia grafikon segítségével modellezheti az ilyen típusú összetett kapcsolatokat.

### <a name="digital-twin-object-models"></a>Digitális ikerobjektum-modellek

[A digitális ikerobjektum-modellek](./concepts-objectmodel-spatialgraph.md#digital-twins-object-models) előre definiált eszközprotokollok és adatsémák. Összehangolják a megoldás tartományspecifikus igényeit a fejlesztés felgyorsítása és egyszerűsítése érdekében.

Egy szobakihasználtsági alkalmazás például előre definiált tértípusokat használhat, például campust, épületet, padlót és szobát.

### <a name="multiple-and-nested-tenants"></a>Többszörös és beágyazott bérlők

Olyan megoldásokat hozhat létre, amelyek biztonságosan skálázhatók, és több bérlő számára is felhasználhatók. Több albérlőt is létrehozhat, amelyek elkülönített és biztonságos módon érhetők el és használhatók.

Egy példa egy helykihasználási alkalmazás, amely úgy van beállítva, hogy elkülönítse a bérlő adatait a többi bérlő adataitól egyetlen épületen belül. Vagy az alkalmazás egyetlen bérlő adatainak kombinálására szolgál számos épülettel.

### <a name="advanced-compute-capabilities"></a>Fejlett számítási képességek

A [felhasználó által definiált függvények](./concepts-user-defined-functions.md)segítségével egyéni függvényeket definiálhat és futtathat a bejövő [eszközadatokkal,](./concepts-device-ingress.md) hogy jeleket küldjön előre meghatározott végpontokra. Ez a fejlett képesség javítja az eszközfeladatok testreszabását és automatizálását.

Egy példa egy intelligens mezőgazdasági alkalmazás, amely magában foglalja a felhasználó által meghatározott funkció, hogy értékelje a talaj nedvességérzékelő mért és az időjárás-előrejelzés. Az alkalmazás ezután jeleket küld az öntözési igényekről.

### <a name="built-in-access-control"></a>Beépített hozzáférés-vezérlés

A hozzáférés- és identitáskezelési funkciók, például [a szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) és [az Azure Active Directory](./security-authenticating-apis.md)használatával biztonságosan szabályozhatja a hozzáférést az egyének és az eszközök számára.

Egy példa egy létesítménykezelő alkalmazás, amely úgy van beállítva, hogy a helyiség lakói egy adott tartományon belül beállíthassák a hőmérsékletet. A létesítmények kezelői bármely helyiségben bármilyen értékre állíthatják a hőmérsékletet.

### <a name="ecosystem"></a>Ökoszisztéma

Az Azure Digital Twins-példányt számos hatékony Azure-szolgáltatáshoz csatlakoztathatja. Ezek közé tartozik az Azure Stream Analytics, az Azure AI és az Azure Storage. Ezek közé tartozik az Azure Maps, a Microsoft Mixed Reality, a Dynamics 365 vagy az Office 365.

Egy ilyen például egy intelligens irodaépítési alkalmazás, amely az Azure Digital Twins segítségével képviseli a csapatok at és eszközöket, amelyek több szinten találhatók. Ahogy az eszközök élő adatokat továbbítanak a kiépített Digital Twin példányba, a Stream Analytics feldolgozza ezeket az adatokat, hogy végrehajtható kulcselemzéseket biztosítson. Az adatok az Azure Storage-ban tárolódnak, és megosztható fájlformátummá alakulnak. A fájl az egész szervezeten belül elvan osztva az Office 365 használatával.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Az Azure Digital Twins előnyeit kihasználó megoldások

Az Azure Digital Twins hasznos a fizikai világ és számos kapcsolat képviseletéhez. Leegyszerűsíti az IoT modellezést, az adatfeldolgozást, az eseménykezelést és az eszközkövetést. Fontolja meg csak néhány az alábbi forgatókönyvek több iparágban. Ők részesülnek annak használata, hogy:

* Egy ingatlankezelő vállalat nak meg kell adnia egy tér foglaltsági szintjét az idő múlásával, hogy megtudjon betekintést az irodaépület beállításának legjobb módjairól.
* Munkarendelési jegyek aktiválása mobilalkalmazáshoz. Használja a küldő biztonsági őrök és a menetrend gondnoki és egyéb szolgáltatások egy kiskereskedelmi térben vagy sporthelyszínen.
* Mutasd meg az épület lakójának, hogy mely szobák vannak elfoglalva egy épületben valós időben. Ezután segítsen az utasnak az igényeiknek megfelelő munkaterületeket tartalékba helyezni.
* Nyomon követheti, hogy az eszközök hol találhatók egy területen belül.
* Optimalizálja az elektromos járművek töltését a felhasználói preferenciák modellezésével és az energiahálózati korlátokkal.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Azure Digital Twins más IoT-szolgáltatások kontextusában

Az Azure Digital Twins az Azure IoT Hub segítségével csatlakozik az IoT-eszközökhöz és -érzékelőkhöz, amelyek naprakész adatokat szolgáltatnak a fizikai világról. Az alábbi ábra bemutatja, hogyan viszonyul az Azure Digital Twins más Azure IoT-szolgáltatásokhoz.

[![Az Azure Digital Twins egy, az Azure IoT Hubra épülő szolgáltatás](media/overview/azure-digital-twins-in-iot-ecosystem.png)](media/overview/azure-digital-twins-in-iot-ecosystem.png#lightbox)

Az IoT-ről további információt az [Azure IoT-technológiák és -megoldások](../iot-fundamentals/iot-services-and-technologies.md)című, a tet.

## <a name="next-steps"></a>További lépések

Látogasson el egy rövid bemutatóra az Azure Digital Twins-ről:

>[!div class="nextstepaction"]
>[Rövid útmutató: Elérhető szobák keresése az Azure Digital Twins használatával](./quickstart-view-occupancy-dotnet.md)

Tekintse meg közelebbről a létesítménykezelő alkalmazásokat az Azure Digital Twins használatával:

>[!div class="nextstepaction"]
>[Oktatóanyag: Az Azure Digital Twins üzembe helyezése és egy térbeli diagram konfigurálása](./tutorial-facilities-setup.md)

Az Azure Digital Twins alapvető fogalmainak ismertetése:

>[!div class="nextstepaction"]
>[A Digital Twins objektummodell és a térbeli intelligencia grafikonjának megértése](./concepts-objectmodel-spatialgraph.md)