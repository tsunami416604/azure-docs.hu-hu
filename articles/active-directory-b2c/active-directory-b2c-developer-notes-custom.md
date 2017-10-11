---
title: "Az Azure Active Directory B2C: Megjegyzések egyéni házirendek segítségével a fejlesztők számára |} Microsoft Docs"
description: "Megjegyzések fejlesztők konfigurálása és karbantartása az Azure AD B2C egyéni házirendekkel"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Az Azure Active Directory B2C egyéni házirend nyilvános előzetes kibocsátási megjegyzései
Az egyéni házirend szolgáltatáskészlet már elérhető az összes Azure Active Directory B2C előzetes verzióját nyilvános értékelésre (az Azure AD B2C) ügyfelek. E szolgáltatáskészlet speciális identitás fejlesztők a legösszetettebb identitáskezelési megoldások kialakításának irányul.  

E szolgáltatáskészlet jelenleg a identitás élmény keretrendszer keresztül közvetlenül XML-fájl szerkesztésével konfigurálhatja a fejlesztők van szükség. A konfigurációs módszer hatékony és összetett. Speciális identitás identitás élmény keretrendszerrel fejlesztők kell terveznie a fektetnek útmutatók befejezése és a hivatkozás dokumentumok olvasási időt. 

## <a name="features-included-in-this-public-preview"></a>A nyilvános előzetes szereplő szolgáltatások
A nyilvános előzetes verziójában bevezetett új funkciókkal fejlesztők számára a következő feladatokat végezheti el:<br>

* Szerző és feltöltése az egyéni hitelesítési felhasználói utak egyéni házirendekkel. 
   * Felhasználói utak cseréjét, részletes Jogcímszolgáltatók közötti ismertetik. 
   * Határozza meg azt a felhasználói utak feltételes ugorhat. 
* Az egyéni hitelesítési felhasználói utak szolgáltatások REST API-t integrálja.  
* Adja hozzá az Identitásszolgáltatók, amelyek megfelelnek a szabványos OpenIDConnect összevonásának. <br>
* Adja hozzá az összevonás az identitás-szolgáltatóktól igazodnia kell a SAML 2.0 protokoll. 

## <a name="terms-of-the-public-preview"></a>A nyilvános előzetes feltételeit.

* Javasoljuk, hogy az új szolgáltatások használatához csak tesztelési célokra.<br>
* Az új szolgáltatások nem feltétlenül egy éles környezetben való használathoz.<br>
* Szolgáltatásszint-szerződések (SLA) nem vonatkoznak az új szolgáltatásokat. <br>
* Támogatási kérelmek nyilvántartásához rendszeres támogatási csatornákon keresztül. <br>
* Nincs megadva általánosan rendelkezésre álló Ígért dátum.<br>
* Saját belátása szerint, és bármilyen okból Microsoft is jelzőt és elutasítása vagy forgatókönyvek és felhasználói útvonal be, amelyek mérete meghaladja az Azure AD B2C termék okleveles felhasználói identitások és hozzáférések (CIAM) felügyeleti platform, mely a körét korlátozza.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Egyéni házirend szolgáltatáskészlet fejlesztők feladatai
Kézi házirend konfigurálása az Azure AD B2C az alapul szolgáló platform alacsonyabb szintű hozzáférést biztosít, és egyedi, teljes mértékben testreszabható megbízhatósági keretrendszer létrehozását eredményezi. Ezek lehetséges kombinációinak egyéni Identitásszolgáltatók, megbízhatósági kapcsolatokat, külső szolgáltatásokkal, és lépésről lépésre munkafolyamatok Integrációk fel őket a speciális fejlesztők nagyobb iránti igények kielégítése érdekében helyezze el.

Teljes mértékben kihasználják a nyilvános előzetes verzióhoz, javasoljuk, hogy a fejlesztők az egyéni házirend szolgáltatáskészlet fel felelnie a következő irányelveket:
* Ismerkedjen meg az identitás élmény motor és a kulcs vagy titkos kulcsok kezelése konfigurációs nyelve.
* Saját tulajdonba forgatókönyveket és a használatot.
* A forgatókönyv módszeres tesztek végrehajtása.
* Hajtsa végre a szoftverek fejlesztési és átmeneti legalább egy fejlesztési és tesztelési környezetben az ajánlott eljárásokról és egy éles környezetben.
* Maradjon naprakész az identitás-szolgáltatóktól és integrálja szolgáltatások új fejlesztéseiről. Például nyomon követheti, változások a titkos kulcsok és a szolgáltatás ütemezett és nem ütemezett módosításait.
* Aktív figyelés beállítása, és figyelje a figyelt termelési környezetben.
* Kapcsolattartási e-mail címek naprakészen tartása, és a Microsoft live hely team e-mailek válaszol marad.
* Ha ehhez a Microsoft live hely csapat által ajánlott időben művelet végrehajtása. 


>[!NOTE]
>Ezek a funkciók végül az Azure AD beépített házirendek, így több elérhető a fejlesztők tartalmazhatja.

## <a name="next-steps"></a>Következő lépések
[Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md).
