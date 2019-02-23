---
title: Az Azure AD hozzáférési felülvizsgálatok segítségével a felhasználók ki vannak zárva a feltételes hozzáférési szabályzatok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokkal a feltételes hozzáférési szabályzatok kizárt felhasználók kezelése
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75ff08111e96fd4b210dfeb09db521856f4f5e52
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727502"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Használja az Azure AD hozzáférési felülvizsgálatok felhasználók ki vannak zárva a feltételes hozzáférési szabályzatok kezelése

Az ideális világában a minden felhasználó követnie a hozzáférési szabályzatok a munkahelyi erőforrásokhoz való hozzáférés biztonságossá tétele érdekében. Azonban néha előfordulhatnak olyan üzleti esetek, amelyek kivételek kérik. Ez a cikk azt ismerteti, néhány példa, amelyben kizárások szükség lehet, és hogyan, az informatikai rendszergazdaként kezelheti ezt a feladatot, felügyeletét, házirend kivételek elkerülése érdekében, és auditorok biztosítanak arról, hogy az ilyen kivételek lektorálhatók rendszeresen használja az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokkal.

> [!NOTE]
> Egy érvényes Azure AD Premium P2, Enterprise Mobility + Security E5 díjköteles vagy próbaidőszaki licence van szükség az Azure AD hozzáférési felülvizsgálatokkal. További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).

## <a name="why-would-you-exclude-users-from-policies"></a>Miért érdemes lenne kizár a házirendek felhasználók?

INFORMATIKAI rendszergazdaként használhat [Azure AD feltételes hozzáférés](../conditional-access/overview.md) a felhasználóktól többtényezős hitelesítést (MFA), vagy jelentkezzen be egy megbízható hálózat vagy az eszköz használatával történő hitelesítéshez. Az üzembe helyezés tervezése, során, ismerje meg, hogy egyes ezek a követelmények nem teljesíthetők összes felhasználó. Például, amely nem része a belső hálózaton távoli irodából dolgozó felhasználók vannak, vagy nincs a vezető, akinek használ egy régi telefonszám, amely nem támogatott. Az üzleti megköveteli, hogy ezek a felhasználók tenni, hogy jelentkezzen be, és a munka elvégzéséhez, ezért azok nem tartoznak a feltételes hozzáférési szabályzatokat.

Másik példaként használható [nevesített helyek](../conditional-access/location-condition.md) a feltételes hozzáférés konfigurálása megyék és régióban, amelyről nem szeretné, hogy a felhasználók a bérlőhöz való hozzáféréshez.

![Névvel ellátott helyek](./media/conditional-access-exclusion/named-locations.png)

Bizonyos esetekben azonban felhasználók rendelkezhet egy indokolt, ezek a blokkolt országokból bejelentkezni. Ha például felhasználók előfordulhat, hogy utazás közben dolgoztak munkahelyi vagy személyes okokból. Ebben a példában a feltételes hozzáférési szabályzat blokkolja ezen országok rendelkezhet egy dedikált felhőbeli biztonsági csoport a felhasználókat, akik nem tartoznak a szabályzat alól. Felhasználók, akiknek hozzáférésre van szükségük, utazás közben is hozzáadhat magukat a csoportba történő [az Azure AD önkiszolgáló csoportkezelés](../users-groups-roles/groups-self-service-management.md).

Egy másik példa lehet az, hogy a feltételes hozzáférési szabályzatot, amely [blokkok örökölt hitelesítés esetében a felhasználók túlnyomó többsége](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). A Microsoft határozottan javasolja, hogy a bérlő javíthatja biztonsági helyzetét örökölt protokollok használatának letiltása. Azonban ha van néhány olyan felhasználót, feltétlenül használatára van szükség az örökölt hitelesítési módszerek az Office 2010 erőforrásai eléréséhez, vagy IMAP/SMTP/POP-alapú ügyfelek számára, majd kizárhat ezeket a felhasználókat, amelyek az örökölt hitelesítési módszerek letiltása a szabályzat alól.

## <a name="why-are-exclusions-challenging"></a>Miért vannak kivételek kihívást?

