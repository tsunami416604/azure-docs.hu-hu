---
title: Folyamatos hozzáférés kiértékelése az Azure AD-ben
description: Válaszadás a felhasználói állapot változásaira a folyamatos hozzáférés kiértékelésével az Azure AD-ben
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
ms.openlocfilehash: 3713901dd3dd5d17c4e1ddcef529c663b68f5b43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112575"
---
# <a name="continuous-access-evaluation"></a>Folyamatos hozzáférés-kiértékelés

A Microsoft szolgáltatásai, például a Azure Active Directory (Azure AD) és az Office 365, nyílt szabványok és protokollok használatával maximalizálják az együttműködési képességet. Az egyik legkritikusabb is a Open ID-kapcsolat (OIDC). Ha egy ügyfélalkalmazás, például az Outlook csatlakozik egy szolgáltatáshoz, például az Exchange Online-hoz, az API-kérések a OAuth 2,0 hozzáférési jogkivonatok használatával engedélyezettek. Alapértelmezés szerint ezek a hozzáférési tokenek egy óráig érvényesek. Amikor lejárnak, a rendszer visszairányítja az ügyfelet az Azure AD-be a frissítéshez. Ez lehetőséget biztosít a felhasználói hozzáférésre vonatkozó szabályzatok újraértékelésére is – dönthet úgy, hogy nem frissíti a jogkivonatot egy feltételes hozzáférési szabályzat miatt, vagy mert a felhasználó le van tiltva a címtárban. 

A jogkivonat lejárata és frissítése egy standard mechanizmus az iparágban. Ez azt jelentette, hogy az ügyfelek az adott felhasználóra vonatkozó kockázati feltételek megváltozása esetén felmerülő aggályokkal rendelkeznek (például: a vállalati irodából a helyi kávézóba vagy a fekete piacon felderített felhasználói hitelesítő adatokkal), és ha a szabályzatok az adott változáshoz kapcsolódnak. A "tompa objektum" megközelítéssel kísérleteztek a kisebb jogkivonat-élettartamok miatt, de a felhasználók a kockázatok kiküszöbölése nélkül csökkenthetik a felhasználói élményt és a megbízhatóságot.

A Szabályzat megsértésével vagy biztonsági problémákkal kapcsolatos időben történő válaszadás esetén a token kiállítója, például az Azure AD és a függő entitás (például az Exchange Online) között "beszélgetésre van szükség. Ez a kétirányú beszélgetés két fontos képességet biztosít nekünk. A függő entitás megfigyelheti, hogy mikor változott meg a dolgok, például egy új helyről érkező ügyfél, és tájékoztassa a jogkivonat kiállítóját. Azt is megadja a jogkivonat kiállítójának, hogy elmondja, hogy a függő entitás leállítja a tokenek betartását egy adott felhasználó számára a fiókok veszélyeztetése, a nem megfelelőség vagy más problémák miatt. A beszélgetés mechanizmusa folyamatos hozzáférés kiértékelése (CAE).

