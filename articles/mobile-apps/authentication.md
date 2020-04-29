---
title: Hitelesítés hozzáadása a Mobile appshez Visual Studio App Center és Azure-szolgáltatásokkal
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint például a Visual Studio App Center, amelyek segítségével beállíthatja a felhasználói hitelesítést, és engedélyezheti a mobil alkalmazások számára a közösségi fiókok, Azure Active Directory és egyéni hitelesítések hitelesítését.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241048"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Hitelesítés hozzáadása és felhasználói identitások kezelése a Mobile apps szolgáltatásban

A felhasználó nézete és az alkalmazáson belüli viselkedésük lehetővé teszi a fejlesztők számára, hogy a felhasználók számára testre szabott tapasztalatokat hozzanak létre. Akár olyan alkalmazás-fejlesztő, aki együttműködési alkalmazást készít a szervezeten belüli felhasználók számára, vagy a következő közösségi hálózati platformot hozza létre, szüksége lesz a felhasználók hitelesítésére és a felhasználói identitások kezelésére. Az Identity Management szolgáltatás a mobil háttérrendszer egyik legfontosabb funkciója.

A következő szolgáltatások segítségével engedélyezheti a felhasználók hitelesítését a Mobile apps szolgáltatásban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Auth](/appcenter/auth/) egy felhőalapú identitáskezelési szolgáltatás, amelyet a fejlesztők a felhasználók hitelesítésére és a felhasználói identitások kezelésére használhatnak. App Center hitelesítés a Visual Studio App Center egyéb részeivel is integrálva van. A fejlesztők a felhasználói identitás használatával [megtekinthetik a felhasználói adatait](/appcenter/data/index) más szolgáltatásokban, sőt [leküldéses értesítéseket is küldhetnek a felhasználóknak az egyes eszközök helyett](/appcenter/push/push-to-user#setting-user-identity). 

**A legfontosabb jellemzők**
- Powered by Azure Active Directory B2C (Azure AD B2C). 
    - Nagyvállalati szintű.
    - Magasan elérhető.
    - Biztonságos és globális szolgáltatás.
- Használhatja a saját identitását és a más népszerű identitás-és hozzáférés-kezelési szolgáltatók használatát, például a Auth0 és a Firebase szolgáltatást.
- Azure Active Directory támogatás.
    - Meglévő Azure AD-bérlők összekapcsolhatók. 
    - Vállalati tartományon belüli hitelesítés engedélyezése.
    - Bizalmas adatokhoz való hozzáférés kezelése.
- Egyszerű felhasználói élmény és a varázslatos SDK-élmény a Microsoft hitelesítési könyvtárának a Visual Studio App Center SDK-val való becsomagolásával.
- Platform-támogatás iOS-, Android-, Xamarin-és natív reagáláshoz.

**Referencia**
- [Regisztráció Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [App Center Auth – első lépések](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
A [Azure ad B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy vállalat – felhasználó (B2C) Identitáskezelés szolgáltatás, amellyel a fejlesztők hitelesítheti ügyfeleit. Ez a White-Label szolgáltatás lehetővé teszi a fejlesztők számára a webes, asztali, mobil vagy egyoldalas alkalmazásokkal való biztonságos interakciót. A Azure AD B2C használatával a felhasználók regisztrálhatnak, bejelentkezhetnek, visszaállíthatják a jelszavakat és szerkeszthetik a profilokat. Azure AD B2C az OpenID Connect és a OAuth 2,0 protokollok formáját valósítja meg. 

**A legfontosabb jellemzők**
- Biztonságosan hitelesítheti az ügyfeleket az előnyben részesített identitás-szolgáltatóval.
- Az ügyfél identitásának és hozzáférésének kezelése.
- A közösségi média (például a Facebook, a GitHub, a Google, a LinkedIn, a Twitter, a WeChat és a WeiBo) bejelentkezési támogatását veheti igénybe.
- A felhasználói fiókokhoz az iparági szabványnak megfelelő protokollok, például az OpenID Connect vagy az SAML használatával csatlakozhat, hogy az Identitáskezelés számos platformon lehetséges legyen.
- Adja meg a márkás regisztrációs és bejelentkezési élményeket.
- Egyszerűen integrálható a CRM-adatbázisokkal, a marketing-elemzési eszközökkel és a fiók-ellenőrzési rendszerekkel.
- A bejelentkezési, a preferencia-és a konverziós adatmennyiség rögzítése az ügyfelek számára.

**Referencia**
- [Azure Portal](https://portal.azure.com/)
- [Azure AD B2C dokumentáció](/azure/active-directory-b2c/)
- [Rövid útmutatók](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Példák](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása, amely segít az alkalmazottaknak bejelentkezni, és hozzáférni a következőkhöz:
- Külső erőforrások, például Microsoft Office 365, a Azure Portal és több ezer más szolgáltatott szoftver (SaaS) alkalmazás.
- Belső erőforrások, például a vállalati hálózaton és az intraneten lévő alkalmazások, valamint a saját szervezete által fejlesztett felhőalapú alkalmazások.

**A legfontosabb jellemzők**
- Zökkenőmentes és biztonságos hozzáférés a felhasználók és a szükséges alkalmazások összekapcsolásával.
- Átfogó identitásvédelem és fokozott biztonság az identitások és a hozzáférés számára a felhasználók, a helyek, az eszközök, az adatok és az alkalmazások kontextusa alapján.
- Több ezer előre integrált alkalmazás a kereskedelmi és egyéni alkalmazásokhoz, például az Office 365, a Salesforce.com és a Box szolgáltatásokhoz.
- A hozzáférés nagy léptékű kezelésének lehetősége.

**Referencia**
- [Azure Portal](https://portal.azure.com/)
- [Mi az Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Ismerkedés az Azure Active Directoryval](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rövid útmutatók](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)