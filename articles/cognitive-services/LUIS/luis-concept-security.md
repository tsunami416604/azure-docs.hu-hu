---
title: Amikor együttműködő biztonsági
titleSuffix: Language Understanding - Azure Cognitive Services
description: Authoring hozzáférés tulajdonosok és a közreműködők érhető el. Saját alkalmazások esetén végponti hozzáférés tulajdonosai és a közreműködők érhető el.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: ac4095d19904db10d4d60364dd2c739e8105c171
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863619"
---
# <a name="authoring-and-endpoint-user-access"></a>Létrehozási és -végpont felhasználói hozzáférés
Authoring hozzáférés tulajdonosok és a közreműködők érhető el. Saját alkalmazások esetén végponti hozzáférés tulajdonosai és a közreműködők érhető el. Egy nyilvános alkalmazás végponti hozzáférés érhető el mindenki számára, amelyek a saját LUIS-fiókkal rendelkezik, és a nyilvános alkalmazás-azonosítóval rendelkezik. 

## <a name="access-to-authoring"></a>Az authoring hozzáférés
Az alkalmazáshoz való hozzáférést a [LUIS](luis-reference-regions.md#luis-website) webhely vagy a [API-k készítése](https://aka.ms/luis-authoring-apis) vezérlik az alkalmazás tulajdonosának. 

A tulajdonos és az összes közreműködők hozzáférése ahhoz, hogy az alkalmazást. 

|Authoring hozzáférés tartalmaz|Megjegyzések|
|--|--|
|Adja hozzá, vagy távolítsa el a végpont kulcsok||
|Verzió exportálása||
|Végpont naplók exportálása||
|Verzió importálása||
|Az alkalmazás nyilvános tétele|Ha egy alkalmazás nyilvános, bárki egy létrehozási vagy a végpont kulcs lekérdezheti az alkalmazás.|
|Modell módosítása|
|Közzététel|
|Tekintse át a végpont utterances [aktív tanulás](luis-how-to-review-endoint-utt.md)|
|Betanítás|

## <a name="access-to-endpoint"></a>Access-végponthoz

Hozzáférés lekérdezni a végpont egy beállítást vezérli a **alkalmazással kapcsolatos adatok** lapját a **kezelés** szakaszban. 

![Nyilvános csoport alkalmazás](./media/luis-concept-security/set-application-as-public.png)

|[Privát végpontjaikat](#private-app-endpoint-security)|[Nyilvános végponthoz](#public-app-endpoint-access)|
|:--|:--|
|Tulajdonos és a közreműködők számára elérhető|Tulajdonos, közreműködő és mindenki számára elérhető más, hogy tudja, hogy alkalmazás azonosítója|

### <a name="private-app-endpoint-security"></a>Saját alkalmazás endpoint securityhez

A magánhálózati alkalmazáshoz végpont csak a következő számára érhető el:

|Kulcs és a felhasználó|Magyarázat|
|--|--|--|
|A tulajdonos szerzői kulcs| Legfeljebb 1000 végpont találatok|
|A közreműködők Authoring Tool kulcsok| Legfeljebb 1000 végpont találatok|
|A LUIS-szerző vagy közreműködő által rendelt bármelyik billentyűt|Kulcshasználat réteg alapján|

#### <a name="microsoft-user-accounts"></a>A Microsoft felhasználói fiókok

Szerzők és a közreműködők kulcsok rendelhet egy privát LUIS-alkalmazás. A LUIS kulcsot az Azure Portalon hoz létre a Microsoft felhasználói fióknak kell lennie, vagy az alkalmazás tulajdonosa, vagy egy alkalmazás közreműködő. Nem rendelhet egy kulcsot a magánhálózati alkalmazáshoz egy másik Azure-fiókjából.

Lásd: [Azure Active Directory-bérlő felhasználói](luis-how-to-collaborate.md#azure-active-directory-tenant-user) további információ az Active Directory felhasználói fiókokat. 

### <a name="public-app-endpoint-access"></a>Nyilvános alkalmazás-végpont hozzáférés

Ha egy alkalmazás van konfigurálva, nyilvános, _bármely_ szerzői vagy LUIS végponti kulcs érvényes LUIS lekérdezheti az alkalmazás mindaddig, amíg a kulcsot nem használta a teljes endpoint kvótát.

A felhasználó, aki nem tulajdonosa vagy közreműködő, csak hozzáférhet a nyilvános app Ha adja meg az alkalmazás azonosítóját. A LUIS nincs nyilvános _piaci_ vagy egyéb módon, egy nyilvános alkalmazás kereséséhez.  

A nyilvános alkalmazás közzé van téve az összes régióban, hogy egy felhasználó egy LUIS-erőforrás régió-alapú kulccsal hozzáférhet az alkalmazás bármelyik régióban az erőforrás-kulcs társítva.

## <a name="microsoft-user-accounts"></a>A Microsoft felhasználói fiókok

Szerzők és a közreműködők adhat hozzá kulcsok LUIS a közzétételi oldalon. A LUIS kulcsot az Azure Portalon hoz létre a Microsoft felhasználói fióknak kell lennie, vagy az alkalmazás tulajdonosa, vagy egy alkalmazás közreműködő. 

Lásd: [Azure Active Directory-bérlő felhasználói](luis-how-to-collaborate.md#azure-active-directory-tenant-user) további információ az Active Directory felhasználói fiókokat. 

## <a name="securing-the-endpoint"></a>A végpont védelme 

Szabályozhatja, hogy ki láthatja a LUIS végpontkulcsának kiszolgálók-környezetben meghívásával. Ha egy robot a LUIS használ, a robot és a LUIS közötti kapcsolat már biztonságos. Ha közvetlenül, hogy a LUIS-végpontot hív, érdemes létrehoznia egy kiszolgálóoldali API-t (például az Azure-beli [függvény](https://azure.microsoft.com/services/functions/)) szabályozott hozzáféréssel rendelkező (például [AAD](https://azure.microsoft.com/services/active-directory/)). Ha a kiszolgálói oldalon az API és a hitelesítési és engedélyezési ellenőrzése, továbbítja a LUIS-be. Amíg ez a stratégia nem man-in-the-middle támadások megelőzése, access, nyomon követheti a felhasználók számára, a végpont obfuscates, és lehetővé teszi, hogy a végpont válasz naplózás hozzáadása (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Biztonsági megfelelőségi
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>További lépések

Lásd: [ajánlott eljárások](luis-concept-best-practices.md) hogyan használja a legjobb előrejelzéseket szándékokat és entitásokat.
