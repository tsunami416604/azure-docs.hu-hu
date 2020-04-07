---
title: Feltételes hozzáférés – Többszintű szolgáltatás megkövetelése minden felhasználó számára – Azure Active Directory
description: Egyéni feltételes hozzáférési házirend létrehozása, amely megköveteli, hogy minden felhasználó többtényezős hitelesítést hajtson végre
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3e7b1a656c92e37a709b57dae463f6644003e42
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755189"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Feltételes hozzáférés: Többkori licenc megkövetelése az összes felhasználó számára

Mint Alex Weinert, a Directory of Identity Security a Microsoft, megemlíti az ő [blogbejegyzést A Pa $ $word nem számít:](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

> A jelszó nem számít, de MFA nem! Kutatásaink alapján fiókja több mint 99,9%-kal kisebb valószínűséggel kerül veszélybe, ha többfa-t használ.

Az ebben a cikkben található útmutatás segít a szervezetnek egy kiegyensúlyozott MFA-házirend létrehozásában a környezetszámára.

## <a name="user-exclusions"></a>Felhasználói kizárások

A feltételes hozzáférési szabályzatok hatékony eszközök, javasoljuk, hogy zárja ki a következő fiókokat a szabályzatból:

* **Vészelérési** vagy **törésüveg-fiókok** a bérlői fiók zárolásának megakadályozása érdekében. Abban a valószínűtlen esetben az összes rendszergazda ki van zárva a bérlőből, a vészelérési felügyeleti fiók segítségével jelentkezzen be a bérlőbe lépéseket a hozzáférés helyreállítása érdekében.
   * További információ a cikkben található, [a vészelérési fiókok kezelése az Azure AD-ben.](../users-groups-roles/directory-emergency-access.md)
* **Szolgáltatásfiókok** és **egyszerű szolgáltatás,** például az Azure AD Connect szinkronizálási fiók. A szolgáltatásfiókok olyan nem interaktív fiókok, amelyek nem kötődnek egy adott felhasználóhoz. Ezeket általában az alkalmazásokhoz programozott hozzáférést lehetővé tevő háttérszolgáltatások használják, de a rendszerekbe való rendszergazdai célú bejelentkezésre is használják őket. Az ilyen szolgáltatásfiókokat ki kell zárni, mivel az MFA nem hajtható végre programozott módon. A szolgáltatásnévi szolgáltatások hívásait a feltételes hozzáférés nem blokkolja.
   * Ha a szervezet ezeket a fiókokat parancsfájlokban vagy kódokban használja, fontolja meg, hogy lecseréli őket [felügyelt identitásokra.](../managed-identities-azure-resources/overview.md) Ideiglenes megoldásként ezeket a konkrét fiókokat kizárhatja az alaptervi házirendből.

## <a name="application-exclusions"></a>Alkalmazáskizárások

Előfordulhat, hogy a szervezetek számos felhőalapú alkalmazással rendelkeznek. Nem minden ilyen alkalmazás igényel azonos biztonságot. Például a bérszámfejtési és jelenléti kérelmek hez szükség lehet az MFA-ra, de a kávézó valószínűleg nem. A rendszergazdák kizárhatnak bizonyos alkalmazásokat a házirendjükből.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési házirend létrehozása

A következő lépések segítségével hozzon létre egy feltételes hozzáférési szabályzatot, amely megköveteli a hozzárendelt felügyeleti szerepköröktől a többtényezős hitelesítés elvégzéséhez.

1. Jelentkezzen be az **Azure Portalon** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza az **Új házirend lehetőséget.**
1. Adjon nevet a szabályzatának. Azt javasoljuk, hogy a szervezetek hozzanak létre egy értelmes szabvány a házirendek nevét.
1. A **Hozzárendelések**csoportban válassza a **Felhasználók és csoportok lehetőséget.**
   1. A **Belefoglalás**csoportban válassza a **Minden felhasználó lehetőséget.**
   1. A **Kizárás csoportban**válassza a **Felhasználók és csoportok** lehetőséget, és válassza ki a szervezet vészelérési vagy törésüvegfiókjait. 
   1. Válassza a **Done** (Kész) lehetőséget.
1. A **Felhőalapú alkalmazások vagy műveletek** > közé tartozik a**Include csoportban**válassza az Összes **felhőalapú alkalmazás**lehetőséget.
   1. A **Kizárás csoportban**jelölje ki azokat az alkalmazásokat, amelyek nem igényelnek többtényezős hitelesítést.
1. A **Feltételek** > **az ügyfélalkalmazások (előzetes verzió)** csoportban állítsa a **Konfigurálás** beállítást **Igen**értékre, és válassza a **Kész gombot.**
1. A**Grant** **access-vezérlők csoportban** > válassza a **Hozzáférési támogatás**lehetőséget, válassza a **Többtényezős hitelesítés megkövetelése**lehetőséget, majd a **Kijelölés**lehetőséget.
1. Erősítse meg a beállításokat, és állítsa a **Házirend engedélyezése** **be**beállítást.
1. A létrehozás gombra a házirend engedélyezéséhez válassza a **Létrehozás** gombot.

### <a name="named-locations"></a>Nevesített helyek

A szervezetek dönthetnek úgy, hogy feltételes hozzáférési házirendjeikbe beépítik az ismert hálózati **helyeket, amelyeket named helynek** neveznek. Ezek a megnevezett helyek megbízható IPv4-hálózatokat is tartalmazhatnak, például a fő irodahelyhez hasonló hálózatokat. Az elnevezett helyek konfigurálásáról további információt a [Mi a helyfeltétel az Azure Active Directory feltételes hozzáférésében?](location-condition.md)

A fenti példaházirendben a szervezet dönthet úgy, hogy nem igényel többtényezős hitelesítést, ha egy felhőalapú alkalmazást a vállalati hálózatról fér hozzá. Ebben az esetben a következő konfigurációt adhatják hozzá a házirendhez:

1. A **Hozzárendelések**csoportban válassza a **Feltételek** > **helyek lehetőséget.**
   1. Állítsa be **az Igen**lehetőséget.
   1. **Tartalmazza a bármely helyet.**
   1. **Az összes megbízható hely kizárása**.
   1. Válassza a **Done** (Kész) lehetőséget.
1. Válassza a **Done** (Kész) lehetőséget.
1. **Mentse** a házirend-módosításokat.

## <a name="next-steps"></a>További lépések

[Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

[Hatás meghatározása csak feltételes hozzáférésű jelentésmódhasználatával](howto-conditional-access-report-only.md)

[Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)