Az Azure AD-ben gazdagépcsoportjaira egy feltételes hozzáférési szabályzatot a felhasználók egy csoportja. Akkor szintén kizárhatja néhány felhasználót címtárbeli szerepkörök, egyes felhasználókat vagy vendégek a felhasználók kiválasztásával. Fontos megjegyezni, hogy ezeket a kivételeket úgy vannak konfigurálva, ha a házirend céljának kifejezettebb nem kényszeríthető azoknak a felhasználóknak. Ha ezeket a kivételeket úgy lettek konfigurálva, listaként vagy egyéni felhasználók vagy az örökölt helyszíni biztonsági csoportot, akkor azt korlátozza, hogy látható-e a kizárási lista (a felhasználók lehet, hogy nem tudja a létezésük) és a rendszergazda szabályozhatja azt (a felhasználók úgy csatlakozhatnak a biztonsági csoport a szabályzat portálban kell). Ezenkívül, amelyek egy időben a kizárás teljes felhasználók nem szükséges vagy, jogosult legyen.

Kizárás elején van egy rövid lista a felhasználók, akik a szabályzat megkerülése. Az idő múlásával egyre több felhasználók ki vannak zárva, és a lista nő. Bizonyos helyzetekben szükség van, tekintse át a listát, és győződjön meg arról, hogy felhasználók továbbra is ki kell zárni. Technikai szempontból a lista kezelése lehet viszonylag egyszerű, de számára lehetővé teszi az üzleti döntéseket hozhat, és hogyan tegye meg róla, hogy az összes naplózható?

Azonban ha konfigurálja a feltételes hozzáférési szabályzat segítségével az Azure AD-csoportok kizárásával, majd használhatja a hozzáférési felülvizsgálatok egy kompenzáló vezérlőként meghajtó látható-e, és a felhasználók, akik rendelkeznek a kivétel számának csökkentése.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Egy kizárási csoportot egy feltételes hozzáférési szabályzat létrehozása

Kövesse az alábbi lépéseket, hozzon létre egy új Azure AD-csoport és a egy feltételes hozzáférési szabályzatot, amely ehhez a csoporthoz nem vonatkozik.

### <a name="create-an-exclusion-group"></a>Hozzon létre egy kizárási csoportot

1. Jelentkezzen be az Azure portálra.

1. A bal oldali navigációs sávján kattintson **Azure Active Directory** majd **csoportok**.

1. A felső menüben kattintson a **új csoport** nyissa meg a csoport ablaktáblát.

1. Az a **csoporttípust** listáról válassza ki **biztonsági**. Adjon meg egy nevet és leírást.

1. Állítsa be a **tagsági** írja be a **hozzárendelt**.

