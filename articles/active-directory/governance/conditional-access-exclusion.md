---
title: Feltételes hozzáférési szabályzatokból kizárt felhasználók kezelése – Azure AD
description: Megtudhatja, hogy miként használhatja az Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokat a feltételes hozzáférési szabályzatokból kizárt felhasználók kezelésére
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422709"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Az Azure AD-hozzáférés-felülvizsgálatok használata a feltételes hozzáférési szabályzatokból kizárt felhasználók kezeléséhez

Egy ideális világban minden felhasználó követné a hozzáférési rendőrségeket, hogy biztonságos hozzáférést biztosítson a szervezet erőforrásaihoz. Néha azonban vannak olyan üzleti esetek, amelyek kivételeket igényelnek. Ez a cikk néhány példát mutat be arra vonatkozóan, hogy hol lehet szükség kizárásokra, és hogyan kezelheti ezt a feladatot, hogyan kerülheti el a házirend-kivételek felügyeletét, és hogyan biztosíthatja az auditorok számára annak igazolását, hogy ezeket a kivételeket rendszeresen felülvizsgálják az Azure használatával Active Directory (Azure AD) hozzáférési felülvizsgálatok.

> [!NOTE]
> Az Azure AD-hozzáférési felülvizsgálatok használatához érvényes Azure AD Premium P2, Enterprise Mobility + Security E5 fizetős vagy próbalicenc szükséges. További információ: [Azure Active Directory-kiadások.](../fundamentals/active-directory-whatis.md)

## <a name="why-would-you-exclude-users-from-policies"></a>Miért zárná ki a felhasználókat a házirendekből?

Rendszergazdaként használhatja az [Azure AD feltételes hozzáférést,](../conditional-access/overview.md) hogy a felhasználók hitelesítése többtényezős hitelesítéssel (MFA) vagy megbízható hálózatról vagy eszközről jelentkezzen be. A központi telepítés tervezése során megtudhatja, hogy ezen követelmények némelyike nem teljesíthető minden felhasználó számára. Vannak például olyan felhasználók, akik olyan távoli irodából dolgoznak, amely nem része a belső hálózatnak, vagy van egy olyan vezető, aki nem támogatott régi telefont használ. A vállalkozás megköveteli, hogy ezek a felhasználók bejelentkezhessenek, és elvégezhessék a munkájukat, ezért ki vannak zárva a feltételes hozzáférési házirendekből.

Egy másik példa, hogy a feltételes hozzáférés [ben megnevezett helyeket](../conditional-access/location-condition.md) használhat olyan megyék és régiók készletének konfigurálásához, amelyekből nem szeretné engedélyezni a felhasználók számára a bérlő elérését.

![Elnevezett helyek a feltételes hozzáférésben](./media/conditional-access-exclusion/named-locations.png)

Bizonyos esetekben azonban előfordulhat, hogy a felhasználóknak jogos okuk van arra, hogy ezekből a letiltott országokból/régiókból jelentkezzenek be. Előfordulhat például, hogy a felhasználók munkahelyi vagy személyes okokból utaznak. Ebben a példában a feltételes hozzáférés házirend blokkolja ezeket az országokat/régiókat lehet egy dedikált felhőbiztonsági csoport a felhasználók számára, akik ki vannak zárva a szabályzatból. Azok a felhasználók, akiknek utazás közben hozzáférésre van szükségük, hozzáadhatják magukat a csoporthoz az [Azure AD önkiszolgáló csoportkezelés használatával.](../users-groups-roles/groups-self-service-management.md)

Egy másik példa lehet, hogy feltételes hozzáférési házirenddel rendelkezik, amely [blokkolja az örökölt hitelesítést a felhasználók túlnyomó többségénél.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) A Microsoft nyomatékosan javasolja, hogy tiltsa le az örökölt protokollok használatát a bérlőben a biztonsági állapot javítása érdekében. Ha azonban vannak olyan felhasználók, akiknek feltétlenül örökölt hitelesítési módszereket kell használniuk az erőforrások Office 2010 vagy IMAP/SMTP/POP alapú ügyfeleken keresztüli eléréséhez, akkor ezeket a felhasználókat kizárhatja az örökölt hitelesítési módszereket tiltó házirendből.

## <a name="why-are-exclusions-challenging"></a>Miért kihívást jelent a kizárás?

