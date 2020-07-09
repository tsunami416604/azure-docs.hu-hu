---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan érheti el Cognitive Services a Docker-tárolókat az adataihoz.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2020
ms.author: aahi
ms.openlocfilehash: 69984f9dbd94bcdca2e272a5bdebbb7fc1464dae
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104408"
---
# <a name="container-support-in-azure-cognitive-services"></a>Tárolók támogatása az Azure Cognitive Services

A tárolók támogatása az Azure Cognitive Services lehetővé teszi a fejlesztők számára, hogy az Azure-ban elérhető, azonos API-kat használják, és lehetővé teszi a rugalmasságot a [Docker-tárolókkal](https://www.docker.com/what-container)rendelkező szolgáltatások üzembe helyezése és üzemeltetése során. A tárolók támogatása jelenleg az Azure Cognitive Services egy részhalmazához érhető el, beleértve a következő részeit:

> [!div class="checklist"]
> * [Anomáliadetektor][ad-containers]
> * [Computer Vision][cv-containers]
> * [Face][fa-containers]
> * [Form Recognizer][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Speech Service API][sp-containers]
> * [Szövegelemzés][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

A tárolókra bontás olyan szoftverterjesztési módszer, amelyben egy alkalmazás vagy szolgáltatás, beleértve annak függőségeit & konfigurációját, a rendszer egy tároló képként együtt csomagolja. Kevés vagy nem módosítható, ha egy tároló-lemezképet telepítenek egy tároló gazdagépre. A tárolók el vannak különítve egymástól és a mögöttes operációs rendszertől, kisebb helyigénysel, mint a virtuális gépek. A tárolók lemezképeit rövid távú feladatokhoz is készíthetik, és ha már nincs szükség rájuk, el lesznek távolítva.

Cognitive Services erőforrások elérhetők [Microsoft Azureon](https://azure.microsoft.com). Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a szolgáltatások Azure-erőforrásainak létrehozásához és megismeréséhez.

## <a name="features-and-benefits"></a>Funkciók és előnyök

- Nem módosítható **infrastruktúra**: a DevOps csapatok lehetővé teszik az ismert rendszerparaméterek egységes és megbízható készletének kihasználása, miközben a változáshoz alkalmazkodni tudnak. A tárolók rugalmasságot biztosítanak a kiszámítható ökoszisztémán belül, és elkerülheti a konfigurációs eltolódást.
- **Vezérlés az adat felett**: lehetővé teszi, hogy az ügyfelek kiválasszák, hol dolgozzák fel a Cognitive Services az adatfeldolgozást. Ez elengedhetetlen olyan ügyfelek számára, akik nem tudnak adatküldeni a felhőbe, de hozzá kell férniük Cognitive Services technológiához. A hibrid környezetek – az adatok, a felügyelet, az identitás és a biztonság – közötti konzisztencia támogatása.
- A **modell frissítéseinek vezérlése**: az ügyfelek számára rugalmasságot biztosítanak a megoldásokban üzembe helyezett modellek verziószámozása és frissítése során.
- **Hordozható architektúra**: lehetővé teszi egy olyan hordozható alkalmazás-architektúra létrehozását, amely az Azure-on, a helyszínen és a peremhálózati üzembe helyezhető. A tárolók üzembe helyezhetők közvetlenül az [Azure Kubernetes szolgáltatásban](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)vagy egy [Azure stack](/azure-stack/operator)üzembe helyezett [Kubernetes](https://kubernetes.io/) -fürtön. További információ: [a Kubernetes telepítése Azure stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Magas átviteli sebesség/kis késés**: az ügyfelek számára lehetővé teszi a nagy átviteli sebesség és az alacsony késési követelmények méretezését azáltal, hogy a Cognitive Services fizikailag közel van az alkalmazás-logikához és az adatokhoz. A tárolók másodpercenként nem kaphatnak tranzakciót (TPS), és az igény szerint méretezhetők, ha megadja a szükséges hardveres erőforrásokat.
- **Méretezhetőség**: a tárolókra bontás és a tároló-összehangoló szoftverek egyre növekvő népszerűsége, például a Kubernetes; a skálázhatóság a technológiai fejlesztések élvonalában van. A skálázható fürt alapjainak kiépítésekor az alkalmazások fejlesztése magas rendelkezésre állást biztosít.

## <a name="containers-in-azure-cognitive-services"></a>Tárolók az Azure-ban Cognitive Services

Az Azure Cognitive Services tárolók a következő Docker-tárolókat biztosítják, amelyek mindegyike az Azure-beli szolgáltatások funkcióinak egy részhalmazát tartalmazza Cognitive Services:

| Szolgáltatás | Támogatott árképzési szintek | Tároló | Leírás |
|--|--|--|--|
| [Anomália detektor][ad-containers] | F0, S0 | **Anomália – detektor** | A rendellenesség-Kiderítő API lehetővé teszi, hogy az idősoros adataiban a gépi tanulással figyelje és azonosítsa a rendellenességeket.<br>[Hozzáférés kérése][request-access] |
| [Computer Vision][cv-containers] | F0, S1 | **Olvasás** | Különböző felületek és hátterek, például a nyugták, a plakátok és a névjegykártyák különböző objektumainak képeiből származó nyomtatott szöveg kibontása. Az olvasási tároló a képek *kézírásos szövegét* is észleli, és PDF/TIFF/többoldalas támogatást biztosít.<br/><br/>**Fontos:** Az olvasási tároló jelenleg csak angol nyelven működik. |
| [Face][fa-containers] | F0, S0 | **Face** | Észleli az emberi arcokat a képekben, és azonosítja az attribútumokat, beleértve az arc-tereptárgyak (például az orr és a szemek), a nemek, az életkor és más, a géppel előre jelzett arc-funkciókat. Az észlelésen kívül a Face utasítással megtekintheti, hogy két arc vagy különböző kép azonos-e a megbízhatósági pontszám használatával, vagy összehasonlítja az arcokat egy adatbázissal, hogy megtudja, hasonló vagy azonos arc létezik-e. A hasonló arcok csoportokba rendezésére is képes, közös vizualizációs jellemzőkkel.<br>[Hozzáférés kérése][request-access] |
| [Űrlap-felismerő][fr-containers] | F0, S0 | **Form Recognizer** | Az űrlap ismerete a gépi tanulási technológiákat alkalmazza a kulcs-érték párok és táblák űrlapokból való azonosításához és kinyeréséhez.<br>[Hozzáférés kérése][request-access] |
| [LUIS][lu-containers] | F0, S0 | **Luis** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Betölti a betanított vagy közzétett Language Understanding modellt, más néven LUIS alkalmazást egy Docker-tárolóba, és hozzáférést biztosít a tároló API-végpontjai lekérdezési előrejelzéséhez. Lekérdezési naplókat gyűjthet a tárolóból, és feltöltheti ezeket a [Luis-portálra](https://www.luis.ai) az alkalmazás előrejelzési pontosságának javítása érdekében. |
| [Speech Service API][sp-containers-stt] | F0, S0 | **Diktálás** | Folyamatos, valós idejű beszédet szöveggé alakít. |
| [Speech Service API][sp-containers-cstt] | F0, S0 | **Custom Speech – szöveg** | A folyamatos valós idejű beszédet szövegbe helyezi egy egyéni modell használatával. |
| [Speech Service API][sp-containers-tts] | F0, S0 | **Szövegfelolvasás** | Az írott szöveget természetesnek hangzó beszéddé alakítja. |
| [Speech Service API][sp-containers-ctts] | F0, S0 | **Egyéni szöveg – beszéd** | Átalakítja a szöveget természetes hangú beszédre egyéni modell használatával. |
| [Szövegelemzés][ta-containers-keyphrase] | F0, S | **Kulcsszókeresés** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | A fő pontok azonosítására szolgáló legfontosabb kifejezések kibontása. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. |
| [Szövegelemzés][ta-containers-language] | F0, S | **Nyelvfelismerés** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | A legfeljebb 120 nyelv esetében azt észleli, hogy a bemeneti szöveg milyen nyelven íródott, és egyetlen nyelvi kódot jelentsen a kérelemben elküldött összes dokumentumhoz. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. |
| [Szövegelemzés][ta-containers-sentiment] | F0, S | **Hangulatelemzés v3** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | A nyers szöveget a pozitív vagy negatív hangulatú nyomokra elemzi. Az érzelmek elemzése ebben a verzióban az egyes dokumentumokhoz és mondatokhoz tartozó hangulati címkéket (például *pozitív* vagy *negatív*) adja vissza. |
| [Szövegelemzés][ta-containers-health] | F0, S | **Text Analytics állapota** | Orvosi adatok kinyerése és címkézése strukturálatlan klinikai szövegből. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Emellett egyes tárolók támogatják a Cognitive Services [**all-in-One ajánlati**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erőforrás-kulcsokat. Egyetlen Cognitive Services is létrehozhat egy teljes körű erőforrást, és ugyanazt a számlázási kulcsot használhatja a támogatott szolgáltatások között a következő szolgáltatásokhoz:

* Computer Vision
* Face
* LUIS
* Szövegelemzés

## <a name="container-availability-in-azure-cognitive-services"></a>Tároló rendelkezésre állása az Azure-ban Cognitive Services

Az Azure Cognitive Services tárolók nyilvánosan elérhetők az Azure-előfizetésen keresztül, a Docker-tárolók rendszerképei pedig a Microsoft Container Registry vagy a Docker hub-ból tölthetők le. A [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal a megfelelő beállításjegyzékből töltheti le a tárolók rendszerképét.

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Services-tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

**Docker-motor**: a Docker-motort helyileg kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)és [Windows](https://docs.docker.com/docker-for-windows/)rendszeren. Windows rendszeren a Docker-t úgy kell konfigurálni, hogy támogassa a Linux-tárolókat. A Docker-tárolók közvetlenül az [Azure Kubernetes szolgáltatásba](../aks/index.yml) vagy [Azure Container Instancesba](../container-instances/index.yml)is telepíthetők.

A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését.

A **microsoft Container Registry és a Docker ismerete**: alapvető ismeretekkel kell rendelkeznie a Microsoft Container Registry és a Docker-fogalmak, például a nyilvántartók, a Tárházak, a tárolók és a tárolók rendszerképeiről, valamint az alapszintű parancsok megismeréséről `docker` .

A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).

Az egyes tárolók saját követelményeiket is magukban foglalhatják, beleértve a kiszolgáló és a memória foglalási követelményeit is.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Következő lépések

A Cognitive Services használható [tároló-receptek](containers/container-reuse-recipe.md) megismerése.

Telepítse és vizsgálja meg a tárolók által biztosított funkciókat az Azure Cognitive Servicesban:

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
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u