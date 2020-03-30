---
title: Hitelesítés hozzáadása mobilalkalmazásaihoz a Visual Studio App Center és az Azure-szolgáltatások segítségével
description: Ismerje meg azokat a szolgáltatásokat, például a Visual Studio App Center, amely segít beállítani a felhasználói hitelesítést, és lehetővé teszi a mobil alkalmazások hitelesítését a közösségi fiókok, az Azure Active Directory és az egyéni hitelesítés.
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241048"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>Hitelesítés hozzáadása és felhasználói identitások kezelése a mobilalkalmazásokban

A felhasználó és az alkalmazáson keresztüli viselkedésük lehetővé teszi a fejlesztők számára, hogy személyre szabott felhasználói élmények létrehozásával jobban bevonják a felhasználókat. Akár olyan alkalmazásfejlesztő, aki együttműködési alkalmazást hoz létre a szervezeten belüli felhasználók számára, vagy a következő közösségi hálózati platformot hozza létre, szüksége van a felhasználók hitelesítésére és a felhasználói identitások kezelésére. Az identitáskezelési szolgáltatás a mobil háttérszolgáltatás egyik legfontosabb szolgáltatása.

Az alábbi szolgáltatások segítségével engedélyezheti a felhasználói hitelesítést a mobilalkalmazásokban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Az App Center Auth](/appcenter/auth/) egy felhőalapú identitáskezelési szolgáltatás, amelyet a fejlesztők a felhasználók hitelesítésére és a felhasználói identitások kezelésére használhatnak. Az App Center Auth a Visual Studio App Center más részeivel is integrálható. A fejlesztők a felhasználói identitás segítségével megtekinthetik a [felhasználói adatokat](/appcenter/data/index) más szolgáltatásokban, és akár [leküldéses értesítéseket is küldhetnek a felhasználóknak az egyes eszközök helyett.](/appcenter/push/push-to-user#setting-user-identity) 

**A legfontosabb jellemzők**
- Az Azure Active Directory B2C (Azure AD B2C) szolgáltatása. 
    - Vállalati besorolási fokozat.
    - Magas rendelkezésre állású.
    - Biztonságos és globális szolgáltatás.
- Hozza létre saját identitását, és más népszerű identitás- és hozzáférés-kezelési szolgáltatók, például az Auth0 és a Firebase használatát.
- Az Azure Active Directory támogatása.
    - Meglévő Azure AD-bérlők csatlakoztatása. 
    - Engedélyezze a hitelesítést egy vállalati tartománnyal szemben.
    - A bizalmas adatokhoz való hozzáférés kezelése.
- Egyszerű felhasználói élmény és varázslatos SDK-élmény a Microsoft Authentication Library és a Visual Studio App Center SDK csomagolásával.
- Platformtámogatás iOS, Android, Xamarin és React Native rendszerhez.

**Referencia**
- [Regisztráció a Visual Studio App Center alkalmazásközpontjában](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [Az App Center Auth – első lépések](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Az Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy vállalat on-consumer (B2C) identitáskezelési szolgáltatás, amelyet a fejlesztők használhatják ügyfeleik hitelesítésére. Ez a fehér címkés szolgáltatás lehetővé teszi a fejlesztők számára, hogy testre szabják és szabályozhassák, hogy a felhasználók hogyan használják biztonságosan webes, asztali, mobilos vagy egyoldalas alkalmazásaikat. Az Azure AD B2C használatával a felhasználók regisztrálhatnak, bejelentkezhetnek, jelszavakat állíthatnak alaphelyzetbe, és szerkeszthetik a profilokat. Az Azure AD B2C az OpenID Connect és az OAuth 2.0 protokollok egy formáját valósítja meg. 

**A legfontosabb jellemzők**
- Biztonságosan hitelesítheti az ügyfeleket az általuk előnyben részesített identitásszolgáltatóval.
- Az ügyfelek identitásának és hozzáférésének kezelése.
- Szerezzen bejelentkezési támogatást a közösségi médiához, például a Facebookhoz, a GitHubhoz, a Google-hoz, a LinkedInhez, a Twitterhez, a WeChathez és a Weibo-hoz.
- Csatlakozzon a felhasználói fiókokhoz az iparági szabványnak megfelelő protokollok, például az OpenID Connect vagy az SAML használatával, hogy az identitáskezelést számos platformon lehetővé tegye.
- Márkajelzéssel ismert regisztráció és bejelentkezési élmény biztosítása.
- Könnyedén integrálható crm-adatbázisokkal, marketingelemző eszközökkel és fiókellenőrző rendszerekkel.
- Rögzítse a bejelentkezési, preferencia- és konverziós adatokat az ügyfelek számára.

**Referencia**
- [Azure-portál](https://portal.azure.com/)
- [Az Azure AD B2C dokumentációja](/azure/active-directory-b2c/)
- [Rövid útmutatók](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [Minták](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/) a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatása, amely segít az alkalmazottaknak a bejelentkezésben és a hozzáférés elérésében:
- Külső erőforrások, például a Microsoft Office 365, az Azure Portal és több ezer más szoftver szolgáltatásként (SaaS) alkalmazás.
- Belső erőforrások, például a vállalati hálózaton és az intraneten lévő alkalmazások, valamint a saját szervezete által kifejlesztett felhőalkalmazások.

**A legfontosabb jellemzők**
- Zökkenőmentes, rendkívül biztonságos hozzáférés a felhasználóknak a szükséges alkalmazásokhoz való csatlakoztatásával.
- Átfogó identitásvédelem és fokozott biztonság az identitások és a hozzáférés felhasználói, tartózkodási hely, adatok és alkalmazáskörnyezet alapján.
- Több ezer előre integrált alkalmazás kereskedelmi és egyéni alkalmazásokhoz, például az Office 365-hez, a Salesforce.com és a Boxhoz.
- A hozzáférés nagy mértékű kezelése.

**Referencia**
- [Azure-portál](https://portal.azure.com/)
- [Mi az Azure AD?](/azure/active-directory/fundamentals/active-directory-whatis)
- [Ismerkedés az Azure Active Directoryval](/azure/active-directory/fundamentals/active-directory-whatis)
- [Rövid útmutatók](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)