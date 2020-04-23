---
title: Folyamatos hozzáférés-kiértékelés az Azure AD-ben
description: Gyorsabb annektadás a felhasználói állapot változásaira az Azure AD folyamatos hozzáférés-kiértékelésével
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873302"
---
# <a name="continuous-access-evaluation"></a>Folyamatos hozzáférés-értékelés

A Microsoft-szolgáltatások, például az Azure Active Directory (Azure AD) és az Office 365 nyílt szabványokat és protokollokat használnak az együttműködés maximalizálása érdekében. Az egyik legkritikusabb az Open ID Connect (OIDC). Amikor egy ügyfélalkalmazás, például az Outlook csatlakozik egy szolgáltatáshoz, például az Exchange Online-hoz, az API-kérelmek oauth 2.0-s hozzáférési jogkivonatokkal engedélyezettek. Alapértelmezés szerint ezek a hozzáférési jogkivonatok egy óráig érvényesek. Ha lejárnak, az ügyfél visszairányítja az Azure AD-hez, hogy frissítse őket. Ez azt is lehetővé teszi, hogy újraértékelje a szabályzatok a felhasználói hozzáférés – előfordulhat, hogy nem frissíti a jogkivonatot, mert a feltételes hozzáférési házirend, vagy mert a felhasználó le van tiltva a címtárban. 

Hallottuk az ügyfeleink elsöprő visszajelzéseit: a feltételes hozzáférési szabályzatok újraalkalmazásának hozzáférési jogkivonat-élettartama és a felhasználói állapot változásai (például: a szabadság miatt letiltva) módosításai miatt nem elég jó.

