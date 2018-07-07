---
title: Megismerheti a hozzáférést a LUIS-alkalmazások – Azure |} A Microsoft Docs
description: Útmutató a szerzői LUIS eléréséhez.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: f55574f7a9ffbcc2a1c8bd160bb66336b59c348c
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888599"
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
|Tekintse át a végpont utterances [aktív tanulás](label-suggested-utterances.md)|
|Betanítás|

## <a name="access-to-endpoint"></a>Access-végponthoz
A végpont a LUIS-lekérdezéshez való hozzáférést vezérlik a **nyilvános** beállítása az alkalmazás a **beállítások** lapot. Egy privát alkalmazás végpont lekérdezés be van jelölve, egy arra jogosult kulcs a hátralévő kvóta találatok. Egy nyilvános alkalmazás-végpont lekérdezésben található az is biztosít egy végponti kulcs (a személy, aki a lekérdezést, hogy így) amely is be van jelölve, a hátralévő kvóta találatok. 

A végpont kulcs vagy a lekérdezési karakterláncban a kijelentkezési a GET-kérés átadódik, vagy kérje meg a fejléc a bejegyzés.

![Nyilvános csoport alkalmazás](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Saját alkalmazás endpoint securityhez
A magánhálózati alkalmazáshoz végpont csak a következő számára érhető el:

|Kulcs és a felhasználó|Magyarázat|
|--|--|--|
|A tulajdonos szerzői kulcs| Legfeljebb 1000 végpont találatok|
|A közreműködők Authoring Tool kulcsok| Legfeljebb 1000 végpont találatok|
|Hozzáadva a végpont kulcsok **[közzététel](luis-how-to-publish-app.md)** lap|Tulajdonos és közreműködő végpont kulcsokat adhat hozzá|

Egyéb szerzői vagy a végpont kulcsok **nincs** hozzáférést.

### <a name="public-app-endpoint-access"></a>Nyilvános alkalmazás-végpont hozzáférés
Konfigurálja az alkalmazást **nyilvános** a a **beállítások** az alkalmazás oldalán. Ha egy alkalmazás van konfigurálva, nyilvános, _bármely_ szerzői vagy LUIS végponti kulcs érvényes LUIS lekérdezheti az alkalmazás mindaddig, amíg a kulcsot nem használta a teljes endpoint kvótát.

A felhasználó, aki nem tulajdonosa vagy közreműködő, csak hozzáférhet a nyilvános app Ha adja meg az alkalmazás azonosítóját. A LUIS nincs nyilvános _piaci_ vagy egyéb módon, egy nyilvános alkalmazás kereséséhez.  

## <a name="microsoft-user-accounts"></a>A Microsoft felhasználói fiókok
Szerzők és a közreműködők adhat hozzá kulcsok LUIS a közzétételi oldalon. A Microsoft felhasználói fiók, amely létrehozza a LUIS-kulcsot az Azure Portalon vagy az alkalmazás tulajdonosa, vagy egy alkalmazás közreműködő kell. 

Lásd: [Azure Active Directory-bérlő felhasználói](luis-how-to-account-settings.md#azure-active-directory-tenant-user) további információ az Active Directory felhasználói fiókokat. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>A végpont védelme 
Szabályozhatja, hogy ki láthatja a LUIS végpontkulcsának kiszolgálók-környezetben meghívásával. Ha egy robot a LUIS használ, a robot és a LUIS közötti kapcsolat már biztonságos. Ha közvetlenül, hogy a LUIS-végpontot hív, érdemes létrehoznia egy kiszolgálóoldali API-t (például az Azure-beli [függvény](https://azure.microsoft.com/services/functions/)) szabályozott hozzáféréssel rendelkező (például [AAD](https://azure.microsoft.com/services/active-directory/)). Ha a kiszolgálói oldalon az API és a hitelesítési és engedélyezési ellenőrzése, továbbítja a LUIS-be. Amíg ez a stratégia nem man-in-the-middle támadások megelőzése, access, nyomon követheti a felhasználók számára, a végpont obfuscates, és lehetővé teszi, hogy a végpont válasz naplózás hozzáadása (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Biztonsági megfelelőségi
A LUIS sikeresen befejeződött, az ISO 27001:2013 és 27018:2014 ISO auditálás hatálya a nulla szabálytalanságokat (eredmények) a naplózási jelentésben. Ezenkívül a LUIS is a legmagasabb lehetséges Gold Awards-elismerést kapott a lejárat képesség értékelése a CSA STAR minősítést kapott. Az Azure az egyedüli fő nyilvános felhőszolgáltatót a minősítés megszerzésére el. További részleteket talál a LUIS tartalmazza a frissített scope utasításnak a az Azure fő [megfelelőség áttekintése](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) dokumentum a hivatkozott [biztonsági és adatkezelési központ](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO oldalakat.  

## <a name="next-steps"></a>További lépések

Lásd: [ajánlott eljárások](luis-concept-best-practices.md) hogyan használja a legjobb előrejelzéseket szándékokat és entitásokat.