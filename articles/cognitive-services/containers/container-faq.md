---
title: Cognitive Services tárolók – gyakori kérdések (GYIK)
titleSuffix: Azure Cognitive Services
description: Gyakori kérdések és válaszok.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 6e218f33bdc33708cef0c94eb85298abf2b8927c
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316624"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure Cognitive Services-tárolók – gyakori kérdések (GYIK)

## <a name="general-questions"></a>Általános kérdések

**K: Mi érhető el?**

**V:** A [tárolók támogatása az azure Cognitive Services](../cognitive-services-container-support.md) lehetővé teszi a fejlesztők számára az Azure-ban elérhető intelligens API-k használatát, de a tárolókra bontás [előnyeit](../cognitive-services-container-support.md#features-and-benefits) . A tárolók támogatása jelenleg előzetes verzióban érhető el az Azure Cognitive Services egy részhalmaza számára, beleértve a következő részeit:

> [!div class="checklist"]
> * [Anomália detektor][ad-containers]
> * [Computer Vision][cv-containers]
> * [Arcfelismerés][fa-containers]
> * [Űrlap-felismerő][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [Szövegelemzés][ta-containers]
<!-- > * [Translator Text][tt-containers] -->

**K: Van különbség a Cognitive Services felhő és a tárolók között?**

**V:** Cognitive Services tárolók a Cognitive Services felhő alternatívája. A tárolók ugyanazokat a funkciókat kínálja, mint a megfelelő felhőalapú szolgáltatások. Az ügyfelek üzembe helyezhetik a tárolókat a helyszínen vagy az Azure-ban. Az alapszintű AI-technológia, a díjszabási szintek, az API-kulcsok és az API-aláírások megegyeznek a tároló és a hozzá tartozó felhőalapú szolgáltatások között. Ezek a [funkciók és előnyök](../cognitive-services-container-support.md#features-and-benefits) a tárolók Felhőbeli szolgáltatással egyenértékűként való kiválasztásához.

**K: Lesznek elérhetők a tárolók az összes Cognitive Services számára, és mik a tárolók következő készletei?**

**V:** További Cognitive Services elérhetővé tesszük a tárolók ajánlatait. Vegye fel a kapcsolatot a helyi Microsoft-fiók kezelőjével, és szerezzen frissítéseket az új tárolók kiadásairól és más Cognitive Services hirdetményekről.

**K: Mi lesz a szolgáltatói szerződés (SLA) Cognitive Services tárolók számára?**

**V:** Cognitive Services tárolók nem rendelkeznek SLA-val.

Cognitive Services az erőforrások tároló-konfigurációit az ügyfelek vezérlik, így a Microsoft nem biztosít SLA-t általánosan elérhetővé (GA). Az ügyfelek szabadon telepíthetik a helyszíni tárolókat, így meghatározzák a gazdagépek környezetét.

> [!IMPORTANT]
> Ha többet szeretne megtudni a Cognitive Services szolgáltatói szerződésekről, [látogasson el az SLA-oldalra](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**K: Elérhetők ezek a tárolók a szuverén felhőkben?**

**V:** Nem mindenki ismeri a "szuverén felhő" kifejezést, ezért kezdjük a definícióval:

> A "szuverén felhő" a [Azure Government](../../azure-government/documentation-government-welcome.md), az [Azure Germany](../../germany/germany-welcome.md)és az [Azure China 21Vianet](https://docs.microsoft.com/azure/china/overview-operations) -felhőkből áll.

Sajnos a Cognitive Services tárolók natív módon *nem* támogatottak a szuverén felhőkben. A tárolók futtathatók ezekben a felhőkben, de a nyilvános felhőből fognak megjelenni, és használati adatokat kell küldeniük a nyilvános végpontnak.

### <a name="versioning"></a>Verziókezelés

**K: Hogyan frissülnek a tárolók a legújabb verzióra?**

**V:** Az ügyfelek választhatnak, hogy mikor szeretnék frissíteni az üzembe helyezett tárolókat. A tárolók szabványos [Docker](https://docs.docker.com/engine/reference/commandline/tag/) `latest` -címkékkel lesznek megjelölve, például a legújabb verzió jelzésére. Azt javasoljuk ügyfeleinknek, hogy a felszabadított tárolók legújabb verzióját használják fel, a pénztár [Azure Container Registry webhookot](../../container-registry/container-registry-webhook.md) , amelyből megtudhatja, hogyan kérhet értesítést a lemezkép frissítésekor.
 
**K: Milyen verziók lesznek támogatottak?**

**V:** A tároló aktuális és utolsó főverziója támogatott lesz. Javasoljuk azonban, hogy az ügyfelek naprakészek maradjanak a legújabb technológia beszerzése érdekében.
 
**K: Hogyan történik a frissítések verziószámozása?**

**V:** A főverzió módosításai azt jelzik, hogy az API-aláírás feltörése módosul. Várható, hogy ez általában egybeesik a főbb, a kognitív szolgáltatásokhoz kapcsolódó felhőalapú ajánlat jelentős változásaival. Az alverzió módosításai a hibajavításokat, a modell frissítéseit és az API-aláírást nem igénylő új funkciókat jeleznek.

## <a name="technical-questions"></a>Technikai kérdések

**K: Hogyan futtatom a Cognitive Services tárolókat a IoT-eszközökön?**

Függetlenül attól, hogy nem rendelkezik-e megbízható internetkapcsolattal, vagy szeretné menteni a sávszélesség-költségeket. Vagy ha alacsony késési követelményekkel rendelkeznek, vagy a helyszínen elemezni kívánt bizalmas adatokkal rendelkeznek, [Azure IoT Edge a Cognitive Services tárolókkal](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) konzisztensek lesznek a felhővel.

**K: Hogyan a termékkel kapcsolatos visszajelzéseket és a szolgáltatásokra vonatkozó ajánlásokat?**

**V:** Javasoljuk, hogy az ügyfeleket [nyilvánosan,](https://cognitive.uservoice.com/) és szavazzon másokkal, akik a lehetséges problémák átfedésben vannak. A felhasználói hangvezérelt eszköz a termékkel kapcsolatos visszajelzésekhez és a szolgáltatásokra vonatkozó javaslatokhoz is használható.

**K: Kihez forduljon a támogatási szolgálathoz?**

**V:** Az ügyfél-támogatási csatornák megegyeznek a Cognitive Services felhőalapú ajánlatával. Minden Cognitive Services tároló tartalmaz olyan naplózási funkciókat, amelyek segítenek a felhasználóknak és a Közösségnek az ügyfelek támogatásában. További támogatásért tekintse meg a következő beállításokat.

### <a name="customer-support-plan"></a>Ügyfél-támogatási csomag

Az ügyfeleknek az Azure- [támogatási csomagra](https://azure.microsoft.com/support/plans/) kell hivatkoznia, hogy ki forduljon a támogatási szolgálathoz.

### <a name="azure-knowledge-center"></a>Azure Knowledge Center

Az ügyfél ingyenesen megismerheti az [Azure Knowledge centert](https://azure.microsoft.com/resources/knowledge-center/) , hogy válaszoljon a kérdésekre és a támogatási problémákra.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack overflow](https://en.wikipedia.org/wiki/Stack_Overflow) a professzionális és lelkes programozóknak szóló kérdés-és válasz-webhely.

Tekintse át az alábbi címkéket az igényeihez igazodó lehetséges kérdésekre és válaszokra vonatkozóan.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft kognitív](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**K: Hogyan működik a számlázás?**

**V:** Az ügyfeleknek a Cognitive Services felhőhöz hasonlóan kell fizetniük. A tárolókat úgy kell konfigurálni, hogy mérési adatküldési adatként küldjenek az Azure-ba, és ennek megfelelően a tranzakciók számlázása is megtörténik. Az üzemeltetett és a helyszíni szolgáltatásokban használt erőforrások egyetlen kvótához lesznek hozzáadva, többszintű díjszabással, mindkét használattal számolva. További részletekért tekintse meg a megfelelő ajánlat díjszabási lapját.

* [Anomália detektor][ad-containers-billing]
* [Computer Vision][cv-containers-billing]
* [Arcfelismerés][fa-containers-billing]
* [Űrlap-felismerő][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Speech Service API][sp-containers-billing]
* [Szövegelemzés][ta-containers-billing]
<!-- * [Translator Text][tt-containers-billing] -->

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services tárolók nem küldenek ügyféladatokat a Microsoftnak.
 
**K: Mi a jelenlegi támogatási garancia a tárolók számára?**

**V:** Az előzetes verziókra nem vonatkozik jótállás. A Microsoft vállalati szoftverekre vonatkozó standard jótállása akkor érvényes, ha a tárolók nyilvánosan elérhetővé tételként jelennek meg.
 
**K: Mi történik Cognitive Services tárolók megszakadásakor az internetkapcsolat elvesztésekor?**

**V:** Cognitive Services tárolók *nem* futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyezniük kell, hogy a tárolók mindig kommunikáljanak a mérési szolgáltatással.

**K: Meddig működhet a tároló az Azure-ral való csatlakozás nélkül?**

**V:** Cognitive Services tárolók *nem* futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyezniük kell, hogy a tárolók mindig kommunikáljanak a mérési szolgáltatással.
 
**K: Mi a tárolók futtatásához szükséges aktuális hardver?**

**V:** Cognitive Services tárolók olyan x64-alapú tárolók, amelyek az x64-es Linux Docker-tárolókat támogató kompatibilis Linux-csomópontokat, virtuális gépeket és peremhálózati eszközöket futtathatnak. Minden processzor processzort igényelnek. Az egyes tárolók minimális és ajánlott konfigurációi a következőkben érhetők el:

* [Anomália detektor][ad-containers-recommendations]
* [Computer Vision][cv-containers-recommendations]
* [Arcfelismerés][fa-containers-recommendations]
* [Űrlap-felismerő][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Speech Service API][sp-containers-recommendations]
* [Szövegelemzés][ta-containers-recommendations]
<!-- * [Translator Text][tt-containers-recommendations] -->
 
**K: A Windows jelenleg támogatja ezeket a tárolókat?**

**V:** A Cognitive Services tárolók Linux-tárolók, azonban a Linux-tárolók támogatása is támogatott a Windows rendszeren. További információ a Windowsos Linux-tárolókkal kapcsolatban: [Docker-dokumentáció](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**K: Hogyan észleli a tárolókat?**

**V:** Cognitive Services tárolók különböző helyszíneken érhetők el, például a Azure Portal, a Docker hub és az Azure Container nyilvántartó rendszerekben. A legutóbbi tárolóhelyek esetében tekintse meg a [tároló-Tárházak és-lemezképek](../cognitive-services-container-support.md#container-repositories-and-images)című témakört.

**K: Hogyan hasonlítja össze Cognitive Services tárolók az AWS-t és a Google-ajánlatokat?**

**V:** A Microsoft első felhőalapú szolgáltatója, hogy az előre betanított AI-modelljeiket tárolókban, egyszerű számlázással helyezze át, mivel az ügyfelek felhőalapú szolgáltatást használnak. A Microsoft úgy véli, hogy a hibrid felhő több lehetőséget kínál ügyfeleinek.

**K: Milyen megfelelőségi tanúsítványok vannak a tárolókban?**

**V:** A kognitív szolgáltatások tárolói nem rendelkeznek megfelelőségi tanúsítvánnyal

**K: Mely régiókban érhetők el Cognitive Services tárolók?**

**V:** A tárolók bárhol futhatnak bármely régióban, de szükségük van egy kulcsra, és visszahívni az Azure-ra mérésre. A Cloud Service összes támogatott régiója támogatott a tároló-mérési híváshoz.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md
<!-- [tt-containers]: ../translator/how-to-install-containers.md -->

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing
<!-- [tt-containers-billing]: ../translator/how-to-install-containers.md#billing -->

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
<!-- [tt-containers-recommendations]: ../translator/how-to-install-containers.md#container-requirements-and-recommendations -->
