---
title: Alkalmazások láthatósága és vezérlése Microsoft Cloud App Security
description: Megtudhatja, hogyan azonosíthatók az alkalmazások kockázati szintjei, hogyan állíthatók be valós időben a behatolások és a szivárgások, és hogyan használhatók az alkalmazás-összekötők a szolgáltatói API-k kihasználása érdekében.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 02/03/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e1a06acddae0973d5dbc7e64212026149f217
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069741"
---
# <a name="cloud-app-visibility-and-control"></a>Felhőbeli alkalmazások láthatósága és vezérlése

A felhőalapú alkalmazások és szolgáltatások teljes körű kihasználása érdekében az informatikai csapatnak meg kell találnia a megfelelő egyensúlyt a hozzáférés támogatásához, miközben a kritikus fontosságú adat védelme érdekében meg kell őrizni a szabályozást. A Microsoft Cloud App Security széles körű láthatóságot, az adatutazások vezérlését és a kifinomult elemzési lehetőségeket nyújt a számítógépes fenyegetések azonosítására és leküzdésére a Microsoft és harmadik felek felhőalapú szolgáltatásaiban.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>A hálózatban lévő árnyék felderítése és kezelése

Ha a rendszergazdák azt kérik, hogy hány Felhőbeli alkalmazás szerint használják az alkalmazottaikat, átlagosan 30 vagy 40, a valóságban az átlag több mint 1 000 külön alkalmazást használ a szervezet alkalmazottai által. Az árnyék segítségével megismerheti és azonosíthatja, hogy mely alkalmazások vannak használatban, és milyen kockázati szinttel rendelkezik. Az alkalmazottak 80 százaléka olyan nem engedélyezett alkalmazásokat használ, amelyek közül senki nem tekintett át, és nem felel meg a biztonsági és megfelelőségi szabályzatoknak. Mivel az alkalmazottai a vállalati hálózaton kívülről is hozzáférhetnek az erőforrásokhoz és alkalmazásokhoz, már nem elég, ha a tűzfalakon vannak szabályok és szabályzatok.

A Microsoft Cloud alkalmazás-felderítés (egy prémium szintű Azure Active Directory P1 szolgáltatás) használatával felderítheti, hogy mely alkalmazások vannak használatban, hogyan derítheti fel az alkalmazások kockázatát, házirendeket konfigurálhat az új kockázatos alkalmazások azonosításához és az alkalmazások visszavonásához, hogy a rendszer natív módon blokkolja azokat proxy vagy tűzfal készülék.

- AZ árnyék felderítése és azonosítása
- Kiértékelés és elemzés
- Alkalmazások kezelése
- Speciális Shadow IT Discovery-jelentéskészítés
- Engedélyezett alkalmazások vezérlése
 
### <a name="learn-more"></a>További információk

- [A hálózatban lévő árnyék felderítése és kezelése](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Felderített alkalmazások Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Felhasználói munkamenet láthatósága és vezérlése 

Az aktuális munkaterületen esetében gyakran nem elég tudnia, mi történik a felhőkörnyezetben bekövetkeztek. Valós időben szeretné leállítani a betöréseket és a szivárgásokat, mielőtt az alkalmazottak szándékosan vagy véletlenül az adataikat és a szervezetét veszélyeztetik. Az Azure Active Directory (Azure AD) szolgáltatással együtt a Microsoft Cloud App Security átfogó és integrált felhasználói élményt nyújt a feltételes hozzáférést biztosító alkalmazás-vezérlő. 

A munkamenet-vezérlő fordított proxy-architektúrát használ, és egyedien integrálható az Azure AD feltételes hozzáférésével. Az Azure AD feltételes hozzáférés használatával bizonyos feltételek alapján kényszerítheti a szervezet alkalmazásaihoz való hozzáférés-vezérlést. A feltételek határozzák meg, hogy kik (felhasználók vagy csoportok) és mi (mely Felhőbeli alkalmazások) és hol (mely helyekről és hálózatokból) a feltételes hozzáférési szabályzat érvényes. A feltételek meghatározása után átirányíthatja a felhasználókat Cloud App Securityba, ahol valós időben biztosíthatja az adatvédelmet.  

Ezzel a vezérlővel a következőket teheti:  
- Fájlok letöltésének szabályozása
- B2B-forgatókönyvek figyelése  
- Fájlok elérésének szabályozása  
- Dokumentumok védelemmel való ellátása letöltéskor  
 
### <a name="learn-more"></a>További információk

- [Alkalmazások elleni védelem a Cloud App Security munkamenet-vezérléssel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Speciális alkalmazások láthatósága és vezérlése 

Az alkalmazás-összekötők az alkalmazásszolgáltatók API-kkal használják, hogy nagyobb láthatóságot és felügyelhető a Microsoft Cloud App Security által az alkalmazásokat. Cloud App Security kihasználja a felhőalapú szolgáltató által biztosított API-kat. Minden szolgáltatásnak van saját keretrendszerrel és API-korlátozásai vannak, például a szabályozás, API-t korlátok, a dinamikus időeltolású API windows és mások. A Cloud App Security termék csapata ezekkel a szolgáltatásokkal optimalizálja az API-k használatát, és biztosítja a legjobb teljesítményt. A különböző korlátozási szolgáltatásokat használó API-k esetében a Cloud App Security motorok a maximálisan megengedett kapacitást használják. Egyes műveletek, például a bérlő összes fájljának vizsgálata, számos API-hívást igényelnek, hogy hosszabb időn keresztül legyenek elosztva. Bizonyos házirendek várhatóan több óráig vagy napon futnak. 
 
### <a name="learn-more"></a>További információk  

- [Alkalmazások összekötése Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Következő lépések

- [A hálózatban lévő árnyék felderítése és kezelése](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Felderített alkalmazások Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Alkalmazások elleni védelem a Cloud App Security munkamenet-vezérléssel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Alkalmazások összekötése Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
