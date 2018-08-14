---
title: Mi az az Azure Active Directory (Azure AD)? | Microsoft Docs
description: Az Azure Active Directory használatával kiterjesztheti meglévő helyszíni identitásait a felhőbe, valamint az Azure AD-vel integrált alkalmazásokat fejleszthet.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 5087f759d682382bc22b5f95f462fe0f08619cc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449994"
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory?
Az Azure Active Directory (Azure AD) a Microsoft több-bérlős felhőalapú címtára és identitáskezelési szolgáltatása, amely egyetlen megoldásban egyesíti az alapvető címtárszolgáltatásokat, az alkalmazáshozzáférés-kezelést és az identitásvédelmet. Az Azure Active Directory egyben egy gazdag, szabványalapú platformot is kínál a fejlesztőknek, hogy központi szabályzatokon és szabályokon alapuló hozzáférés-vezérléssel bővíthessék az alkalmazásaikat.

![Az Azure AD Connect-verem](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Rendszergazdáknak.** Az Azure AD egy biztonságosabb megoldást kínál a szervezetek számára, amely a hatékonyabb identitáskezelést és egyszeri bejelentkezéses (SSO) hozzáférést biztosít [felhőalapú SaaS-alkalmazások](../saas-apps/tutorial-list.md) és helyszíni alkalmazások ezreihez. Ezeken az alkalmazásokon keresztül is felhőalapú alkalmazásbiztonságot, zökkenőmentes hozzáférést, továbbfejlesztett együttműködést és automatizált identitás-életciklust valósíthat meg az alkalmazottak számára, így növelhető a biztonság és a megfelelőség.

    Emellett mindössze [négy kattintással](./../connect/active-directory-aadconnect-get-started-express.md) integrálhatja az Azure AD-t a meglévő Windows Server Active Directoryval, így a szervezet már kiépített helyszíni identitáskezelő rendszereivel felügyelheti a felhőalapú SaaS-alkalmazások elérését.

- **Alkalmazásfejlesztőknek.** Az Azure AD-használatával az alkalmazások létrehozására összpontosíthat, mivel egy globálisan szervezetek milliói által használt identitáskezelő megoldást integrálhat fejlesztéseibe.

- **Office 365-, Azure- és Dynamics CRM Online-ügyfeleknek.** Ön már használja az Azure AD-t. Minden Office 365-, Azure- és Dynamics CRM Online-bérlő valójában Azure AD-bérlő, így Ön akár most azonnal elkezdheti az alkalmazottak az integrált felhőbeli alkalmazásokhoz való hozzáférésének felügyeletét.

## <a name="how-reliable-is-azure-ad"></a>Mennyire megbízható az Azure AD?
Az Azure AD-t több-bérlős, földrajzilag elosztott, magas rendelkezésre állású kialakításának köszönhetően kritikus fontosságú üzleti célokra is megbízhatóan használhatja. Az Azure AD a világ különböző helyein lévő 28 adatközpontban működik, így Ön mindig biztos lehet abban, hogy adatai biztonságban vannak – még ha valamelyik adatközpont elérhetetlenné válik is, a címtáradatok legalább két, regionálisan elosztott adatközpontban továbbra is rendelkezésre állnak, és használhatók a rendszerek eléréséhez.

A szolgáltatói szerződésekről a [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) oldalán tájékozódhat bővebben.

## <a name="choose-an-edition"></a>Kiadás kiválasztása
Az összes üzleti Microsoft Online-szolgáltatás az Azure Active Directoryt (Azure AD) alkalmazza a bejelentkezéshez és az identitásokkal kapcsolatos egyéb igényekhez. Az üzleti Microsoft Online-szolgáltatások (például az Office 365 vagy a Microsoft Azure) előfizetőjeként az Azure AD összes ingyenes szolgáltatásához hozzáférést kap. Az Azure Active Directory ingyenes kiadása lehetővé teszi a felhasználók és csoportok kezelését, a helyi címtárakkal való szinkronizálást, valamint az egyszeri bejelentkezés használatát az Azure-ban, az Office 365-ben és olyan népszerű SaaS-alkalmazások ezreiben, mint a Salesforce, a Workday, a Concur, a DocuSign, a Google Apps, a Box, a ServiceNow és a Dropbox. 

Az Azure Active Directoryt kiegészítheti fizetős képességekkel is, az Azure Active Directory Alapszintű, Prémium P1 és Prémium P2 kiadásával. Az Azure Active Directory fizetős kiadásai a meglévő ingyenes címtárra épülnek, és különféle nagyvállalati képességeket biztosítanak az önkiszolgáló ügyintézéstől, a fejlett monitorozáson, a biztonsági jelentéseken és a többtényezős hitelesítésen (MFA) keresztül a mobil munkaerő biztonságos hozzáférésének biztosításáig.

> [!NOTE]
> Ezeknek a kiadásoknak az árképzésével kapcsolatban lásd az [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/). Az Azure Active Directory Prémium P1 és Prémium P2, valamint az Azure Active Directory Alapszintű kiadás használata Kínában jelenleg nem támogatott. Az Azure Active Directory díjszabásával kapcsolatos további információért tekintse meg az Azure Active Directoryval foglalkozó fórumot.
>

* **Alapszintű Azure Active Directory** – A felhőközpontú igényekkel rendelkező, feladatorientált felhasználóknak szánt kiadás felhőalapú alkalmazás-hozzáférési és önkiszolgáló identitáskezelési megoldásokat kínál. Az Azure Active Directory Alapszintű kiadása különböző hatékonyságnövelő és költségcsökkentő funkciókat biztosít, többek között csoportalapú hozzáférés-kezelést és önkiszolgáló jelszó-visszaállítást felhőalkalmazásokhoz, valamint az Azure Active Directory Application Proxy szolgáltatást, amellyel helyszíni környezetben futó webes alkalmazások tehetők közzé az Azure Active Directoryval – mindezt nagyvállalati szintű, 99,9%-os garantált szolgáltatási szintben vállalt rendelkezésre állás mellett.
* **Azure Active Directory Prémium P1** – A komolyabb identitás- és hozzáférés-kezelési igényekkel rendelkező szervezetek számára készült Prémium szintű Azure Active Directory funkciókban gazdag, nagyvállalati szintű identitáskezelési képességeket kínál, és lehetővé teszi a hibrid felhasználók számára a helyszíni és a felhőalapú képességek gördülékeny elérését. A kiadás minden szükséges képességgel ellátja a hibrid környezetben dolgozó infomunkásokat és identitáskezelő adminisztrátorokat, legyen szó az alkalmazásokhoz való hozzáférésről, önkiszolgáló identitás- és hozzáférés-kezelési (IAM) funkciókról, az identitások védelméről vagy a felhő biztonságáról. Támogatja az olyan speciális felügyeleti és delegálási erőforrásokat, mint a dinamikus csoportok és az önkiszolgáló csoportkezelés. Tartalmazza a Microsoft Identity Managert (ez egy helyszíni identitás- és hozzáférés-felügyeleti csomag), és felhőalapú visszaírási képességeivel különféle megoldásokat biztosít, például az új jelszavak önkiszolgáló igénylését a helyszíni felhasználók számára.
* **Azure Active Directory Prémium P2** – Ez az összes felhasználó és rendszergazda fejlett védelmét célzó új ajánlat az Azure AD Prémium P1 összes funkcióját tartalmazza, valamint az Identity Protection és a Privileged Identity Management szolgáltatást. Az Azure Active Directory Identity Protection jelek milliárdjain alapuló kockázatalapú feltételes hozzáférést biztosít az alkalmazásokhoz és a kritikus vállalati adatokhoz. Az Azure Active Directory Privileged Identity Management pedig lehetővé teszi az emelt szintű fiókok felügyeletét és védelmét, így felderítheti, korlátozhatja és monitorozhatja a rendszergazdákat és azok az erőforrásokhoz való hozzáférését, és igény szerinti hozzáférést biztosíthat.  

> [!NOTE]
> Az Azure Active Directory egyes képességei használatalapú kiadásokban érhetők el:
>
> * Az Active Directory B2C egy identitás- és hozzáférés-kezelő megoldás a felhasználók számára elérhető alkalmazásokhoz. További információt az [Azure Active Directory B2C-t](https://azure.microsoft.com/documentation/services/active-directory-b2c/) ismertető cikkben talált.
> * Az Azure Multi-Factor Authentication felhasználónként vagy hozzáférésenként számlázó szolgáltatókon keresztül érhető el. További információt [az Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md) ismertetőjében talál.
>

## <a name="how-can-i-get-started"></a>Hogyan kezdhetem meg a szolgáltatások használatát?

**Ha Ön rendszergazda:**

* [Próbálja ki!](https://azure.microsoft.com/trial/get-started-active-directory/) – a hivatkozást követve regisztrálhat egy 30 napos ingyenes próbaverzióra, és kevesebb mint öt perc alatt üzembe helyezheti első felhőalapú megoldását

* Az Azure AD-bérlők gyors üzembe helyezésével kapcsolatos tippekért és trükkökért olvassa el [az Azure AD első lépéseit](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) ismertető cikket.

**Ha Ön fejlesztő:**
 
* Tekintse át az Azure Active Directory [fejlesztői útmutatóját](../develop/azure-ad-developers-guide.md).

* [Próbaidőszak igénylése](https://azure.microsoft.com/trial/get-started-active-directory/) – regisztráljon még ma egy 30 napos ingyenes próbaverzióra, és kezdje el alkalmazásait integrálni az Azure AD-vel.

## <a name="next-steps"></a>További lépések
[További információk az Azure-identitáskezelés és -hozzáféréskezelés alapjairól](https://docs.microsoft.com/azure/active-directory/identity-fundamentals).
