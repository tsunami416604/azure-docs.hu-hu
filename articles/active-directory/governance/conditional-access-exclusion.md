---
title: Hozzáférési felülvizsgálatok használata a feltételes hozzáférési házirendből kizárt felhasználók felügyeletéhez – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokat a feltételes hozzáférési házirendből kizárt felhasználók kezeléséhez
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
ms.openlocfilehash: 246503fec6436bf49dcd5fb89c2dc0ed345a43ca
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499918"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Az Azure AD hozzáférési felülvizsgálatok használata a feltételes hozzáférési házirendből kizárt felhasználók felügyeletéhez

Az ideális világban minden felhasználó a hozzáférési szabályzatokat követve gondoskodik a szervezet erőforrásaihoz való hozzáférésről. Bizonyos esetekben azonban előfordulhat, hogy kivételeket kell elvégeznie. Ez a cikk néhány példát ismertet, amelyekben szükség lehet a kizárásokra, valamint arról, hogy a rendszergazda hogyan kezelheti ezt a feladatot, elkerülheti a házirend-kivételek felügyeletét, és biztosíthatja a könyvvizsgálók számára, hogy az Azure-ban rendszeresen felülvizsgálják ezeket a kivételeket. Active Directory (Azure AD) hozzáférési felülvizsgálatok.

> [!NOTE]
> Az Azure AD hozzáférési felülvizsgálatok használatához érvényes prémium szintű Azure AD P2, Enterprise Mobility + Security E5 fizetett vagy próbaverziós licenc szükséges. További információk: [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Azure Active Directory-kiadások).

## <a name="why-would-you-exclude-users-from-policies"></a>Miért zárja ki a felhasználókat a szabályzatokból?

Rendszergazdaként az [Azure ad feltételes hozzáférés](../conditional-access/overview.md) használatával megkövetelheti, hogy a felhasználók hitelesítést végezzenek a többtényezős hitelesítés (MFA) használatával, vagy egy megbízható hálózatról vagy eszközről jelentkezzenek be. Az üzembe helyezés megtervezése során kiderül, hogy az összes felhasználó nem teljesítheti ezeket a követelményeket. Például vannak olyan felhasználók, akik olyan távoli irodából dolgoznak, amely nem része a belső hálózatnak, vagy egy olyan Executive, aki nem támogatott régi telefont használ. A vállalat megköveteli, hogy ezeknek a felhasználóknak engedélyezzék a bejelentkezést és a munkájukat, ezért azokat kizárják a feltételes hozzáférési házirendből.

Egy másik példa, hogy a feltételes hozzáférés [névvel ellátott helyekkel](../conditional-access/location-condition.md) konfigurálja azon megyék és régiók készletét, amelyekről nem kívánja engedélyezni a felhasználók számára a bérlőhöz való hozzáférést.

![Nevesített helyszínek a feltételes hozzáférésben](./media/conditional-access-exclusion/named-locations.png)

Bizonyos esetekben azonban a felhasználóknak jogos oka lehet a tiltott országokból/régiókból való bejelentkezés. Előfordulhat például, hogy a felhasználók munkahelyi vagy személyes okokból utaznak. Ebben a példában az ezen országokat/régiókat blokkoló feltételes hozzáférési szabályzat egy dedikált Felhőbeli biztonsági csoporttal rendelkezik a szabályzatból kizárt felhasználók számára. Azok a felhasználók, akiknek utazás közben kell hozzáférni, hozzáadhatják magukat a csoporthoz az [Azure ad önkiszolgáló csoport kezelése](../users-groups-roles/groups-self-service-management.md)használatával.

Egy másik példa lehet, hogy rendelkezik egy feltételes hozzáférési szabályzattal, amely [blokkolja a felhasználók túlnyomó többségének örökölt hitelesítését](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). A Microsoft nyomatékosan javasolja, hogy az örökölt protokollok használatát letiltsa a bérlőben a biztonsági helyzet javítása érdekében. Ha azonban vannak olyan felhasználók, akik teljes mértékben szükségük van a régi hitelesítési módszerek használatára az erőforrások Office 2010 vagy IMAP/SMTP/POP-alapú ügyfeleken keresztüli eléréséhez, akkor kizárhatja ezeket a felhasználókat az örökölt hitelesítési módszereket blokkoló szabályzatból.

## <a name="why-are-exclusions-challenging"></a>Miért vitatják meg a kizárásokat?

