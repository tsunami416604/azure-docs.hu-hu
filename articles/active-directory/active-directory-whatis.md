---
title: Mi az az Azure Active Directory?
description: "Azure Active Directory használata a meglévő helyszíni identitások kiterjeszti a felhőbe, vagy az Azure AD integrált alkalmazások fejlesztéséhez."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.openlocfilehash: b6746afd508832afbd54153851b6f2ae404af147
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory?
Azure Active Directory (Azure AD) érték a Microsoft által a több-bérlős, a felhő alapú címtár- és identitáskezelési felügyeleti szolgáltatás. Az Azure AD directory alapszolgáltatásokat, speciális identitás irányítás és alkalmazáshozzáférés-kezeléshez egyesíti. Az Azure AD is biztosít, amely lehetővé teszi a fejlesztők számára a hozzáférés-vezérlést biztosítanak az alkalmazások központi házirend és a szabályok alapján a gazdag, szabványokon alapuló platformja. 

Az IT-rendszergazdák, az Azure AD egy megfizethető, könnyen használható megoldás biztosítja az alkalmazottak és üzleti partnerek egyszeri bejelentkezés (SSO) hozzáférést biztosít [cloud SaaS-alkalmazásokhoz ezer](active-directory-saas-tutorial-list.md) például az Office365, a Salesforce.com, a DropBox és a Concur.

Alkalmazásfejlesztők, az Azure AD lehetővé teszi az alkalmazás felépítése azáltal, hogy gyorsan és egyszerűen egy globális osztály identitáskezelési megoldás használják szervezetek világszerte több millió integrálása koncentrálhat.

Az Azure AD is beleértve a többtényezős hitelesítést, eszközregisztráció, önkiszolgáló jelszókezelés, önkiszolgáló csoportkezelési, magas jogosultsági szintű fiók felügyeleti identitáskezelési képességeit teljes készlete, a szerepköralapú hozzáférés-vezérlés, alkalmazás-használat figyelését, részletes naplózás és a biztonsági figyelés és riasztás. Ezeket a képességeket is biztonságos felhőalapú alkalmazások súgójának, IT-folyamatok egyszerűsítésére, költségeket és biztosítható, hogy a vállalati megfelelőség célok teljesülnek-e.

Emellett a csak [négy kattintással](./connect/active-directory-aadconnect-get-started-express.md), az Azure AD integrálható a meglévő Windows Server Active Directory, mivel a szervezetek lehetővé teszi, hogy kihasználja a meglévő helyszíni identitás beruházások felhőalapú elérésének kezelése alapú SaaS-alkalmazásokhoz.

Ha az Office 365, Azure vagy a Dynamics CRM Online felhasználóinál, akkor előfordulhat, hogy nem vegye figyelembe, hogy már használja az Azure AD. Minden Office 365, az Azure és a Dynamics CRM-bérlőt már ténylegesen az Azure AD-bérlő. Ha azt szeretné, kezdheti bérlőre kezeléséhez hozzáférést több ezer más felhőalapú alkalmazásokhoz az Azure AD integrálható!

