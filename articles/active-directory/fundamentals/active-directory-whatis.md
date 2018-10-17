---
title: Mi az az Azure Active Directory (Azure AD)? | Microsoft Docs
description: Ismerje meg, hogy az Azure Active Directory használatával hogyan terjesztheti ki meglévő helyszíni identitásait a felhőbe, és hogyan fejleszthet az Azure AD-vel integrált alkalmazásokat.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 755c301651e7c49faa8b05b2b443c5cce1a03c90
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364053"
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory?
Az Azure Active Directory (Azure AD) a Microsoft több-bérlős, felhőalapú címtár és identitáskezelési szolgáltatása. Az Azure AD egyetlen megoldásban egyesíti az alapvető címtárszolgáltatásokat, az alkalmazáshozzáférés-felügyeletet és az identitás védelmet, egy szabványalapú platform, amely segít a fejlesztőknek, hogy központi szabályzatokon és szabályokon alapuló hozzáférés-vezérléssel bővíthessék alkalmazásaikat.

![Az Azure AD Connect-verem](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Az Azure AD előnyei
Az Azure AD segítségével:

-   A vállalat minden egyes felhasználójához létrehozhat és kezelhet egy-egy identitást, így a felhasználók, csoportok és eszközök folyamatosan szinkronizálva lesznek az [Azure AD Connecttel](../connect/active-directory-aadconnect.md).

-   Egyszeri bejelentkezéses hozzáférést biztosíthat alkalmazásaihoz (például több ezer előre integrált SaaS-alkalmazáshoz), illetve sokkal biztonságosabb távelérést biztosíthat helyszíni SaaS-alkalmazásokhoz az [Azure AD Application Proxyval](../manage-apps/application-proxy.md).

-   A [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) házirend alapú szabályok kikényszerítésével fokozhatja az alkalmazás-hozzáférés biztonságát a helyszíni és a felhőalkalmazásokban is.

-   [Új jelszó önkiszolgáló kérésével](../user-help/user-help-reset-password.md), valamint a [MyApps portálról](../user-help/active-directory-saas-access-panel-introduction.md) indított csoport- és alkalmazás-hozzáférési kérelmekkel fokozhatja a felhasználói termelékenységet.

-   Kihasználhatja az egész világra kiterjedő, nagyvállalati szintű, felhőalapú identitás- és hozzáférés-kezelési megoldás [magas rendelkezésre állásából és megbízhatóságából](https://docs.microsoft.com/azure/architecture/checklist/availability) fakadó előnyöket.

## <a name="who-uses-azure-ad"></a>Ki használja az Azure AD-t
Az Azure AD IT rendszergazdák, alkalmazásfejlesztők és az Office 365, Azure és a Dynamics CRM Online felhasználói számára készült.

- **IT rendszergazdák.** Az Azure AD egy biztonságosabb megoldást kínál a szervezetek számára, amely a hatékonyabb identitáskezelést és egyszeri bejelentkezéses (SSO) hozzáférést biztosít [felhőalapú SaaS-alkalmazások](../saas-apps/tutorial-list.md) és helyszíni alkalmazások ezreihez. Ezeken az alkalmazásokon keresztül is felhőalapú alkalmazásbiztonságot, zökkenőmentes hozzáférést, továbbfejlesztett együttműködést és automatizált identitás-életciklust valósíthat meg a felhasználók számára, így növelhető a biztonság és a megfelelőség.

    Emellett az [Azure AD Connecttel](../connect/active-directory-aadconnect-get-started-express.md) integrálhatja az Azure AD-t a meglévő Windows Server Active Directoryval, így a szervezet már kiépített helyszíni identitáskezelő rendszereivel felügyelheti a felhőalapú SaaS-alkalmazások elérését.

- **Alkalmazásfejlesztőknek.** Az Azure AD-használatával az alkalmazások létrehozására összpontosíthat, mivel egy globálisan szervezetek milliói által használt identitáskezelő megoldást integrálhat fejlesztéseibe.

- **Office 365-, Azure- és Dynamics CRM Online-ügyfeleknek.** Ön már használja az Azure AD-t. Minden Office 365-, Azure- és Dynamics CRM Online-bérlő valójában Azure AD-bérlő, így Ön akár most azonnal elkezdheti a felhasználók integrált felhőbeli alkalmazásokhoz való hozzáférésének felügyeletét.

## <a name="how-reliable-is-azure-ad"></a>Mennyire megbízható az Azure AD?
Az Azure AD-t több-bérlős, földrajzilag elosztott és nagy rendelkezésre állású kialakításának köszönhetően kritikus fontosságú üzleti célokra is megbízhatóan használható. Azure AD a világ különböző pontjain 28 adatközpontban fut automatikus feladatátvétellel. Ez azt jelenti, hogy még ha egy adatközpont le is áll, a címtár adatok másolatai legalább két másik területileg elosztott adatközpontban megvannak és azonnal elérhetők.

A szolgáltatói szerződésekről a [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) oldalán tájékozódhat bővebben.

## <a name="choose-an-edition"></a>Kiadás kiválasztása
Az összes üzleti Microsoft Online-szolgáltatás az Azure AD-t alkalmazza a bejelentkezési és egyéb identitásokkal kapcsolatos igényekhez. Bármely üzleti Microsoft Online-szolgáltatás (például az Office 365 vagy a Microsoft Azure) előfizetőjeként az Azure AD összes ingyenes szolgáltatásához automatikusan hozzáférést kap. Az Azure Active Directory ingyenes kiadása lehetővé teszi a felhasználók és csoportok kezelését, a helyi címtárakkal való szinkronizálást, valamint az egyszeri bejelentkezés használatát az Azure-ban, az Office 365-ben és olyan népszerű SaaS-alkalmazások ezreiben, mint a Salesforce, a Workday, a Concur, a DocuSign, a Google Apps, a Box, a ServiceNow és a Dropbox. 

Az Azure AD kiegészíthető fizetős funkciókkal is, ha frissít az alapszintű Azure Active Directory, a P1 prémium és P2 prémium kiadásokra. Az Azure AD fizetős kiadásai a meglévő ingyenes címtárra épülnek, és különféle nagyvállalati képességeket biztosítanak az önkiszolgáló ügyintézéstől, a fejlett monitorozáson, a biztonsági jelentéseken és a Multi-Factor Authentication (MFA) hitelesítésen át a mobil munkaerő számára a biztonságos hozzáférés biztosításáig.

> [!NOTE]
> Ezeknek a kiadásoknak az árképzésével kapcsolatban lásd az [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/). Az Azure Active Directory Prémium P1 és Prémium P2, valamint az Azure Active Directory Alapszintű kiadás használata Kínában jelenleg nem támogatott. Az Azure Active Directory díjszabásával kapcsolatos további információért tekintse meg az Azure Active Directoryval foglalkozó fórumot.

- **Alapszintű Azure Active Directory.** A felhőközpontú igényekkel rendelkező, feladatorientált felhasználóknak szánt kiadás felhőalapú alkalmazás-hozzáférési és önkiszolgáló identitáskezelési megoldásokat kínál. Az Alapszintű kiadás különböző hatékonyságnövelő és költségcsökkentő funkciókat biztosít, többek között csoportalapú hozzáférés-kezelést és önkiszolgáló jelszó-visszaállítást felhőalkalmazásokhoz, valamint az Azure Active Directory Application Proxy szolgáltatást, amellyel helyszíni környezetben futó webes alkalmazások tehetők közzé az Azure AD-val – mindezt nagyvállalati, 99,9%-os garantált szolgáltatási szinten vállalt rendelkezésre állás mellett.

- **P1 prémium szintű Azure Active Directory.** A komolyabb identitás- és hozzáférés-kezelési igényekkel rendelkező szervezeteknek szánt prémium szintű Azure Active Directory funkciókban gazdag, nagyvállalati szintű identitáskezelési képességeket kínál, és lehetővé teszi a hibrid felhasználók számára a helyszíni és a felhőalapú képességek gördülékeny elérését. A kiadás minden szükséges képességgel ellátja a hibrid környezetben dolgozó infomunkásokat és identitáskezelő adminisztrátorokat, legyen szó az alkalmazásokhoz való hozzáférésről, önkiszolgáló identitás- és hozzáférés-kezelési (IAM) funkciókról, az identitások védelméről vagy a felhő biztonságáról. Támogatja az olyan speciális felügyeleti és delegálási erőforrásokat, mint a dinamikus csoportok és az önkiszolgáló csoportkezelés. Tartalmazza a Microsoft Identity Managert (ez egy helyszíni identitás- és hozzáférés-felügyeleti csomag), és felhőalapú visszaírási képességeivel különféle megoldásokat biztosít, például az új jelszavak önkiszolgáló igénylését a helyszíni felhasználók számára.

- **P2 prémium szintű Azure Active Directory.** Ez a felhasználók és rendszergazdák továbbfejlesztett védelme köré tervezett új ajánlat a P1 prémium szintű Azure AD valamennyi funkciójához adja hozzá az Identity Protection és a Privileged Identity Management megoldásokat. Az Azure Active Directory Identity Protection jelek milliárdjain alapuló kockázatalapú feltételes hozzáférést biztosít az alkalmazásokhoz és a kritikus vállalati adatokhoz. Az Azure Active Directory Privileged Identity Management pedig lehetővé teszi az emelt szintű fiókok felügyeletét és védelmét, így felderítheti, korlátozhatja és monitorozhatja a rendszergazdákat és azok az erőforrásokhoz való hozzáférését, és igény szerinti hozzáférést biztosíthat.  

> [!NOTE]
> Az Azure Active Directory számos funkciója használatalapú kiadásokban is elérhetők:<ul><li>**Azure Active Directory B2C.** Identitás- és hozzáférés-kezelő megoldás a fogyasztók számára elérhető alkalmazásokhoz. További információk: [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).</li><li>**Azure Multi-Factor Authentication.** Felhasználónként vagy hitelesítés szolgáltatónként használható. További információk: [Mi az Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="as-an-admin-how-do-i-get-started"></a>Rendszergazdaként hogyan kezdhetek hozzá?
Regisztráljon az ingyenes 30 napos próbaverzióra és máris üzembe helyezheti első felhőalapú megoldását, lásd: [Prémium szintű Azure Active Directory próbaverzió](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="as-a-developer-how-do-i-get-started"></a>Fejlesztőként hogyan kezdhetek hozzá?
Regisztráljon az ingyenes 30 napos próbaverzióra, és kezdje el alkalmazásait integrálni az Azure AD-be, lásd: [Prémium szintű Azure Active Directory próbaverzió](https://azure.microsoft.com/trial/get-started-active-directory/). További információ található még az Azure Active Directory [Fejlesztői útmutatójában](../develop/azure-ad-developers-guide.md) is.

## <a name="next-steps"></a>További lépések
- [További információk az Azure-identitáskezelés és -hozzáféréskezelés alapjairól](identity-fundamentals.md).

- [Az Azure AD integrálása a Windows Server Active directory szolgáltatással](../hybrid/how-to-connect-install-express.md).