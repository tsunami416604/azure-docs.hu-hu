---
title: A külső felhasználók számára az RBAC használatával Azure-erőforrásokhoz való hozzáférés kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure-erőforrásokhoz való hozzáférést szerepköralapú hozzáférés-vezérlés (RBAC) használatával egy szervezeten kívüli felhasználók számára.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 91548a4df4a77623978ea4bcb214b76427c026a6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012023"
---
# <a name="manage-access-to-azure-resources-for-external-users-using-rbac"></a>A külső felhasználók számára az RBAC használatával Azure-erőforrásokhoz való hozzáférés kezelése

Szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi a nagy szervezetek számára, és az SMB-khez jobb biztonságkezelés külső közreműködők, a beszállítóknak meghatározott erőforrásoknak a környezetben, de nem feltétlenül a teljes hozzáférést igénylő freelancers használata infrastruktúra- vagy bármely számlázással kapcsolatos hatókörök. Az RBAC lehetővé teszi, hogy a rendszergazdai fiók (szolgáltatás-rendszergazda szerepkörrel előfizetés szintjén) által kezelt a rugalmas tulajdonos egy Azure-előfizetéssel, és több felhasználó meghívott egy előfizetésen belül, de bármilyen rendszergazdai jogosultságok nélkül működjenek .

> [!NOTE]
> Az Office 365-előfizetések vagy az Azure Active Directory-licencek (például: A Microsoft 365 felügyeleti központban nem megfelelőek az RBAC használatával üzembe helyezett Azure Active Directoryban való hozzáférés).

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Rendeljen RBAC-szerepköröket egy előfizetésre

Ha az RBAC használja (de nem kizárólag), van két gyakori példa:

* Hogy a szervezetek a külső felhasználók (nem a rendszergazda felhasználó Azure Active Directory-bérlő része) meghívott bizonyos erőforrásokhoz, vagy a teljes előfizetés kezelése
* A felhasználók a szervezet (részei a felhasználó Azure Active Directory-bérlő), de a különböző csapatok vagy csoportokat, amelyeknek az egyes erőforráscsoportokhoz vagy erőforrás-hatóköröket a környezetben vagy a teljes előfizetés részletes hozzáférés belüli használata

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Hozzáférés engedélyezése egy előfizetési szinten egy felhasználó Azure Active Directoryn kívül

RBAC-szerepkörök kizárólag a kaphatnak **tulajdonosok** az előfizetés. Ezért a rendszergazdának kell bejelentkeznie, ezzel a szerepkörrel rendelkező felhasználóként előre hozzárendelt vagy az Azure-előfizetést hozott létre.

Az Azure Portalról után jelentkezzen be rendszergazdaként, válassza ki "az előfizetések", és válassza a kívántra.
![az Azure portal előfizetés paneljén](./media/role-assignments-external-users/0.png) alapértelmezés szerint a rendszergazda felhasználó megvásárolta az Azure-előfizetéssel, ha a felhasználó fog megjelenni **Fiókadminisztrátor**, ez az előfizetés szerepkör alatt. Az Azure-előfizetési szerepkörökhöz kapcsolatos további információkért lásd: [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái](../billing/billing-add-change-azure-subscription-administrator.md).

Ebben a példában a felhasználó "alflanigan@outlook.com" van a **tulajdonos** az "ingyenes próbaverzió" előfizetést, az AAD bérlői "Alapértelmezett bérlőt Azure". Mivel ez a felhasználó kezdeti Microsoft Account "Outlook" az Azure-előfizetés létrehozója (a Microsoft Account = az Outlook, élő stb.) az alapértelmezett tartomány nevét, ezen a bérlőn hozzáadott összes többi felhasználó számára lesz **"\@ alflaniganuoutlook.onmicrosoft.com"**. A kialakításból fakadóan az új tartomány szintaxisa a következő bármik lehetnek, a felhasználó, aki létrehozta a bérlő nevét felhasználónevét és tartományát, és vegye fel a bővítmény megfelelő **". onmicrosoft.com"**.
Ezenkívül felhasználók is után kell bejelentkeznie a bérlőben lévő egyéni tartománynév hozzáadása és ellenőrzi az új bérlőhöz. Az Azure Active Directory-bérlő egyéni tartománynév ellenőrzése További információkért lásd: [egyéni tartománynév hozzáadása a címtárhoz](../active-directory/fundamentals/add-custom-domain.md).

Ebben a példában a "Alapértelmezett bérlőt Azure" könyvtárban található csak azok a felhasználók, a tartomány nevét "\@alflanigan.onmicrosoft.com".

Az előfizetés kiválasztása után a rendszergazda felhasználónak kell kattintson **hozzáférés-vezérlés (IAM)** , majd **adjon hozzá egy új szerepkör**.

![hozzáférés-vezérlés IAM szolgáltatás az Azure Portalon](./media/role-assignments-external-users/1.png)

![Új felhasználó hozzáadása a hozzáférés-vezérlés IAM szolgáltatás az Azure Portalon](./media/role-assignments-external-users/2.png)