Az Azure AD-ben egy feltételes hozzáférési szabályzatot is beállíthat a felhasználók körére. Ezen felhasználók némelyikét kihagyhatja az Azure AD-szerepkörök, az egyes felhasználók vagy a felhasználók felhasználóinak kiválasztásával is. Fontos megjegyezni, hogy ha ezek a kizárások konfigurálva vannak, a szabályzat célja nem kényszeríthető ki ezeknél a felhasználóknál. Ha ezek a kizárások egyéni felhasználók vagy egy örökölt helyszíni biztonsági csoporton keresztül lettek konfigurálva, akkor ez korlátozza a kizárási lista láthatóságát (Előfordulhat, hogy a felhasználók nem tudják a létezését), és a rendszergazda maga vezérli azt (a felhasználók csatlakozhatnak a következőhöz: biztonsági csoport – adja át a szabályzatot). Emellett előfordulhat, hogy a kizárási jogosultsággal rendelkező felhasználók számára már nem szükséges, vagy jogosultak rá.

A kizárás kezdetekor a szabályzat megkerülését végző felhasználók rövid listája látható. Az idő múlásával egyre több felhasználó ki van zárva, és a lista növekszik. Egy ponton át kell tekintenie a listát, és meg kell győződnie arról, hogy a felhasználók mindegyikét továbbra is ki kell zárni. A lista technikai nézetből való kezelése viszonylag egyszerű, de ki teszi az üzleti döntéseket, és hogyan gondoskodik arról, hogy az összes naplózható legyen?

Ha azonban egy Azure AD-csoport használatával konfigurálja a kizárást a feltételes hozzáférési szabályzatba, akkor a hozzáférési felülvizsgálatokat kompenzáló vezérlőként használhatja, a láthatóságot és a kivételt okozó felhasználók számának csökkentése érdekében.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Kizárási csoport létrehozása feltételes hozzáférési házirendben

Az alábbi lépéseket követve hozzon létre egy új Azure AD-csoportot és egy feltételes hozzáférési szabályzatot, amely nem vonatkozik erre a csoportra.

### <a name="create-an-exclusion-group"></a>Kizárási csoport létrehozása

1. Jelentkezzen be az Azure portálra.

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory** , majd a **csoportok**elemre.

1. A felső menüben kattintson az **új csoport** elemre a csoport ablaktábla megnyitásához.

1. A **csoport típusa** listában válassza a **Biztonság**elemet. Adja meg a nevet és a leírást.

1. Ügyeljen arra, hogy a **tagság** típusa legyen **hozzárendelve**.