A Microsoft az OpenID Foundation [megosztott jelzések és események](https://openid.net/wg/sse/) munkacsoportjának részeként a Folyamatos hozzáférés értékelési protokoll (Continuous Access Evaluation Protocol, CAEP) kezdeményezés korai résztvevője volt. Az identitásszolgáltatók és a függő felek a munkacsoport által meghatározott biztonsági eseményeket és jelzéseket használhatják a hozzáférés újbóli engedélyezéséhez vagy megszüntetéséhez. Ez izgalmas munka, és javítja a biztonságot számos platformon és alkalmazásban.

Mivel a biztonsági előnyök olyan nagyok, egy Microsoft-specifikus kezdeti implementációt vezetünk be a szabványügyi testületeken belüli folyamatos munkánkkal párhuzamosan. Miközben azon dolgozunk, hogy ezeket a folyamatos hozzáférés-kiértékelési (CAE) képességeket telepítsük a Microsoft szolgáltatásaiban, sokat tanultunk, és ezeket az információkat megosztjuk a szabványügyi közösséggel. Reméljük, hogy a telepítéssel kapcsolatos tapasztalataink segíthetnek egy még jobb iparági szabvány tájékoztatásában, és elkötelezettek vagyunk a szabvány ratifikálása után történő megvalósítása mellett, lehetővé téve, hogy minden részt vevő szolgáltatás számára előnyös legyen.

## <a name="how-does-cae-work-in-microsoft-services"></a>Hogyan működik a CAE a Microsoft-szolgáltatásokban?

Az Exchange és a Teams folyamatos hozzáférés-értékelésének kezdeti megvalósítására összpontosítunk. Reméljük, hogy a jövőben kiterjesztjük a támogatást más Microsoft-szolgáltatásokra is. Elkezdjük engedélyezni a folyamatos hozzáférés kiértékelését csak a feltételes hozzáférési szabályzatokkal nem rendelkező bérlők számára. A CAE e fázisából származó tanulásainkat arra fogjuk használni, hogy tájékoztassuk a CAE folyamatos bevezetését.

## <a name="service-side-requirements"></a>Szolgáltatási oldali követelmények

A folyamatos hozzáférés-kiértékelést úgy valósítja meg, hogy lehetővé teszi a szolgáltatások (erőforrás-szolgáltatók) számára, hogy az Azure AD kritikus eseményeire iratkozzanak fel, hogy ezek az események kiértékelhetők és kikényszeríthetők közel valós időben. Ebben a kezdeti CAE-bevezetésben a következő események lesznek érvényesítve:

- A felhasználói fiók törlődik vagy le van tiltva
- A felhasználó jelszava megváltozik vagy alaphelyzetbe áll
- A rendszergazda kifejezetten visszavonja a felhasználó összes frissítési jogkivonatát
- Az Azure AD Identity Protection által észlelt fokozott felhasználói kockázat

A jövőben reméljük, hogy további eseményeket, például hely- és eszközállapot-változásokat hozunk hozzá. **Míg a célunk az, hogy a végrehajtás azonnali legyen, egyes esetekben akár 15 perces késés figyelhető meg az esemény terjedési ideje miatt.** 

## <a name="client-side-claim-challenge"></a>Ügyféloldali igénylési kihívás

A folyamatos hozzáférés kiértékelése előtt az ügyfelek mindig megpróbálják visszajátszani a hozzáférési jogkivonatot a gyorsítótárból, amíg az nem járt le. A CAE-vel egy új esetet vezetünk be, amelyet egy erőforrás-szolgáltató akkor is elutasíthat, ha az még nem járt le. Annak érdekében, hogy tájékoztassa az ügyfeleket, hogy megkerülje a cache annak ellenére, hogy a gyorsítótárazott tokenek még nem járt le, bevezetjük a mechanizmus úgynevezett **követelés kihívás**. A CAE-nek ügyfélfrissítésre van szüksége a jogcímkihívás megértéséhez. Az alábbi alkalmazások legújabb verziója támogatási igénylési kihívás:

- A Windows Outlook 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Windows-csapatok
- Csapatok iOS 
- Csapatok Android 
- Csapatok Mac 

## <a name="token-lifetime"></a>Jogkivonat élettartama

Mivel a kockázat és a szabályzat kiértékelése valós időben történik, a folyamatos hozzáférés-kiértékelésre képes munkameneteket egyeztető ügyfelek a meglévő statikus hozzáférési jogkivonat élettartamra vonatkozó szabályzatai helyett a CAE-munkamenetekre támaszkodnak, ami azt jelenti, hogy a konfigurálható jogkivonat élettartamra vonatkozó szabályzata többé nem lesz tiszteletben a CAE-kompatibilis ügyfelek számára, amelyek cae-kompatibilis munkameneteket tárgyalnak.

A hozzáférési jogkivonat élettartamát 24 órára növeljük a CAE-munkamenetekben. A visszavonást kritikus események és házirend-értékelés vezérli, nem pedig egy tetszőleges időszak. Ez a módosítás növeli az alkalmazások stabilitását anélkül, hogy befolyásolná a biztonsági pozíciót. 

## <a name="example-flows"></a>Példa folyamatokra

### <a name="user-revocation-event-flow"></a>Felhasználói visszavonási eseményfolyamat:

![Felhasználói visszavonási eseményfolyamat](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. A CAE-képes ügyfél bemutatja a hitelesítő adatokat vagy egy frissítési jogkivonatot az AAD-nek, amely egy erőforrás hozzáférési jogkivonatát kéri.
1. A rendszer egy hozzáférési jogkivonatot ad vissza az ügyfélnek más összetevőkkel együtt.
1. A rendszergazda kifejezetten [visszavonja a felhasználó összes frissítési jogkivonatát.](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) A rendszer visszavonási eseményt küld az erőforrás-szolgáltatónak az Azure AD-ből.
1. Egy hozzáférési jogkivonat jelenik meg az erőforrás-szolgáltató. Az erőforrás-szolgáltató kiértékeli a jogkivonat érvényességét, és ellenőrzi, hogy van-e visszavonási esemény a felhasználó számára. Az erőforrás-szolgáltató ezt az információt arra használja, hogy úgy döntsön, hogy hozzáférést biztosít-e az erőforráshoz, vagy sem.
1. Ebben az esetben az erőforrás-szolgáltató megtagadja a hozzáférést, és 401+ jogcímkihívást küld vissza az ügyfélnek
1. A CAE-képes ügyfél megérti a 401+ igénylési kihívást. Megkerüli a gyorsítótárakat, és visszatér az 1. Az Azure AD ezután újraértékeli az összes feltételt, és ebben az esetben a felhasználót újrahitelesíti.
 
## <a name="faqs"></a>Gyakori kérdések

### <a name="what-is-the-lifetime-of-my-access-token"></a>Mi a hozzáférési jogkivonatom élettartama?

Ha nem cae-kompatibilis ügyfeleket használ, az alapértelmezett hozzáférési jogkivonat élettartama továbbra is 1 óra marad, hacsak nem konfigurálta a hozzáférési jogkivonat élettartamát a [konfigurálható jogkivonat élettartama (CTL)](../develop/active-directory-configurable-token-lifetimes.md) előzetes funkcióval.

Ha CAE-kompatibilis ügyfeleket használ, amelyek egyeztetik a CAE-kompatibilis munkameneteket, a hozzáférési jogkivonat élettartamára vonatkozó CTL-beállítások felülíródnak, és az Access Token élettartama 24 óra lesz.

### <a name="how-quick-is-enforcement"></a>Milyen gyors a végrehajtás?

A célunk az, hogy a kényszerítés azonnali legyen, bizonyos esetekben az esemény terjedési ideje miatt legfeljebb 15 perces késés figyelhető meg.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hogyan fog működni a CAE a bejelentkezési gyakorisággal?

A bejelentkezési gyakoriság cae-vel vagy anélkül lesz megtisztelve.

## <a name="next-steps"></a>További lépések

[A hozzáférés folyamatos értékelésének bejelentése](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