A következő lépés, hogy válassza ki a szerepkör hozzárendelését és a felhasználó, akinek az RBAC-szerepkört rendel. Az a **szerepkör** legördülő menüben a rendszergazda felhasználó látja csak a beépített RBAC-szerepkör, amely elérhető az Azure-ban. További részletes magyarázatát minden szerepkör és a hozzárendelhető hatókörökkel,: [beépített szerepkörök az Azure-erőforrások](built-in-roles.md).

A rendszergazdai felhasználót kell majd adja hozzá a külső felhasználó e-mail-címét. A várt működése a külső felhasználó számára nem jelenik meg a meglévő bérlőhöz. Után a külső felhasználó kapott meghívót, ő lesz látható a **előfizetések > hozzáférés-vezérlés (IAM)** az összes aktuális felhasználóval, amely már hozzá vannak rendelve az RBAC-szerepkör az előfizetések szintjén.

![engedélyek hozzáadása új RBAC-szerepkör](./media/role-assignments-external-users/3.png)

![előfizetési szinten RBAC-szerepkörök listája](./media/role-assignments-external-users/4.png)

A felhasználó "chessercarlton@gmail.com" kell kapott meghívót egy **tulajdonos** az "Ingyenes próbaverzió" előfizetéshez. A meghívó küldése, után a külső felhasználót egy aktiválási hivatkozás egy megerősítő e-mailt kapja.
![e-mailben kapott RBAC szerepkör](./media/role-assignments-external-users/5.png)

Folyamatban van a szervezeten kívüli, az új felhasználó nincs meglévő attribútumokat a címtárban "Alapértelmezett bérlőt Azure". Ezek létrehozása után a külső felhasználó hozzájárult rögzítsen a rendszer a címtárban, az előfizetéshez társított rendelt szerepkör.

![meghívó e-mailt az RBAC-szerepkör](./media/role-assignments-external-users/6.png)

A külső felhasználó azt mutatja be az Azure Active Directory-bérlő mostantól külső felhasználóként, és ez az Azure Portalon is megtekinthetők.

![felhasználók panel az azure active Directoryval az Azure portal](./media/role-assignments-external-users/7.png)

Az a **felhasználók** megtekintése, a külső felhasználók felismeri a különböző ikontípust, az Azure Portalon.

