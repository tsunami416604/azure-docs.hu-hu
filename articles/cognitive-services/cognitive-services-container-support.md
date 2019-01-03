---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan Docker-tárolók férhetnek közelebb a Cognitive Services az adatok.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 4ee98c2d8170df5ef5878f5b534b545eb105667e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543913"
---
# <a name="container-support-in-azure-cognitive-services"></a>Az Azure Cognitive Servicesben tároló támogatása

Tároló támogatása az Azure Cognitive Services lehetővé teszi a fejlesztőknek, hogy az azonos API-k gazdag elérhető az Azure-ban, és lehetővé teszi a rugalmasság a szolgáltatásokat, amelyek kapható üzembe helyezése és hol [Docker-tárolók](https://www.docker.com/what-container). Tárolótámogatás jelenleg érhető el az Azure Cognitive Services, beleértve egy részhalmazát előzetes verziójában [Computer Vision](Computer-vision/Home.md), [Face](Face/Overview.md), [Szövegelemzés](text-analytics/overview.md), és [ Language Understanding](LUIS/luis-container-howto.md) (LUIS).

Bontás, amelyben egy alkalmazás vagy szolgáltatás, beleértve a függőségeket és a konfigurációk, csomagolt össze egy tárolórendszerképet, szoftverterjesztés egy megközelítést. Alig vagy egyáltalán nem módosítással egy tárolórendszerképet egy tároló-gazdagépen is telepíthető. Tárolók el különítve egymással és az alapjául szolgáló operációs rendszer, tárhely kisebb, mint egy virtuális gépet. A tárolók rövid távú feladatok tárolórendszerképeket a példányt, és távolítja el, amikor már nincs rá szükség.

A következő videó bemutatja a Cognitive Services-tároló használatával.

[![Cognitive Services tároló bemutató](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

A [Computer Vision](Computer-vision/Home.md), [Face](Face/Overview.md), [Szövegelemzés](text-analytics/overview.md), és [Language Understanding (LUIS)](LUIS/what-is-luis.md) szolgáltatások elérhetők a [Microsoft Azure](https://azure.microsoft.com). Jelentkezzen be a [az Azure portal](https://portal.azure.com/) hozhat létre, és Fedezze fel az Azure-szolgáltatások erőforrásait.

## <a name="features-and-benefits"></a>Funkciók és előnyök

- **Adatok felett**: Lehetővé teszi az ügyfelek számára, hogy válassza ki, ahol a ezeket a Cognitive Services az adatok feldolgozása. Ez a alapvető olyan ügyfelek számára, nem tud adatokat küldeni a felhőben, de a Cognitive Services technológia hozzáférésre van szükségük. Támogatja a konzisztencia hibrid környezetben – keresztül adatok, felügyeleti, identitáskezelési és biztonsági.
- **Adatmodell-frissítéseket felett**: Verziókezelés és a modellek üzembe helyezett megoldásaikat frissítése megadhassa az ügyfeleknek.
- **Hordozható architektúra**: Egy hordozható alkalmazásarchitektúra is üzembe helyezhetők az Azure-ban, a helyszíni és a peremhálózaton létrehozásának engedélyezése. Tárolók közvetlenül telepíthető [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), vagy egy [Kubernetes](https://kubernetes.io/) fürtben telepített [Azure Stack](../azure-stack/index.yml). További információkért lásd: [Kubernetes üzembe helyezése az Azure Stackhez](../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).
- **Nagy átviteli sebesség / kis késés**: Lehetővé teszi a nagy átviteli sebességet és közel valós idejű követelmények méretezését, engedélyezi a Cognitive Services futtatásához fizikailag közel az alkalmazáslogika és az adatokat az ügyfelek. A tárolók nem költségplafont, másodpercenkénti tranzakciók (TPS), és a horizontális felskálázás és kibővítés is kezeléséhez igény szerint, ha megadja a szükséges hardver-erőforrások lehet tenni. 


## <a name="containers-in-azure-cognitive-services"></a>A tárolók az Azure Cognitive Services

Az Azure Cognitive Services-tárolók meg azt az alábbi Docker-tárolókat, amelyek mindegyike tartalmaz egy része az Azure Cognitive Services szolgáltatások funkciói:

| Szolgáltatás | Tároló| Leírás |
|---------|----------|-------------|
|[Computer Vision](Computer-vision/computer-vision-how-to-install-containers.md) |**Szövegének felismerése** |Kivonatok nyomtatott szöveg különféle objektumokat a különféle felületekkel és hátterek, például a visszaigazolások és poszterek vagy tájékoztató névjegykártyák-rendszerképeket.<br/><br/>**Fontos:** A szöveg felismerése tároló jelenleg csak angol nyelven használható.<br>[Hozzáférés kérése](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Arcfelismerés](Face/face-how-to-install-containers.md) |**Arcfelismerés** |Észleli az emberi arcok a képeken, és azonosítja az attribútumokat, (például noses és szemek) arcrészek, nemek, életkor és egyéb gép – előre meghatározott arcjellemzőket sorolják fel. Észlelési, mellett Face is ellenőrizze, hogy két arc ugyanazt a lemezképet vagy különböző képek egy megbízhatósági pontszám használatával azonosak, vagy olyan adatbázison annak ellenőrzéséhez, hogy a hasonló megjelenésű arcokat összehasonlítása vagy azonos face már létezik. Azt is is csoportokba rendezheti a hasonló arcokat keres, közös visual tulajdonságok használatával.<br>[Hozzáférés kérése](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[LUIS](LUIS/luis-container-howto.md) |**A LUIS** ([kép](https://go.microsoft.com/fwlink/?linkid=2043204))|Betanított vagy közzétett Language Understanding modell, más néven LUIS-alkalmazásokon, tölt be egy docker-tárolót, és hozzáférést biztosít az API-végpontokat a tárolót a lekérdezés előrejelzéseket. Lekérdezés naplók összegyűjtése a tárolóból, és töltse fel ezeket a vissza a [LUIS portál](https://www.luis.ai) való az alkalmazás előrejelzési pontosság növeléséhez.|
|[Szövegelemzés](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |**Kulcs kulcsszókeresés** ([kép](https://go.microsoft.com/fwlink/?linkid=2018757)) |Kiolvassa a fő pontokat azonosíthatja a kulcskifejezéseket. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. |
|[Szövegelemzés](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Nyelv észlelése** ([kép](https://go.microsoft.com/fwlink/?linkid=2018759)) |Legfeljebb 120 nyelvet mely a bemeneti szöveg nyelven van megírva nyelvet és a jelentés minden egyes dokumentum, a kérelem küldése egyetlen nyelvkód észleli. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. |
|[Szövegelemzés](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**Hangulatelemzés** ([kép](https://go.microsoft.com/fwlink/?linkid=2018654)) |Elemzi a nyers szöveg a keresőmotorok kapcsolatos pozitív vagy negatív véleményeket. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám. Az elemzési modellek is előre betanított használatával egy Microsoft szöveg és a természetes nyelvi technológiák széles körű törzse. [Bizonyos nyelvek](./text-analytics/language-support.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak. |

## <a name="container-availability-in-azure-cognitive-services"></a>Az Azure Cognitive Servicesben tároló elérhetősége

Az Azure Cognitive Services-tárolók nyilvánosan elérhető Azure-előfizetéssel, és Docker-tárolórendszerképekhez lekérhetik a Microsoft Container Registry vagy a Docker Hubból. Használhatja a [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal letöltheti egy tárolórendszerképet a megfelelő beállításjegyzékből.

> [!IMPORTANT]
> Jelenleg, meg kell adnia a regisztrációs folyamaton eléréséhez a [Face](Face/face-how-to-install-containers.md) és [szöveg felismerése](Computer-vision/computer-vision-how-to-install-containers.md) tárolók, amelyben adja meg, és meg, a vállalati és a használati eset kapcsolatos kérdése van a kérdőív beküldése legyen a tárolók megvalósításához. Miután Ön hozzáférést és a megadott hitelesítő adatokat, majd kérheti le a tárolórendszerképeket az Arcfelismerés és szöveg felismerése tárolókhoz az Azure Container Registry által üzemeltetett egy privát tárolójegyzékben.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Services-tárolók használata előtt meg kell felelniük a következő előfeltételek vonatkoznak:

**Docker-motor**: Helyileg telepített Docker-motor kell rendelkeznie. A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), és [Windows](https://docs.docker.com/docker-for-windows/). A Windows Linux-tárolók támogatják a Docker kell konfigurálni. Docker-tárolóit közvetlenül is telepíthető [Azure Kubernetes Service](../aks/index.yml) vagy [Azure Container Instances](../container-instances/index.yml).

Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz.

**A Microsoft Container Registry és a Docker ismerős**: Rendelkeznie kell Microsoft Container Registry és a Docker fő fogalmaira, például beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel `docker` parancsokat.

A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).

Az egyes tárolók követelményeknek, is, beleértve a kiszolgáló, és lefoglalási memóriakövetelményei rendelkezhet.

## <a name="developer-samples"></a>Fejlesztői minták

Fejlesztői minták esetén érhető el a [GitHub-adattár](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>További lépések

Telepítse, és ismerje meg a tárolók az Azure Cognitive Services által biztosított funkciókat:

* [Telepítheti és használhatja a Computer Vision tárolók](Computer-vision/computer-vision-how-to-install-containers.md)
* [Telepítheti és használhatja a Face tárolók](Face/face-how-to-install-containers.md)
* [Telepítheti és használhatja a Text Analytics tárolók](text-analytics/how-tos/text-analytics-how-to-install-containers.md)
* [Telepítése és Language Understanding (LUIS) tárolók használata](LUIS/luis-container-howto.md)
