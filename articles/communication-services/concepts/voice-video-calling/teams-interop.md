---
title: Együttműködési csoportokat tárgyaló csapatok
titleSuffix: An Azure Communication Services concept document
description: Csatlakozás a csapatok üléseihez
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a7e2240e3f74f82186827ec82bb1aa39a5b93f6c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123834"
---
# <a name="teams-interoperability"></a>Csapatok együttműködése

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Az Azure kommunikációs szolgáltatásaival egyéni Meeting-élmények hozhatók létre, amelyek a Microsoft csapatait használják. A kommunikációs szolgáltatási megoldás (ok) felhasználóinak interakcióba léphetnek a csapat résztvevőinek a hang-, videó-és nyomtatómegosztást.

Ez az együttműködési képesség lehetővé teszi, hogy egyéni Azure-alkalmazásokat hozzon létre, amelyek összekapcsolják a felhasználókat a csapat ülésein. Az egyéni alkalmazások felhasználóinak nem kell Azure Active Directory identitásokkal vagy csapatokkal rendelkezniük a képesség megtapasztalása érdekében. Ez ideális megoldás arra, hogy az alkalmazottak (akik ismerik a csapatokat) és a külső felhasználók (egyéni alkalmazási élmény használatával) zökkenőmentes találkozási élményben legyenek. Így az alábbihoz hasonló élmények hozhatók létre:

1. Az alkalmazottak csapatok használatával ütemezhetnek egy értekezletet
2. Az egyéni kommunikációs szolgáltatások alkalmazás a Microsoft Graph API-kat használja az értekezlet részleteinek eléréséhez
3. Az értekezlet részletei az egyéni alkalmazáson keresztül vannak megosztva a külső felhasználókkal
4. A külső felhasználók az egyéni alkalmazással csatlakozhatnak a Teams Meeting szolgáltatáshoz (a kommunikációs szolgáltatások által meghívott ügyféloldali kódtár használatával)

Az ehhez a célra használt magas szintű architektúra a következőképpen néz ki: 

![Architektúra a csapatok együttműködéséhez](..//media/call-flows/teams-interop.png)

Míg egyes csapatok olyan funkciókat tartanak fenn, mint a Kiemelt Hand, a közös üzemmód, és a breakout-szobák csak a csapatok felhasználói számára lesznek elérhetők, az egyéni alkalmazás hozzáférhet az értekezlet alapvető hang-, videó-és képernyő-megosztási képességeihez.

Amikor a kommunikációs szolgáltatások felhasználója összekapcsolja a csapatok értekezletét, a hívó ügyfél függvénytárában megadott megjelenítendő név jelenik meg a csapatok felhasználói számára. A kommunikációs szolgáltatások felhasználója más módon lesz kezelve, mint a Teams névtelen felhasználója. Az egyéni alkalmazásnak meg kell fontolnia a felhasználók hitelesítését és más biztonsági intézkedéseket a csapatok üléseinek védelme érdekében. Vegye figyelembe, hogy a névtelen felhasználók hogyan csatlakozhatnak értekezletekhez, és a [csapatok biztonsági útmutató](https://docs.microsoft.com/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) segítségével konfigurálhatják a névtelen felhasználók számára elérhető képességeket.

A kommunikációs szolgáltatások felhasználói csatlakozhatnak az ütemezett csapatok üléseihez, amíg a névtelen illesztések engedélyezve vannak az [értekezlet beállításaiban](https://docs.microsoft.com/microsoftteams/meeting-settings-in-teams).



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A hívó alkalmazás csatlakoztatása egy Teams-értekezlethez](../../quickstarts/voice-video-calling/get-started-teams-interop.md)
