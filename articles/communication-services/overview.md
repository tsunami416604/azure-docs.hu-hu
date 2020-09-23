---
title: Mi az az Azure kommunikációs szolgáltatás?
description: Ismerje meg, hogy az Azure kommunikációs szolgáltatásai hogyan segíthetnek a valós idejű kommunikációban gazdag felhasználói élmények fejlesztésében.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a006ece278e4ec750040bde5bd4b7a6144c9e720
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947827"
---
# <a name="what-is-azure-communication-services"></a>Mi az az Azure kommunikációs szolgáltatás?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások lehetővé teszik a valós idejű multimédiás hang-, videó-és telefonos IP-alapú kommunikációs funkciók egyszerű hozzáadását az alkalmazásaihoz. A kommunikációs szolgáltatások ügyféloldali kódtárai lehetővé teszik a csevegési és SMS-funkciók hozzáadását a kommunikációs megoldásokhoz.

A kommunikációs szolgáltatásokat hang-, videó-, szöveg-és adatkommunikációra használhatja számos esetben:

- Böngésző – böngésző, böngésző – alkalmazás és alkalmazás – alkalmazás kommunikáció
- Botokkal vagy más szolgáltatásokkal kommunikáló felhasználók
- A nyilvános kapcsolóval rendelkező telefonos hálózaton keresztül kommunikáló felhasználók és robotok

A vegyes forgatókönyvek támogatottak. A kommunikációs szolgáltatások alkalmazásai például a böngészőkből és a hagyományos telefonos eszközökből származó felhasználókkal is rendelkezhetnek. A kommunikációs szolgáltatások a Azure Bot Service használatával is kombinálhatók a robot által vezérelt interaktív hangválasztó (IVR) rendszerek létrehozásához.

## <a name="common-scenarios"></a>Gyakori helyzetek

Az alábbi források nagyszerű kiindulópontot jelentenek, ha most ismerkedik az Azure kommunikációs szolgáltatásokkal:
<br>

