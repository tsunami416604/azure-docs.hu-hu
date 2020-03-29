---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan juthat a Docker-tárolók közelebb az adatokhoz.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: d75962b98543991a065f6b165279215614175925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219411"
---
# <a name="container-support-in-azure-cognitive-services"></a>Tárolótámogatás az Azure Cognitive Servicesben

Az Azure Cognitive Services tárolótámogatása lehetővé teszi a fejlesztők számára, hogy ugyanazokat a gazdag API-kat használják, amelyek az Azure-ban érhetők el, és rugalmasságot biztosít a [Docker-tárolókhoz](https://www.docker.com/what-container)kapcsolódó szolgáltatások üzembe helyezésében és üzemeltetésében. A tárolótámogatás jelenleg az Azure Cognitive Services egy részhalmazához érhető el, beleértve a következők et is:

> [!div class="checklist"]
> * [Anomaly Detector][ad-containers]
> * [Számítógépes látás][cv-containers]
> * [Arcfelismerés][fa-containers]
> * [Form Recognizer][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [Szövegelemzés][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

A tárolók szolgáltatása a szoftverterjesztés olyan megközelítése, amelyben egy alkalmazás vagy szolgáltatás, beleértve a függőségeit & konfigurációját, tárolórendszerképként van csomagolva. Kis vagy semmilyen módosítással egy tárolórendszerkép telepíthető egy tárológazdagépen. A tárolók egymástól és az alapul szolgáló operációs rendszertől vannak elkülönítve, kisebb helyigényűek, mint egy virtuális gép. A tárolók példányosíthatók a tárolórendszerképekből a rövid távú feladatokhoz, és eltávolíthatók, ha már nincs rá szükség.

A Cognitive Services-erőforrások a [Microsoft Azure-ban](https://azure.microsoft.com)érhetők el. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com/) és hozza létre és fedezze fel a szolgáltatások Azure-erőforrásait.

## <a name="features-and-benefits"></a>Jellemzők és előnyök

- **Módosítható infrastruktúra:** Engedélyezze a DevOps-csapatok számára, hogy az ismert rendszerparaméterek konzisztens és megbízható készletét használják ki, miközben alkalmazkodhatnak a változásokhoz. A tárolók rugalmasságot biztosítanak a kiszámítható ökoszisztémán belüli elforduláshoz és a konfigurációeltolódás elkerüléséhez.
- **Az adatok vezérlése:** Lehetővé teheti az ügyfelek számára, hogy kiválasszák, hol dolgozzák fel ezeket a Cognitive Services-adatokat. Ez alapvető fontosságú olyan ügyfelek számára, akik nem tudnak adatokat küldeni a felhőbe, de hozzáférésre van szükségük a Cognitive Services technológiához. Támogatja a konzisztenciát a hibrid környezetekben – az adatok, a felügyelet, az identitás és a biztonság között.
- **A modellfrissítések szabályozása:** Rugalmasságot biztosíthat az ügyfeleknek a megoldásaikban üzembe helyezett modellek verziószámozásában és frissítésében.
- **Hordozható architektúra:** Engedélyezze egy hordozható alkalmazásarchitektúra létrehozását, amely telepíthető az Azure-ban, a helyszíni és a peremhálózaton. A tárolók közvetlenül az [Azure Kubernetes-szolgáltatásra](../aks/index.yml), az Azure Container Instances-re vagy az [Azure Stackbe](/azure-stack/operator)telepített [Kubernetes-fürtre](https://kubernetes.io/) [telepíthetők.](../container-instances/index.yml) További információ: [Kubernetes telepítése az Azure Stackbe.](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)
- **Nagy átviteli/ alacsony késésű:** Lehetővé teszi az ügyfelek számára a nagy átviteli és alacsony késési követelmények méretezését azáltal, hogy lehetővé teszi a Cognitive Services számára, hogy fizikailag közel fussanak az alkalmazáslogikájukhoz és az adataikhoz. A tárolók nem korlátozzák a tranzakciók másodpercenkénti korlátját (TPS), és a szükséges hardvererőforrások biztosítása esetén fel- és kiskálázhatók az igények kielégítésére.
- **Méretezhetőség:** A konténerezés és a tároló vezénylési szoftverek, például a Kubernetes egyre növekvő népszerűségével; a technológiai fejlődés élvonalában van. A skálázható fürtalapzatra építve az alkalmazásfejlesztés magas rendelkezésre állást biztosít.

## <a name="containers-in-azure-cognitive-services"></a>Tárolók az Azure Cognitive Servicesben

Az Azure Cognitive Services-tárolók a következő Docker-tárolókat biztosítják, amelyek mindegyike az Azure Cognitive Services szolgáltatásainak egy részét tartalmazza:

| Szolgáltatás | Támogatott tarifacsomag | Tároló | Leírás |
|---------|----------|----------|-------------|
|[Anomáliadetektor][ad-containers] |F0, Sz0|**Anomália-detektor** |Az Anomáliadetektor API lehetővé teszi az idősorozat-adatok rendellenességeinek figyelését és észlelését gépi tanulással.<br>[Hozzáférés kérése](https://aka.ms/adcontainer)|
|[Számítógépes látás][cv-containers] |F0, S1|**Olvasás** |Kinyeri a nyomtatott szöveget különböző, különböző felületű és háttérrel rendelkező objektumok , például nyugták, poszterek és névjegykártyák képeiből. Az Olvasás tároló a *képeken lévő kézzel írt szöveget* is észleli, és PDF/TIFF/többoldalas támogatást biztosít.<br/><br/>**Fontos:** A Read tároló jelenleg csak angol nyelven működik.|
|[Arcfelismerés][fa-containers] |F0, Sz0|**Arcfelismerés** |Felismeri az emberi arcokat a képeken, és azonosítja az attribútumokat, beleértve az arctájéktárgyakat (például az orrokat és a szemeket), a nemet, az életkort és más, a gép által előre jelzett arcvonásokat. Az észlelés mellett az Arc ellenőrizheti, hogy ugyanazon a képen vagy különböző képekben lévő két arc azonos-e megbízhatósági pontszám használatával, vagy összehasonlíthatja az arcokat egy adatbázissal, hogy lássa, létezik-e már hasonló kinézetű vagy azonos arc. A hasonló arcokat csoportokba is rendezheti, megosztott vizuális tulajdonságok használatával.<br>[Hozzáférés kérése](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Űrlapfelismerő][fr-containers] |F0, Sz0|**Form Recognizer** |Az űrlapmegértés gépi tanulási technológiát alkalmaz a kulcs-érték párok és táblák azonosítására és kinyerésére az űrlapokból.<br>[Hozzáférés kérése](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, Sz0|**LUIS** ([kép](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Betölt egy betanított vagy közzétett language understanding modell, más néven egy LUIS-alkalmazás, egy docker-tárolóba, és hozzáférést biztosít a lekérdezés-előrejelzések a tároló API-végpontok. Lekérdezési naplók gyűjtése a tárolóból, és töltse fel ezeket vissza a [LUIS-portálra](https://www.luis.ai) az alkalmazás előrejelzési pontosságának javítása érdekében.|
|[Speech Service API][sp-containers-stt] |F0, Sz0|**Beszédfelismerés** |Folyamatos, valós idejű beszédet szöveggé alakít.|
|[Speech Service API][sp-containers-cstt] |F0, Sz0|**Egyéni beszédfelismerési szöveg** |A folyamatos valós idejű beszéd szöveggé történő átírása egyéni modell használatával.|
|[Speech Service API][sp-containers-tts] |F0, Sz0|**Szövegfelolvasás** |Az írott szöveget természetesnek hangzó beszéddé alakítja.|
|[Speech Service API][sp-containers-ctts] |F0, Sz0|**Egyéni szövegfelolvasás** |A szöveget természetes hangzású beszédté alakítja egyéni modell használatával.|
|[Szövegelemzés][ta-containers-keyphrase] |F0, S|**Kulcskifejezés kivonása** ([kép](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Kibontja a kulcsfontosságú kifejezéseket a főpontok azonosításához. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. |
|[Szövegelemzés][ta-containers-language]|F0, S|**Nyelvfelismerés** ([kép](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Legfeljebb 120 nyelven észleli, hogy a bemeneti szöveg melyik nyelven van megírva, és a kérelemre küldött minden dokumentumhoz egyetlen nyelvi kódot jelent. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. |
|[Szövegelemzés][ta-containers-sentiment]|F0, S|**Hangulatelemzés** ([kép](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Nyers szöveget elemez a pozitív vagy negatív érzésekkel kapcsolatos nyomokért. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám. Az elemzési modellek előre kivannak képezve a Microsoft számos szöveg- és természetes nyelvi technológiájának használatával. [Bizonyos nyelvek](./text-analytics/language-support.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Emellett egyes tárolók támogatja a Cognitive Services [**all-in-one kínál**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erőforráskulcsokat. Egyetlen Cognitive Services all-in-one erőforrást hozhat létre, és ugyanazt a számlázási kulcsot használhatja a támogatott szolgáltatások között a következő szolgáltatásokhoz:

* Számítógépes látástechnológia
* Arcfelismerés
* LUIS
* Szövegelemzés

## <a name="container-availability-in-azure-cognitive-services"></a>A tároló elérhetősége az Azure Cognitive Servicesben

Az Azure Cognitive Services-tárolók nyilvánosan elérhetők az Azure-előfizetésen keresztül, és a Docker-tárolórendszerképek lehívhatók a Microsoft Container Registry vagy a Docker Hub rendszeréből. A [docker lekéréseparanccsal](https://docs.docker.com/engine/reference/commandline/pull/) letöltheti a tárolórendszerképet a megfelelő beállításjegyzékből.

> [!IMPORTANT]
> Jelenleg ki kell töltenie egy regisztrációs folyamatot a következő tárolók eléréséhez, amelyben kitölthet i és egy kérdőívet, amely kérdéseket tartalmaz Önről, a vállalatáról és a használati esetről, amelyhez a tárolókat végre kívánja hajtani. Miután megkapta a hozzáférést, és megadta a hitelesítő adatokat, majd lehívhatja a tárolórendszerképeket egy privát tároló beállításjegyzék az Azure Container Registry által üzemeltetett.
> * [Anomáliadetektor](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Arcfelismerés](Face/face-how-to-install-containers.md)
> * [Form Recognizer](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Olvasás](computer-vision/computer-vision-how-to-install-containers.md)
> * [Beszéd-szöveg és szövegfelolvasás](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Services-tárolók használata előtt meg kell felelnie az alábbi előfeltételeknek:

**Docker Engine:** A Docker Engine-t helyileg kell telepítenie. A Docker olyan csomagokat biztosít, amelyek a Docker-környezetet [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)és [Windows](https://docs.docker.com/docker-for-windows/)rendszeren konfigurálják. Windows rendszeren a Docker-t linuxos tárolók támogatására kell konfigurálni. A Docker-tárolók közvetlenül az [Azure Kubernetes-szolgáltatásra](../aks/index.yml) vagy [az Azure Container Instances-re is telepíthetők.](../container-instances/index.yml)

A Docker-t úgy kell konfigurálni, hogy a tárolók csatlakozhassanak az Azure-hoz, és számlázási adatokat küldjenek az Azure-ba.

A Microsoft Container Registry és a **Docker ismertsége:** Alapvető ismeretekkel kell rendelkeznie a Microsoft Container Registry és a Docker fogalmairól, például a kibocsátásiegység-forgalmi jegyzékekről, adattárakról, tárolókról és tárolórendszerképekről, valamint az alapvető `docker` parancsok ismeretéről.

A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).

Az egyes tárolóknak saját követelményeik is lehetnek, beleértve a kiszolgáló- és memóriafoglalási követelményeket is.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a Kognitív szolgáltatásokkal használható [tárolórecepteket.](containers/container-reuse-recipe.md)

Telepítse és fedezze fel a tárolók által az Azure Cognitive Servicesben nyújtott funkciókat:

* [Anomáliadetektor-tárolók][ad-containers]
* [Computer Vision tárolók][cv-containers]
* [Arctárolók][fa-containers]
* [Űrlapfelismerő tárolók][fr-containers]
* [Nyelvi ismertetési (LUIS) tárolók][lu-containers]
* [Beszédszolgáltatás API-tárolói][sp-containers]
* [Szövegelemzési tárolók][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment