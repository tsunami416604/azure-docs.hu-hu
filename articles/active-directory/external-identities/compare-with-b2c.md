---
title: Külső identitások összehasonlítása – Azure Active Directory | Microsoft Docs
description: Az Azure AD külső identitásai lehetővé teszik a szervezeten kívüli személyek számára, hogy saját identitásukat használva hozzáférjenek alkalmazásaihoz és erőforrásaihoz. Külső identitásokkal kapcsolatos megoldások összehasonlítása, beleértve a Azure Active Directory B2B együttműködés és a Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08623e6d69ac4db1790c1e9b46089f0c72c0526d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87906062"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Mik a külső identitások a Azure Active Directoryban?

Az Azure AD külső identitásokkal lehetővé teheti, hogy a szervezeten kívüli személyek hozzáférjenek az alkalmazásokhoz és az erőforrásokhoz, miközben a felhasználó bármilyen identitással bejelentkezhetnek. Partnerei, terjesztői, szállítói, szállítói és más vendégei "saját identitást hozhatnak". Függetlenül attól, hogy az Azure AD vagy más IT által felügyelt rendszer részét képezik-e, vagy olyan nem felügyelt közösségi identitással rendelkezik, mint a Google vagy a Facebook, a saját hitelesítő adataikat használhatják a bejelentkezéshez. Az identitás-szolgáltató kezeli a külső felhasználó identitását, és az Azure AD-vel kezelheti az alkalmazásaihoz való hozzáférést, hogy az erőforrások védve legyenek. 

## <a name="external-identities-scenarios"></a>Külső identitások forgatókönyvei

Az Azure AD külső identitások kevesebbet mutatnak a felhasználó és a szervezet közötti kapcsolaton, és így az egyes felhasználók szeretnének bejelentkezni az alkalmazásaiba és erőforrásaiba. Ebben a keretrendszerben az Azure AD különböző forgatókönyveket támogat a vállalatok közötti (B2B) együttműködésből az ügyfelek és a fogyasztók (vállalat – fogyasztó vagy B2C) alkalmazások fejlesztéséhez.

- **Alkalmazások megosztása külső felhasználókkal (B2B-együttműködés)**. A külső felhasználók meghívása a saját bérlőbe "vendég" felhasználóként, hogy engedélyeket rendeljen hozzá (az engedélyezéshez), miközben lehetővé teszi számukra a meglévő hitelesítő adatok (hitelesítéshez) használatát. A felhasználók egy egyszerű meghívás és beváltási folyamat használatával jelentkeznek be a megosztott erőforrásokra a munkahelyi fiókkal, az iskolai fiókkal vagy bármely e-mail-fiókkal. Az önkiszolgáló bejelentkezési felhasználói folyamatok (előzetes verzió) rendelkezésre állása mellett a külső felhasználók számára is megadhat bejelentkezési élményt a megosztani kívánt alkalmazáson keresztül. A felhasználói folyamat beállításait beállíthatja annak szabályozására, hogy a felhasználó hogyan regisztrálja az alkalmazást, és lehetővé teszi a felhasználók számára a munkahelyi fiók, az iskolai fiók vagy bármilyen közösségi identitás (például a Google vagy a Facebook) használatát.  További információt az [Azure ad B2B dokumentációjában](index.yml)talál.

- **Más Azure ad-bérlőknek szánt alkalmazások fejlesztése (egybérlős vagy több-bérlős)**. Az Azure AD-alkalmazások fejlesztésekor megcélozhatja a felhasználókat egyetlen szervezetből (egyetlen bérlőből), vagy bármely olyan szervezet felhasználóitól, amely már rendelkezik Azure AD-Bérlővel (több-bérlős alkalmazásokkal). Ezeket a több-bérlős alkalmazásokat a saját Azure AD-ben saját maga regisztrálja, de bármely szervezet bármelyik felhasználója felhasználhatja az Ön részéről további munka nélkül.

- **A felhasználók és az ügyfelek számára készült, fehér címkével ellátott alkalmazások fejlesztése (Azure ad B2C)**. Ha Ön üzleti vagy fejlesztői ügyfélre irányuló alkalmazást hoz létre, akkor Azure AD B2C segítségével méretezheti a felhasználókat, az ügyfeleket és a polgárokat. A fejlesztők az Azure ad-t teljes funkcionalitású identitásrendszer használhatják az alkalmazáshoz, miközben az ügyfelek a már létrehozott identitással jelentkeznek be (például a Facebook vagy a Gmail szolgáltatásban). A Azure AD B2C segítségével teljes mértékben testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat az alkalmazások használatakor. További információ: [Azure ad B2C dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/).

## <a name="compare-external-identities-solutions"></a>External Identities-megoldások összehasonlítása

Az alábbi táblázat részletesen összehasonlítja a különböző forgatókönyveket, amelyekkel engedélyezheti az Azure AD külső identitásait.

| Több-bérlős alkalmazások  | Külső felhasználói együttműködés (B2B) | Alkalmazások fogyasztók vagy ügyfelek számára (B2C)  |
| ---- | --- | --- |
| Elsődleges forgatókönyv: vállalati szoftver-szolgáltatás (SaaS) | Elsődleges forgatókönyv: együttműködés a Microsoft-alkalmazásokkal (Microsoft 365, csapatok,...) vagy a saját csoportmunka-szoftverrel.  | Elsődleges forgatókönyv: az egyéni fejlesztésű alkalmazásokat használó tranzakciós alkalmazások.   |
| A következő célra készült: olyan szervezetek, amelyek számos nagyvállalati ügyfél számára kívánnak szoftvert nyújtani.    | Kinek készült: olyan szervezeteknek, ahol igény van a partner szervezetek felhasználóinak hitelesítésére az identitásszolgáltatótól függetlenül.    | Cél: a mobil-és webalkalmazások ügyfeleinek meghívása, függetlenül attól, hogy magánszemélyek, intézményi vagy szervezeti ügyfelek egy Azure AD-címtárban legyenek elkülönítve a saját szervezete címtárával. |
| Támogatott identitások: az Azure AD-fiókkal rendelkező alkalmazottak. | Támogatott identitások: Munkahelyi vagy iskolai fiókkal rendelkező alkalmazottak, munkahelyi vagy iskolai fiókkal vagy e-mail címmel rendelkező partnerek. Hamarosan támogatni fogja a közvetlen összevonást.      | Támogatott identitások: Fogyasztói felhasználók helyi vonatkozású fiókkal (bármilyen e-mail-cím vagy felhasználónév), vagy bármely támogatott közösségi identitás közvetlen összevonással.       |
| A külső felhasználók felügyelete a saját címtárban történik, amely az alkalmazás regisztrálásának helyétől elkülönített könyvtárból van elkülönítve.    | A külső felhasználók kezelése az alkalmazottakkal megegyező könyvtárban történik, de kifejezetten megjegyzésekkel ellátva. Az alkalmazottakkal megegyező módon kezelhetők, de hozzáadhatók ugyanahhoz a csoporthoz, és így tovább.    | A külső felhasználókat az alkalmazás könyvtára kezeli. Ezeket külön kezelik a szervezet alkalmazotti és partneri könyvtára (ha van ilyen).  |
| Egyszeri bejelentkezés: az egyszeri bejelentkezés minden Azure AD-hez csatlakoztatott alkalmazáshoz támogatott.          | Egyszeri bejelentkezés: az egyszeri bejelentkezés minden Azure AD-hez csatlakoztatott alkalmazáshoz támogatott. Hozzáférést biztosíthat például Microsoft 365 vagy helyszíni alkalmazásokhoz, valamint más SaaS-alkalmazásokhoz, például Salesforce vagy munkanapokhoz.    | Egyszeri bejelentkezés: az Azure AD B2C bérlőn belül az ügyfél tulajdonában lévő alkalmazásokhoz való egyszeri bejelentkezés támogatott. Az egyszeri bejelentkezés Microsoft 365 vagy más Microsoft SaaS-alkalmazásokhoz nem támogatott.    |
| Ügyfél-életciklus: a felhasználó otthoni szervezete felügyeli.      | Partner életciklus: A vendéglátó/meghívó szervezet felügyeli.    | Ügyféléletciklus: Önkiszolgáló vagy az alkalmazás felügyeli.      |
| Biztonsági házirend és megfelelőség: a gazdagép/meghívó szervezet (például [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)) által felügyelt.           | Biztonsági házirend és megfelelőség: a gazdagép/meghívó szervezet (például [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)) által felügyelt. | Biztonsági házirend és megfelelőség: Az alkalmazás kezeli.        |
| Branding: a gazdagép és a meghívó szervezet márkája használatban van.   | Branding: a gazdagép és a meghívó szervezet márkája használatban van.    | Védjegyek: Az alkalmazás kezeli. Általában termékek márkái láthatók, a szervezet inkább háttérbe szorul.   |
| További információ: [Identitások kezelése több-bérlős alkalmazásokban](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [útmutató](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | További információk: [Blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentáció](what-is-b2b.md)                   | További információk: [Termék oldala](https://azure.microsoft.com/services/active-directory-b2c/), [Dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Az Azure AD külső identitásokkal biztonságossá teheti és kezelheti a szervezet határain túli ügyfeleket és partnereket.

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Tudnivalók az Azure AD B2C-ről](https://docs.microsoft.com/azure/active-directory-b2c/overview)
