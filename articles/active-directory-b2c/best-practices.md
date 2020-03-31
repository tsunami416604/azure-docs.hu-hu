---
title: Gyakorlati tanácsok az Azure AD B2C-hez
titleSuffix: Azure AD B2C
description: Javaslatok és gyakorlati tanácsok az Azure Active Directory B2C (Azure AD B2C) használatával való munka során.
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136159"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Javaslatok és gyakorlati tanácsok az Azure Active Directory B2C-hez

Az alábbi ajánlott eljárások és javaslatok az Azure Active Directory (Azure AD) B2C meglévő vagy új alkalmazáskörnyezetbe való integrálásának néhány elsődleges szempontját fedik le.

## <a name="fundamentals"></a>Alapok

|  |  |
|--|--|
| Felhasználói folyamatok kiválasztása a legtöbb forgatókönyvhöz | Az Azure AD B2C identitáskezelési keretrendszere a szolgáltatás alapvető erőssége. A házirendek teljes mértékben ismertetik az identitáskezelési élményeket, például a regisztrációt, a bejelentkezést vagy a profilszerkesztést. A leggyakoribb identitáskezelési feladatok beállítása érdekében az Azure AD B2C portál előre definiált, konfigurálható szabályzatokat, úgynevezett felhasználói folyamatokat tartalmaz. A felhasználói folyamatokkal percek alatt, néhány kattintással nagyszerű felhasználói élményt hozhat létre. [Megtudhatja, hogy mikor kell használni a felhasználói folyamatokat és az egyéni házirendeket.](custom-policy-overview.md#comparing-user-flows-and-custom-policies)|
| Alkalmazásregisztrációk | Minden alkalmazást (webes, natív) és API-t, amely biztonságos az Azure AD B2C regisztrálva kell lennie. Ha egy alkalmazás rendelkezik az iOS és az Android webes és natív verziójával, regisztrálhatja őket egyetlen alkalmazásként az Azure AD B2C-ben ugyanazzal az ügyfélazonosítóval. Ismerje meg, hogyan [regisztrálhatja az OIDC, SAML, web és natív alkalmazásokat.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications) További információ az [Azure AD B2C-ben használható alkalmazástípusokról.](https://docs.microsoft.com/azure/active-directory-b2c/application-types) |
| Ugrás a havi aktív felhasználók számlázására | Az Azure AD B2C a havi aktív hitelesítésről a havi aktív felhasználók (MAU) számlázására váltott. A legtöbb ügyfél ezt a modellt költséghatékonynak fogja találni. [További információ a havi aktív felhasználók számlázásáról.](https://azure.microsoft.com/updates/mau-billing/) |

## <a name="planning-and-design"></a>Tervezés és tervezés

Határozza meg az alkalmazás- és szolgáltatásarchitektúrát, leltározhatja az aktuális rendszereket, és tervezze meg az Azure AD B2C-be való migrálást.

|  |  |
|--|--|
| A végpontok között megoldás megtervezése | Az Azure AD B2C-integráció tervezésekor az alkalmazások összes függőségét is belekell foglalni. Vegye figyelembe az összes olyan szolgáltatást és terméket, amely jelenleg a környezetében van, vagy amelyet esetleg hozzá kell adni a megoldáshoz, például az Azure Functionst, az ügyfélkapcsolat-kezelési (CRM) rendszereket, az Azure API Management átjárót és a tárolási szolgáltatásokat. Vegye figyelembe az összes szolgáltatás biztonságát és méretezhetőségét. |
| A felhasználók felhasználói élményének dokumentálása | Részletezze az összes felhasználói utazást, amelyet az ügyfelek az alkalmazásban tapasztalhatnak. Tartalmazza az összes képernyőt és az elágazási folyamatokat, amelyekkel az alkalmazás identitás- és profilszempontjainak kölcsönhatásasorán találkozhatnak. A tervezésbe belefoglalhatja a használhatóságot, a kisegítő lehetőségeket és a honosítást. |
| Válassza ki a megfelelő hitelesítési protokollt |  A különböző alkalmazásforgatókönyvek és az ajánlott hitelesítési folyamatok bontásáról a [Forgatókönyvek és a támogatott hitelesítési folyamatok című témakörben van.](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) |
| A proof-of-concept (POC) végpontok között felhasználói élmény | Kezdje [a Microsoft-kódmintákkal](code-samples.md) és [a közösségi mintákkal.](https://github.com/azure-ad-b2c/samples) |
| Áttelepítési terv létrehozása |Az előre tervezés gördülékenyebbé teheti az áttelepítést. További információ a [felhasználók áttelepítéséről](user-migration.md).|
| Használhatóság és biztonság | A megoldásnak meg kell találnia a megfelelő egyensúlyt az alkalmazás használhatósága és a szervezet elfogadható kockázati szintje között. |
| A helyszíni függőségek áthelyezése a felhőbe | A rugalmas megoldás biztosítása érdekében fontolja meg a meglévő alkalmazásfüggőségek áthelyezése a felhőbe. |
| Meglévő alkalmazások áttelepítése b2clogin.com | 2020. december 4-én lépett életbe login.microsoftonline.com az összes Azure AD B2C-bérlő evesi. [További információ](b2clogin.md). |

## <a name="implementation"></a>Megvalósítás

A végrehajtási szakaszban vegye figyelembe a következő ajánlásokat.

|  |  |
|--|--|
| Egyéni szabályzatok szerkesztése az Azure AD B2C bővítményt a Visual Studio-kód | Töltse le a Visual Studio-kódot és ezt a közösség által készített [bővítményt a Visual Studio Kódpiactérről.](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) Bár nem hivatalos Microsoft-termék, a Visual Studio-kód Azure AD B2C bővítménye számos olyan funkciót tartalmaz, amelyek megkönnyítik az egyéni házirendek használatát. |
| Az Azure AD B2C hibáinak elhárítása | További információ az egyéni házirendek fejlesztési soráni [hibaelhárításáról.](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) Ismerje meg, hogyan néz ki egy normál hitelesítési folyamat, és használjon eszközöket az anomáliák és hibák felderítéséhez. Például az [Application Insights](troubleshoot-with-application-insights.md) segítségével tekintse át a felhasználói utak kimeneti naplóit. |
| Használja ki bevált egyéni házirend-minták könyvtárát | [Mintákat](https://github.com/azure-ad-b2c/samples) kereshet számos továbbfejlesztett Azure AD B2C ügyfélidentitás- és hozzáférés-kezelési (CIAM) felhasználói úthoz. |


## <a name="testing"></a>Tesztelés

Tesztelje és automatizálja az Azure AD B2C implementációját.

|  |  |
|--|--|
| A globális forgalom számlája | A teljesítmény- és honosítási követelmények teszteléséhez használjon különböző globális címről származó forgalmi forrásokat. Győződjön meg arról, hogy az összes HTML, CSS és függőség megfelel a teljesítményigényeinek. |
| Funkcionális és felhasználói felülettesztelés | Tesztelje a felhasználói folyamatok végpontok között. Adjunk hozzá szintetikus vizsgálatok néhány percenként a szelén, VS Web Test, stb |
| Tollvizsgálat | Mielőtt élőben a megoldás, hajtsa végre a penetrációs tesztelési gyakorlatokat, hogy ellenőrizze, hogy minden összetevő biztonságos,, beleértve a harmadik féltől származó függőségeket. Ellenőrizze, hogy az API-kat hozzáférési jogkivonatokkal biztosította-e, és az alkalmazásforgatókönyvhöz megfelelő hitelesítési protokollt használta-e. További információ a [penetráció teszteléséről](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) és a [Microsoft Cloud unified penetrációs tesztelési szabályairól.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| A/B tesztelés | Flight az új funkciók egy kis, véletlenszerű felhasználói előtt gördülő ki a teljes lakosság. Ha a JavaScript engedélyezve van az Azure AD B2C-ben, integrálhatja az A/B tesztelési eszközökkel, például az Optimizely, clarity és más eszközökkel. |
| Terheléses tesztelés | Az Azure AD B2C skálázható, de az alkalmazás csak akkor skálázható, ha az összes függőségek skálázható. Tesztelje az API-kat és a CDN-t. |
| Throttling |  Az Azure AD B2C szabályozza a forgalmat, ha túl sok kérést küld ugyanabból a forrásból rövid idő alatt. Több forgalmi forrás t a terheléstesztelés során, és kezelje a hibakódot szabályosan az `AADB2C90229` alkalmazásokban. |
| Automation | A tesztelés és üzembe helyezés automatizálásához folyamatos integrációs és kézbesítési (CI/CD) folyamatok at használhat, például [az Azure DevOps.](deploy-custom-policies-devops.md) |

## <a name="operations"></a>Műveletek

Az Azure AD B2C környezet kezelése.

|  |  |
|--|--|
| Több környezet létrehozása | A könnyebb műveletek és a telepítés bevezetése érdekében hozzon létre külön környezeteket a fejlesztéshez, teszteléshez, üzem előtti és éles környezethez. Hozzon létre az Azure AD B2C-bérlők minden. |
| Verziókövetés használata az egyéni házirendekhez | Fontolja meg a GitHub, az Azure Repos vagy más felhőalapú verziókezelő rendszer használatát az Azure AD B2C egyéni szabályzataihoz. |
| A Microsoft Graph API segítségével automatizálhatja a B2C-bérlők felügyeletét | Microsoft Graph API-k:<br/>[Identitáskezelési keretrendszer](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) kezelése (egyéni házirendek)<br/>[Kulcsok](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Felhasználói folyamatok](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integráció az Azure DevOps-szal | A [CI/CD-folyamat](deploy-custom-policies-devops.md) megkönnyíti a kód mozgatását a különböző környezetek között, és biztosítja a termelésre való felkészültséget.   |
| Integráció az Azure Monitorral | [A naplóesemények](view-audit-logs.md) csak hét napig őrződnek meg. [Integrálja](azure-monitor.md) az Azure Monitorral a naplók hosszú távú használatra való megőrzéséhez, vagy integrálja a külső biztonsági információkkal és eseménykezelési (SIEM) eszközökkel, hogy betekintést nyerjen a környezetébe. |
| Aktív riasztás és figyelés beállítása | [Kövesse nyomon](active-directory-b2c-custom-guide-eventlogger-appins.md) a felhasználói viselkedést az Azure AD B2C-ben az Application Insights használatával. |


## <a name="support-and-status-updates"></a>Támogatási és állapotfrissítések

Legyen naprakész a szolgáltatás állapotával kapcsolatban, és keresse meg a támogatási lehetőségeket.

|  |  |
|--|--|
| [Szolgáltatásfrissítések](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Legyen naprakész az Azure AD B2C termékfrissítéseivel és bejelentéseivel. |
| [Microsoft támogatási szolgálat](support-options.md) | Nyújtson be támogatási kérelmet az Azure AD B2C technikai problémákhoz. A számlázási és előfizetés-kezelési támogatás díjmentesen biztosított. |
| [Az Azure állapota](https://status.azure.com/status) | Tekintse meg az összes Azure-szolgáltatás aktuális állapotát. |