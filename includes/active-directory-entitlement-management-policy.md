---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 07/31/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: db16a2f122da1bf6c767e0a47c93c22f1882c406
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817221"
---
### <a name="policy-for-users-in-your-directory"></a>Szabályzat: A címtárban lévő felhasználók számára

Kövesse az alábbi lépéseket, ha azt szeretné, hogy a házirend a címtár azon felhasználói számára legyen, akik igényelhetik ezt a hozzáférési csomagot.  A **címtárban lévő felhasználók** a belső felhasználókra, valamint a korábban meghívott külső felhasználókra is utalnak, amelyek a jogosultságok felügyeletének megkövetelését kérik egy másik hozzáférési csomaggal, vagy az Azure ad B2B-vel meghívást kapnak. A szabályzat meghatározásakor megadhat egyéni felhasználókat vagy gyakrabban felhasználói csoportokat. Előfordulhat például, hogy a szervezet már rendelkezik egy csoporttal, például az **összes alkalmazottal**.  Ha ez a csoport hozzá van adva a szabályzatban azon felhasználók számára, akik hozzáférést igényelhetnek, akkor a csoport bármelyik tagja hozzáférhet a hozzáféréshez.

1. A **hozzáférést kérő felhasználók** területen válassza a **címtárban lévő felhasználók**lehetőséget.

    Vegye figyelembe, hogy a címtárban lévő **felhasználók** a címtárhoz hozzáadott tagokat és vendég felhasználókat is tartalmazzák. Ha csak a tagokat és a vendég felhasználókat szeretné bevonni, válassza **a címtárban lévő felhasználók** elemet, majd válassza ki a tag felhasználói csoportot. Ha szükséges, létrehozhat egy dinamikus csoportot a tag felhasználók (User. userType-EQ "tag") használatával. További információ: [Azure Active Directory-csoportok dinamikus tagsági szabályai](../articles/active-directory/users-groups-roles/groups-dynamic-membership.md).

1. A **felhasználók és csoportok kiválasztása** szakaszban kattintson a **felhasználók és csoportok hozzáadása**lehetőségre.