Az Azure AD-ben egy feltételes hozzáférési szabályzat ot a felhasználók egy készletére. Emellett kizárhatja ezeket a felhasználókat az Azure AD-szerepkörök, az egyéni felhasználók vagy a felhasználók vendégei kiválasztásával. Fontos megjegyezni, hogy ha ezek a kizárások konfigurálva vannak, a házirend-szándék nem kényszeríthető ki az adott felhasználók számára. Ha ezeket a kizárásokat az egyes felhasználók listájaként vagy egy örökölt helyszíni biztonsági csoporton keresztül konfigurálták, akkor korlátozza a kizárási lista láthatóságát (a felhasználók nem tudnak annak létezéséről) és a rendszergazda felett (a felhasználók csatlakozhatnak a biztonsági csoportot, hogy megtetse a házirendet). Emellett előfordulhat, hogy azoka felhasználók, akik egyszerre jogosultak a kizárásra, már nem lesz szükségük rá, vagy jogosultak rá.

A kizárás elején van egy rövid lista azokról a felhasználókról, akik megkerülik a házirendet. Idővel egyre több felhasználó ki van zárva, és a lista növekszik. Egy bizonyos ponton szükség van a lista áttekintésére, és annak megerősítésére, hogy ezek a felhasználók továbbra is ki kell zárni. Kezelése a lista technikai szempontból, lehet viszonylag egyszerű, de ki teszi az üzleti döntéseket, és hogyan győződjön meg arról, hogy minden auditálható?

Ha azonban konfigurálja a kizárást a feltételes hozzáférési szabályzat egy Azure AD-csoport használatával, majd használhatja a hozzáférési felülvizsgálatok kompenzációs vezérlőként, a láthatóság, és csökkenti a felhasználók száma, akik kivételt.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Kizárási csoport létrehozása feltételes hozzáférési házirendben

Az alábbi lépésekkel hozzon létre egy új Azure AD-csoportot és egy feltételes hozzáférési szabályzatot, amely nem vonatkozik az adott csoportra.

### <a name="create-an-exclusion-group"></a>Kizárási csoport létrehozása

1. Jelentkezzen be az Azure portálra.

1. A bal oldali navigációs sávon kattintson az **Azure Active Directory** elemre, majd a Csoportok **parancsra.**

1. A csoportablak megnyitásához kattintson a felső menü **Új csoport** parancsára.

1. A **Csoporttípus** listában válassza a **Biztonság**lehetőséget. Adja meg a nevet és a leírást.

1. Ügyeljen arra, hogy a **tagság** típusát **Hozzárendelve**állítsa be.