1. Válassza ki a kizárási csoport részét képező felhasználókat, majd kattintson a **Létrehozás**gombra.

    ![Új csoport ablaktábla Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Hozzon létre egy feltételes hozzáférési szabályzatot, amely kizárja a csoportot

Mostantól létrehozhat egy olyan feltételes hozzáférési szabályzatot, amely ezt a kizárási csoportot használja.

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory** elemre, majd kattintson a **feltételes hozzáférés** elemre a **házirendek** panel megnyitásához.

1. Az **új** ablaktábla megnyitásához kattintson az **új házirend** elemre.

1. Adjon meg egy nevet.

1. A hozzárendelések területen kattintson a **felhasználók és csoportok**elemre.

1. A **beágyazás** lapon válassza a **minden felhasználó**lehetőséget.

1. A **kizárás** lapon jelölje be a **felhasználók és csoportok** jelölőnégyzetet, majd kattintson a **kizárt felhasználók kiválasztása**lehetőségre.

1. Válassza ki a létrehozott kizárási csoportot.

    > [!NOTE]
    > Ajánlott eljárásként célszerű kizárni legalább egy rendszergazdai fiókot a szabályzatból, ha tesztelni szeretné, hogy ne zárja ki a bérlőből.

1. Folytassa a feltételes hozzáférési házirend beállításával a szervezeti követelmények alapján.

    ![A feltételes hozzáférés területen válassza a kizárt felhasználók ablaktáblát](./media/conditional-access-exclusion/select-excluded-users.png)

Ismerkedjen meg két példával, amelyekkel a hozzáférési felülvizsgálatok segítségével kezelheti a feltételes hozzáférési szabályzatok kizárásait.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>1\. példa: A tiltott országokból/régiókból hozzáférő felhasználók hozzáférési felülvizsgálata

Tegyük fel, hogy van egy feltételes hozzáférési szabályzata, amely blokkolja bizonyos országok/régiók hozzáférését. Tartalmaz egy csoportot, amely ki van zárva a szabályzatból. Az alábbiakban egy ajánlott hozzáférési felülvizsgálatot talál, ahol a csoport tagjait vizsgálják felül.

> [!NOTE]
> Hozzáférési felülvizsgálatok létrehozásához globális rendszergazdai vagy felhasználói rendszergazdai szerepkörre van szükség.

1. A felülvizsgálat minden héten újra fog történni.

2. Soha nem fejeződik be, hogy a lehető legnaprakészebb legyen a kizárási csoport.

3. A csoport minden tagja a felülvizsgálat hatókörében lesz.

4. Minden felhasználónak meg kell határoznia, hogy továbbra is hozzá kell férnie ezekből a blokkolt országokból/régiókból, ezért továbbra is a csoport tagjának kell lennie.

5. Ha a felhasználó nem válaszol a felülvizsgálati kérelemre, a rendszer automatikusan eltávolítja őket a csoportból, így a bérlők a továbbiakban nem férhetnek hozzá az ezen országokba/régiókhoz.

6. E-mail-értesítések engedélyezése, hogy a felhasználók értesítést kapjanak a hozzáférési felülvizsgálat megkezdéséről és befejezéséről.

    ![Hozzáférés-felülvizsgálati ablaktábla létrehozása az 1. példában](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>2\. példa Örökölt hitelesítéssel rendelkező felhasználók hozzáférési felülvizsgálata

Tegyük fel, hogy van egy feltételes hozzáférési szabályzata, amely blokkolja a felhasználók hozzáférését az örökölt hitelesítést és a régebbi verziójú ügyfeleket használó felhasználók számára. Tartalmaz egy csoportot, amely ki van zárva a szabályzatból. Az alábbiakban egy ajánlott hozzáférési felülvizsgálatot talál, ahol a csoport tagjait vizsgálják felül.

1. A felülvizsgálatnak ismétlődő felülvizsgálatra van szüksége.

2. A csoport összes tagját felül kell vizsgálni.

3. Konfigurálható úgy, hogy az üzleti egység tulajdonosait kilistázza a kiválasztott felülvizsgálók számára.

4. Automatikusan alkalmazza az eredményeket, és távolítsa el azokat a felhasználókat, akik nem hagyták jóvá a korábbi hitelesítési módszerek használatát.

5. Hasznos lehet a javaslatok lehetővé tétele, hogy a nagyméretű csoportok áttekintő tagjai könnyedén megtudják a döntéseiket.

6. E-mail-értesítések engedélyezése, hogy a felhasználók értesítést kapjanak a hozzáférési felülvizsgálat megkezdéséről és befejezéséről.

    ![Hozzáférési felülvizsgálati ablaktábla létrehozása a 2. példához](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro-tipp**: Ha sok kizárási csoporttal rendelkezik, és ezért több hozzáférési felülvizsgálatra van szükség, most már van egy API a Microsoft Graph Beta-végponton, amely lehetővé teszi a programozott módon történő létrehozását és kezelését. Első lépésként tekintse meg az [Azure ad hozzáférési felülvizsgálatok API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) -referenciáját, és [példát az Azure ad hozzáférési felülvizsgálatok Microsoft Graph használatával történő](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)lekérésére.

## <a name="access-review-results-and-audit-logs"></a>Hozzáférési felülvizsgálati eredmények és naplók

Most, hogy mindent megtesz a hely, a csoport, a feltételes hozzáférési szabályzat és a hozzáférési felülvizsgálatok terén, itt az idő, hogy figyelje és nyomon kövesse a felülvizsgálatok eredményeit.

1. A Azure Portal nyissa meg a **hozzáférési felülvizsgálatok** panelt.

1. Nyissa meg a kizárási csoport kezeléséhez létrehozott vezérlőt és programot.

1. Az **eredmények** lehetőségre kattintva megtekintheti, hogy ki hagyta jóvá a listát, és ki lett távolítva.

    ![A hozzáférési felülvizsgálatok eredményeinek megjelenítése, akit jóváhagytak](./media/conditional-access-exclusion/access-reviews-results.png)

1. Ezután kattintson a **naplók** elemre a felülvizsgálat során végrehajtott műveletek megtekintéséhez.

    ![Hozzáférési felülvizsgálatok naplófájljainak listázása műveletek](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Rendszergazdaként biztos lehet abban, hogy a kizárási csoportok a szabályzatokhoz való kezelése időnként elkerülhetetlen. Azonban a csoportok fenntartása, az üzleti tulajdonos vagy a felhasználók rendszeresen felülvizsgálják azokat, és az Azure AD hozzáférési felülvizsgálatok révén könnyebben naplózzák ezeket a módosításokat.

## <a name="next-steps"></a>További lépések

- [Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása](create-access-review.md)
- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)
