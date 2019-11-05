---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 6f2b5eb96eeb1c4b7d07219d5fe54a8a0ca9e28a
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73412976"
---
## <a name="for-users-in-your-directory"></a>A címtárban lévő felhasználók számára

Kövesse az alábbi lépéseket, ha engedélyezni szeretné a címtárban lévő felhasználók számára a hozzáférési csomag igénylését. A kérelem szabályzatának meghatározásakor megadhat egyéni felhasználókat vagy gyakrabban felhasználói csoportokat. Előfordulhat például, hogy a szervezet már rendelkezik egy csoporttal, például az **összes alkalmazottal**.  Ha ez a csoport hozzá van adva a szabályzatban azon felhasználók számára, akik hozzáférést igényelhetnek, akkor a csoport bármelyik tagja hozzáférhet a hozzáféréshez.

1. A **hozzáférést kérő felhasználók** területen kattintson a **címtárban lévő felhasználók**elemre.

    Ha ezt a beállítást választja, az új beállítások úgy jelennek meg, hogy tovább pontosítsa, hogy kik a címtárban is igényelhetik ezt a hozzáférési csomagot.

    ![Hozzáférési csomag – kérelmek – a címtárban lévő felhasználók számára](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Válasszon egyet a következő lehetőségek közül:

    |  |  |
    | --- | --- |
    | **Adott felhasználók és csoportok** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy csak a címtárban lévő felhasználók és csoportok férhessenek hozzá a hozzáférési csomaghoz. |
    | **Minden tag (a vendégek kivételével)** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a címtárban lévő összes tag felhasználó kérje ezt a hozzáférési csomagot. Ez a beállítás nem tartalmazza a címtárban esetleg meghívott vendég felhasználókat. |
    | **Minden felhasználó (beleértve a vendégeket is)** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a címtár összes tagja és vendég felhasználója hozzáférhessen a hozzáférési csomaghoz. |

    A vendég felhasználók a címtárban az [Azure ad B2B](../articles/active-directory/b2b/what-is-b2b.md)használatával meghívott külső felhasználókra vonatkoznak. A felhasználók és a vendég felhasználók közötti különbségekről a [Mi az alapértelmezett felhasználói engedélyek a Azure Active Directoryban?](../articles/active-directory/fundamentals/users-default-permissions.md)című témakörben talál további információt.

1. Ha **meghatározott felhasználókat és csoportokat adott**meg, kattintson a **felhasználók és csoportok hozzáadása**lehetőségre.

1. A felhasználók és csoportok kiválasztása panelen válassza ki a hozzáadni kívánt felhasználókat és csoportokat.

    ![Hozzáférési csomag – kérelmek – felhasználók és csoportok kiválasztása](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. A felhasználók és csoportok hozzáadásához kattintson a **kiválasztás** gombra.

1. Ugorjon a [jóváhagyás](#approval) szakaszra.

## <a name="for-users-not-in-your-directory"></a>A címtárban nem szereplő felhasználók számára

Kövesse az alábbi lépéseket, ha engedélyezni szeretné a címtárban lévő felhasználók számára a hozzáférési csomag kérését. A **címtárban lévő felhasználók** egy másik Azure ad-címtárban vagy-tartományban lévő felhasználókra hivatkoznak, és előfordulhat, hogy még nem kérték fel a címtárba. Az Azure AD-címtárakat úgy kell konfigurálni, hogy az **együttműködési korlátozásokban**engedélyezze a meghívásokat. További információkért tekintse meg a [külső B2B-együttműködés engedélyezése és a vendégek meghívására alkalmas személyek kezelése](../articles/active-directory/b2b/delegate-invitations.md)című témakört.

> [!NOTE]
> Egy vendég felhasználói fiók jön létre egy olyan felhasználó számára, aki még nem szerepel a címtárban, amelynek a kérelmét jóváhagyták vagy automatikusan jóváhagyták. A vendég meghívásra kerül, de nem kap meghívót e-mailben. Ehelyett e-mailt kapnak a hozzáférési csomag hozzárendelésének megérkezése után. Alapértelmezés szerint később, amikor a vendég felhasználó már nem rendelkezik hozzáférési csomagbeli hozzárendelésekkel, mert az utolsó hozzárendelésük lejárt vagy meg lett szakítva, a vendég felhasználói fiók le lesz tiltva a bejelentkezésből, és azt követően törlődik. Ha azt szeretné, hogy a vendég felhasználók határozatlan ideig maradjanak a címtárban, akkor is, ha nem rendelkeznek hozzáférési csomag hozzárendeléseivel, módosíthatja a jogosultsági felügyeleti konfiguráció beállításait. További információ a vendég felhasználói objektumról: [Azure Active Directory B2B együttműködési felhasználó tulajdonságai](../articles/active-directory/b2b/user-properties.md).

1. A **hozzáférést kérő felhasználók** területen kattintson a **címtárban nem szereplő felhasználók**elemre.

    Ha ezt a beállítást választja, az új beállítások jelennek meg.

    ![Hozzáférési csomag – kérelmek – a címtárban nem szereplő felhasználók számára](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Válasszon egyet a következő lehetőségek közül:

    |  |  |
    | --- | --- |
    | **Adott csatlakoztatott szervezetek** | Akkor válassza ezt a lehetőséget, ha ki szeretné választani a rendszergazda által korábban hozzáadott szervezetek listáját. A kiválasztott szervezetek összes felhasználója ezt a hozzáférési csomagot fogja kérni. |
    | **Minden csatlakoztatott szervezet** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy az összes csatlakoztatott szervezet összes felhasználója hozzáférhessen a hozzáférési csomaghoz. |

    A csatlakoztatott szervezet egy külső Azure AD-címtár vagy-tartomány, amelyhez kapcsolata van.

1. Ha az **adott csatlakoztatott szervezetek**lehetőséget választotta, kattintson a **címtárak hozzáadása** lehetőségre a rendszergazda által korábban hozzáadott csatlakoztatott szervezetek listájából való kiválasztáshoz.

1. A korábban csatlakoztatott szervezet kereséséhez írja be a név vagy a tartomány nevét.

    ![Hozzáférési csomag – kérelmek – könyvtárak kiválasztása](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Ha a szervezet, amely együttműködik a-val, nem szerepel a listában, megkérheti a rendszergazdát, hogy a hozzá csatlakoztatott szervezetként vegye fel. További információt a [csatlakoztatott szervezet hozzáadása](../articles/active-directory/governance/entitlement-management-organization.md)című témakörben talál.

1. Miután kiválasztotta az összes csatlakoztatott szervezetet, kattintson a **kiválasztás**elemre.

    > [!NOTE]
    > A kiválasztott csatlakoztatott szervezetek összes felhasználója ezt a hozzáférési csomagot fogja kérni. Ez magában foglalja az Azure AD-beli felhasználókat a szervezethez társított összes altartományból, kivéve, ha ezeket a tartományokat az Azure B2B engedélyezési vagy megtagadási listája blokkolja. További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](../articles/active-directory/b2b/allow-deny-list.md).

1. Ugorjon a [jóváhagyás](#approval) szakaszra.

## <a name="none-administrator-direct-assignments-only"></a>Nincs (csak a rendszergazdai közvetlen hozzárendelések)

Kövesse az alábbi lépéseket, ha szeretné megkerülni a hozzáférési kérelmeket, és lehetővé teszi, hogy a rendszergazdák közvetlenül rendeljenek hozzá konkrét felhasználókat a hozzáférési csomaghoz. A felhasználóknak nem kell a hozzáférési csomagot igényelnie. Továbbra is beállíthatja az életciklus-beállításokat, de nincsenek a kérelmek beállításai.

1. A **hozzáférést kérő felhasználók** részen kattintson a **nincs (csak rendszergazdai közvetlen hozzárendelések**elemre.

    ![Hozzáférési csomag – kérelmek – nincsenek rendszergazdai közvetlen hozzárendelések](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    A hozzáférési csomag létrehozása után közvetlenül rendelhet hozzá konkrét belső és külső felhasználókat a hozzáférési csomaghoz. Ha külső felhasználót ad meg, a rendszer létrehoz egy vendég felhasználói fiókot a címtárban. A felhasználók közvetlen hozzárendelésével kapcsolatos információkért tekintse meg a [hozzáférési csomag hozzárendelésének megtekintése, hozzáadása és eltávolítása](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)című témakört.

1. Ugorjon le a [kérelmek engedélyezése](#enable-requests) szakaszra.

## <a name="approval"></a>Jóváhagyás

A jóváhagyás szakaszban megadhatja, hogy szükség van-e jóváhagyásra, ha a felhasználók ezt a hozzáférési csomagot kérik. A jóváhagyási beállítások a következő módon működnek:

- Csak az egyik kiválasztott jóváhagyó vagy tartalék jóváhagyónak kell jóváhagynia a kérelmet. Az összes jóváhagyó jóváhagyása nem szükséges.
- A jóváhagyási döntés azon alapul, hogy bármelyik jóváhagyó megtekinti-e először a kérést.

Kövesse az alábbi lépéseket a korábban kiválasztott felhasználók jóváhagyási beállításainak megadásához.

1. A kiválasztott felhasználóktól érkező kérések jóváhagyásának megköveteléséhez állítsa az **Igen**értékre a **jóváhagyás megkövetelése** kapcsolót. Ha szeretné, hogy a rendszer automatikusan jóváhagyja a kéréseket, állítsa a kapcsolót a **nem**értékre.

1. Ha szeretné megkövetelni, hogy a felhasználók indoklást szolgáltassanak a hozzáférési csomag igényléséhez, állítsa a **kérelmező indoklásának megkövetelése** **beállítást igen**értékre.

    ![Hozzáférési csomag – kérelmek – jóváhagyási beállítások](./media/active-directory-entitlement-management-request-policy/approval.png)

### <a name="single-stage-approval"></a>Egyfázisú jóváhagyás

1. Jóváhagyók esetében válassza a **felettes jóváhagyása** lehetőséget, vagy **válassza a megadott jóváhagyók lehetőséget**.

    ![Hozzáférési csomag – kérelmek – egyfázisú beállítások](./media/active-directory-entitlement-management-request-policy/approval-single-stage.png)

1. Ha a kezelő mint jóváhagyó lehetőséget választotta, kattintson a **tartalék hozzáadása** lehetőségre, ha egy vagy több felhasználót vagy csoportot kíván kijelölni a címtárban, hogy tartalék jóváhagyó legyen abban az esetben, ha a jogosultságok kezelése nem találja a kezelőt.

    A kezelőt az Azure AD felhasználói profiljának **Manager** attribútuma határozza meg. További információ: [felhasználói profil adatainak hozzáadása vagy frissítése Azure Active Directory használatával](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Ha az egyes jóváhagyók kiválasztása lehetőséget választotta, kattintson a **Jóváhagyók hozzáadása** lehetőségre a címtárban lévő felhasználók vagy csoportok jóváhagyásának kiválasztásához.

1. A **döntésben**meg kell határozni, hogy hány nap múlva kell megadnia, hogy a jóváhagyónak hány napig kell áttekintenie a hozzáférési csomagra vonatkozó kérelmet.

    Ha egy kérelem nem lett jóváhagyva ezen az időtartamon belül, a rendszer automatikusan letiltja. A felhasználónak egy másik kérelmet kell benyújtania a hozzáférési csomaghoz.

1. Ahhoz, hogy a felhasználók indoklást szolgáltassanak a hozzáférési csomag igényléséhez, az **Igen**értékre kell állítani a **jóváhagyó indoklását** .

    Egy indoklás látható a többi jóváhagyó és a kérelmező számára.

### <a name="alternate-approvers"></a>Alternatív jóváhagyók

A kérelmeket jóváhagyó elsődleges jóváhagyók megadása mellett alternatív jóváhagyókat is megadhat. Ezzel biztosíthatja, hogy a rendszer a kérelmeket a lejárat előtt jóváhagyja vagy megtagadja (időtúllépés).

Ha alternatív jóváhagyó (ka) t ad meg, abban az esetben, ha az elsődleges jóváhagyó (k) nem tudta jóváhagyni vagy megtagadni a kérést, a függőben lévő kérést a rendszer a házirend beállításakor megadott továbbítási ütemterv szerint továbbítja a másodlagos jóváhagyó (k) nek. E-mailt kapnak a függőben lévő kérelem jóváhagyásához vagy elutasításához.

Miután a kérést továbbították a másodlagos jóváhagyóknak, az elsődleges jóváhagyók továbbra is jóváhagyják vagy eltagadhatják a kérést. A másodlagos jóváhagyók ugyanazt a saját hozzáférési helyet használják, mint az elsődleges jóváhagyó, a függőben lévő kérelem jóváhagyásához vagy elutasításához.

Listázhat személyeket vagy csoportokat, akik az elsődleges jóváhagyók és az alternatív jóváhagyók. Győződjön meg arról, hogy az elsődleges jóváhagyóként és a másodlagos jóváhagyóként különböző személyek listáját sorolja fel.
Ha például az Alice és a Bob elsődleges jóváhagyó (k), a Carol és a Dave listázása az alternatív jóváhagyó (k). A következő lépésekkel adhat hozzá alternatív jóváhagyókat egy hozzáférési csomaghoz:

1. Kattintson a **speciális kérelmek beállításainak megjelenítése**lehetőségre.

    ![Hozzáférési csomag – szabályzat – speciális kérelmek beállításainak megjelenítése](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Állítsa be **, hogy nem történt-e művelet, továbbítsa a másodlagos jóváhagyóknak?** váltás az **Igen**értékre.

1. Kattintson az **alternatív Jóváhagyók hozzáadása** lehetőségre, és válassza ki a listából a másodlagos jóváhagyó (ka) t.

    ![Hozzáférési csomag – házirend – alternatív Jóváhagyók hozzáadása](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. A **továbbítás a másodlagos jóváhagyó (ok)** hoz a napok száma után, hogy a jóváhagyóknak hány napig kell jóváhagyni vagy megtagadni a kérést. Ha a kérelem időtartama előtt egyetlen jóváhagyó sem hagyta jóvá vagy nem utasította el a kérelmet, a kérelem lejár (időtúllépés), és a felhasználónak egy másik kérelmet kell benyújtania a hozzáférési csomaghoz. 

    A kérések továbbítása csak a kérés időtartamát követő nap után lehetséges a másodlagos jóváhagyóknak. Ebben a példában a kérelem időtartama 14 nap. Ez azt jelenti, hogy a kérelem időtartama a 7. napon eléri a felezési időt. Így a kérést nem lehet a 8. nap elején továbbítani. A kérések időtartamának utolsó napján a kérelmeket nem lehet továbbítani a másodlagos jóváhagyónak. Így a példában a legutóbbi kérelem továbbítása a 13. nap.

## <a name="enable-requests"></a>Kérelmek engedélyezése

1. Ha azt szeretné, hogy a hozzáférési csomag azonnal elérhető legyen a kérési házirendben szereplő felhasználók számára, kattintson az **Igen** gombra az engedélyezéshez.

    A jövőben bármikor engedélyezheti azt a hozzáférési csomag létrehozása után.

    Ha a **nincs (csak rendszergazda közvetlen hozzárendelések)** lehetőséget választotta, és az engedélyezés a **nem**értékre van állítva, akkor a rendszergazdák nem tudják közvetlenül hozzárendelni ezt a hozzáférési csomagot.

    ![Hozzáférési csomag – házirend – házirend-beállítás engedélyezése](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Kattintson a **Tovább** gombra.