A Microsoft az OpenID Foundation [közös jelek és események](https://openid.net/wg/sse/) munkacsoportjának részeként a folyamatos hozzáférés-értékelési protokoll (CAEP) kezdeményezés egyik korai résztvevője. Az identitás-szolgáltatók és a függő entitások képesek lesznek kihasználni a munkacsoport által meghatározott biztonsági eseményeket és jeleket a hozzáférés engedélyezéséhez vagy megszüntetéséhez. Izgalmas munkát tesznek, és számos platformon és alkalmazáson keresztül javítják a biztonságot.

Mivel a biztonsági előnyök olyan nagyszerűek, a Microsoft-specifikus kezdeti implementáció párhuzamosan, a szabványok szervein belüli folyamatos munkavégzés keretében történik. Ahogy dolgozunk ezen a folyamatos hozzáférés-értékelési (CAE) funkcióknak a Microsoft-szolgáltatásokban való üzembe helyezésén, sokat tanultam, és megosztjuk ezeket az információkat a szabványokkal foglalkozó Közösséggel. Reméljük, hogy az üzembe helyezés során felmerülő tapasztalataink segíthetnek a még jobb iparági szabványok bevezetésében, és elkötelezettek a standard ratifikálása után is, így minden résztvevő szolgáltatás Benefit.

## <a name="how-does-cae-work-in-microsoft-services"></a>Hogyan működik a CAE a Microsoft-szolgáltatásokban?

Az Exchange és a Teams szolgáltatás folyamatos hozzáférés-értékelésének kezdeti megvalósítására koncentrálunk. Reméljük, hogy a jövőben további Microsoft-szolgáltatásokra is kiterjesztjük a támogatást. A folyamatos hozzáférés kiértékelését a feltételes hozzáférési szabályzatok nélküli bérlők számára is engedélyezzük. A CAE ezen fázisában megtanuljuk a bevezetését, hogy tájékoztassuk a CAE folyamatos kiépítéséről.

## <a name="service-side-requirements"></a>Szolgáltatási oldali követelmények

A folyamatos hozzáférés kiértékelését a szolgáltatások (erőforrás-szolgáltatók) lehetővé teszik az Azure AD kritikus eseményeire való előfizetéshez, hogy ezek az események közel valós időben legyenek kiértékelve és érvényesítve. Ebben a kezdeti CAE-bevezetésben a következő események lesznek érvényben:

- A felhasználói fiók törölve vagy Letiltva
- A felhasználó jelszavának módosítása vagy alaphelyzetbe állítása
- A rendszergazda explicit módon visszavonja a felhasználó összes frissítési jogkivonatát.
- Emelt szintű felhasználói kockázat észlelhető Azure AD Identity Protection

A jövőben reméljük, hogy további eseményeket fogunk hozzáadni, beleértve az eseményeket, például a hely és az eszköz állapotának változásait. **Habár a célunk az, hogy a kényszerítés azonnali legyen, bizonyos esetekben előfordulhat, hogy az esemény propagálási ideje miatt akár 15 percet**is igénybe vehet. 

## <a name="client-side-claim-challenge"></a>Ügyféloldali jogcímek kihívása

A folyamatos hozzáférés kiértékelése előtt az ügyfelek mindig megpróbálják visszajátszani a hozzáférési tokent a gyorsítótárból mindaddig, amíg nem járt le. A CAE-vel új esetet vezetünk be, hogy egy erőforrás-szolgáltató akkor is elutasíthatja a tokent, ha az nem járt le. Ahhoz, hogy tájékoztassa az ügyfeleket a gyorsítótár megkerüléséről annak ellenére, hogy a gyorsítótárazott tokenek nem jártak le, bevezetünk egy **jogcím-kihívás**nevű mechanizmust. A CAE-nek szüksége van egy ügyfél frissítésére a jogcím-kihívás megismerése érdekében. A következő alkalmazások legújabb verziója támogatja a jogcím kihívását:

- Windows Outlook 
- Outlook iOS 
- Outlook Android 
- Outlook Mac 
- Windows rendszerű csapatok
- Csapatok iOS 
- Csapat Android 
- Csapatok Mac 

## <a name="token-lifetime"></a>Jogkivonat élettartama

Mivel a kockázatok és a házirendek valós időben lettek kiértékelve, a folyamatos hozzáférés-értékelést tárgyaló ügyfelek a meglévő statikus hozzáférési jogkivonat élettartam-szabályzatai helyett a CAE-t használják, ami azt jelenti, hogy a konfigurálható jogkivonat-élettartamra vonatkozó házirend többé nem lesz betartva a CAE-kompatibilis munkameneteket tárgyaló, a CAE-t támogató ügyfelek számára.

A hozzáférési token élettartamát 24 órára növeljük a CAE-munkamenetekben. A visszavonást a kritikus események és a házirendek kiértékelése vezérli, nem pedig egy tetszőleges időszakra. Ez a változás növeli az alkalmazások stabilitását anélkül, hogy ez hatással lenne a biztonsági helyzetre. 

## <a name="example-flows"></a>Példa folyamatokra

### <a name="user-revocation-event-flow"></a>Felhasználó visszavonási eseményének folyamata:

![Felhasználó visszavonási eseményének folyamata](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. A CAE-kompatibilis ügyfelek bemutatják a hitelesítő adatokat vagy egy frissítési jogkivonatot, amely HRE kér egy bizonyos erőforrás hozzáférési jogkivonatát.
1. A rendszer a hozzáférési jogkivonatot más összetevőkkel együtt adja vissza az ügyfélnek.
1. A rendszergazda explicit módon [visszavonja a felhasználó összes frissítési jogkivonatát](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). A rendszer visszavonási eseményt küld az erőforrás-szolgáltatónak az Azure AD-től.
1. Az erőforrás-szolgáltató hozzáférési tokent mutat be. Az erőforrás-szolgáltató kiértékeli a jogkivonat érvényességét, és ellenőrzi, hogy van-e visszavonási esemény a felhasználó számára. Az erőforrás-szolgáltató ezt az információt használja annak eldöntésére, hogy hozzáférést biztosítson az erőforráshoz, vagy sem.
1. Ebben az esetben az erőforrás-szolgáltató megtagadja a hozzáférést, és egy 401 + jogcím-feladatot küld vissza az ügyfélnek.
1. A CAE-kompatibilis ügyfél tisztában van a 401 + jogcím kihívással. Megkerüli a gyorsítótárat, és visszakerül az 1. lépésre, és visszaküldi a frissítési tokent az Azure AD-re vonatkozó jogcím-kihívással együtt. Az Azure AD ezután újraértékeli az összes feltételt, és megkéri a felhasználót, hogy végezze el az újrahitelesítést ebben az esetben.
 
## <a name="faqs"></a>Gyakori kérdések

### <a name="what-is-the-lifetime-of-my-access-token"></a>Mi a hozzáférési token élettartama?

Ha nem használ CAE-kompatibilis ügyfeleket, akkor az alapértelmezett hozzáférési jogkivonat élettartama még 1 óra lesz, hacsak nem konfigurálta a hozzáférési jogkivonat élettartamát a [konfigurálható jogkivonat élettartama (CTL)](../develop/active-directory-configurable-token-lifetimes.md) előzetes verziójával.

Ha olyan CAE-kompatibilis ügyfeleket használ, amelyek a CAE-t támogató munkameneteket egyeztetik, a hozzáférési jogkivonat élettartamára vonatkozó CTL-beállítások felül lesznek írva, és a hozzáférési jogkivonat élettartama 24 óra lesz.

### <a name="how-quick-is-enforcement"></a>Milyen gyors a kényszerítés?

Habár a célunk az, hogy a kényszerítés azonnali legyen, bizonyos esetekben előfordulhat, hogy az esemény propagálási ideje miatt akár 15 percet is igénybe vehet.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hogyan működik a CAE a bejelentkezési gyakorisággal?

A bejelentkezések gyakorisága a CAE-vel vagy anélkül is megbecsülhető.

## <a name="next-steps"></a>További lépések

[A folyamatos hozzáférés kiértékelésének bejelentése](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
