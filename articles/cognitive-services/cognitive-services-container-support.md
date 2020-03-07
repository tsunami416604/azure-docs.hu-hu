---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan Docker-tárolók férhetnek közelebb a Cognitive Services az adatok.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: d75962b98543991a065f6b165279215614175925
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390769"
---
# <a name="container-support-in-azure-cognitive-services"></a>Az Azure Cognitive Servicesben tároló támogatása

A tárolók támogatása az Azure Cognitive Services lehetővé teszi a fejlesztők számára, hogy az Azure-ban elérhető, azonos API-kat használják, és lehetővé teszi a rugalmasságot a [Docker-tárolókkal](https://www.docker.com/what-container)rendelkező szolgáltatások üzembe helyezése és üzemeltetése során. A tárolók támogatása jelenleg az Azure Cognitive Services egy részhalmazához érhető el, beleértve a következő részeit:

> [!div class="checklist"]
> * [Anomália detektor][ad-containers]
> * [Computer Vision][cv-containers]
> * [Arc][fa-containers]
> * [Űrlap-felismerő][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [Szövegelemzés][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Bontás, amelyben egy alkalmazás vagy szolgáltatás, beleértve a függőségeket és a konfigurációk, csomagolt össze egy tárolórendszerképet, szoftverterjesztés egy megközelítést. Alig vagy egyáltalán nem módosítással egy tárolórendszerképet egy tároló-gazdagépen is telepíthető. Tárolók el különítve egymással és az alapjául szolgáló operációs rendszer, tárhely kisebb, mint egy virtuális gépet. A tárolók rövid távú feladatok tárolórendszerképeket a példányt, és távolítja el, amikor már nincs rá szükség.

Cognitive Services erőforrások elérhetők [Microsoft Azureon](https://azure.microsoft.com). Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a szolgáltatások Azure-erőforrásainak létrehozásához és megismeréséhez.

## <a name="features-and-benefits"></a>Funkciók és előnyök

- Nem módosítható **infrastruktúra**: a DevOps csapatok lehetővé teszik az ismert rendszerparaméterek egységes és megbízható készletének kihasználása, miközben a változáshoz alkalmazkodni tudnak. A tárolók rugalmasságot biztosítanak a kiszámítható ökoszisztémán belül, és elkerülheti a konfigurációs eltolódást.
- **Vezérlés az adat felett**: lehetővé teszi, hogy az ügyfelek kiválasszák, hol dolgozzák fel a Cognitive Services az adatfeldolgozást. Ez a alapvető olyan ügyfelek számára, nem tud adatokat küldeni a felhőben, de a Cognitive Services technológia hozzáférésre van szükségük. A hibrid környezetek – az adatok, a felügyelet, az identitás és a biztonság – közötti konzisztencia támogatása.
- A **modell frissítéseinek vezérlése**: az ügyfelek számára rugalmasságot biztosítanak a megoldásokban üzembe helyezett modellek verziószámozása és frissítése során.
- **Hordozható architektúra**: lehetővé teszi egy olyan hordozható alkalmazás-architektúra létrehozását, amely az Azure-on, a helyszínen és a peremhálózati üzembe helyezhető. A tárolók üzembe helyezhetők közvetlenül az [Azure Kubernetes szolgáltatásban](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)vagy egy [Azure stack](/azure-stack/operator)üzembe helyezett [Kubernetes](https://kubernetes.io/) -fürtön. További információ: [a Kubernetes telepítése Azure stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Magas átviteli sebesség/kis késés**: az ügyfelek számára lehetővé teszi a nagy átviteli sebesség és az alacsony késési követelmények méretezését azáltal, hogy a Cognitive Services fizikailag közel van az alkalmazás-logikához és az adatokhoz. A tárolók nem költségplafont, másodpercenkénti tranzakciók (TPS), és a horizontális felskálázás és kibővítés is kezeléséhez igény szerint, ha megadja a szükséges hardver-erőforrások lehet tenni.
- **Méretezhetőség**: a tárolókra bontás és a tároló-összehangoló szoftverek egyre növekvő népszerűsége, például a Kubernetes; a skálázhatóság a technológiai fejlesztések élvonalában van. A skálázható fürt alapjainak kiépítésekor az alkalmazások fejlesztése magas rendelkezésre állást biztosít.

## <a name="containers-in-azure-cognitive-services"></a>A tárolók az Azure Cognitive Services

Az Azure Cognitive Services-tárolók meg azt az alábbi Docker-tárolókat, amelyek mindegyike tartalmaz egy része az Azure Cognitive Services szolgáltatások funkciói:

| Szolgáltatás | Támogatott árképzési szintek | Tároló | Leírás |
|---------|----------|----------|-------------|
|[Anomália detektor][ad-containers] |F0, S0|**Anomália – detektor** |A rendellenesség-Kiderítő API lehetővé teszi, hogy az idősoros adataiban a gépi tanulással figyelje és azonosítsa a rendellenességeket.<br>[Hozzáférés kérése](https://aka.ms/adcontainer)|
|[Computer Vision][cv-containers] |F0, S1|**Olvasás** |Kivonatok nyomtatott szöveg különféle objektumokat a különféle felületekkel és hátterek, például a visszaigazolások és poszterek vagy tájékoztató névjegykártyák-rendszerképeket. Az olvasási tároló a képek *kézírásos szövegét* is észleli, és PDF/TIFF/többoldalas támogatást biztosít.<br/><br/>**Fontos:** Az olvasási tároló jelenleg csak angol nyelven működik.|
|[Arc][fa-containers] |F0, S0|**Arc** |Észleli az emberi arcok a képeken, és azonosítja az attribútumokat, (például noses és szemek) arcrészek, nemek, életkor és egyéb gép – előre meghatározott arcjellemzőket sorolják fel. Észlelési, mellett Face is ellenőrizze, hogy két arc ugyanazt a lemezképet vagy különböző képek egy megbízhatósági pontszám használatával azonosak, vagy olyan adatbázison annak ellenőrzéséhez, hogy a hasonló megjelenésű arcokat összehasonlítása vagy azonos face már létezik. Azt is is csoportokba rendezheti a hasonló arcokat keres, közös visual tulajdonságok használatával.<br>[Hozzáférés kérése](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Űrlap-felismerő][fr-containers] |F0, S0|**Űrlap-felismerő** |Az űrlap ismerete a gépi tanulási technológiákat alkalmazza a kulcs-érték párok és táblák űrlapokból való azonosításához és kinyeréséhez.<br>[Hozzáférés kérése](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**Luis** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Betanított vagy közzétett Language Understanding modell, más néven LUIS-alkalmazásokon, tölt be egy docker-tárolót, és hozzáférést biztosít az API-végpontokat a tárolót a lekérdezés előrejelzéseket. Lekérdezési naplókat gyűjthet a tárolóból, és feltöltheti ezeket a [Luis-portálra](https://www.luis.ai) az alkalmazás előrejelzési pontosságának javítása érdekében.|
|[Speech Service API][sp-containers-stt] |F0, S0|**Beszédfelismerés** |Folyamatos, valós idejű beszédet szöveggé alakít.|
|[Speech Service API][sp-containers-cstt] |F0, S0|**Custom Speech – szöveg** |A folyamatos valós idejű beszédet szövegbe helyezi egy egyéni modell használatával.|
|[Speech Service API][sp-containers-tts] |F0, S0|**Szövegfelolvasás** |Az írott szöveget természetesnek hangzó beszéddé alakítja.|
|[Speech Service API][sp-containers-ctts] |F0, S0|**Egyéni szöveg – beszéd** |Átalakítja a szöveget természetes hangú beszédre egyéni modell használatával.|
|[Szövegelemzés][ta-containers-keyphrase] |F0, S|**Kulcsszókeresés** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Kiolvassa a fő pontokat azonosíthatja a kulcskifejezéseket. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. |
|[Szövegelemzés][ta-containers-language]|F0, S|**Nyelvfelismerés** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Legfeljebb 120 nyelvet mely a bemeneti szöveg nyelven van megírva nyelvet és a jelentés minden egyes dokumentum, a kérelem küldése egyetlen nyelvkód észleli. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. |
|[Szövegelemzés][ta-containers-sentiment]|F0, S|**Hangulatelemzés** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Elemzi a nyers szöveg a keresőmotorok kapcsolatos pozitív vagy negatív véleményeket. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám. Az elemzési modellek is előre betanított használatával egy Microsoft szöveg és a természetes nyelvi technológiák széles körű törzse. [Bizonyos nyelvek](./text-analytics/language-support.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Emellett egyes tárolók támogatják a Cognitive Services [**all-in-One ajánlati**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erőforrás-kulcsokat. Egyetlen Cognitive Services is létrehozhat egy teljes körű erőforrást, és ugyanazt a számlázási kulcsot használhatja a támogatott szolgáltatások között a következő szolgáltatásokhoz:

* Számítógépes látástechnológia
* Arcfelismerés
* LUIS
* Szövegelemzés

## <a name="container-availability-in-azure-cognitive-services"></a>Az Azure Cognitive Servicesben tároló elérhetősége

Az Azure Cognitive Services-tárolók nyilvánosan elérhető Azure-előfizetéssel, és Docker-tárolórendszerképekhez lekérhetik a Microsoft Container Registry vagy a Docker Hubból. A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal a megfelelő beállításjegyzékből töltheti le a tárolók rendszerképét.

> [!IMPORTANT]
> Jelenleg a következő tárolók eléréséhez el kell végeznie egy regisztrációs folyamatot, amelyben ki kell töltenie és el kell küldenie egy kérdőívet az Ön, a vállalata és a használati eset alapján, amelynek a tárolóit szeretné megvalósítani. Miután megadta a hozzáférést és a megadott hitelesítő adatokat, lekérheti a tároló lemezképeit a Azure Container Registry által üzemeltetett privát tároló-beállításjegyzékből.
> * [Anomália detektor](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Arc](Face/face-how-to-install-containers.md)
> * [Űrlap-felismerő](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Olvasás](computer-vision/computer-vision-how-to-install-containers.md)
> * [Beszéd – szöveg és szöveg – beszéd](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Services-tárolók használata előtt meg kell felelniük a következő előfeltételek vonatkoznak:

**Docker-motor**: a Docker-motort helyileg kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)és [Windows](https://docs.docker.com/docker-for-windows/)rendszeren. A Windows Linux-tárolók támogatják a Docker kell konfigurálni. A Docker-tárolók közvetlenül az [Azure Kubernetes szolgáltatásba](../aks/index.yml) vagy [Azure Container Instancesba](../container-instances/index.yml)is telepíthetők.

Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz.

A **microsoft Container Registry és a Docker megismerése**: a Microsoft Container Registry és a Docker-fogalmak, például a nyilvántartások, a Tárházak, a tárolók és a tárolók lemezképének alapvető ismerete szükséges, valamint az alapszintű `docker` parancsok ismerete.

A Docker és a Container alapjairól a [Docker áttekintésében](https://docs.docker.com/engine/docker-overview/)talál további információt.

Az egyes tárolók követelményeknek, is, beleértve a kiszolgáló, és lefoglalási memóriakövetelményei rendelkezhet.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Következő lépések

A Cognitive Services használható [tároló-receptek](containers/container-reuse-recipe.md) megismerése.

Telepítse, és ismerje meg a tárolók az Azure Cognitive Services által biztosított funkciókat:

* [Anomáliák detektor tárolói][ad-containers]
* [Computer Vision tárolók][cv-containers]
* [Face-tárolók][fa-containers]
* [Űrlap-felismerő tárolók][fr-containers]
* [Language Understanding (LUIS) tárolók][lu-containers]
* [Speech Service API-tárolók][sp-containers]
* [Text Analytics tárolók][ta-containers]

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