1. A felhasználók és csoportok kiválasztása panelen válassza ki a hozzáadni kívánt felhasználókat és csoportokat.

    ![Hozzáférési csomag – házirend – felhasználók és csoportok kiválasztása](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. A felhasználók és csoportok hozzáadásához kattintson a **kiválasztás** gombra.

1. Ugorjon le a [szabályzatra: Kérelem](#policy-request) szakasz.

### <a name="policy-for-users-not-in-your-directory"></a>Szabályzat: A címtárban nem szereplő felhasználók számára

Kövesse az alábbi lépéseket, ha azt szeretné, hogy a házirend olyan felhasználók számára legyen, akik nem a címtárban vannak, és amelyekre ezt a hozzáférési csomagot kérhetik. A **címtárban lévő felhasználók** egy másik Azure ad-címtárban lévő felhasználókra hivatkoznak, és előfordulhat, hogy még nem kérték a címtárba.  A címtárakat úgy kell konfigurálni, hogy engedélyezve legyenek a **szervezeti kapcsolatok együttműködési korlátozásai** beállításokban.

> [!NOTE]
> Egy vendég külső felhasználói fiók jön létre egy olyan felhasználó számára, aki még nem szerepel a címtárban, amelynek a kérelmét jóváhagyták vagy automatikusan jóváhagyták. A vendég meghívásra kerül, de nem kap meghívót e-mailben. Ehelyett e-mailt kapnak a hozzáférési csomag hozzárendelésének megérkezése után. Alapértelmezés szerint később, amikor a vendég felhasználó már nem rendelkezik hozzáférési csomagbeli hozzárendelésekkel, mert az utolsó hozzárendelésük lejárt vagy meg lett szakítva, a vendég felhasználói fiók le lesz tiltva a bejelentkezésből, és azt követően törlődik. Ha azt szeretné, hogy a vendég felhasználók határozatlan ideig maradjanak a címtárban, akkor is, ha nem rendelkeznek hozzáférési csomag hozzárendeléseivel, módosíthatja a jogosultsági felügyeleti konfiguráció beállításait.

1. A **hozzáférést kérő felhasználók** területen válassza a **címtárban nem szereplő felhasználók**lehetőséget.

1. A **külső Azure ad-címtár kiválasztása** szakaszban kattintson a **címtárak hozzáadása**lehetőségre.

1. Adja meg a tartománynevet, és keressen rá az adott tartománynévvel rendelkező Azure AD-címtárra.

1. Ellenőrizze, hogy a könyvtár neve és a kezdeti tartomány megfelelő-e.

    > [!NOTE]
    > A címtár összes felhasználója ezt a hozzáférési csomagot fogja kérni. Ide tartoznak a címtárhoz társított összes altartományból származó felhasználók, nem csak a keresésben használt tartomány.

    ![Hozzáférési csomag – házirend – könyvtárak kijelölése](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. A címtár hozzáadásához kattintson a **Hozzáadás** gombra.

1. További könyvtárak hozzáadásához ismételje meg ezt a lépést.

1. Miután hozzáadta az összes olyan könyvtárat, amelyet fel szeretne venni a szabályzatba, kattintson a **kiválasztás**elemre.

1. Ugorjon le a [szabályzatra: Kérelem](#policy-request) szakasz.

### <a name="policy-none-administrator-direct-assignments-only"></a>Szabályzat: Nincs (csak a rendszergazdai közvetlen hozzárendelések)

Kövesse az alábbi lépéseket, ha azt szeretné, hogy a szabályzat megkerüljék a hozzáférési kérelmeket, és lehetővé tegye, hogy a rendszergazdák közvetlenül rendeljenek hozzá konkrét felhasználókat a hozzáférési csomaghoz. A felhasználóknak nem kell a hozzáférési csomagot igényelnie. Továbbra is beállíthatja a lejárati beállításokat, de nincsenek megadva a kérelmek beállításai.

1. Azokon a felhasználóknál, **akik hozzáférhetnek** a hozzáféréshez szakaszban válassza a **nincs (csak rendszergazdai közvetlen hozzárendelések**lehetőséget.

    A hozzáférési csomag létrehozása után közvetlenül rendelhet hozzá konkrét belső és külső felhasználókat a hozzáférési csomaghoz. Ha külső felhasználót ad meg, a rendszer létrehoz egy vendég felhasználói fiókot a címtárban.

1. Ugorjon le a [szabályzatra: Lejárat](#policy-expiration) szakasz.

### <a name="policy-request"></a>Szabályzat: Kérés

A kérelem szakaszban megadhatja a jóváhagyási beállításokat, amikor a felhasználók a hozzáférési csomagot kérik.

1. A kiválasztott felhasználóktól érkező kérések jóváhagyásának megköveteléséhez állítsa az **Igen**értékre a **jóváhagyás** megkövetelése kapcsolót. Ha szeretné, hogy a rendszer automatikusan jóváhagyja a kéréseket, állítsa a kapcsolót a **nem**értékre.

1. Ha jóváhagyásra van szüksége, a **jóváhagyók kiválasztása** szakaszban kattintson a **Jóváhagyók hozzáadása**elemre.

1. A jóváhagyók kiválasztása panelen jelöljön ki egy vagy több felhasználót és/vagy csoportot a jóváhagyóknak.

    Csak az egyik kiválasztott jóváhagyónak kell jóváhagynia egy kérést. Az összes jóváhagyó jóváhagyása nem szükséges. A jóváhagyási döntés azon alapul, hogy bármelyik jóváhagyó megtekinti-e először a kérést.

    ![Hozzáférési csomag – házirend – jóváhagyók kiválasztása](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. A jóváhagyók hozzáadásához kattintson a **kiválasztás** gombra.

1. További beállítások megjelenítéséhez kattintson a **speciális kérési beállítások megjelenítése** lehetőségre.

    ![Hozzáférési csomag – házirend – könyvtárak kijelölése](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Ahhoz, hogy a felhasználók indoklást szolgáltassanak a hozzáférési csomag igényléséhez, az **Igen**értékre kell állítani az **indoklást** .

1. Ha meg szeretné követelni, hogy a jóváhagyó indoklást nyújtson a hozzáférési csomagra vonatkozó kérelem jóváhagyásához, állítsa az **Igen**értékre a **jóváhagyó indoklásának** megkövetelése beállítást.

1. A **jóváhagyási kérelem időkorlátja (nap)** mezőben határozza meg, hogy a jóváhagyóknak mennyi ideig kell áttekinteniük a kérést. Ha egyetlen jóváhagyó sem tekinti át az adott számú napon, a kérelem lejár, és a felhasználónak egy másik kérelmet kell benyújtania a hozzáférési csomaghoz.

### <a name="policy-expiration"></a>Szabályzat: Lejárat

A lejárat szakaszban megadhatja, hogy mikor járjon le egy felhasználó a hozzáférési csomaghoz.

1. A **lejárat** szakaszban állítsa be a **hozzáférési csomag érvényességét** **dátum**, **napok száma**vagy **soha nem**értékre.

    A **on Date (dátum**) beállításnál válassza ki a lejárati dátumot a jövőben.

    A **napok számának**megadásához 0 és 3660 nap közötti számot kell megadni.

    A kiválasztás alapján a felhasználó a hozzáférési csomaghoz való hozzárendelése egy adott napon lejár, bizonyos számú nappal a jóváhagyás után, vagy soha nem.

1. További beállítások megjelenítéséhez kattintson a **speciális lejárati beállítások megjelenítése** lehetőségre.

1. Annak engedélyezéséhez, hogy a felhasználó kiterjessze a hozzárendeléseket, állítsa a **felhasználók számára az** **Igen**értékre való kiterjesztést.

    Ha a kiterjesztések engedélyezve vannak a szabályzatban, a felhasználó 14 napig, a hozzáférési csomag hozzárendelésének megkezdése előtt pedig 1 nappal e-mailt kap, hogy lejárja a hozzárendelés kiterjesztését.

    ![Hozzáférési csomag – házirend-lejárati beállítások](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Szabályzat: Szabályzat engedélyezése

1. Ha azt szeretné, hogy a hozzáférési csomag azonnal elérhető legyen a szabályzatban szereplő felhasználók számára, kattintson az **Igen** gombra a szabályzat engedélyezéséhez.

    A jövőben bármikor engedélyezheti azt a hozzáférési csomag létrehozása után.

    ![Hozzáférési csomag – házirend – házirend-beállítás engedélyezése](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Kattintson a **tovább** vagy a **Létrehozás**gombra.
