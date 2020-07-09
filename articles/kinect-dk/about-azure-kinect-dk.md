---
title: Tudnivalók az Azure Kinect DK-ról
description: Az Azure Kinect Developer Kit (DK) eszközeinek és integrált szolgáltatásainak áttekintése.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: Azure, Kinect, áttekintés, fejlesztői készlet, DK, eszköz, mélység, Body Tracking, beszédfelismerés, kognitív szolgáltatások, SDK-k, SDK, belső vezérlőprogram
ms.openlocfilehash: 6b3215ba6761521214ebffa2a100c5a1a030ab4c
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "85277964"
---
# <a name="about-azure-kinect-dk"></a>Tudnivalók az Azure Kinect DK-ról

 ![Azure Kinect DK](./media/index/device-image.jpg)

Az Azure Kinect DK egy fejlett AI-érzékelőkkel rendelkező fejlesztői készlet, amely kifinomult számítógépes látási és beszédfelismerési modelleket biztosít.  A Kinect részletes érzékelőt, térbeli mikrofon tömböt tartalmaz egy videokamera és egy tájolási érzékelővel, amely egyetlen kis méretű eszköz, több mód, lehetőség és szoftverfejlesztői készlet (SDK) révén. A szolgáltatás megvásárolható a [Microsoft online áruházban](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq).

Az Azure Kinect DK fejlesztési környezet a következő több SDK-t tartalmazza:

- Sensor SDK az alacsony szintű érzékelőkhöz és az eszközökhöz való hozzáféréshez.
- Body Tracking SDK a 3D nyomkövető szervei számára.
- Speech Cognitive Services SDK a mikrofon-hozzáférés engedélyezéséhez és az Azure felhőalapú beszédfelismerési szolgáltatásaihoz.

Emellett a kognitív szemléletű szolgáltatások az eszköz RGB-kamerával is használhatók.

   ![Azure Kinect SDK-k diagramja](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Azure Kinect Sensor SDK

Az Azure Kinect Sensor SDK alacsony szintű érzékelő-hozzáférést biztosít az Azure Kinect DK hardveres érzékelőkhöz és az eszközök konfigurálásához.

További információ az Azure Kinect Sensor SDK-ról: [az Sensor SDK használata](about-sensor-sdk.md).

### <a name="azure-kinect-sensor-sdk-features"></a>Azure Kinect Sensor SDK-funkciók

Az Sensor SDK a következő funkciókkal rendelkezik, amelyek telepítése és futtatása az Azure Kinect DK-on történik:

- Részletes kamera-hozzáférés és üzemmód-vezérlés (passzív IR mód, valamint széles és keskeny mező-nézet mélységi mód) 
- RGB kamera-hozzáférés és-vezérlés (például a kitettség és a fehéregyensúly) 
- Mozgásérzékelő (giroszkóp és gyorsulásmérő) hozzáférése 
- Szinkronizált mélység – RGB kamera streaming a kamerák közötti konfigurálható késleltetéssel 
- Külső eszközök szinkronizálásának vezérlése az eszközök közötti konfigurálható késleltetési eltolással 
- A kamera keretének meta-adathozzáférése képfelbontáshoz, időbélyeg-hez stb. 
- Eszköz-kalibrálási adathozzáférés 

### <a name="azure-kinect-sensor-sdk-tools"></a>Azure Kinect Sensor SDK-eszközök

Az érzékelő SDK-ban az alábbi eszközök érhetők el:

- Egy megjelenítő eszköz az eszköz adatstreamek figyelésére és a különböző üzemmódok konfigurálására.
- A Matroska Container FORMATT használó Sensor Recording Tool és lejátszási olvasó API.
- Egy Azure Kinect DK belső vezérlőprogram-frissítési eszköz.

## <a name="azure-kinect-body-tracking-sdk"></a>Azure Kinect Body Tracking SDK

A Body Tracking SDK tartalmaz egy Windows-függvénytárat és-futtatókörnyezetet, amellyel nyomon követheti a 3D-ket az Azure Kinect DK hardverrel való használat során.

### <a name="azure-kinect-body-tracking-features"></a>Az Azure Kinect Body követési funkciói

A következő szövegtörzs-követési funkciók érhetők el a kapcsolódó SDK-ban:

- A törzs szegmentálását biztosítja.
- Egy anatómiailag helyes csontvázat tartalmaz a FOV minden részleges vagy teljes törzse számára.
- Egyedi identitást biztosít minden törzshöz.
- A szervezetek időbeli követését is nyomon követheti.

### <a name="azure-kinect-body-tracking-tools"></a>Azure Kinect Body nyomkövető eszközök

- A Body tracker egy megjelenítői eszközzel nyomon követheti a szervezeteket a 3D-ben.

## <a name="speech-cognitive-services-sdk"></a>A Speech kognitív Services SDK

A Speech SDK lehetővé teszi az Azure-hoz csatlakoztatott Speech Services használatát.

### <a name="speech-services"></a>Beszédszolgáltatások

- Diktálás
- Beszédfordítás
- Szövegfelolvasás

>[!NOTE]
>Az Azure Kinect DK nem rendelkezik hangszórókkal.

További részleteket és információkat a [Speech Service dokumentációjában](https://docs.microsoft.com/azure/cognitive-services/speech-service/)találhat.

## <a name="vision-services"></a>Látási szolgáltatások

A következő [Azure-beli kognitív szemléletű szolgáltatások](https://azure.microsoft.com/services/cognitive-services/directory/vision/) olyan Azure-szolgáltatásokat biztosítanak, amelyek képes azonosítani és elemezni a tartalmat a képeken és videókon.

- [Számítógép jövőképe](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Arcfelismerés](https://azure.microsoft.com/services/cognitive-services/face/)
- [Videó-indexelő](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Tartalom moderátora](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Egyéni jövőkép](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

A szolgáltatások folyamatosan fejlődnek és javítják a szolgáltatásokat, ezért ne feledje, hogy az alkalmazások tökéletesítése érdekében rendszeresen új vagy további [kognitív szolgáltatásokat](https://azure.microsoft.com/services/cognitive-services/) kell megnéznie. A kialakulóban lévő új szolgáltatások korai szakaszában tekintse meg a [kognitív szolgáltatások Labs szolgáltatást](https://labs.cognitive.microsoft.com/).

## <a name="azure-kinect-hardware-requirements"></a>Az Azure Kinect hardverkövetelmények

Az Azure Kinect DK a Microsoft legújabb érzékelő technológiáját integrálja egyetlen USB-csatlakozású tartozékra. További információ: [Azure Kinect DK hardver-specifikáció](hardware-specification.md).

## <a name="next-steps"></a>További lépések

Most már rendelkezik az Azure Kinect DK áttekintésével. A következő lépés a bemerülése és beállítása!

> [!div class="nextstepaction"]
>[Gyors útmutató: az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)
