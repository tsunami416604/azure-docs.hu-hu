---
title: "Konfigurálja az Azure AD Privileged Identity Management |} Microsoft Docs"
description: "Ez a témakör azt ismerteti, mi az Azure AD Privileged Identity Management és a PIM használatát a felhő biztonság növelése érdekében."
services: active-directory
documentationcenter: 
author: barclayn
manager: mtillman
editor: 
ms.assetid: c548ed2e-06e3-4eaf-a63d-0f02ee72da25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 9a013820689742faa732971c00b82c7ad8638a95
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Mi az Azure AD Privileged Identity Management?

Az Azure Active Directory (AD) Privileged Identity Management segítségével kezelheti, irányíthatja és felügyelheti a szervezeten belüli hozzáféréseket. Ez magában foglalja erőforrásokhoz való hozzáférést, az Azure AD-Azure-erőforrások (előzetes verzió), és más Microsoft Online szolgáltatások, például Office 365-öt vagy a Microsoft Intune.

> [!NOTE]
> A privileged Identity Management a rendszergazdák az Azure Active Directory Premium P2 kiadásával licenceli esetén érhető el a teljes szervezet számára. További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).

A szervezetek szeretne személyek, akiknek információ vagy az erőforrások biztonságos elérését, mert, amely csökkenti az esélyét, hogy a hozzáférést egy rosszindulatú felhasználó, vagy véletlenül a bizalmas erőforrások érintő hitelesített felhasználó számának minimalizálása érdekében.  Azonban felhasználók továbbra is el kell végezniük a jogosultságokhoz kötött műveletek az Azure AD-Azure, az Office 365 vagy az SaaS-alkalmazásokhoz. A szervezetek felhasználók privilegizált hozzáférést biztosíthat Azure erőforrásokat, például az előfizetések és az Azure AD számára. Nincs szükség a felhasználók tevékenységeit a rendszergazda jogosultságokkal a felügyeletet. Az Azure AD Privileged Identity Management túlzott, szükségtelen a kockázatok csökkentése érdekében segítségével, vagy helytelen a hozzáférési jogosultságokat.

Az Azure AD Privileged Identity Management segít a szervezet:

- Tekintse meg, mely felhasználók legyenek társítva kiemelt szerepköröket (előzetes verzió), az Azure-erőforrások kezeléséhez, valamint, hogy mely felhasználók legyenek társítva rendszergazdai szerepkörök az Azure ad-ben
- Engedélyezze az igény, "csak az időben" rendszergazdai hozzáférést a Microsoft Online szolgáltatások, például Office 365 és az Intune-ban, és előfizetések, csoportok és az egyes erőforrások, például virtuális gépek az Azure erőforrások (előzetes verzió) 
-   Tekintse meg a rendszergazdai aktiválás, beleértve a milyen módosításokat rendszergazdák végzett Azure-erőforrások (előzetes verzió) előzményeit
- Értesítéskérés a módosításokat a rendszergazda-hozzárendelések
- Védett módú használatának az Azure AD rendszergazdai szerepkörök (előzetes verzió) aktiválása jóváhagyás szükséges 
- Tekintse át a rendszergazdai szerepkörök tagságát, és arra, hogy egy folyamatos tagsági indokát, a felhasználóknak

Az Azure ad-ben, az Azure AD Privileged Identity Management kezelheti a felhasználókhoz rendelt a beépített szerepkörök az Azure AD szervezeti, például globális rendszergazda. Az Azure az Azure AD Privileged Identity Management kezelheti a felhasználók és csoportok Azure RBAC-szerepkörök, beleértve a tulajdonos vagy közreműködő keresztül kiosztott.

## <a name="just-in-time-administrator-access"></a>Csak a rendszergazdai hozzáférés időpontja