1. Válassza ki a felhasználók, érdemes lehet a kizárási csoport része, és kattintson a **létrehozás**.

    ![Új csoport ablaktábla](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Egy feltételes hozzáférési szabályzat, amely nem tartalmazza a csoport létrehozása

Most már létrehozhat egy feltételes hozzáférési szabályzatot, amely a kizárási csoport használja.

1. A bal oldali navigációs sávján kattintson **Azure Active Directory** majd **feltételes hozzáférési** megnyitásához a **házirendek** panelen.

1. Kattintson a **új szabályzat** megnyitásához a **új** ablaktáblán.

1. Adjon meg egy nevet.

1. Kattintson a hozzárendelések **felhasználók és csoportok**.

1. Az a **Belefoglalás** lapon jelölje be **minden felhasználó**.

1. Az a **kizárása** lapon maradva adja hozzá a pipa jellel, **felhasználók és csoportok** majd **kizárt felhasználók kiválasztása**.

1. Válassza ki a létrehozott kizárási csoportot.

    > [!NOTE]
    > Ajánlott eljárásként javasoljuk, hogy legalább egy rendszergazdai fiók zárni a szabályzatból, győződjön meg arról, hogy nem záródik ki a bérlő tesztelésekor.

1. Folytassa a a szervezeti követelmények alapján feltételes hozzáférési házirend beállításával.

    ![Kizárt felhasználók kiválasztása](./media/conditional-access-exclusion/select-excluded-users.png)

Nézzük terjed ki a két példa, ahol a hozzáférési felülvizsgálatok használhatja a feltételes hozzáférési szabályzatok kizárások kezelése.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countries"></a>1. példa: Hozzáférési felülvizsgálat letiltott országokból elérő felhasználók számára

Tegyük fel, hogy a feltételes hozzáférési szabályzatot, hogy blokkolja a hozzáférést az egyes országok. Ez magában foglalja egy csoportot, amely ki van zárva a szabályzat alól. Íme egy ajánlott hozzáférési felülvizsgálat, ahol a csoport tagjai nyilvánosan lektorálhatók.

> [!NOTE]
> A hozzáférési felülvizsgálatok hozzon létre egy globális rendszergazdai vagy a felhasználóifiók-adminisztrátor szerepkör szükséges.

1. A felülvizsgálat minden héten lesz bekövetkezésére.

2. Annak érdekében, hogy Ön már tartja a kizárási csoport a legfrissebb soha nem fejeződik.

3. Ez a csoport összes tagja lesz terjed ki a felülvizsgálatot.

4. Minden felhasználó saját igazolja, hogy azok továbbra is hozzáféréssel kell rendelkeznie a letiltott ezekben az országokban, ezért továbbra is szükségük van a csoport tagjának lennie kell.

5. Ha a kérelem áttekintése a felhasználó nem válaszol, akkor automatikusan törlődik a csoportból, és ezért már nem tud hozzáférni a bérlő az ezekben az országokban utazás közben.

6. Engedélyezi az e-mail értesítések, így a felhasználók értesítést kapjanak a kezdő és a hozzáférési felülvizsgálat befejezése után.

    ![Hozzáférési felülvizsgálat létrehozása](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>2. példa Az örökölt hitelesítési elérő felhasználók számára a hozzáférési felülvizsgálat

Tegyük fel, hogy a feltételes hozzáférési szabályzatot, hogy blokkolja az örökölt hitelesítéssel és a régebbi ügyfél felhasználók számára hozzáférést. Ez magában foglalja egy csoportot, amely ki van zárva a szabályzat alól. Íme egy ajánlott hozzáférési felülvizsgálat, ahol a csoport tagjai nyilvánosan lektorálhatók.

1. A felülvizsgálat kell lennie egy ismétlődő áttekintése.

2. A csoport minden tagja kell vizsgálni.

3. Az üzleti egység tulajdonosai, mint a kijelölt felülvizsgálók listázásához sikerült konfigurálni.

4. Az eredmények automatikus alkalmazása, és folytatja az örökölt hitelesítési módszerekkel nem jóváhagyott felhasználók eltávolítása.

5. Érdemes lehet, így a nagyméretű csoportok felülvizsgálók egyszerűen döntéseket hozhat a javaslatok lehetővé.

6. Engedélyezi az e-mail értesítések, így a felhasználók értesítést kapjanak a kezdő és a hozzáférési felülvizsgálat befejezése után.

    ![Hozzáférési felülvizsgálat létrehozása](./media/conditional-access-exclusion/create-access-review-2.png)

**A Pro-tipp**: Ha sok Kizárási csoportokat, és ezért létre kell hoznia a több hozzáférési felülvizsgálatok, most már van API, amely lehetővé teszi, hogy hozzon létre, és programozott módon kezelheti azokat a Microsoft Graph béta végpontját. Első lépésként tekintse meg a [az Azure AD hozzáférési felülvizsgálatok API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) és [példa beolvasása az Azure AD hozzáférési felülvizsgálatok keresztül a Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Hozzáférési felülvizsgálat eredményei és a vizsgálati naplók

Most, hogy minden a helyen, a csoport, a feltételes hozzáférési szabályzat és a hozzáférési felülvizsgálatok, azt az idő, figyeléséhez és nyomon követéséhez az ilyen eredményeit.

1. Az Azure Portalon nyissa meg a **hozzáférési felülvizsgálatokkal** panelen.

1. Nyissa meg a vezérlő és a program létrehozott a kizárási csoport kezeléséhez.

1. Kattintson a **eredmények** , akik jóváhagyták. a listán maradnak, és aki el lett távolítva.

    ![Hozzáférési felülvizsgálati eredmények](./media/conditional-access-exclusion/access-reviews-results.png)

1. Kattintson a **Auditnaplók** a felülvizsgálat alatt végrehajtott műveletek megtekintéséhez.

    ![A hozzáférési felülvizsgálatok auditnaplók](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

INFORMATIKAI rendszergazdaként, hogy a szabályzatokhoz kizárási csoportok kezelése néha elkerülhetetlen. Azonban ezek a csoportok karbantartása, magukat, és naplózás, ezeket a változásokat is megkönnyíti az Azure AD hozzáférési megtekintésével azokat a vállalat tulajdonosa vagy felhasználói rendszeresen ellenőrzi.

## <a name="next-steps"></a>További lépések

- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md)
- [Mi az az Azure Active Directory feltételes hozzáférés?](../conditional-access/overview.md)
