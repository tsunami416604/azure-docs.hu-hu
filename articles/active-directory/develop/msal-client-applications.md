---
title: Nyilvános és bizalmas ügyfélalkalmazások (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: A nyilvános ügyfél- és bizalmas ügyfélalkalmazásokról a Microsoft hitelesítési tárában (MSAL) olvashat.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d59819c0ab614b0f6cc102c7ebe8c760fb851599
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084117"
---
# <a name="public-client-and-confidential-client-applications"></a>Nyilvános ügyfél- és bizalmas ügyfélalkalmazások
A Microsoft Authentication Library (MSAL) kétféle ügyfelet határoz meg: nyilvános és bizalmas ügyfeleket. A két ügyféltípust az különbözteti meg, hogy képesek biztonságosan hitelesíteni magukat az engedélyezési kiszolgálóval, és megőrizni az ügyfélhitelesítő adatok titkosságát. Ezzel szemben az Azure AD hitelesítési könyvtár (ADAL) az úgynevezett *hitelesítési környezet (amely* az Azure AD-vel való kapcsolat) használja.

- **A bizalmas ügyfélalkalmazások** olyan alkalmazások, amelyek kiszolgálókon (webalkalmazásokon, web API-alkalmazásokon vagy akár szolgáltatás-/démonalkalmazásokon) futnak. Nehezen hozzáférhetőnek tartják őket, és ezért képesek titokban tartani egy alkalmazást. A bizalmas ügyfelek konfigurációs idejű titkos kulcsokat tarthatnak. Az ügyfél minden példánya külön konfigurációval rendelkezik (beleértve az ügyfélazonosítót és az ügyféltitkos kulcsot). Ezeket az értékeket a végfelhasználók nehezen bontják ki. A webalkalmazás a leggyakoribb bizalmas ügyfél. Az ügyfélazonosító a webböngészőn keresztül érhető el, de a titkos kulcsot csak a hátsó csatornában adják át, és soha nem teszik elérhetővé.

    Bizalmas ügyfélalkalmazások: <BR>
    ![Webalkalmazás](media/msal-client-applications/web-app.png) ![Webes](media/msal-client-applications/web-api.png) ![API-démona/szolgáltatása](media/msal-client-applications/daemon-service.png)

- **A nyilvános ügyfélalkalmazások** olyan alkalmazások, amelyek eszközökön vagy asztali számítógépeken vagy webböngészőben futnak. Nem megbízható, hogy biztonságosan tartsa az alkalmazás titkos kulcsokat, így csak a felhasználó nevében férnek hozzá a webes API-khoz. (Csak a nyilvános ügyfélfolyamatokat támogatják.) A nyilvános ügyfelek nem tarthatnak meg konfigurációs idejű titkokat, így nem rendelkeznek ügyféltitkokkal.

    Nyilvános ügyfélalkalmazások: <BR>
    ![Asztali](media/msal-client-applications/desktop-app.png) ![alkalmazás böngésző](media/msal-client-applications/browserless-app.png) ![nélküli API Mobile alkalmazás](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> Az MSAL.js-ben a nyilvános és a bizalmas ügyfélalkalmazások nem különítik el.  Az MSAL.js az ügyfélalkalmazásokat felhasználói ügynök-alapú alkalmazásként jelöli, nyilvános ügyfelekként, amelyekben az ügyfélkódot egy felhasználói ügynökben, például egy webböngészőben hajtják végre. Ezek az ügyfelek nem tárolnak titkokat, mert a böngésző környezete nyíltan elérhető.

## <a name="comparing-the-client-types"></a>Az ügyféltípusok összehasonlítása
Íme néhány hasonlóság és különbség a nyilvános ügyfél- és a bizalmas ügyfélalkalmazások között:

- Mindkét típusú alkalmazás felhasználói jogkivonat-gyorsítótárat tart fenn, és csendesen szerezhet be egy jogkivonatot (ha a token már a token gyorsítótárában van). A bizalmas ügyfélalkalmazások is rendelkeznek egy alkalmazástoken-gyorsítótárral az alkalmazáshoz készült tokenek hez.
- Mindkét típusú alkalmazás kezelheti a felhasználói fiókokat, és beszerezhet egy fiókot a felhasználói jogkivonat gyorsítótárából, fiókot kaphat az azonosítójából, vagy eltávolíthat egy fiókot.
- A nyilvános ügyfélalkalmazások négy módon szerezhetnek be egy jogkivonatot (négy hitelesítési folyamat). A bizalmas ügyfélalkalmazások három módon szerezhetnek be egy jogkivonatot (és az identitásszolgáltató engedélyezett végpontjának URL-címének kiszámításának egyik módja). További információ: [Tokenek beszerzése](msal-acquire-cache-tokens.md).

Ha már használta az ADAL, észreveheti, hogy ellentétben az ADAL hitelesítési környezetben, az ügyfélazonosító (más néven az *alkalmazás azonosítója* vagy *alkalmazásazonosító)* az alkalmazás építésekor egyszer kerül átadásra. Nem kell újra átadni, amikor az alkalmazás beszerez egy jogkivonatot. Ez mind a nyilvános, mind a bizalmas ügyfélalkalmazásokra igaz. A bizalmas ügyfélalkalmazások konstruktorai is átadott ügyfélhitelesítő adatok: a titkos, hogy megosszák az identitásszolgáltatóval.

## <a name="next-steps"></a>További lépések
Ismerkedjen meg a következőkkel:
- [Ügyfélalkalmazás konfigurációs beállításai](msal-client-application-configuration.md)
- [Ügyfélalkalmazások példányosítása MSAL.NET](msal-net-initializing-client-applications.md)
- [Ügyfélalkalmazások példányosítása az MSAL.js használatával](msal-js-initializing-client-applications.md)
