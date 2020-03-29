---
title: A Cognitive Services tárolói gyakran feltett kérdésekre (GYIK)
titleSuffix: Azure Cognitive Services
description: Gyakran ismételt kérdések és válaszok.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 33b99d50db4384c7de818a7dd0bb8492c86bef97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73961894"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Az Azure Cognitive Services tárolói gyakran feltett kérdésekre (GYIK)

## <a name="general-questions"></a>Általános kérdések

**K: Mi érhető el?**

**V:** [Az Azure Cognitive Services tárolótámogatása](../cognitive-services-container-support.md) lehetővé teszi a fejlesztők számára, hogy ugyanazokat az intelligens API-kat használják, amelyek az Azure-ban érhetők el, de a [tárolók](../cognitive-services-container-support.md#features-and-benefits) használatával. A tárolótámogatás jelenleg előzetes verzióban érhető el az Azure Cognitive Services egy részhalmazához, beleértve a következők et is:

> [!div class="checklist"]
> * [Anomaly Detector][ad-containers]
> * [Számítógépes látás][cv-containers]
> * [Arcfelismerés][fa-containers]
> * [Form Recognizer][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [Szövegelemzés][ta-containers]

**K: Van-e különbség a Cognitive Services-felhő és a tárolók között?**

**A.** A Cognitive Services-tárolók a Cognitive Services-felhő alternatívája. A tárolók ugyanazokat a képességeket kínálják, mint a megfelelő felhőszolgáltatások. Az ügyfelek üzembe helyezhetik a tárolókat a helyszínen vagy az Azure-ban. Az alapvető AI-technológia, a tarifacsomagok, az API-kulcsok és az API-aláírás megegyezik a tároló és a megfelelő felhőszolgáltatások között. Az alábbiakban azokat a [funkciókat és előnyöket,](../cognitive-services-container-support.md#features-and-benefits) amelyek segítségével a tárolókat a felhőszolgáltatás-megfelelőjük helyett választják ki.

**K: A tárolók elérhetők lesznek az összes Cognitive Services számára, és melyek a következő tárolók, amelyekre számíthatunk?**

**A.** További Cognitive Services-szolgáltatásokat szeretnénk elérhetővé tenni tárolóajánlatként. Lépjen kapcsolatba a helyi Microsoft-fiókkezelővel, és kérjen frissítéseket az új tárolókiadásokról és a Cognitive Services egyéb értesítéseiről.

**K: Mi lesz a szolgáltatási szintű szerződés (SLA) a Cognitive Services-tárolók?**

**A.** Cognitive Services-tárolók nem rendelkeznek SLA.Cognitive Services containers have not have a SLA.

A Cognitive Services tárolókonfigurációit az ügyfelek szabályozzák, így a Microsoft nem kínál sla-t az általános rendelkezésre álláshoz (GA). Az ügyfelek szabadon telepíthetik a tárolókat a helyszínen, így ők határozzák meg a gazdakörnyezeteket.

> [!IMPORTANT]
> Ha többet szeretne megtudni a Cognitive Services szolgáltatási szintű szerződéseiről, [látogasson el az SLA-oldalra.](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

**K: Ezek a tárolók szuverén felhőkben érhetők el?**

**A.** Nem mindenki ismeri a "szuverén felhő" kifejezést, ezért kezdjük a meghatározással:

> A "szuverén felhő" az [Azure Government](../../azure-government/documentation-government-welcome.md), az Azure [Germany](../../germany/germany-welcome.md)és az Azure [China 21Vianet](https://docs.microsoft.com/azure/china/overview-operations) felhőkből áll.

Sajnos a Cognitive Services-tárolók *natívan nem* támogatott a szuverén felhők. A tárolók ezekben a felhőkben futtathatók, de a nyilvános felhőből lesznek lehúzva, és használati adatokat kell küldeniük a nyilvános végpontra.

### <a name="versioning"></a>Verziókezelés

**K: Hogyan frissülnek a tárolók a legújabb verzióra?**

**A.** Az ügyfelek kiválaszthatják, hogy mikor szeretnék frissíteni az általuk telepített tárolókat. A tárolók szabványos [Docker-címkékkel](https://docs.docker.com/engine/reference/commandline/tag/) lesznek megjelölve, például `latest` a legújabb verzió jelzésére. Javasoljuk az ügyfeleknek, hogy a frissítések készítésekor a tárolók legújabb verzióját, a pénztár [Azure Container Registry webhookokat](../../container-registry/container-registry-webhook.md) kérjék le, hogy részletesen megtudják, hogyan kaphatnak értesítést egy lemezkép frissítésekor.
 
**K: Milyen verziók támogatottak?**

**A.** A tároló aktuális és utolsó főverziója támogatott lesz. Arra biztatjuk azonban ügyfeleinket, hogy maradjanak naprakészek, és a legújabb technológiát kapják meg.
 
**K: Hogyan történik a frissítések verzióverziója?**

**A.** A főverziók módosításai azt jelzik, hogy az API-aláírás megromlott. Arra számítunk, hogy ez általában egybeesik a megfelelő Cognitive Service-felhőajánlat főverzió-módosításaival. Az alverziók módosításai hibajavításokat, modellfrissítéseket vagy olyan új funkciókat jeleznek, amelyek nem módosítják az API-aláírást.

## <a name="technical-questions"></a>Technikai kérdések

**K: Hogyan kell futtatni a Cognitive Services-tárolók IoT-eszközökön?**

Akár nem rendelkezik megbízható internetkapcsolattal, vagy sávszélesség-költséget szeretne megtakarítani. Vagy ha alacsony késési követelményekkel rendelkeznek, vagy a helyszínen elemzendő bizalmas adatokkal foglalkoznak, [az Azure IoT Edge a Cognitive Services-tárolókkal](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) konzisztenciát biztosít a felhővel.

**K: Hogyan adhatok visszajelzést a termékekről és a szolgáltatásjavaslatokról?**

**A.** Az ügyfeleket arra ösztönzik, hogy nyilvánosan [hangot adjanak aggályaiknak,](https://cognitive.uservoice.com/) és szavazzanak azokra, akik ugyanezt tették, ha a lehetséges problémák átfedik egymást. A felhasználói hangeszköz használható mind a termék visszajelzései, mind a szolgáltatásjavaslatok hoz.

**K: Kihez fordulhatok segítségért?**

**A.** Az ügyfélszolgálati csatornák megegyeznek a Cognitive Services felhőajánlatával. A Cognitive Services minden tárolója olyan naplózási funkciókat tartalmaz, amelyek segítenek nekünk és a közösségi támogatási ügyfeleknek. További támogatást az alábbi lehetőségek közül választhat.

### <a name="customer-support-plan"></a>Ügyfélszolgálati terv

Az ügyfeleknek az [Azure támogatási csomagjukban](https://azure.microsoft.com/support/plans/) meg kell tudniuk, hogy kihez fordulhatnak segítségért.

### <a name="azure-knowledge-center"></a>Azure tudásközpont

Az ügyfelek szabadon felfedezhetik az [Azure tudásközpontot,](https://azure.microsoft.com/resources/knowledge-center/) hogy megválaszolják a kérdéseket és támogathatják a problémákat.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack túlcsordulás](https://en.wikipedia.org/wiki/Stack_Overflow) egy kérdés-válasz oldalon a szakmai és lelkes programozók.

Fedezze fel az alábbi címkéket az igényeinek megfelelő lehetséges kérdésekés válaszok megismeréséhez.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft Kognitív](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**K: Hogyan működik a számlázás?**

**A.** Az ügyfelek a felhasználás alapján, hasonlóan a Cognitive Services-felhő. A tárolókat úgy kell konfigurálni, hogy mérési adatokat küldjenek az Azure-ba, és a tranzakciókat ennek megfelelően számlázzuk. A üzemeltetett és a helyszíni szolgáltatások ban használt erőforrások hozzáadódnak az egyetlen kvótához rétegzett díjszabással, mindkét használathoz képest számítva. További részletekért tekintse meg a megfelelő ajánlat díjszabási oldalát.

* [Anomaly Detector][ad-containers-billing]
* [Számítógépes látás][cv-containers-billing]
* [Arcfelismerés][fa-containers-billing]
* [Form Recognizer][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Speech Service API][sp-containers-billing]
* [Szövegelemzés][ta-containers-billing]

> [!IMPORTANT]
> A Cognitive Services-tárolók nem rendelkeznek licenccel anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. A Cognitive Services-tárolók nem küldenek ügyféladatokat a Microsoftnak.
 
**K: Mi a jelenlegi jótállás a konténerekre?**

**A.** Az előzetesek nem vállalnak jótállást. A Microsoft vállalati szoftverekre vonatkozó általános jótállási garanciája akkor érvényes, ha a tárolókat hivatalosan általános anamandósként (GA) jelentik be.
 
**K: Mi történik a Cognitive Services-tárolók, ha az internetkapcsolat megszakad?**

**A.** A Cognitive Services-tárolók nem rendelkeznek *licenccel* anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikáljanak a mérési szolgáltatással.

**K: Mennyi ideig működhet a tároló anélkül, hogy csatlakozna az Azure-hoz?**

**A.** A Cognitive Services-tárolók nem rendelkeznek *licenccel* anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikáljanak a mérési szolgáltatással.
 
**K: Mi a tárolók futtatásához szükséges aktuális hardver?**

**A.** A Cognitive Services-tárolók x64-alapú tárolók, amelyek bármely kompatibilis Linux-csomópontot, virtuális gép- és peremhálózati eszközt futtathatnak, amely támogatja az x64 Linux Docker-tárolókat. Mindegyiknek szüksége van CPU processzorokra. Az egyes tárolóajánlatok minimális és ajánlott konfigurációi az alábbiakban érhetők el:

* [Anomaly Detector][ad-containers-recommendations]
* [Számítógépes látás][cv-containers-recommendations]
* [Arcfelismerés][fa-containers-recommendations]
* [Form Recognizer][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Speech Service API][sp-containers-recommendations]
* [Szövegelemzés][ta-containers-recommendations]
 
**K: Ezek a tárolók jelenleg támogatottak a Windows rendszerben?**

**A.** A Cognitive Services-tárolók Linux-tárolók, azonban a Windows Linux-tárolóinak is támogatott. A Windows Linux-tárolóiról a [Docker dokumentációjában](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)olvashat bővebben.
 
**K: Hogyan fedezhetem fel a tárolókat?**

**A.** A Cognitive Services-tárolók különböző helyeken érhetők el, például az Azure Portalon, a Docker-központban és az Azure-tároló-beállításjegyzékekben. A legutóbbi tárolóhelyekről tekintse meg a [tárolótárolókat és -rendszerképeket.](../cognitive-services-container-support.md#container-repositories-and-images)

**K: Hogyan viszonyulnak a Cognitive Services-tárolók az AWS- és a Google-ajánlatokhoz?**

**A.** A Microsoft az első felhőszolgáltató, amely előre betanított AI-modelljeit tranzakciónként egyszerű számlázással rendelkező tárolókban mozgatja, mintha az ügyfelek felhőszolgáltatást használnának. A Microsoft úgy véli, hogy a hibrid felhő több választási lehetőséget biztosít az ügyfeleknek.

**K: Milyen megfelelőségi tanúsítványokkal rendelkeznek a tárolók?**

**A.** A kognitív szolgáltatások tárolói nem rendelkeznek megfelelőségi tanúsítvánnyal

**K: Milyen régiókban érhetők el a Cognitive Services-tárolók?**

**A.** A tárolók bármely régióban futtathatók, de szükségük van egy kulcsra, és visszahívhatják az Azure-ba a méréshez. A felhőszolgáltatás minden támogatott régiója támogatott a tárolók mérési hívásához.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
