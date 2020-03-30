---
title: Az alkalmazások láthatósága és vezérlése a Microsoft Cloud App Security alkalmazásbiztonsággal
description: Ismerje meg, hogyan azonosíthatja az alkalmazáskockázati szinteket, hogyan állíthatja le a kiszivárgásokat és a szivárgásokat valós időben, és használhatja az alkalmazásösszekötőket a szolgáltatói API-k használatának kihasználására a láthatóság és a szabályozás érdekében.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77069741"
---
# <a name="cloud-app-visibility-and-control"></a>Felhőalkalmazások láthatósága és vezérlése

A felhőalapú alkalmazások és szolgáltatások teljes körű kihasználása érdekében az informatikai csapatnak meg kell találnia a megfelelő egyensúlyt a hozzáférés támogatásához, miközben a kritikus adatok védelme érdekében is biztosítania kell a vezérlést. A Microsoft Cloud App Security gazdag láthatóságot, az adatforgalom szabályozását és kifinomult elemzéseket biztosít a kiberfenyegetések azonosítása és leküzdése érdekében az összes Microsoft- és külső felhőszolgáltatásban.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Árnyék-infrastruktúra feltárása és kezelése a hálózatban

Amikor a rendszer megkérdezi az informatikai rendszergazdákat, hogy szerintük hány felhőalapú alkalmazást használnak, átlagosan 30-at vagy 40-et mondanak, amikor a valóságban az átlag több mint 1000 különálló alkalmazást használ a szervezet alkalmazottai által. Az árnyékinformatikai funkció segítségével megállapíthatja és azonosíthatja, hogy mely alkalmazásokat használja, és mi a kockázati szintje. Az alkalmazottak nyolcvan százaléka olyan nem engedélyezett alkalmazásokat használ, amelyeket senki sem tekintett át, és nem biztos, hogy megfelel az Ön biztonsági és megfelelőségi irányelveinek. Mivel az alkalmazottak a vállalati hálózaton kívülről is hozzáférhetnek az erőforrásokhoz és az alkalmazásokhoz, már nem elegendő a tűzfalakon lévő szabályok és szabályzatok.

A Microsoft Cloud App Discovery (egy Azure Active Directory Premium P1 funkció) segítségével felderítheti, hogy mely alkalmazásokat használja, fedezze fel az alkalmazások kockázatát, konfigurálja a szabályzatokat az új kockázatos alkalmazások azonosítására, és ne hagyja jóváeznem ezeket az alkalmazásokat annak érdekében, hogy letiltsa őket natív módon a proxy vagy tűzfal készülék.

- Árnyék-infrastruktúra feltárása és azonosítása
- Értékelés és elemzés
- Alkalmazások kezelése
- Speciális árnyékinformatikai felderítési jelentéskészítés
- Szankcionált alkalmazások szabályozása
 
### <a name="learn-more"></a>Részletek

- [Az árnyékinformatikai késedelmfelderítés és -kezelés a hálózatban](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Felderített alkalmazások a Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>A felhasználói munkamenet láthatósága és vezérlése 

A mai munkahelyen gyakran nem elég tudni, hogy mi történik a felhőkörnyezetben a tény után. Valós időben szeretné megakadályozni a szabálysértéseket és a szivárgásokat, mielőtt az alkalmazottak szándékosan vagy véletlenül veszélybe sodornák az adatokat és a szervezetet. Az Azure Active Directoryval (Azure AD) együtt a Microsoft Cloud App Security ezeket a képességeket a feltételes hozzáférés-hozzáférés-vezérléssel holisztikus és integrált környezetben nyújtja. 

A munkamenet-vezérlés fordított proxyarchitektúrát használ, és egyedülálló módon integrálva van az Azure AD feltételes hozzáféréssel. Az Azure AD feltételes hozzáférés lehetővé teszi, hogy bizonyos feltételek alapján kényszerítse a szervezet alkalmazásainak hozzáférés-vezérlését. A feltételek határozzák meg, hogy kire (felhasználó vagy felhasználócsoport) és mit (mely felhőalapú alkalmazásokra) és hol (mely helyekre és hálózatokra) alkalmazza a feltételes hozzáférési szabályzat. Miután meghatározta a feltételeket, átirányíthatja a felhasználókat a Cloud App Security szolgáltatásba, ahol valós időben védheti az adatokat.  

Ezzel a vezérlővel a következőket teheti:  
- Fájlletöltések szabályozása
- B2B-forgatókönyvek figyelése  
- Fájlokhoz való hozzáférés szabályozása  
- A letöltéssel kapcsolatos dokumentumok védelme  
 
### <a name="learn-more"></a>Részletek

- [Alkalmazások védelme a Munkamenet-felügyelet szolgáltatással a Cloud App Security szolgáltatásban](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Az alkalmazások fejlett láthatósága és vezérlése 

Az alkalmazásösszekötők az alkalmazásszolgáltatók API-jait használják, hogy a Microsoft Cloud App Security nagyobb láthatóságot és vezérlést tegyenek lehetővé a kapcsolódó alkalmazások felett. A Cloud App Security a felhőszolgáltató által biztosított API-kat használja. Minden szolgáltatás saját keretrendszer- és API-korlátozásokkal rendelkezik, például a szabályozással, az API-korlátokkal, a dinamikus időváltással elváltozó API-ablakokkal és más szolgáltatásokkal. A Cloud App Security termékcsapata ezekkel a szolgáltatásokkal együttműködve optimalizálta az API-k használatát, és a legjobb teljesítményt nyújtotta. Figyelembe véve a különböző korlátozások at API-k, a Cloud App Security motorok a maximálisan megengedett kapacitást. Egyes műveletek, például a bérlőösszes fájl beolvasása, számos API-hívást igényelnek, így hosszabb ideig vannak elosztva. Egyes házirendek futtatása több órán vagy napon keresztül. 
 
### <a name="learn-more"></a>Részletek  

- [Alkalmazások csatlakoztatása a Cloud App Security szolgáltatásban](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>További lépések

- [Az árnyékinformatikai késedelmfelderítés és -kezelés a hálózatban](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Felderített alkalmazások a Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Alkalmazások védelme a Munkamenet-felügyelet szolgáltatással a Cloud App Security szolgáltatásban](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Alkalmazások csatlakoztatása a Cloud App Security szolgáltatásban](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
