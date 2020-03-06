---
title: Ajánlott eljárások Azure AD B2C
titleSuffix: Azure AD B2C
description: Javaslatok és ajánlott eljárások, amelyeket érdemes figyelembe venni Azure Active Directory B2C (Azure AD B2C) használatakor.
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: 6c737af85e2a7205dca6a56174dfda565da1410d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304393"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Javaslatok és ajánlott eljárások Azure Active Directory B2C

Az alábbi ajánlott eljárások és javaslatok az Azure Active Directory (Azure AD) B2C meglévő vagy új alkalmazási környezetbe való integrálásának főbb szempontjait fedik le.

## <a name="fundamentals"></a>Alapok

|  |  |
|--|--|
| A legtöbb forgatókönyv esetén válassza a felhasználói folyamatok lehetőséget | A Azure AD B2C identitási élményének keretrendszere a szolgáltatás alapvető erőssége. A szabályzatok teljes mértékben leírják a személyazonossággal kapcsolatos tapasztalatokat, például a regisztrálást, a bejelentkezést vagy a profil szerkesztését. A leggyakoribb identitási feladatok beállításához a Azure AD B2C-portál a felhasználói folyamatok nevű előre definiált, konfigurálható szabályzatokat tartalmazza. A felhasználói folyamatok esetében percek alatt hozhat létre nagyszerű felhasználói élményt, mindössze néhány kattintással. [Ismerje meg, hogy mikor használhatja a felhasználói folyamatokat és az egyéni házirendeket](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Alkalmazásregisztrációk | A védett összes alkalmazást (webes, natív) és API-t regisztrálni kell Azure AD B2Cban. Ha egy alkalmazásnak az iOS és az Android rendszerhez készült webes és natív verziója is van, akkor egyetlen alkalmazásként regisztrálhatja őket Azure AD B2C ugyanazzal az ügyfél-AZONOSÍTÓval. Ismerje meg, hogyan [regisztrálhat OIDC-, SAML-, web-és natív alkalmazásokat](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications). További információ a [Azure ad B2C használható alkalmazási típusokról](https://docs.microsoft.com/azure/active-directory-b2c/application-types). |
| Áttérés a havi aktív felhasználók számlázására | Azure AD B2C a havi aktív hitelesítéstől a havi aktív felhasználók (MAU) számlázására került. A legtöbb ügyfél költséghatékonyan fogja megtalálni ezt a modellt. [További információ a havi aktív felhasználók számlázásáról](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Tervezés és kialakítás

Határozza meg az alkalmazás és a szolgáltatás architektúráját, a leltár jelenlegi rendszereit, és tervezze meg az áttelepítést Azure AD B2Cra.

|  |  |
|--|--|
| Teljes körű megoldás az építész számára | Az összes alkalmazás függőségeinek belefoglalása Azure AD B2C integráció tervezésekor. Vegye figyelembe az összes olyan szolgáltatást és terméket, amely jelenleg a környezetben van, vagy amelyet hozzá kell adni a megoldáshoz, például Azure Functions, ügyfélkapcsolat-kezelési (CRM) rendszerek, Azure API Management Gateway és Storage Services. Vegye figyelembe az összes szolgáltatás biztonságát és méretezhetőségét. |
| Dokumentálja felhasználói élményeit | Részletesen megtekintheti, hogy az ügyfelek milyen felhasználói élményt nyújtanak az alkalmazásban. Minden képernyő és elágazó folyamat belefoglalása, amelyek az alkalmazás identitás-és profil-szempontjaival való interakció során előfordulhatnak. A tervezéshez a használhatóság, a hozzáférhetőség és a honosítás is használható. |
| Válassza ki a megfelelő hitelesítési protokollt |  A különböző alkalmazási forgatókönyvek és az ajánlott hitelesítési folyamatok részletezését lásd: [forgatókönyvek és támogatott hitelesítési folyamatok](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| A megvalósíthatósági (POC) végfelhasználói élmény kipróbálása | Kezdje a [Microsoft Code-mintákat](code-samples.md) és a [közösségi mintákat](https://github.com/azure-ad-b2c/samples). |
| Áttelepítési terv létrehozása |A tervezéssel az áttelepítés zökkenőmentesen elvégezhető. További információ a [felhasználók áttelepítéséről](user-migration.md).|
| Használhatóság és biztonság | A megoldásnak megfelelő egyensúlyt kell teremtenie az alkalmazások használhatósága és a szervezete elfogadható kockázati szintje között. |
| Helyszíni függőségek áthelyezése a felhőbe | A rugalmas megoldás biztosításához érdemes lehet áthelyezni a meglévő alkalmazás-függőségeket a felhőbe. |
| Meglévő alkalmazások migrálása a b2clogin.com-be | A login.microsoftonline.com elavulttá vált az összes Azure AD B2C bérlőn, a 2020. december 04. között. [További információk](b2clogin.md). |

## <a name="implementation"></a>Megvalósítás

A megvalósítási fázisban vegye figyelembe az alábbi javaslatokat.

|  |  |
|--|--|
| Egyéni szabályzatok szerkesztése a Visual Studio Code-hoz készült Azure AD B2C bővítménnyel | Töltse le a Visual Studio Code-ot és a Közösség által készített [bővítményt a Visual Studio Code piactérről]((https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)). Habár nem hivatalos Microsoft-termék, a Visual Studio Code-hoz készült Azure AD B2C-bővítmény számos olyan funkciót tartalmaz, amelyek megkönnyítik az egyéni házirendek használatát. |
| Tudnivalók a Azure AD B2C hibakereséséről | Ismerje meg, hogy miként lehet [elhárítani az egyéni házirendeket](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) a fejlesztés során. Megtudhatja, hogyan néz ki egy normál hitelesítési folyamat a rendellenességek és hibák felderítésére szolgáló eszközök használatával. Például a [Application Insights](troubleshoot-with-application-insights.md) segítségével tekintheti át a felhasználói útvonalak kimeneti naplóit. |
| A bevált egyéni szabályzatok mintájának kihasználása | Többek között számos továbbfejlesztett Azure AD B2C ügyfél-identitás-és hozzáférés-kezelési (CIAM) felhasználói útvonalon talál [mintákat](https://github.com/azure-ad-b2c/samples) . |


## <a name="testing"></a>Tesztelés

Tesztelje és automatizálja Azure AD B2C megvalósítását.

|  |  |
|--|--|
| Fiók a globális forgalomhoz | Különböző globális címről származó adatforgalmi források használatával tesztelheti a teljesítménnyel és a honosítással kapcsolatos követelményeket. Győződjön meg arról, hogy az összes HTML, CSS és függőség megfelel a teljesítmény igényeinek. |
| Funkcionális és felhasználói felületi tesztelés | Tesztelje a felhasználó végpontok közötti folyamatát. Vegyen fel néhány percenként szintetikus teszteket a szelén, a VS web test stb. használatával. |
| Tollas tesztelés | Mielőtt elkezdené a megoldását, végezze el a behatolás tesztelési gyakorlatait az összes összetevő biztonságának ellenőrzéséhez, beleértve a külső felektől származó függőségeket is. Győződjön meg arról, hogy az API-kat hozzáférési jogkivonatokkal biztosította, és a megfelelő hitelesítési protokollt használta az alkalmazási forgatókönyvhöz. További információ a [behatolási tesztekről](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) , valamint az összevonással kapcsolatos [Microsoft Cloud egységes penetráció tesztelési szabályairól](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| A/B tesztelés | Az új funkciók egy kis, véletlenszerű felhasználói csoportba kerülnek, mielőtt elkezdené a teljes sokaságot. Ha a JavaScript engedélyezve van a Azure AD B2Cban, integrálhatja az A/B tesztelési eszközöket, például az optimalizálást, A világosságot és egyebeket. |
| Terheléses tesztelés | Azure AD B2C méretezheti a méretezést, de az alkalmazás csak akkor méretezhető, ha az összes függősége méretezhető. Az API-k és a CDN betöltésének tesztelése. |
| Szabályozás |  A Azure AD B2C szabályozza a forgalmat, ha túl sok kérelmet továbbítanak ugyanabból a forrásból rövid idő alatt. A tesztelés során több forgalmi forrást is használhat, és a `AADB2C90229` hibakódot szabályosan kezelheti az alkalmazásaiban. |
| Automatizálás | A folyamatos integrációs és kézbesítési (CI/CD) folyamatokkal automatizálhatja a tesztelést és az üzembe helyezést, például az [Azure DevOps](deploy-custom-policies-devops.md)-t. |

## <a name="operations"></a>Műveletek

Kezelheti Azure AD B2C-környezetét.

|  |  |
|--|--|
| Több környezet létrehozása | A könnyebb működés és üzembe helyezés érdekében hozzon létre külön környezetet a fejlesztéshez, teszteléshez, üzem előtti és éles környezetekhez. Hozzon létre Azure AD B2C bérlőket mindegyikhez. |
| Az egyéni szabályzatok verziókövetés használata | Vegye fontolóra a GitHub, az Azure Repos vagy más felhőalapú verziókövető rendszer használatát a Azure AD B2C egyéni szabályzatokhoz. |
| A B2C-bérlők felügyeletének automatizálásához használja a Microsoft Graph API-t | Microsoft Graph API-k:<br/>Az [identitási élmény keretrendszere](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) (egyéni szabályzatok) kezelése<br/>[Kulcsok](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Felhasználói folyamatok](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integrálás az Azure DevOps | A [CI/CD-folyamat](deploy-custom-policies-devops.md) lehetővé teszi a különböző környezetek közötti váltást, és minden alkalommal biztosítja a termelési készültséget.   |
| Integrálás Azure Monitor | A [naplózási naplózási események](view-audit-logs.md) csak hét napig őrződnek meg. A [Azure monitor integrálása](azure-monitor.md) a naplófájlok hosszú távú használatra való megőrzéséhez, illetve a harmadik féltől származó biztonsági információkkal és ESEMÉNYKEZELŐ (SIEM) eszközök integrálásához, hogy betekintést nyerjen a környezetbe. |
| Aktív riasztások és figyelés beállítása | A [felhasználó viselkedésének nyomon követése](active-directory-b2c-custom-guide-eventlogger-appins.md) Azure AD B2C a Application Insights használatával. |


## <a name="support-and-status-updates"></a>Támogatás és állapot frissítései

Maradjon naprakész a szolgáltatás állapotával és támogatási lehetőségekkel.

|  |  |
|--|--|
| [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Naprakészen tarthatja Azure AD B2C termék frissítéseit és hirdetményeit. |
| [Microsoft ügyfélszolgálata](support-options.md) | Támogatási kérelem nyújtása Azure AD B2C technikai problémákhoz. A számlázás és az előfizetések kezelésének támogatása díjmentesen igénybe vehető. |
| [Azure állapota](https://status.azure.com/status) | Az összes Azure-szolgáltatás aktuális állapotának megtekintése. |