Hagyományosan felhasználó sikerült hozzárendelése az Azure-portálon, a más Microsoft Online Services portál vagy az Azure AD-parancsmagok a Windows PowerShell rendszergazda szerepkörrel. Ennek eredményeképpen, ő lesz annak egy **állandó rendszergazdai**, mindig a hozzárendelt szerepkör aktív. Az Azure AD Privileged Identity Management jelenik meg az egy **jogosult felügyeleti**. Jogosult rendszergazdák kell lennie a felhasználók kiemelt igénylő férhetnek hozzá, nem napos, de minden nap. A szerepkör nem aktív, amíg a felhasználó kell elérnie, akkor az aktiválási folyamat befejezéséhez, és egy előre meghatározott időn egy aktív felügyeleti válik. Ezt a módszert használja a csökkentése vagy kiküszöbölése "állandó rendszergazdai hozzáféréssel" kiemelt szerepköröket egyre több szervezet vannak választva.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Privileged Identity Management engedélyezéséhez a címtáron

Az Azure AD Privileged Identity Management indíthatja a [Azure-portálon](https://portal.azure.com/).

> [!NOTE]
> Egy olyan szervezeti fiókkal egy globális rendszergazdának kell lennie (például @yourdomain.com), nem Microsoft-fiókkal (például @outlook.com), a címtár az Azure AD Privileged Identity Management engedélyezéséhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a címtára globális rendszergazdájaként.
2. Ha a szervezet több címtárral rendelkezik, akkor kattintson a felhasználónevére az Azure Portal jobb felső sarkában. Válassza ki a könyvtárat, ahol az Azure AD Privileged Identity Management fogja használni.
3. Válassza a **További szolgáltatások** lehetőséget, és a Szűrők szövegmezővel keresse meg az **Azure AD Privileged Identity Management** alkalmazást.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

Ha Ön az első, aki az Azure AD Privileged Identity Management használata a könyvtárban, és navigáljon a Azure Active directory szerepkörök, amelyek akkor keresse meg az Azure Active directory szerepkörök, egy [biztonsági varázsló](active-directory-privileged-identity-management-security-wizard.md) végigvezeti a kezdeti hozzárendelés felületet. Ezt követően automatikusan vált az első **biztonsági rendszergazda** és **kiemelt szerepkörű rendszergazda** a könyvtár.

Az Azure AD-szerepkörök csak egy felhasználót, aki a kiemelt szerepkör-Rendszergazda szerepkörhöz hozzárendelések más Azure AD PIM-rendszergazdák számára is kezelheti. Is [más felhasználók számára a PIM directory szerepkörök kezelése](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). A globális rendszergazdák, biztonsági rendszergazdák és biztonsági olvasók tekintheti meg az Azure AD szerepkör-hozzárendelések az Azure AD PIM.
Azure RBAC-szerepkörök csak egy előfizetési rendszergazda, erőforrás tulajdonosa, és egy erőforrás felhasználói hozzáférés adminisztrátora kezeléséhez hozzárendelések más Azure AD PIM-rendszergazdák számára.  Kiemelt szerepkör rendszergazdák, biztonsági rendszergazdák vagy biztonsági olvasók felhasználók alapértelmezés szerint nem rendelkeznek az Azure AD PIM Azure RBAC-szerepkörök-hozzárendelések megtekintése.

## <a name="privileged-identity-management-overview-entry-point"></a>Privileged Identity Management áttekintése (belépési pont)

Az Azure AD Privileged Identity Management támogatja az Azure Active directory szerepkörök és a szerepkörök felügyelete az Azure-erőforrások (előzetes verzió). Szerepkörök az Azure-erőforrások funkciót az Azure AD rendszergazdai szerepkörei eltérnek. Azure-erőforrás szerepkör, amellyel hozzárendelt erőforrás és az összes alárendelt erőforrásokhoz (más néven öröklési) erőforrás-hierarchiában részletes engedélyeket ad. [További információ az RBAC, erőforrás-hierarchia és öröklési további](role-based-access-control-configure.md). Mind az Azure Active directory szerepkörök PIM, és az Azure-erőforrások (előzetes verzió) felügyelhetők a megfelelő hivatkozásra a PIM áttekintése belépési pont bal oldali navigációs menü kezelése részében található elérésével.

A PIM kényelmes hozzáférést biztosít a szerepkörök aktiválása, és tekintse meg függő aktiválások/kérések, függőben lévő jóváhagyások (az Azure Active directory szerepkörök), és ellenőrzi, hogy a válasz a bal oldali navigációs menü feladatok részéből függőben.

Elérésekor a feladatok menü elemek bármelyikét a áttekintése belépési pontról, az eredményül kapott nézet tartalmazza az Azure Active directory szerepkörök, mind az Azure erőforrás-szerepkörök (előzetes verzió).

![Első lépések](./media/active-directory-privileged-identity-management-configure/quick-start.png)

A szerepkörök az Azure Active directory szerepkörök és az Azure erőforrás-szerepkörök (előzetes verzió) aktív és a megfelelő szerepkör-hozzárendelések listáját tartalmazza. [További információ a megfelelő szerepkör-hozzárendelések aktiválása](active-directory-privileged-identity-management-how-to-activate-role.md).

Szerepkörök aktiválása az Azure-erőforrások (előzetes verzió) bevezet egy új felület, amely lehetővé teszi az aktiválási a jövőbeni dátum/idő ütemezése, és válasszon egy adott aktiválási időtartamot a rendszergazdák által engedélyezett maximális belül szerepkör jogosult tagjai.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

Abban az esetben, ha egy ütemezett aktiválása már nincs szükség, a felhasználók megszüntetheti a függőben lévő kérelem függőben lévő kérelmek bal oldali navigációs menüjében útvonalon, majd kattintson a Mégse gombra a külön az adott kérelmet.

![függőben lévő kérelmek](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Privileged Identity Management felügyeleti Irányítópult

Az Azure AD Privileged Identity Manager biztosít egy felügyeleti irányítópultot, amely fontos információkat, például:

* Riasztások, amely felhívja a biztonság növelése érdekében lehetőségek
* Minden kiemelt szerepkörhöz rendelt felhasználók száma  
* A jogosult és állandó rendszergazdák száma
* Egy grafikonon kiemelt szerepkörű tartanak a könyvtárban
*   Csak időponthoz kötött időhöz kötött és állandó száma hozzárendelések Azure erőforrás-szerepkörök (előzetes verzió)
*   Új szerepkör-hozzárendelések az elmúlt 30 napban (Azure erőforrás-szerepkörök) rendelkező felhasználók és csoportok


![A PIM irányítópult – képernyőkép][2]

## <a name="privileged-role-management"></a>Kiemelt szerepkörű kezelése

Az Azure AD Privileged Identity Management kezelheti a rendszergazdák hozzáadásával vagy eltávolításával végleges vagy jogosult rendszergazdák számára az Azure Active directory szerepkörök szerepkörönként. PIM az Azure-erőforrások (előzetes verzió), a tulajdonosok, a felhasználói hozzáférés rendszergazdák és a globális rendszergazdák, amelyek lehetővé teszik a bérlőben előfizetések felügyeleti rendelhet felhasználók vagy csoportok jogosult Azure-erőforrás szerepkörök (csak időponthoz kötött access), vagy időhöz kötött () az aktiválás nem szükséges) hozzáférés kezdő és záró dátum/idő vagy állandó (Ha a szerepkör-beállítások engedélyezve).

![A PIM hozzáadása rendszergazdák – képernyőkép][3]

## <a name="configure-the-role-activation-settings"></a>A szerepkör-aktiválási beállításainak konfigurálása

Használja a [szerepkör beállításainak](active-directory-privileged-identity-management-how-to-change-default-settings.md) állíthatja be a megfelelő szerepkör aktiválási tulajdonságait az Azure Active directory szerepkörök többek között:

* A szerepkör-aktiválási időszak
* A szerepkör-aktiválási értesítés
* A felhasználó adatokat kell megadni. a szerepkör-aktiválási folyamat során
* Szolgáltatás-jegy vagy incidens száma
* [Jóváhagyási munkafolyamat követelmények – előzetes](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![Képernyőfelvétel a PIM - rendszergazdai aktiválás - beállítások][4]

Vegye figyelembe, hogy a kép gombjai **multi-factor Authentication** le vannak tiltva. Egyes, nagy kiemelt szerepköröket, a megnöveli védelemhez szükséges többtényezős Hitelesítést.

Az Azure erőforrás-szerepkörök (előzetes verzió) szerepkör-beállítások lehetővé teszik a rendszergazdák konfigurálhatják közvetlenül-In-Time és közvetlen hozzárendelési beállításokat, beleértve:

- A felhasználók vagy csoportok hozzárendelése szerepkörökhöz nélkül befejezési dátum/idő (tartós hozzárendelés) lehetőséget
- Az alapértelmezett időtartam (Ha nem állandó) hozzárendelés
- A maximális aktiválás időtartama (amikor egy jogosult szerepkör tagjának aktiválja)
- A felhasználó adja meg a szerepkör aktiválása során ezekre az információkra (csak időponthoz kötött hozzárendelések) vagy a hozzárendelési folyamat (közvetlen hozzárendelések)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Szerepkör aktiválása

A [szerepkört aktiváló](active-directory-privileged-identity-management-how-to-activate-role.md), egy erre jogosult rendszergazda kér egy időhöz kötött "aktiválás" a szerepkörhöz. Az aktiválás használatával kell adniuk a **a szerepkör aktiválásához** Azure AD Privileged Identity Management beállítása.

Egy rendszergazda, aki szerepkört aktiváló szeretne kell inicializálni az Azure AD Privileged Identity Management az Azure portálon.

Szerepkör aktiválása testre szabható. A PIM beállítások segítségével meghatározhatja az aktiválás és milyen adatokat a rendszergazdának kell megadni. a szerepkör aktiválásához.

![PIM rendszergazda kérelem szerepkör aktiválása – képernyőkép][5]

## <a name="review-role-activity"></a>Felülvizsgálati szerepkör tevékenység

Két módon lehet kísérje figyelemmel, hogy az alkalmazottak és a rendszergazdák kiemelt szerepköröket használ. Az első lehetőség által használt [Directory szerepkörök naplózási előzmények](active-directory-privileged-identity-management-how-to-use-audit-log.md). A naplózási előzmények változások követése bejelentkezik kiemelt szerepkör-hozzárendelések, szerepkör-aktiválási előzményeit, és és az Azure erőforrás-szerepkörök (előzetes verzió) beállítások módosításait. 

![A PIM aktiválási előzmények – képernyőkép][6]

A második beállítás lesz rendszeres beállítása [értékelést hozzáférési](active-directory-privileged-identity-management-how-to-start-security-review.md). Ezek hozzáférés értékelést végzi, és hozzárendelt felülvizsgáló (például egy csoport manager) vagy az alkalmazottak saját magukat tudja ellenőrizni. Ez az a legjobb módszer a figyelheti, akik továbbra is hozzáférést igényel, és ki nem tesz.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Az Azure AD PIM előfizetés lejárati:

A bérlő rendelkeznie kell egy Azure AD prémium P2 (vagy az EMS E5) próbaverziós vagy fizetős előfizetésre a bérlőben Azure AD PIM használata előtt.  Ezenkívül licencet hozzá kell rendelni a bérlői rendszergazdákat.  Pontosabban licencek hozzá kell rendelni az Azure AD-szerepkörök rendszergazdák kezeli az Azure AD PIM keresztül, az Azure RBAC-szerepkörök rendszergazdák felügyelt – Azure AD PIM és hozzáférési értékelést a nem rendszergazda felhasználónak.
Ha a szervezet nem újítja meg Azure AD Premium P2, vagy a próbaidőszak lejárata, az Azure AD PIM funkciók már nem lesz elérhető az Ön bérlőjében, jogosult szerepkör-hozzárendelések törlődni fog, és felhasználók nem fognak tudni szerepkörök aktiválása. A további a [Azure AD PIM előfizetés követelményeinek](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
