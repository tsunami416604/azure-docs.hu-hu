---
title: Hitelesítés hozzáadása a Mobile appshez Visual Studio App Center és Azure-szolgáltatásokkal
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint például a App Center, amelyek segítségével beállíthatja a felhasználói hitelesítést, és engedélyezheti a mobil alkalmazások számára a közösségi fiókok, Azure Active Directory és egyéni hitelesítések hitelesítését.
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8885f6cd91179f4dee6456277d47581f68c21723
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795756"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Hitelesítés hozzáadása és felhasználói identitások kezelése a Mobile apps szolgáltatásban

A felhasználó nézete és az alkalmazáson belüli viselkedésük lehetővé teszi a fejlesztők számára, hogy a felhasználók számára testre szabott tapasztalatokat hozzanak létre. Akár a szervezeten belüli felhasználók számára, akár a következő közösségi hálózati platformot létrehozó alkalmazás fejlesztője van, szüksége lesz a felhasználók hitelesítésére és a felhasználói identitások kezelésére. Az Identity Management szolgáltatás a mobil háttérrendszer egyik legfontosabb funkciója.

A következő szolgáltatások segítségével engedélyezheti a felhasználók hitelesítését a Mobile apps szolgáltatásban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Auth](/appcenter/auth/) egy felhőalapú Identitáskezelő szolgáltatás, amely lehetővé teszi a fejlesztők számára a felhasználók hitelesítését és a felhasználói identitások kezelését. A App Center-hitelesítés a App Center más részeivel is integrálva van, így a fejlesztők a felhasználói identitást kihasználva más szolgáltatásokban [tekinthetik meg a felhasználói adatait](/appcenter/data/index) , és nem [az egyes eszközök helyett leküldéses értesítéseket is küldhetnek](/appcenter/push/push-to-user#app-center-auth-set-identity). 

**Főbb funkciók**
- **Powered by Azure Active Directory B2C (Azure AD B2C)** 
    - Nagyvállalati szintű.
    - Magasan elérhető.
    - Biztonságos és globális szolgáltatás.
- **Saját identitása** és más népszerű identitás-és hozzáférés-kezelési szolgáltatók, például a Auth0 és a Firebase használata.
- **HRE-támogatás** 
    - Meglévő HRE-bérlők összekapcsolhatók. 
    - Vállalati tartományon belüli hitelesítés engedélyezése.
    - Bizalmas adatokhoz való hozzáférés kezelése.
- **Egyszerű felhasználói élmény** és a varázslatos SDK-élmény a MSAL Library app Center SDK-val való becsomagolásával.
- **Platform-támogatás** – iOS, Android, Xamarin, natív reagálás.

**Hivatkozik**
- [Regisztráció App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [App Center Auth – első lépések](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
A [Azure ad B2C](https://azure.microsoft.com/services/active-directory-b2c/) a vállalattól a fogyasztótól származó Identitáskezelő szolgáltatás, amely lehetővé teszi a fejlesztők számára az ügyfelek hitelesítését. Ez a White-Label szolgáltatás lehetővé teszi a fejlesztők számára a webes, asztali, mobil vagy egyoldalas alkalmazásokkal való biztonságos interakciót. A Azure AD B2C használatával a felhasználók regisztrálhatnak, bejelentkezhetnek, visszaállíthatják a jelszavakat és szerkeszthetik a profilokat. Azure AD B2C az OpenID Connect és a OAuth 2,0 protokollok formáját valósítja meg. 

**Főbb funkciók**
- Biztonságosan hitelesítheti az ügyfeleket az előnyben részesített identitás-szolgáltatóval.
- **Ügyfél-identitás és hozzáférés-kezelés**.
- A közösségi bejelentkezések támogatottak, például a Facebook, a GitHub, a Google, a LinkedIn, a Twitter, a WeChat és a WeiBo.
- A felhasználói fiókokhoz az **iparági szabványnak megfelelő protokollok** , például az OpenID Connect vagy az SAML használatával lehet csatlakozni, hogy számos platformon lehetővé váljon az Identitáskezelés.
- Adja meg a márkás regisztrációs és bejelentkezési élményeket.
- Egyszerűen integrálható a CRM-adatbázisokkal, a marketing-elemzési eszközökkel és a fiók-ellenőrzési rendszerekkel.
- Az ügyfelekhez tartozó bejelentkezési, beállítási és konverziós adatok rögzítése.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com/)
- [Azure AD B2C dokumentáció](/azure/active-directory-b2c/)
- [Rövid útmutatók](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Minták](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása, amely segítséget nyújt az alkalmazottak számára a bejelentkezéshez és a hozzáféréshez.
- Külső erőforrások, például Microsoft Office 365, a Azure Portal és több ezer más SaaS-alkalmazás.
- Belső erőforrások, például a vállalati hálózaton és az intraneten lévő alkalmazások, valamint a saját szervezete által fejlesztett felhőalapú alkalmazások.

**Főbb funkciók**
- **Zökkenőmentes és biztonságos hozzáférés** a felhasználók és a szükséges alkalmazások összekapcsolásával.
- **Átfogó identitásvédelem** és fokozott biztonság az identitások és a hozzáférés számára a felhasználók, a helyek, az eszközök, az adatok és az alkalmazások kontextusa alapján.
- **Több ezer előre integrált** alkalmazás kereskedelmi és egyéni alkalmazásokkal, például az Office 365, a Salesforce.com és a Box szolgáltatással.
- Nagy **léptékű hozzáférés kezelése**.

**Hivatkozik**
- [Azure Portalra](https://portal.azure.com/)
- [Mi az Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Ismerkedés az Azure Active Directoryval](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rövid útmutatók](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)