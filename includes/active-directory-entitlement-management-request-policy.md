---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 7fd716be397d9ef6b9d6132cd4470f653f3cea0f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655940"
---
## <a name="for-users-in-your-directory"></a>A címtárban lévő felhasználók számára

Kövesse az alábbi lépéseket, ha engedélyezni szeretné, hogy a címtár felhasználói igényelhessék ezt a hozzáférési csomagot. A kérelemházirend meghatározásakor megadhatja az egyes felhasználókat, vagy gyakrabban a felhasználói csoportokat. Előfordulhat például, hogy a szervezetnek már van egy csoportja, például **a Minden alkalmazott.**  Ha ez a csoport szerepel a házirendben azon felhasználók számára, akik hozzáférést kérhetnek, akkor a csoport bármely tagja hozzáférést kérhet.

1. A **Felhasználók, akik hozzáférést kérhetnek,** kattintson **a Címtárban lévő felhasználók számára**elemre.

    Ha ezt a beállítást választja, új beállítások jelennek meg, amelyek tovább finomítják, hogy a címtárban ki kérheti ezt a hozzáférési csomagot.

    ![Access csomag – Kérések – A címtárban lévő felhasználók számára](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Válasszon egyet az alábbi lehetőségek közül:

    |  |  |
    | --- | --- |
    | **Adott felhasználók és csoportok** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy csak a könyvtárban megadott felhasználók és csoportok igényelhetnék ezt a hozzáférési csomagot. |
    | **Minden tag (a vendégek kivételével)** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a címtár összes tagfelhasználója igényelhesse ezt a hozzáférési csomagot. Ez a beállítás nem tartalmazza azönt ön által a címtárba meghívott vendégfelhasználókat. |
    | **Minden felhasználó (beleértve a vendégeket is)** | Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a címtárban szereplő összes tag- és vendégfelhasználó kérje ezt a hozzáférési csomagot. |

    A vendégfelhasználók olyan külső felhasználókra hivatkoznak, akiket az [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md)segítségével hívtak meg a címtárba. A tag- és vendégfelhasználók közötti különbségekről a Következő témakörben olvashat: [Mik az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?](../articles/active-directory/fundamentals/users-default-permissions.md)

1. Ha **a Konkrét felhasználók és csoportok**lehetőséget választotta, kattintson a Felhasználók és csoportok hozzáadása **gombra.**

1. A Felhasználók és csoportok kiválasztása ablaktáblában jelölje ki a hozzáadni kívánt felhasználókat és csoportokat.

    ![Access csomag – Kérések – Felhasználók és csoportok kiválasztása](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. A felhasználók és csoportok hozzáadásához kattintson a **Kijelölés** gombra.

1. Ugorjon le a [Jóváhagyás](#approval) szakaszra.

## <a name="for-users-not-in-your-directory"></a>A címtárban nem lévő felhasználók számára

 **A címtárban nem található felhasználók** olyan felhasználókra hivatkoznak, akik egy másik Azure AD-címtárban vagy tartományban vannak. Előfordulhat, hogy ezek a felhasználók még nem lettek meghívva a könyvtárba. Az Azure AD-könyvtárakat úgy kell konfigurálni, hogy engedélyezze a meghívásokat **az együttműködési korlátozásokban.** További információ: [B2B külső együttműködés engedélyezése és annak kezelése, hogy ki hívhat meg vendégeket.](../articles/active-directory/b2b/delegate-invitations.md)

> [!NOTE]
> Egy vendég felhasználói fiók jön létre egy olyan felhasználó számára, aki még nem szerepel a címtárban, akinek a kérelmét jóváhagyták vagy automatikusan jóváhagyták. A vendég meghívást kap, de nem kap meghívó e-mailt. Ehelyett e-mailt kapnak, amikor a hozzáférési csomag hozzárendelését kézbesítik. Alapértelmezés szerint később, amikor a vendégfelhasználó már nem rendelkezik hozzáférési csomag-hozzárendelésekkel, mivel az utolsó hozzárendelése lejárt vagy megszakadt, a vendégfelhasználói fiók nem jelentkezhet be, majd törlődik. Ha azt szeretné, hogy a vendégfelhasználók határozatlan ideig a címtárban maradjanak, még akkor is, ha nincs enek hozzáférési csomag-hozzárendelésük, módosíthatja a jogosultságkezelési konfiguráció beállításait. A vendégfelhasználói objektumról további információt az [Azure Active Directory B2B együttműködési felhasználó tulajdonságai című](../articles/active-directory/b2b/user-properties.md)témakörben talál.

Ha engedélyezni szeretné, hogy a címtárban nem szereplő felhasználók kérjék ezt a hozzáférési csomagot:

1. A **Felhasználók, akik hozzáférést kérhetnek,** kattintson **a Címtárban nem lévő felhasználók számára**elemre.

    Ha ezt a beállítást választja, új beállítások jelennek meg.

    ![Access csomag - Kérések - A címtárban nem lévő felhasználók számára](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Válasszon egyet az alábbi lehetőségek közül:

    |  |  |
    | --- | --- |
    | **Adott összekapcsolt szervezetek** | Akkor válassza ezt a lehetőséget, ha a rendszergazda által korábban hozzáadott szervezetek listájából szeretne választani. A kijelölt szervezetek minden felhasználója kérheti ezt a hozzáférési csomagot. |
    | **Minden kapcsolódó szervezet** | Akkor válassza ezt a lehetőséget, ha az összes csatlakoztatott szervezet összes felhasználója kérheti ezt a hozzáférési csomagot. |
    | **Minden felhasználó (Minden csatlakoztatott szervezet + új külső felhasználók)** | Akkor válassza ezt a lehetőséget, ha az összes csatlakoztatott szervezet összes felhasználója kérheti ezt a hozzáférési csomagot, és hogy a B2B engedélyezési vagy megtagadási listabeállításoknak elsőbbséget kell élvezniük minden új külső felhasználó számára. |

    A csatlakoztatott szervezet egy külső Azure AD-címtár vagy tartomány, amely kapcsolatban áll.

1. Ha **a Konkrét csatlakoztatott szervezetek**lehetőséget választotta, kattintson a Könyvtárak hozzáadása **gombra** a korábban hozzáadott csatlakoztatott szervezetek listájából.

1. Írja be a nevet vagy a tartománynevet egy korábban csatlakoztatott szervezet kereséséhez.

    ![Access csomag - Kérések – Könyvtárak kiválasztása](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Ha az a szervezet, akivel együtt kíván működni, nem szerepel a listában, megkérheti a rendszergazdát, hogy vegye fel kapcsolt szervezetként. További információt a [Csatlakoztatott szervezet hozzáadása](../articles/active-directory/governance/entitlement-management-organization.md)című témakörben talál.

1. Miután kiválasztotta az összes csatlakoztatott szervezetet, kattintson a **Kijelölés gombra.**

    > [!NOTE]
    > A kijelölt csatlakoztatott szervezetek minden felhasználója kérheti ezt a hozzáférési csomagot. Ez magában foglalja az Azure AD-ben a szervezethez társított összes altartomány felhasználóit, kivéve, ha ezeket a tartományokat az Azure B2B engedélyezési vagy megtagadási lista blokkolja. További információt az [Adott szervezetek B2B-felhasználóinak szóló meghívók engedélyezése vagy letiltása című témakörben talál.](../articles/active-directory/b2b/allow-deny-list.md)

1. Ugorjon le a [Jóváhagyás](#approval) szakaszra.

## <a name="none-administrator-direct-assignments-only"></a>Nincs (csak rendszergazdai közvetlen hozzárendelések esetén)

Kövesse az alábbi lépéseket, ha meg szeretné kerülni a hozzáférési kérelmeket, és lehetővé szeretné tenni a rendszergazdák számára, hogy közvetlenül hozzárendeljenek bizonyos felhasználókat ehhez a hozzáférési csomaghoz. A felhasználóknak nem kell kérniük a hozzáférési csomagot. Az életciklus-beállítások továbbra is megállíthatók, de nincsenek kérésbeállítások.

1. A **Felhasználók, akik hozzáférést kérhetnek,** kattintson a **Nincs (csak rendszergazdai közvetlen hozzárendelések**) elemre.

    ![Access package – Kérelmek – Nincs rendszergazdai közvetlen hozzárendelés csak](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    A hozzáférési csomag létrehozása után közvetlenül hozzárendelhet időbelső és külső felhasználókat a hozzáférési csomaghoz. Ha külső felhasználót ad meg, vendégfelhasználói fiók jön létre a címtárban. A felhasználó közvetlen hozzárendeléséről a [Hozzáférési csomag hozzárendelésének megtekintése, hozzáadása és eltávolítása](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)című témakörben talál további információt.

1. Ugorjon le a [Kérelmek engedélyezése](#enable-requests) szakaszra.

## <a name="approval"></a>Jóváhagyás

A Jóváhagyás szakaszban megadhatja, hogy szükség van-e jóváhagyásra, amikor a felhasználók ezt a hozzáférési csomagot kérik. A jóváhagyási beállítások a következőképpen működnek:

- Csak az egyik kiválasztott jóváhagyók vagy tartalék jóváhagyók jóvá kell hagynia az egylépcsős jóváhagyási kérelmet. 
- Az egyes fázisokból kiválasztott jóváhagyók közül csak egynek kell jóváhagynia a kétlépcsős jóváhagyásra vonatkozó kérelmet.
- A jóváhagyó lehet menedzser, belső szponzor vagy külső szponzor attól függően, hogy ki a hozzáférésre vonatkozó irányelv.
- Minden kiválasztott jóváhagyó jóváhagyása nem szükséges az egy- vagy kétlépcsős jóváhagyáshoz.
- A jóváhagyási döntés alapja az, amelyik jóváhagyó felülvizsgálhatja a kérelmet először.

Ha meg szeretné tudni, hogyan vehet fel jóváhagyókat egy kérelemre vonatkozó irányelvbe, tekintse meg az alábbi videót:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Ha meg szeretné tudni, hogyan adhat hozzá többlépcsős jóváhagyást egy kérelemre vonatkozó irányelvhez, tekintse meg az alábbi videót:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

A hozzáférési csomag ra vonatkozó kérelmek jóváhagyási beállításainak megadásához kövesse az alábbi lépéseket:

1. Ha a kijelölt felhasználók kérelmeinek jóváhagyására van szükség, állítsa a **Jóváhagyás megkövetelése** kapcsolót **Igen (Igen)** beállításra. Ha automatikusan szeretné jóváhagyni a kérelmeket, állítsa a kapcsolót **Nem**beállításra.

1. Ha azt szeretné, hogy a felhasználók nak meg kell adniuk a hozzáférési csomag kérésének indoklását, állítsa a **Kérő vagy indoklás megkövetelése** kapcsolót **Igen**beállításra.
    
1. Most határozza meg, hogy a kérelmek egy vagy két lépcsős jóváhagyást igényelnek.Now determine if requests will require single or 2-stage approval. Állítsa a **Hány szakasz** kapcsolót **1-re** egyfokozatú jóváhagyáshoz, vagy állítsa a kapcsolót **2-re** kétlépcsős jóváhagyáshoz.

    ![Access csomag - Kérések - Jóváhagyási beállítások](./media/active-directory-entitlement-management-request-policy/approval.png)

A következő lépésekkel vehet fel jóváhagyókat, miután kiválasztotta, hogy hány szakaszra van szüksége: 

### <a name="single-stage-approval"></a>Egylépcsős jóváhagyás

1. Az **első jóváhagyó**hozzáadása:
    
    Ha a házirend úgy van beállítva, hogy szabályozza a hozzáférést a címtárban lévő felhasználók számára, a **Kezelő t jóváhagyóként lehetőséget választhatja.** Vagy adjon hozzá egy adott felhasználót a **Jóváhagyók hozzáadása** gombra kattintva, miután a legördülő menüBen a Jóváhagyók kiválasztása lehetőséget választotta.
    
    ![Access csomag - Kérések - A címtárban lévő felhasználók számára - Első jóváhagyó](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Ha ez a házirend úgy van beállítva, hogy szabályozza a hozzáférést a nem a címtárban nem szereplő felhasználók számára, válassza a **Külső szponzor** vagy a **Belső szponzor**lehetőséget. Vagy adjon hozzá egy adott felhasználót a Jóváhagyók vagy csoportok hozzáadása csoportban a **Jóváhagyók hozzáadása** elemre kattintva.
    
    ![Access package - Requests - Címtáron kívüli felhasználók számára - Első jóváhagyó](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Ha a **Menedzsert** választotta első jóváhagyóként, kattintson a **Tartalék hozzáadása** gombra, és a címtárban egy vagy több felhasználót vagy csoportot jelöljön ki tartalék jóváhagyónak. A tartalék jóváhagyók akkor kapják meg a kérelmet, ha a jogosultságkezelés nem találja a hozzáférést kérő felhasználó kezelőjét.

    A kezelőt a Jogosultságkezelés a **Kezelő** attribútum használatával találja meg. Az attribútum a felhasználó profiljában található az Azure AD-ben. További információt a [Felhasználói profiladatok hozzáadása vagy frissítése az Azure Active Directory használatával című témakörben talál.](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)

1. Ha **a Konkrét jóváhagyók kiválasztása**lehetőséget választotta, kattintson a **Jóváhagyók hozzáadása** gombra, ha a címtárban egy vagy több felhasználót vagy csoportot szeretne jóváhagyóként kijelölni.

1. A határozat **rovatban hány napon belül kell meghozni?** adja meg, hogy a jóváhagyónak hány napig kell felülvizsgálnia a hozzáférési csomagra vonatkozó kérelmet.

    Ha egy kérelmet ezen időszakon belül nem hagyjóvá, a rendszer automatikusan elutasítja. A felhasználónak újabb kérelmet kell benyújtania a hozzáférési csomagra vonatkozóan.

1. Ha azt szeretné, hogy a jóváhagyók nak meg kell indokolniuk döntésüket, állítsa be a Jóváhagyó indoklásának **megkövetelése lehetőséget Igen**beállításra.

    Az indoklás látható a többi jóváhagyó és a kérelmező számára.

### <a name="2-stage-approval"></a>Kétlépcsős jóváhagyás

Ha kétlépcsős jóváhagyást választott, hozzá kell adnia egy második jóváhagyót.

1. A **második jóváhagyó**hozzáadása: 
    
    Ha a felhasználók a címtárban vannak, adjon hozzá egy adott felhasználót második jóváhagyóként, ha a **Jóváhagyók hozzáadása** csoportban a Jóváhagyók kiválasztása csoportban kattint.

    ![Access csomag - Kérések - A címtárban lévő felhasználók számára - Második jóváhagyó](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Ha a felhasználók nem a címtárban, válassza **a Belső szponzor** vagy külső **szponzor** a második jóváhagyó. A jóváhagyó kiválasztása után adja hozzá a tartalék jóváhagyókat.

    ![Access package - Requests - Címtáron kívüli felhasználók számára - Második jóváhagyó](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Adja meg, hogy a második jóváhagyónak hány napig kell jóváhagynia a kérelmet a **Határozat mezőben, hány napon belül kell meghoznia?**. 

1. Állítsa a Jóváhagyó indoklásának megnevezését Igen vagy Nem **(Igen)** vagy **nem**re.

### <a name="alternate-approvers"></a>Alternatív jóváhagyók

Alternatív jóváhagyókat is megadhat, hasonlóan a kérelmeket jóváhagyó első és második jóváhagyók megadásához. Az alternatív jóváhagyók segítenek annak biztosításában, hogy a kérelmeket a lejártuk előtt jóváhagyják vagy megtagadják (időkérés). Az első jóváhagyót és a második jóváhagyót listázhatja a kétlépcsős jóváhagyáshoz. 

Alternatív jóváhagyók megadásával abban az esetben, ha az első vagy a második jóváhagyó nem tudta jóváhagyni vagy megtagadni a kérelmet, a függőben lévő kérelem továbbításra kerül a másik jóváhagyóknak, a házirend beállítása során megadott továbbítási ütemezés szerint. Kapnak egy e-mailt, hogy jóváhagyja vagy megtagadja a függőben lévő kérelmet.

A kérelem továbbítása után a másik jóváhagyók, az első vagy a második jóváhagyók továbbra is jóváhagyja vagy elutasítja a kérelmet. Az alternatív jóváhagyók ugyanazt a Saját hozzáférési webhelyet használják a függőben lévő kérelem jóváhagyásához vagy elutasításához.

Felsorolhatjuk azokat az embereket vagy csoportokat, akik jóváhagyók és alternatív jóváhagyók. Győződjön meg arról, hogy az első, második és alternatív jóváhagyók ként különböző személyeket sorol fel.
Ha például Alice és Bob ot nevezte meg első jóváhagyóként, akkor Carolt és Dave-et soroljuk fel alternatív jóváhagyóként. Az alábbi lépésekkel alternatív jóváhagyókat adhat hozzá egy hozzáférési csomaghoz:

1. Az Első jóváhagyó, a Második jóváhagyó vagy mindkettő csoportban kattintson a **Speciális kérelembeállítások megjelenítése gombra.**

    ![Access csomag – Házirend – Speciális kérelembeállítások megjelenítése](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Állítsa **be: Ha nincs művelet, továbbítsa az alternatív jóváhagyóknak?** **Yes**

1. Kattintson **az Alternatív jóváhagyók hozzáadása** gombra, és válassza ki az alternatív jóváhagyót a listából.

    ![Access csomag - Házirend – Alternatív jóváhagyók hozzáadása](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. A **Továbbítás alternatív jóváhagyónak(k) mezőben hány nap elteltével** tegye be, hogy a jóváhagyókhány napig hagyják jóvá vagy tagadják meg a kérelmet. Ha egyetlen jóváhagyó sem hagyta jóvá vagy tagadta meg a kérelmet a kérelem időtartama előtt, a kérelem lejár (időhosszabbítás), és a felhasználónak egy másik kérelmet kell benyújtania a hozzáférési csomaghoz. 

    A kérelmek csak egy nappal azt követően továbbíthatók alternatív jóváhagyóknak, hogy a kérelem időtartama eléri a felezési idő. Ebben a példában a kérelem időtartama 14 nap. Tehát a kérelem időtartama eléri a felezési idő a 7. Tehát a kérés nem továbbítható a 8. Emellett a kérelmek nem továbbíthatók a kérelem időtartamának utolsó napján. Így a példában a kérelem legutolsó lehet továbbítani a 13.

## <a name="enable-requests"></a>Kérelmek engedélyezése

1. Ha azt szeretné, hogy a hozzáférési csomag azonnal elérhetővé váljon a kérelemházirendben lévő felhasználók számára, helyezze át az Engedélyezés kapcsolót **Igen**szintre.

    A hozzáférési csomag létrehozása után a jövőben bármikor engedélyezheti.

    Ha a **Nincs (csak rendszergazdai közvetlen hozzárendelések)** lehetőséget választotta, és az engedélyezést **Nem**értékre állította, akkor a rendszergazdák nem rendelhetik hozzá közvetlenül ezt a hozzáférési csomagot.

    ![Access csomag – Házirend – Házirendbeállítás engedélyezése](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Kattintson a **Tovább** gombra.
