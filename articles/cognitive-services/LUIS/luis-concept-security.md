---
title: LUIS alkalmazások – Azure hozzáférést megértése |} Microsoft Docs
description: Útmutató a szerzői LUIS eléréséhez.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 44380e12e6d095e8d40675af0b6b2fddc5e4c4e9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264267"
---
# <a name="authoring-and-endpoint-user-access"></a>Jelentéskészítő és -végpont felhasználói hozzáférés
Szerzői hozzáférés tulajdonosok és a közreműködők érhető el. Személyes alkalmazások esetén végpont is elérhető a tulajdonosok és a közreműködők. A nyilvános alkalmazás végpont akkor elérhető az összes felhasználója számára a saját LUIS fiókkal rendelkezik, és rendelkezik a nyilvános app ID azonosítóval. 

## <a name="access-to-authoring"></a>Szerzői elérésére
Az alkalmazáshoz való hozzáférés a [LUIS] [ LUIS] webhelyen vagy a [API-k szerzői](https://aka.ms/luis-authoring-apis) az alkalmazás tulajdonosa vezérli. 

A tulajdonos és összes közreműködők ahhoz, hogy az alkalmazás hozzáférjen. 

|Szerzői hozzáférés tartalmazza|Megjegyzések|
|--|--|
|Hozzáadni vagy eltávolítani a végpont-kulcsok||
|Verzió exportálása||
|Végpont logs exportálása||
|Verzió importálása||
|Alkalmazás nyilvános tétele|Ha egy alkalmazás nyilvános, bárki szerzői vagy a végpont kulccsal rendelkező lekérdezheti az alkalmazás.|
|Modell módosítása|
|Közzététel|
|Tekintse át a végpont utterances [aktív tanulás](label-suggested-utterances.md)|
|Tanítás|

## <a name="access-to-endpoint"></a>Végpont elérésére
A végpontot a lekérdezés LUIS elérésére vezérli a **nyilvános** az alkalmazás beállítása a **beállítások** lap. A személyes alkalmazások végpont lekérdezés be van jelölve, egy hitelesített kulcsok kvóta találatok fennmaradó esetében. A nyilvános app végpont lekérdezés tartalmaz egy végpont kulcsot (a személy, aki a lekérdezést, hogy így) is így amely is be van jelölve, a fennmaradó kvóta találatok. 

A végpont kulcs vagy a lekérdezési karakterláncban a GET kérelem átadása, vagy kérje meg a FELADÁS egy vagy több fejléce.

![A nyilvános set app](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>Saját alkalmazás végpontok közötti védelem
A személyes alkalmazások végpont csak érhető el a következőhöz:

|Kulcs és a felhasználó|Magyarázat|
|--|--|--|
|A tulajdonos szerzői kulcs| Legfeljebb 1000 végpont találatok|
|A közreműködők szerzői kulcsok| Legfeljebb 1000 végpont találatok|
|A hozzáadott végpont kulcsok **[közzététel](publishapp.md)** lap|Virtuális gép tulajdonosaként és közreműködők adhat hozzá a végpont-kulcsok|

Más szerzői vagy a végpont kulcsokhoz **nem** hozzáférést.

### <a name="public-app-endpoint-access"></a>Nyilvános hozzáférésének végpont
Konfigurálja az alkalmazást **nyilvános** a a **beállítások** az alkalmazás lapján. Ha egy alkalmazás van konfigurálva, nyilvános, _bármely_ szerzői vagy LUIS végpont kulcs érvényes LUIS lekérdezheti az alkalmazást, mindaddig, amíg a kulcsot nem használta a teljes végpont kvótát.

Nincs olyan tulajdonos vagy közreműködő, akik érhetnek el a felhasználók csak a nyilvános app Ha az alkalmazás azonosítóját. LUIS nem rendelkezik nyilvános _piaci_ vagy más módon nyilvános alkalmazások kereséséhez.  

## <a name="microsoft-user-accounts"></a>A Microsoft felhasználói fiókok
Szerzők és közreműködők adhat hozzá kulcsok LUIS a közzététel oldalon. A Microsoft felhasználói fiók, amely a LUIS kulcsot hoz létre az Azure portálon vagy az alkalmazás tulajdonosa, vagy egy alkalmazás közreműködő kell. 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>A végpont védelme 
Azt is szabályozhatja, hogy ki láthatja a LUIS végpontkulcs kiszolgálók környezetben meghívásával. A bot LUIS használ, ha a kapcsolatot a botot és LUIS már biztonságos. Hívásakor a LUIS végpont közvetlenül, készítsen egy kiszolgálóoldali API (például egy Azure [függvény](https://azure.microsoft.com/services/functions/)) ellenőrzött hozzáféréssel rendelkező (például [AAD](https://azure.microsoft.com/services/active-directory/)). Ha a kiszolgálóoldali API nevezik, és a hitelesítési és engedélyezési ellenőrzése, továbbítja a LUIS be. Amíg ezt a stratégiát-átjárójának nem megelőzése érdekében obfuscates a végponthoz, a felhasználók a hozzáférés, nyomon követheti, és lehetővé teszi a végpont válasz naplózás (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="security-compliance"></a>Biztonsági megfelelőség
LUIS sikeresen befejeződött, az ISO-27001:2013 és ISO 27018:2014 naplózása a nulla nem neve (eredmények) a naplózási jelentés. Emellett LUIS is kapott a lejárat funkció értékelési legmagasabb lehetséges arany odaítélésére CSA csillag hitelesítéssel. Azure a fő csak nyilvános felhőszolgáltatótól vett szolgáltatás érvényessége alatt a hitelesítésszolgáltató. A részleteket megtalálja a LUIS tartalmazza a frissített scope utasítás az Azure fő [megfelelőség áttekintése](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) hivatkozott dokumentum [biztonsági és adatkezelési központ](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO lapokat.  

## <a name="next-steps"></a>További lépések

Lásd: [gyakorlati tanácsok](luis-concept-best-practices.md) megtudhatja, hogyan használható az ajánlott előrejelzés leképezések és entitások.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website