1. Jelölje ki azokat a felhasználókat, akik nek a kizárási csoport tagjainak kell lenniük, majd kattintson a **Létrehozás gombra.**

    ![Új csoportablaktábla az Azure Active Directoryban](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>A csoportot kizáró feltételes hozzáférési házirend létrehozása

Most létrehozhat egy feltételes hozzáférési házirendet, amely ezt a kizárási csoportot használja.

1. A bal oldali navigációs, kattintson az **Azure Active Directory,** majd a **feltételes hozzáférés** a **házirendek** panel megnyitásához.

1. Kattintson az **Új házirend gombra** az **Új** ablaktábla megnyitásához.

1. Adjon meg egy nevet.

1. A Hozzárendelések csoportban kattintson a **Felhasználók és csoportok**elemre.

1. A **Belefoglalás** lapon válassza az **Összes felhasználó lehetőséget.**

1. A **Kizárás** lapon jelölje be a **Felhasználók és csoportok** elemet, majd kattintson a Kizárt felhasználók **kijelölése gombra.**

1. Jelölje ki a létrehozott kizárási csoportot.

    > [!NOTE]
    > Ajánlott eljárásként ajánlott kizárni legalább egy rendszergazdai fiókot a szabályzatból a tesztelés során, hogy megbizonyosodjon arról, hogy nincs kizárva a bérlőből.

1. Folytassa a feltételes hozzáférési házirend beállítását a szervezeti követelmények alapján.

    ![Kizárt felhasználók ablaktábla kijelölése a Feltételes hozzáférés programban](./media/conditional-access-exclusion/select-excluded-users.png)

Mutassunk be két példát, ahol a hozzáférés-felülvizsgálatok segítségével kezelheti a kizárásokat a feltételes hozzáférési házirendekben.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>1. példa: Hozzáférés felülvizsgálata a letiltott országokból/régiókból hozzáférő felhasználók számára

Tegyük fel, hogy rendelkezik egy feltételes hozzáférési szabályzattal, amely blokkolja a hozzáférést bizonyos országokból/régiókból. Olyan csoportot tartalmaz, amely ki van zárva a házirendből. Az alábbiakban egy ajánlott hozzáférési felülvizsgálatot javasol, ahol a csoport tagjait felülvizsgálják.

> [!NOTE]
> A hozzáférési felülvizsgálatok létrehozásához globális rendszergazdai vagy felhasználói rendszergazdai szerepkör szükséges.

1. A felülvizsgálat minden héten újra megjelenik.

2. Soha nem ér véget annak érdekében, hogy megbizonyosodjon arról, hogy ez a kizárási csoport a leginkább naprakész.

3. A csoport minden tagja jelen lesz a felülvizsgálat hatókörében.

4. Minden felhasználónak saját maga tanúsítja, hogy továbbra is hozzá kell férnie ezekből a blokkolt országokból/régiókból, ezért továbbra is a csoport tagjának kell lennie.

5. Ha a felhasználó nem válaszol az ellenőrzési kérelemre, akkor automatikusan törlődik a csoportból, és ezért már nem tudja elérni a bérlőt, miközben ezekbe az országokba/régiókba utazik.

6. Engedélyezze a levelezési értesítéseket, hogy a felhasználók értesítést kapjanak a hozzáférési felülvizsgálat kezdetéről és befejezéséről.

    ![Hozzáférés-ellenőrző ablaktábla létrehozása például 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>2. példa: Hozzáférés-ellenőrzés az örökölt hitelesítéssel hozzáférő felhasználók számára

Tegyük fel, hogy rendelkezik egy feltételes hozzáférési házirenddel, amely blokkolja a hozzáférést a felhasználók számára az örökölt hitelesítés és a régebbi ügyfélverziók használatával. Olyan csoportot tartalmaz, amely ki van zárva a házirendből. Az alábbiakban egy ajánlott hozzáférési felülvizsgálatot javasol, ahol a csoport tagjait felülvizsgálják.

1. Ennek a felülvizsgálatnak ismétlődő felülvizsgálatnak kell lennie.

2. A csoport minden tagjának felül kell vizsgálnia.

3. Beállítható úgy, hogy a részlegtulajdonosokat sorolja fel a kiválasztott ellenőrzőként.

4. Alkalmazza automatikusan az eredményeket, és távolítsa el a nem jóváhagyott felhasználókat az örökölt hitelesítési módszerek további használatára.

5. Hasznos lehet az ajánlások engedélyezése, hogy a nagy csoportok látogatói könnyen meghozhassák döntéseiket.

6. Engedélyezze a levelezési értesítéseket, hogy a felhasználók értesítést kapjanak a hozzáférési felülvizsgálat kezdetéről és befejezéséről.

    ![Hozzáférés-ellenőrző ablaktábla létrehozása például 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro Tipp:** Ha sok kizárási csoport, és ezért létre kell hoznia több hozzáférési felülvizsgálatok, most már van egy API a Microsoft Graph béta-végpont, amely lehetővé teszi, hogy hozzon létre és kezelje őket programozott módon. Első lépések: az [Azure AD access reviews API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) és [példa az Azure AD-hozzáférési felülvizsgálatok microsoft graph-on keresztül történő beolvasására című témakörben.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)

## <a name="access-review-results-and-audit-logs"></a>Hozzáférés az ellenőrzési eredményekhez és a naplókhoz

Most, hogy mindent a helyén, csoport, feltételes hozzáférési szabályzat és a hozzáférési felülvizsgálatok, itt az ideje, hogy figyelemmel kíséri és nyomon követi az eredményeket.

1. Az Azure Portalon nyissa meg az **Access-értékelések** panelt.

1. Nyissa meg a kizárási csoport kezeléséhez létrehozott vezérlőt és programot.

1. **Kattintson az Eredmények gombra** annak megtekintéséhez, hogy ki maradt a listán, és kit távolítottak el.

    ![A hozzáférési felülvizsgálatok eredményei azt mutatják, hogy kit hagytak jóvá](./media/conditional-access-exclusion/access-reviews-results.png)

1. Ezután kattintson **a Naplók naplózása gombra** az ellenőrzés során végrehajtott műveletek megtekintéséhez.

    ![Access-ellenőrzések naplózási naplóinak felsorolási műveletek](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Informatikai rendszergazdaként tudja, hogy a kizárási csoportok kezelése a szabályzatok néha elkerülhetetlen. Azonban ezek a csoportok karbantartása, a vállalkozás tulajdonosa vagy maguk a felhasználók rendszeres áttekintése, és ezek a módosítások naplózása megkönnyítheti az Azure AD-hozzáférési felülvizsgálatok.

## <a name="next-steps"></a>További lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Mi a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)
