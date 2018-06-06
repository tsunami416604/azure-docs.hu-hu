---
title: Mi az Azure Active Directory (Azure AD)? | Microsoft Docs
description: Azure Active Directory használata a meglévő helyszíni identitások kiterjeszti a felhőbe, vagy az Azure AD integrált alkalmazások fejlesztéséhez.
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
ms.topic: article
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: daeda2a44cbfd39cd2b796f9d1fe1327e93bf4aa
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714204"
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory?
Azure Active Directory (Azure AD) a rendszer a Microsoft több-bérlős, felhőalapú címtár, identity management szolgáltatás, amely core címtárszolgáltatások, alkalmazás-hozzáférés kezelése és egységes megoldássá azonosító adatok védelmét. Az Azure AD is biztosít, amely lehetővé teszi a fejlesztők számára a hozzáférés-vezérlést biztosítanak az alkalmazások központi házirend és a szabályok alapján a gazdag, szabványokon alapuló platformja.

![Az Azure AD Connect-verem](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Az IT-rendszergazdák.** Az Azure AD a szervezet erősebb Identitáskezelés és egyszeri bejelentkezés (SSO) hozzáférést több ezer biztonságosabb megoldást kínál [felhőalapú Szolgáltatottszoftver-alkalmazásoknál](active-directory-saas-tutorial-list.md) és a helyszíni alkalmazások. Ezen alkalmazások esetében keresztül is kap app felhőalapú biztonsági, zökkenőmentes hozzáférést, speciális együttműködés és az identitás-életciklus automation alkalmazottai számára, elősegíti a biztonság és a megfelelőség növelése.

    Emellett a csak [négy kattintással](./connect/active-directory-aadconnect-get-started-express.md), az Azure AD integrálása egy meglévő Windows Server Active Directory, ha engedélyezi, hogy a szervezet használja a meglévő helyszíni identitás beruházások kezeléséhez felhőalapú Szolgáltatottszoftver-alkalmazás eléréséhez .

- **Alkalmazás-fejlesztőknek.** Az Azure AD lehetővé teszi az alkalmazások létrehozása és egy identitás-felügyeleti megoldás, amellyel a szervezetek világszerte több millió integrációjához azáltal koncentrálhat.

- **Az Office 365, Azure vagy a Dynamics CRM Online ügyfelek.** Az Azure AD már használja. Minden egyes Office 365, Azure és a Dynamics CRM Online bérlő az ténylegesen az Azure AD-bérlő, és így azonnal megkezdik alkalmazott-hozzáférési jogát a integrált felhőalapú alkalmazásokat kezeléséhez.

## <a name="how-reliable-is-azure-ad"></a>Hogyan megbízható az Azure AD?
A több-bérlős, földrajzilag elosztott, magas rendelkezésre állású kialakítása az Azure AD azt jelenti, hogy támaszkodhat a kritikus fontosságú üzleti igényeknek. Elfogy a világ különböző 28 adatközpontok automatikus feladatátvétellel, konfigurálnia kell a kényelmét ismerete, hogy az Azure AD megbízható és, hogy akkor is, ha egy adott adatközpont leáll, a címtár esetén a működés közbeni legalább két további – elosztott adatközpontokban példányát és azonnali hozzáférési érhető el.

Szolgáltatásszint-szerződések kapcsolatos további információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Válassza ki a kiadás
Bejelentkezés az Azure Active Directory (Azure AD) támaszkodnak az összes Microsoft Online szolgáltatás, és más az identitásukat. Ha előfizet a Microsoft Online üzleti szolgáltatások (például Office 365 vagy a Microsoft Azure), emellett hozzáférhetnek az összes szabad szolgáltatás az Azure AD kap. Az Azure Active Directory ingyenes kiadásával is kezelhető felhasználókat és csoportokat, szinkronizálja a helyszíni címtárak, Azure, az Office 365 és a népszerű SaaS-alkalmazásokhoz Salesforce, Workday, Concur, DocuSign, Google Apps, mező, ServiceNow, dropbox-ba, és több ezer egyszeri bejelentkezés beolvasása. 

Az Azure Active Directory javítása érdekében, az Azure Active Directory Basic, a Premium P1 és a Premium P2 verziójával fizetős képességeket is hozzáadhat. Kiadás fizetős Azure Active Directory épülő a meglévő szabad directory vállalati átfedés önkiszolgáló, a kibővített figyelési, jelentéskészítési biztonsági, többtényezős hitelesítés (MFA) és biztonságos hozzáférést a mobil munkaerő igényeire osztály képességek biztosítása.

> [!NOTE]
> A következő kiadásokra árképzési beállítások, lásd: [Azure Active Directory árképzési](https://azure.microsoft.com/pricing/details/active-directory/). Az Azure Active Directory Premium P1, Premium P2 és Azure Active Directory alapvető jelenleg nem támogatottak Kínában. Az Azure AD árazással kapcsolatos további információkért lépjen kapcsolatba az Azure Active Directory fórumán.
>

* **Alapszintű Azure Active Directory** -felhő-első igényekkel biztosíthasson az adminisztratív munkatársak tervezték, a kiadás tartalmazza felhő-központú alkalmazás hozzáférést és önkiszolgáló identitáskezelési megoldások. Az Azure Active Directory Alapszintű kiadása különböző hatékonyságnövelő és költségcsökkentő funkciókat biztosít, többek között csoportalapú hozzáférés-kezelést és önkiszolgáló jelszó-visszaállítást felhőalkalmazásokhoz, valamint az Azure Active Directory Application Proxy szolgáltatást, amellyel helyszíni környezetben futó webes alkalmazások tehetők közzé az Azure Active Directoryval – mindezt nagyvállalati szintű, 99,9%-os garantált szolgáltatási szintben vállalt rendelkezésre állás mellett.
* **Az Azure Active Directory Premium P1** - tervezett építve a vállalatok nehezebb identitások és hozzáférések felügyeletéhez szükséges, az Azure Active Directory Premium edition hozzáadja gazdag vállalati szintű identitás-kezelési képességei és lehetővé teszi a hibrid felhasználók zavartalanul hozzáférni a helyszíni és a felhőalapú képességek. Ez a kiadás tartalmaz mindent, ami szükséges információkkal dolgozó szakember és hibrid környezetekben identitás rendszergazdák különböző alkalmazás-hozzáférés, önkiszolgáló identitáskezelési és hozzáférés-kezelés (IAM) azonosító adatok védelmét és biztonsági a felhőben. Speciális felügyelet és a delegálás erőforrásokhoz, mint a dinamikus csoportok és az önkiszolgáló csoportkezelési támogatja. Magában foglalja a Microsoft Identity Manager (egy a helyszíni identitások és hozzáférések felügyeleti csomag) és a felhőalapú megoldások, például az önkiszolgáló jelszó-visszaállítást a helyi felhasználók engedélyezése késleltetve visszaírt képességeket biztosít.
* **Az Azure Active Directory Premium P2** -kialakítása során a felhasználók és rendszergazdák speciális védelem, az új ajánlat tartalmazza a képességek az Azure AD Premium P1, valamint a azonosító adatok védelmét és a Privileged Identity Management. Az Azure Active Directory Identity Protection jelek a kockázat-alapú feltételes hozzáférést biztosítanak az alkalmazások és a kritikus vállalati adatokat egy kihasználja. Azt is súgó kezelése és a kiemelt jogosultságú fiókok Azure Active Directory Privileged Identity Management védi, ezért is felderítheti, korlátozza, és rendszergazdák és azok erőforrásokhoz való hozzáférés figyelésére, és csak időben való hozzáférésre, szükség esetén adja meg.  

> [!NOTE]
> Számos Azure Active Directory képességet "használatalapú" kiadás keresztül érhetők el:
>
> * Active Directory B2C jelenleg a identitás- és hozzáférés-kezelési megoldás a felhasználók felé néző alkalmazásokhoz. További információkért lásd: [Azure Active Directory B2C-vel](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * Az Azure multi-factor Authentication használható keresztül, felhasználónként, vagy a hitelesítésszolgáltatókat. További információkért lásd: [Mi az Azure multi-factor Authentication?](authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Hogyan lehet kezdjem el?

**Ha egy informatikai rendszergazda:**

* [Próbálja ki!](https://azure.microsoft.com/trial/get-started-active-directory/) -Regisztráljon egy ingyenes 30 napos próbaverzió ma, és ezen a hivatkozáson keresztül az öt percen belül a első felhőalapú megoldás üzembe helyezése

* Olvasási [Ismerkedés az Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) a tippek és trükkök az első Azure AD bérlői működik, és gyors

**Ha egy fejlesztő:**
 
* Tekintse meg a [fejlesztői útmutató](active-directory-developers-guide.md) az Azure Active Directoryhoz

* [A próbaverzió elindítása](https://azure.microsoft.com/trial/get-started-active-directory/) – regisztráljon egy ingyenes 30 napos próbaverzió ma, és indítsa el az alkalmazások integrálása az Azure ad szolgáltatással

## <a name="next-steps"></a>További lépések
[További tudnivalók az Azure identitások és hozzáférések felügyeletéhez alapjai](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