![Az Azure AD Connect-verem](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Hogyan megbízható az Azure AD?
A több-bérlős, földrajzilag elosztott, magas rendelkezésre állású kialakítása az Azure AD azt jelenti, hogy támaszkodhat a kritikus fontosságú üzleti igényeknek. Elfogy a világ különböző 28 adatközpontok automatikus feladatátvétellel, konfigurálnia kell a kényelmét ismerete, hogy az Azure AD megbízható és, hogy akkor is, ha egy adott adatközpont leáll, a címtár esetén a működés közbeni legalább két további – elosztott adatközpontokban példányát és azonnali hozzáférési érhető el.

További részletekért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Válassza ki a kiadás
Bejelentkezés az Azure Active Directory (Azure AD) támaszkodnak az összes Microsoft Online szolgáltatás, és más az identitásukat. Ha előfizet a Microsoft Online üzleti szolgáltatások (például Office 365 vagy a Microsoft Azure), emellett hozzáférhetnek az összes szabad szolgáltatás az Azure AD kap. Az Azure Active Directory ingyenes kiadásával is kezelhető felhasználókat és csoportokat, szinkronizálja a helyszíni címtárak, Azure, az Office 365 és a népszerű SaaS-alkalmazásokhoz Salesforce, Workday, Concur, DocuSign, Google Apps, mező, ServiceNow, dropbox-ba, és több ezer egyszeri bejelentkezés beolvasása. 

Az Azure Active Directory javítása érdekében, az Azure Active Directory Basic, a Premium P1 és a Premium P2 verziójával fizetős képességeket is hozzáadhat. Kiadás fizetős Azure Active Directory épülő a meglévő szabad directory vállalati átfedés önkiszolgáló, a kibővített figyelési, jelentéskészítési biztonsági, többtényezős hitelesítés (MFA) és biztonságos hozzáférést a mobil munkaerő igényeire osztály képességek biztosítása.

> [!NOTE]
> A következő kiadásokra árképzési beállítások, lásd: [Azure Active Directory árképzési](https://azure.microsoft.com/pricing/details/active-directory/). Az Azure Active Directory Premium P1, Premium P2 és Azure Active Directory alapvető jelenleg nem támogatottak Kínában. Lépjen kapcsolatba velünk az Azure Active Directory fórum további információt a.
>

* **Alapszintű Azure Active Directory** -felhő-első igényekkel biztosíthasson az adminisztratív munkatársak tervezték, a kiadás tartalmazza felhő központú alkalmazás-hozzáférést és önkiszolgáló identitáskezelési megoldások. Az Azure Active Directory Alapszintű kiadása különböző hatékonyságnövelő és költségcsökkentő funkciókat biztosít, többek között csoportalapú hozzáférés-kezelést és önkiszolgáló jelszó-visszaállítást felhőalkalmazásokhoz, valamint az Azure Active Directory Application Proxy szolgáltatást, amellyel helyszíni környezetben futó webes alkalmazások tehetők közzé az Azure Active Directoryval – mindezt nagyvállalati szintű, 99,9%-os garantált szolgáltatási szintben vállalt rendelkezésre állás mellett.
* **Az Azure Active Directory Premium P1** - tervezett építve a vállalatok nehezebb identitások és hozzáférések felügyeletéhez szükséges, az Azure Active Directory Premium edition gazdag vállalati szintű identitás-felügyeleti képességét biztosítja, és lehetővé teszi a hibrid felhasználók zavartalanul hozzáférni a helyszíni és a felhőalapú képességek. A kiadás minden szükséges képességgel ellátja a hibrid környezetben dolgozó infomunkásokat és identitáskezelő adminisztrátorokat, legyen szó az alkalmazásokhoz való hozzáférésről, önkiszolgáló identitás- és hozzáférés-kezelési funkciókról, az identitások védelméről és a felhő biztonságáról. Speciális felügyelet és a delegálás erőforrásokhoz, mint a dinamikus csoportok és az önkiszolgáló csoportkezelési támogatja. Magában foglalja a Microsoft Identity Manager (egy a helyszíni identitások és hozzáférések felügyeleti csomag) és a felhőalapú megoldások, például az önkiszolgáló jelszó-visszaállítást a helyi felhasználók engedélyezése késleltetve visszaírt képességeket biztosít.
* **Az Azure Active Directory Premium P2** -kialakítása során a felhasználók és rendszergazdák speciális védelem, az új ajánlat tartalmazza a képességek az Azure AD Premium P1, valamint az új Identity Protection és a Privileged Identity Management. Az Azure Active Directory Identity Protection jelek a kockázat-alapú feltételes hozzáférést biztosítanak az alkalmazások és a kritikus vállalati adatokat egy kihasználja. Azt is súgó kezelése és a kiemelt jogosultságú fiókok Azure Active Directory Privileged Identity Management védi, ezért is felderítheti, korlátozza és figyelje a rendszergazdák és azok erőforrásokhoz való hozzáférésének, és csak időben való hozzáférésre, szükség esetén adja meg.  

> [!NOTE]
> Számos Azure Active Directory képességet "használatalapú" kiadás keresztül érhetők el:
>
> * Active Directory B2C jelenleg a identitás- és hozzáférés-kezelési megoldás a felhasználók felé néző alkalmazásokhoz. További részletekért lásd: [Azure Active Directory B2C-vel](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Az Azure multi-factor Authentication használható keresztül, felhasználónként, vagy a hitelesítésszolgáltatókat. További részletekért lásd: [Mi az Azure multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Hogyan lehet kezdjem el?

**Ha egy informatikai rendszergazda:**

* [Próbálja ki!](https://azure.microsoft.com/trial/get-started-active-directory/) -Regisztráljon a 30 napos ingyenes próbaverzió ma, és ezen a hivatkozáson keresztül a 5 perc múlva a első felhőalapú megoldás üzembe helyezése

* Olvasási [Ismerkedés az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) a tippek és trükkök az első Azure AD bérlői működik, és gyors

**Ha egy fejlesztő:**
 
* Tekintse meg a [fejlesztői útmutató](active-directory-developers-guide.md) az Azure Active Directoryhoz

* [A próbaverzió elindítása](https://azure.microsoft.com/trial/get-started-active-directory/) – Regisztráljon a 30 napos ingyenes próbaverzió ma, és indítsa el az alkalmazások integrálása az Azure ad szolgáltatással

## <a name="next-steps"></a>Következő lépések
[További tudnivalók az Azure identitások és hozzáférések felügyeletéhez alapjai](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