| Erőforrás                               |Leírás                           |
|---                                    |---                                   |
|**[Kommunikációs szolgáltatások erőforrásának létrehozása](./quickstarts/create-communication-resource.md)**|Az Azure kommunikációs szolgáltatások használatának megkezdéséhez használja a Azure Portal vagy a kommunikációs szolgáltatások felügyeleti ügyféloldali függvénytárát az első kommunikációs szolgáltatások erőforrásának kiépítéséhez. A kommunikációs szolgáltatások erőforrás-kapcsolati karakterlánca után kiépítheti az első felhasználói hozzáférési jogkivonatait.|
|**[Az első felhasználói hozzáférési jogkivonat létrehozása](./quickstarts/access-tokens.md)**|A felhasználói hozzáférési jogkivonatok segítségével hitelesítheti szolgáltatásait az Azure kommunikációs szolgáltatások erőforrásán. Ezek a tokenek a kommunikációs szolgáltatások felügyeleti ügyféloldali kódtár használatával lettek kiépítve és kibocsátva.|
|**[Telefonszám beszerzése](./quickstarts/telephony-sms/get-phone-number.md)**|Az Azure kommunikációs szolgáltatásokkal telefonszámokat helyezhet üzembe és szabadíthat fel. Ezeket a telefonszámokat a kimenő hívások kezdeményezésére és SMS kommunikációs megoldások létrehozására lehet használni.|
|**[SMS küldése az alkalmazásból](./quickstarts/telephony-sms/send.md)**|Az Azure kommunikációs szolgáltatások SMS ügyféloldali kódtára lehetővé teszi SMS-üzenetek küldését és fogadását a .NET-és JavaScript-alkalmazásokból.|
|**[Ismerkedés a hang-és videohívások meghívásával](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Az Azure kommunikációs szolgáltatások lehetővé teszik hang-és videohívás hozzáadását az alkalmazásokhoz a hívó ügyféloldali kódtár használatával. Ezt a kódtárat a WebRTC működteti, és lehetővé teszi, hogy az alkalmazásokon belül társközi, multimédia és valós idejű kommunikációt hozzon létre.|
|**[Ismerkedés a csevegéssel](./quickstarts/chat/get-started.md)**|Az Azure kommunikációs szolgáltatások csevegési ügyfelének könyvtára használható a valós idejű csevegés alkalmazásba való integrálásához.|


## <a name="samples"></a>Példák

Az alábbi minták az Azure kommunikációs szolgáltatások ügyféloldali kódtárainak végpontok közötti kihasználtságát szemléltetik. Nyugodtan használhatja ezeket a mintákat a saját kommunikációs szolgáltatások megoldásának elindításához.
<br>

| Minta neve                               | Leírás                           |
|---                                    |---                                   |
|**[A Hero-mintát hívó csoport](./samples/calling-hero-sample.md)**|Megtudhatja, hogyan használható a kommunikációs szolgáltatások ügyféloldali kódtára egy csoportos hívási élmény létrehozásához.|
|**[A csoportos csevegési hős mintája](./samples/chat-hero-sample.md)**|Megtudhatja, hogyan használható a kommunikációs szolgáltatások ügyféloldali kódtára egy csoportos csevegési élmény létrehozásához.|


## <a name="platforms-and-client-libraries"></a>Platformok és ügyféloldali kódtárak

Az alábbi források segítséget nyújtanak az Azure kommunikációs szolgáltatások ügyféloldali kódtárainak megismeréséhez:

| Erőforrás                               | Leírás                           |
|---                                    |---                                   |
|**[Ügyféloldali kódtárak és REST API-k](./concepts/sdk-options.md)**|Az Azure kommunikációs szolgáltatások képességeit koncepcionálisan hat területre rendezik, amelyek mindegyike egy ügyféloldali kódtár által reprezentált. A valós idejű kommunikációs igények alapján eldöntheti, hogy mely ügyféloldali kódtárakat kívánja használni.|
|**[Az ügyféloldali kódtár meghívása – áttekintés](./concepts/voice-video-calling/calling-sdk-features.md)**|Tekintse át a kommunikációs szolgáltatásokat hívó ügyféloldali függvénytár áttekintését.|
|**[Csevegő ügyféloldali kódtár – áttekintés](./concepts/chat/sdk-features.md)**|Tekintse át a kommunikációs szolgáltatások csevegési ügyfél függvénytárának áttekintését.|
|**[SMS ügyféloldali kódtár – áttekintés](./concepts/telephony-sms/sdk-features.md)**|Tekintse át a kommunikációs szolgáltatások SMS-ügyfél függvénytárának áttekintését.|

## <a name="compare-azure-communication-services"></a>Az Azure kommunikációs szolgáltatások összehasonlítása

Két másik Microsoft kommunikációs termék közül választhat, amelyek a kommunikációs szolgáltatásokkal közvetlenül nem működnek együtt:

 - [Microsoft Graph a felhőalapú kommunikációs API](https://docs.microsoft.com/graph/cloud-communications-concept-overview) -k lehetővé teszik a szervezetek számára, hogy a M365-licencekkel Azure Active Directory felhasználókhoz kötött kommunikációs élményt hozzanak létre. Ez ideális a Azure Active Directoryhoz kötött alkalmazások számára, illetve a termelékenységi tapasztalatok kibővítésére a Microsoft Teams szolgáltatásban. Emellett API-k is létrehozhatnak alkalmazásokat és testreszabásokat a [csapatok felhasználói felületén belül.](https://docs.microsoft.com/microsoftteams/platform/?view=msteams-client-js-latest&preserve-view=true)

 - Az [Azure PlayFab fél](https://docs.microsoft.com/gaming/playfab/features/multiplayer/networking/) leegyszerűsíti az alacsony késésű csevegés és az adatkommunikáció hozzáadását a játékokhoz. A kommunikációs szolgáltatásokkal rendelkező Power Gaming chat-és hálózatkezelési rendszerek esetében a PlayFab egy testre szabott lehetőség, és az Xbox-on is ingyenes.


## <a name="next-steps"></a>Következő lépések

 - [Kommunikációs szolgáltatások erőforrásának létrehozása](./quickstarts/create-communication-resource.md)