Azonban megadásának **tulajdonosa** vagy **közreműködői** , külső felhasználó a hozzáférést a **előfizetés** hatókörét, nem engedélyezi a hozzáférést a rendszergazdai felhasználót a címtárban, kivéve, ha a **Globális rendszergazdai** lehetővé teszi ezt. A felhasználó képernyőhöz a **felhasználótípus**, két általános paramétert, amelynek **tag** és **vendég** azonosíthatók. Egy tag egy felhasználó regisztrált a címtárban a címtár egy külső forrásból meghívott felhasználó pedig a vendég nem. További információkért lásd: [hogyan Azure Active Directory-rendszergazdák hozzá B2B együttműködési felhasználókat](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Győződjön meg arról, hogy miután megadta a hitelesítő adatait a portálon, a külső felhasználó kiválasztja a megfelelő címtárba való bejelentkezéshez. Ugyanaz a felhasználó így is rendelkezik több címtár hozzáféréssel és is egyikét őket az Azure Portal jobb felső felhasználónév kattintva válassza ki, és a legördülő listából válassza ki a megfelelő könyvtárra.

Ugyanakkor a Vendég a címtárban, a külső felhasználó kezelheti az Azure-előfizetéshez tartozó összes erőforrást, de a könyvtár nem hozzáférhető.

![az azure active Directoryval az Azure Portalra korlátozott hozzáférés](./media/role-assignments-external-users/9.png)

Az Azure Active Directory és az Azure-előfizetés nem rendelkezik a szülő-gyermek kapcsolat például a más Azure-erőforrások (például: virtuális gépek, virtuális hálózatok, webalkalmazások, tárolás stb.) az Azure-előfizetéssel rendelkezik. Minden az utóbbi létrehozott, felügyelt, és egy Azure-előfizetéshez számlázzuk, míg a egy Azure-előfizetést egy Azure-címtárhoz a hozzáférés kezelésére szolgál. További információkért lásd: [módját az Azure-előfizetések kapcsolata az Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Az összes a beépített RBAC-szerepkörök **tulajdonosa** és **közreműködői** kínálnak a teljes felügyeleti erőforrásokhoz való teljes hozzáférés a környezetben, a különbség az, hogy egy munkatárs nem hozható létre, és új RBAC-szerepkörök törlése . A beépített szerepkörök, például **virtuális gépek Közreműködője** csak az erőforrásokat, függetlenül attól, hogy a név által jelzett összes felügyeleti hozzáférést nyújtanak a **erőforráscsoport** az éppen létrehozott.

A beépített RBAC-szerepkör-hozzárendelés **virtuális gépek Közreműködője** előfizetés szintjén, azt jelenti, hogy a felhasználó a szerepkörrel:

* Megtekintheti az összes virtuális gép függetlenül attól, hogy azok telepítési dátumát és az erőforráscsoportok részei
* A virtuális gépek teljes felügyeleti hozzáféréssel rendelkezik az előfizetés
* Bármilyen más típusú erőforrásokat nem tekintheti meg az előfizetés
* Nem működik, számlázási szempontból a módosításokat

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Beépített RBAC szerepkör hozzárendelése a külső felhasználó

Az ebben a tesztben a külső felhasználók más-más forgatókönyvet "alflanigan@gmail.com" kerülnek be egy **virtuális gépek Közreműködője**.

![virtuális gép közreműködő beépített szerepkört](./media/role-assignments-external-users/11.png)

A külső felhasználó a beépített szerepkörrel rendelkező normál viselkedését, hogy megjelenítheti és kezelheti a csak a virtuális gépek és azok szomszédos erőforrás-kezelő csak szükséges erőforrások üzembe helyezése során. A kialakításból fakadóan ezen korlátozott szerepkörök ajánlat csak a hozzáférést az Azure Portalon létrehozott levelező erőforrásaikat.

![virtuális gép közreműködő szerepkör áttekintése az Azure Portalon](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Hozzáférés engedélyezése egy előfizetési szinten egy felhasználó ugyanabban a címtárban

A folyamat megegyezik a külső felhasználók hozzáadása, biztosítása az RBAC-szerepkör, valamint a felhasználó rendszergazdai szempontjából egyaránt hozzáférést megkapják a szerepkörhöz. A különbség az, hogy a meghívott felhasználó nem fog kapni minden e-mailben meghívást, a bejelentkezés után az előfizetésen belüli összes erőforrás hatókör az irányítópult elérhető lesz.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Rendeljen RBAC-szerepköröket az erőforrás-csoport hatóköre:

Az RBAC szerepkör hozzárendelése egy **erőforráscsoport** hatókör van egy azonos folyamat hozzárendeléséhez a szerepkört az előfizetés szintjén mindkét típusú felhasználók – külső vagy belső (ugyanabban a címtárban része). A felhasználók, az RBAC-szerepkör, hogy tekintse meg a környezetben csak az erőforráscsoportot, van hozzájuk rendelve a hozzáférés a **erőforráscsoportok** ikonra az Azure Portalon.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Rendeljen RBAC-szerepköröket az erőforrás-hatókörben

Egy erőforrás hatókörre az Azure-beli RBAC szerepkör hozzárendelése van egy azonos folyamat hozzárendeléséhez a szerepkört az előfizetés szintjén vagy az erőforráscsoport szintjén, a következő ugyanabban a munkafolyamatban mindkét forgatókönyvet támogatja. Újra, a felhasználók, az RBAC-szerepkör csak akkor lett hozzárendelve hozzáférés vagy a cikkek látható a **összes erőforrás** lapján vagy közvetlenül az irányítópulton.

Fontos elemét alkotják a RBAC egyaránt csoporthatókör erőforrás vagy erőforráscsoport hatókörrel van, ügyeljen arra, hogy jelentkezzen be a megfelelő címtárba, hogy a felhasználók számára.

![Directory-bejelentkezés az Azure Portalon](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Rendeljen RBAC-szerepköröket egy Azure Active Directory-csoport

Az RBAC használatával, az Azure-ban három különböző hatókör összes forgatókönyv kínálnak a jogosultság felügyelete, üzembe helyezésének és különböző erőforrások felügyelete a hozzárendelt felhasználók nincs szükség személyes előfizetés kezelése. Függetlenül az RBAC-szerepkör az olyan előfizetés, erőforráscsoport vagy erőforrás hatókör van hozzárendelve, a hozzárendelt felhasználók a további létrehozott összes erőforrást számlázzuk, ahol a felhasználók férhetnek hozzá egy Azure-előfizetéshez. Ezzel a módszerrel számlázási a teljes Azure-előfizetés rendszergazdai engedélyekkel rendelkező felhasználók rendelkezik teljes áttekintése a használat, függetlenül attól, hogy az erőforrások kezelését.

A nagyobb vállalatok számára az Azure Active Directory-csoportokat a mérlegeli, hogy a rendszergazdai felhasználó hozzáférést szeretne biztosítani a részletes csapatoknál vagy részlegeknél teljes, nem külön-külön minden felhasználó számára, így a mérlegeli szempontjából azonos módon alkalmazható RBAC-szerepkörök Ez rendkívül idő- és felügyeleti hatékony beállítás. Ez a példa szemléltetéséhez a **közreműködői** szerepkör van adva egy csoporthoz a bérlő az előfizetés szintjén.

![AAD-csoportokat az RBAC-szerepkör hozzáadása](./media/role-assignments-external-users/14.png)

Ezek a csoportok biztonsági csoportokat, amelyek kiépített és felügyelt csak az Azure Active Directoryn belüli.

