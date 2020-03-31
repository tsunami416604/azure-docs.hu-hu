---
title: Alkalmazásfelügyeleti felügyeleti dauerok delegálása – Azure AD | Microsoft dokumentumok
description: Engedélyek megadása az Azure Active Directory ban az alkalmazáshozzáférés-kezeléshez
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 251bc1c2277f9e43543f95c49d0b730a5a41c3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253038"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Alkalmazásregisztrációs engedélyek delegálása az Azure Active Directoryban

Ez a cikk ismerteti, hogyan használhatja az egyéni szerepkörök által az Azure Active Directoryban (Azure AD) megadott engedélyeket az alkalmazáskezelési igények kielégítésére. Az Azure AD-ben az alábbi módokon delegálhatja az alkalmazások létrehozásához és kezeléséhez szükséges engedélyeket:

- [Annak korlátozása, hogy ki hozhat létre alkalmazásokat,](#restrict-who-can-create-applications) és ki kezelheti az általuk létrehozott alkalmazásokat. Alapértelmezés szerint az Azure AD-ben minden felhasználó regisztrálhatja az alkalmazásregisztrációkat, és kezelheti az általuk létrehozott alkalmazások minden aspektusát. Ez korlátozható úgy, hogy csak az engedélyt engedélyező személyeket engedélyezze.
- [Egy vagy több tulajdonos hozzárendelése egy alkalmazáshoz.](#assign-application-owners) Ez egy egyszerű módja annak, hogy valaki képes kezelni az Azure AD-konfiguráció minden szempontból egy adott alkalmazáshoz.
- [Egy beépített felügyeleti szerepkör hozzárendelése,](#assign-built-in-application-admin-roles) amely hozzáférést biztosít a konfiguráció kezeléséhez az Azure AD-ben az összes alkalmazás hoz. Ez az ajánlott módja annak, hogy informatikai szakértők számára hozzáférést biztosítson az általános alkalmazáskonfigurációs engedélyek kezeléséhez anélkül, hogy hozzáférést biztosítana az Azure AD más, az alkalmazáskonfigurációhoz nem kapcsolódó részeinek kezeléséhez.
- [Egyéni szerepkör létrehozása](#create-and-assign-a-custom-role-preview) nagyon specifikus engedélyek definiálása és hozzárendelése valakihez, vagy egyetlen alkalmazás hatóköréhez korlátozott tulajdonosként, vagy a címtárhatókörben (az összes alkalmazás) korlátozott rendszergazdaként.

Fontos, hogy fontolja meg a hozzáférés megadását a fenti módszerek egyikének használatával két okból. Először is a felügyeleti feladatok elvégzésének lehetősége csökkenti a globális rendszergazdai terhelést. Másodszor, a korlátozott engedélyek használata javítja a biztonsági pozíciót, és csökkenti a jogosulatlan hozzáférés lehetőségét. A delegálási problémákat és az általános irányelveket az [Azure Active Directory delegálási felügyelete](roles-concept-delegation.md)tárgyalja.

## <a name="restrict-who-can-create-applications"></a>Annak korlátozása, hogy ki hozhat létre alkalmazásokat

Alapértelmezés szerint az Azure AD-ben minden felhasználó regisztrálhatja az alkalmazásregisztrációkat, és kezelheti az általuk létrehozott alkalmazások minden aspektusát. Mindenkinek lehetősége van arra is, hogy hozzájáruljon ahhoz, hogy az alkalmazások hozzáférjenek a vállalati adatokhoz a nevükben. Dönthet úgy, hogy ezeket az engedélyeket szelektíven adja meg, ha a globális kapcsolókat "Nem"-re állítja, és hozzáadja a kiválasztott felhasználókat az Alkalmazásfejlesztői szerepkörhöz.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Alkalmazásregisztrációk létrehozásának vagy alkalmazásokhoz való hozzájárulásának letiltása

1. Jelentkezzen be az Azure AD-szervezetbe egy olyan fiókkal, amely jogosult az Azure AD-szervezet globális rendszergazdai szerepkörére.
1. Állítsa be az alábbiak egyikét vagy mindkettőt:

    - A [szervezet Felhasználói beállítások lapján](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)állítsa be, hogy a **Felhasználók regisztrálhatnak alkalmazásokat,** állítsa a Nem beállítást. Ez letiltja a felhasználók alapértelmezett alkalmazásregisztrációk létrehozására való képességét.
    - A [vállalati alkalmazások felhasználói beállításainál](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)állítsa be, hogy a Felhasználók beleegyezhetnek abba, hogy a **vállalati adatokhoz a nevükben hozzáférő alkalmazások** beállítása Nem. Ez letiltja azt az alapértelmezett lehetőséget, hogy a felhasználók hozzájáruljanak ahhoz, hogy a vállalati adatokhoz a nevükben hozzáférő alkalmazások hozzáférjenek.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Egyéni engedélyek megadása az alkalmazások létrehozásához és beleegyezéséhez, ha az alapértelmezett képesség le van tiltva

Rendelje hozzá az Alkalmazás fejlesztői szerepkörét, hogy lehetővé tegye az alkalmazásregisztrációk létrehozását, ha a **Felhasználók regisztrálhatnak alkalmazásokat,** és az alkalmazás beállítása Nem. Ez a szerepkör is engedélyt ad a hozzájárulásra a saját nevében, ha a **Felhasználók beleegyezhetnek abba, hogy a vállalati adatokhoz a nevükben hozzáférő alkalmazások** beállítása Nem. Rendszerbeli viselkedésként, amikor egy felhasználó új alkalmazásregisztrációt hoz létre, a rendszer automatikusan hozzáadja őket az első tulajdonosként. A tulajdonosi engedélyek lehetővé teszik a felhasználó számára, hogy a saját alkalmazásregisztrációjának vagy vállalati alkalmazásának minden aspektusát kezelje.

## <a name="assign-application-owners"></a>Alkalmazástulajdonosok hozzárendelése

A tulajdonosok hozzárendelése egy egyszerű módja annak, hogy egy adott alkalmazásregisztrációvagy vállalati alkalmazás esetén az Azure AD-konfiguráció minden aspektusát kezelje. Rendszerbeli viselkedésként, amikor egy felhasználó új alkalmazásregisztrációt hoz létre, a rendszer automatikusan hozzáadja őket első tulajdonosként. A tulajdonosi engedélyek lehetővé teszik a felhasználó számára, hogy a saját alkalmazásregisztrációjának vagy vállalati alkalmazásának minden aspektusát kezelje. Az eredeti tulajdonos eltávolítható, és további tulajdonosok is hozzáadhatók.

### <a name="enterprise-application-owners"></a>Vállalati alkalmazások tulajdonosai

Tulajdonosként a felhasználó kezelheti a vállalati alkalmazás szervezetspecifikus konfigurációját, például az egyszeri bejelentkezési konfigurációt, a kiépítést és a felhasználói hozzárendeléseket. A tulajdonosok ezen kívül eltávolíthatnak vagy hozzáadhatnak más tulajdonosokat. A globális rendszergazdákkal ellentétben a tulajdonosok csak a saját vállalati alkalmazásaikat kezelhetik.

Bizonyos esetekben az alkalmazáskatalógusból létrehozott vállalati alkalmazások vállalati alkalmazást is tartalmaznak egy vállalati alkalmazás és egy alkalmazásregisztráció. Ha ez igaz, ha tulajdonos hozzáadása a vállalati alkalmazáshoz automatikusan hozzáadja a tulajdonost a megfelelő alkalmazásregisztrációhoz tulajdonosként.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Tulajdonos hozzárendelése vállalati alkalmazáshoz

1. Jelentkezzen be [az Azure AD-szervezetbe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely jogosult a szervezet alkalmazás- vagy felhőalkalmazás-rendszergazdájára.
1. A szervezet [alkalmazásregisztrációk lapján](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) válasszon ki egy alkalmazást az alkalmazás áttekintése lap megnyitásához.
1. Válassza a **Tulajdonosok** lehetőséget az alkalmazás tulajdonosainak listájának megtekintéséhez.
1. Válassza a **Hozzáadás** lehetőséget, ha ki szeretne választani egy vagy több tulajdonost az alkalmazásba.

> [!IMPORTANT]
> A felhasználók és a szolgáltatástagok az alkalmazásregisztrációk tulajdonosai lehetnek. Csak a felhasználók lehetnek vállalati alkalmazások tulajdonosai. A csoportok nem rendelhetők hozzá egyik tulajdonosához sem.
>
> A tulajdonosok hitelesítő adatokat adhatnak hozzá egy alkalmazáshoz, és ezekkel a hitelesítő adatokkal megszemélyesíthetik az alkalmazás identitását. Előfordulhat, hogy az alkalmazás több engedéllyel rendelkezik, mint a tulajdonos, és így a tulajdonos felhasználói vagy egyszerű szolgáltatásként való hozzáférésével kapcsolatos jogosultság illetéktelen megszerzését jelentené. Az alkalmazás tulajdonosa az alkalmazás engedélyeitől függően felhasználókat vagy más objektumokat hozhat létre vagy frissíthet az alkalmazás megszemélyesítése közben.

## <a name="assign-built-in-application-admin-roles"></a>Beépített alkalmazásfelügyeleti szerepkörök hozzárendelése

Az Azure AD beépített rendszergazdai szerepkörökkészletével rendelkezik, amelyek hozzáférést biztosítanak az Azure AD-ben az összes alkalmazás konfigurációjának kezeléséhez. Ezek a szerepkörök az ajánlott módja annak, hogy informatikai szakértők számára hozzáférést biztosítson az általános alkalmazáskonfigurációs engedélyek kezeléséhez anélkül, hogy hozzáférést biztosítana az Azure AD más, az alkalmazáskonfigurációhoz nem kapcsolódó részeinek kezeléséhez.

- Alkalmazásadminisztrátor: Az ebben a szerepkörben lévő felhasználók létrehozhatják és kezelhetik a vállalati alkalmazások, az alkalmazásregisztrációk és az alkalmazásproxy-beállítások minden aspektusát. Ez a szerepkör is lehetővé teszi, hogy hozzájáruljon a delegált engedélyek, és alkalmazás engedélyeket, kivéve a Microsoft Graph. Az ehhez a szerepkörhöz rendelt felhasználók nem kerülnek tulajdonosként új alkalmazásregisztrációk vagy vállalati alkalmazások létrehozásakor.
- Felhőalkalmazás-rendszergazda: A szerepkörben lévő felhasználók ugyanolyan engedélyekkel rendelkeznek, mint az Alkalmazás-rendszergazda szerepkör, kivéve az alkalmazásproxy kezelését. Az ehhez a szerepkörhöz rendelt felhasználók nem kerülnek tulajdonosként új alkalmazásregisztrációk vagy vállalati alkalmazások létrehozásakor.

További információt és a szerepkörök leírásának megtekintéséhez olvassa el az Elérhető szerepkörök című [témakört.](directory-assign-admin-roles.md#available-roles)

Kövesse a [Szerepkörök hozzárendelése](../fundamentals/active-directory-users-assign-role-azure-portal.md) a felhasználókaz Azure Active Directory útmutató útmutató az alkalmazásrendszergazda i vagy a felhőbeli alkalmazás-rendszergazdai szerepkörök hozzárendelése utasításokat.

> [!IMPORTANT]
> Az alkalmazás- és felhőalkalmazás-rendszergazdák hitelesítő adatokat adhatnak hozzá az alkalmazásokhoz, és ezekkel a hitelesítő adatokkal megszemélyesíthetik az alkalmazás identitását. Előfordulhat, hogy az alkalmazás olyan engedélyekkel rendelkezik, amelyek a rendszergazdai szerepkör engedélyeit fölé ni. A szerepkörben lévő rendszergazda az alkalmazás engedélyeitől függően felhasználókat vagy más objektumokat hozhat létre vagy frissíthet az alkalmazás megszemélyesítése közben.
> Egyik szerepkör sem teszi lehetővé a feltételes hozzáférési beállítások kezelését.

## <a name="create-and-assign-a-custom-role-preview"></a>Egyéni szerepkör létrehozása és hozzárendelése (előzetes verzió)

Az egyéni szerepkörök létrehozása és az egyéni szerepkörök hozzárendelése külön lépés:

- [Hozzon létre egyéni *szerepkör-definíciót,* ](roles-create-custom.md) és [adjon hozzá engedélyeket egy készletlistából.](roles-custom-available-permissions.md) Ezek ugyanazok az engedélyek, amelyeket a beépített szerepkörökben használnak.
- [Hozzon létre *szerepkör-hozzárendelést* ](roles-assign-powershell.md) az egyéni szerepkör hozzárendeléséhez.

Ez a szétválasztás lehetővé teszi, hogy egyetlen szerepkör-definíciót hozzon létre, majd többször hozzárendelje különböző *hatókörökhöz.* Egy egyéni szerepkör szervezeti szintű hatókörhöz rendelhető, vagy a hatókörhöz rendelhető, ha egyetlen Azure AD-objektum. Egy objektumhatókör például egyetlen alkalmazásregisztráció. Különböző hatókörök használatával ugyanaz a szerepkör-definíció rendelhető Sally-hez a szervezet összes alkalmazásregisztrációja során, majd a Naveen-hez csak a Contoso Költségjelentések alkalmazás regisztrációja esetén.

Tippek egyéni szerepkörök létrehozásához és használatához az alkalmazáskezelés delegálásakor:
- Az egyéni szerepkörök csak az Azure AD-portál legújabb alkalmazásregisztrációs paneljein biztosítanak hozzáférést. Nem biztosítanak hozzáférést az örökölt alkalmazásregisztrációs panelek.
- Egyéni szerepkörök nem adnak hozzáférést az Azure AD-portálhoz, ha a "Hozzáférés korlátozása az Azure AD felügyeleti portálhoz" felhasználói beállítás van beállítva Igen.
- Az alkalmazásregisztrációk, amelyekhez a felhasználó hozzáférhet a szerepkör-hozzárendelések használatával, csak az Alkalmazás regisztrációs lapjának "Minden alkalmazás" lapján jelennek meg. Nem jelennek meg a "Saját alkalmazások" lapon.

Az egyéni szerepkörök alapjairól az [egyéni szerepkörök áttekintésében,](roles-custom-overview.md)valamint az [egyéni szerepkör létrehozásának](roles-create-custom.md) módjában és a [szerepkör hozzárendelésében](roles-assign-powershell.md)olvashat bővebben.

## <a name="next-steps"></a>További lépések

- [Alkalmazásregisztrációs altípusok és engedélyek](roles-custom-available-permissions.md)
- [Azure AD rendszergazdai szerepkör-hivatkozás](directory-assign-admin-roles.md)
