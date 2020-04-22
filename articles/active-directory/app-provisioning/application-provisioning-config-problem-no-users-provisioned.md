---
title: A felhasználók nincsenek kiépítve az alkalmazásomban
description: Az azure AD-vel való felhasználói kiépítéshez konfigurált Azure AD-alkalmazásban megjelenő felhasználók nem látod a problémákat?
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac6d4f24d3b6c21828ccb11785005df736c6b070
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680337"
---
# <a name="no-users-are-being-provisioned"></a>Egyetlen felhasználó létrehozása sem történt meg 
>[!NOTE]
>2020.16.04-től megváltoztattuk az alapértelmezett hozzáférési szerepkörhöz rendelt felhasználók viselkedését. Kérjük, olvassa el az alábbi szakaszban a részleteket. 
>
Miután az automatikus kiépítés konfigurálva van egy alkalmazáshoz (beleértve annak ellenőrzését, hogy az Azure AD-nek az alkalmazáshoz való csatlakozáshoz megadott alkalmazáshitelesítő adatok érvényesek-e), majd a felhasználók és/vagy csoportok ki vannak építve az alkalmazásba. A kiépítést a következő dolgok határozzák meg:

-   Mely felhasználók és csoportok lettek **hozzárendelve** az alkalmazáshoz. Vegye figyelembe, hogy a beágyazott csoportok vagy az Office 365-csoportok kiépítése nem támogatott. A hozzárendelésről további információt a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz az Azure Active Directoryban című témakörben talál.](../manage-apps/assign-user-or-group-access-portal.md)
-   Függetlenül attól, hogy **az attribútum-leképezések** engedélyezve vannak-e, és úgy vannak konfigurálva, hogy szinkronizálják az érvényes attribútumokat az Azure AD-ből az alkalmazásba. Az attribútumleképezésekről az Azure Active Directoryban az [SaaS-alkalmazások felhasználói kiépítési attribútum-leképezései testreszabása](customize-application-attributes.md)című témakörben talál további információt.
-   Attól függetlenül, hogy van-e olyan **hatókörszűrő,** amely a felhasználókat adott attribútumértékek alapján szűri. A szűrők hatókörének kezeléséről további információt az [Attribútumalapú alkalmazáskiépítés hatókörszűrőkkel című témakörben talál.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
  
  
Ha azt észleli, hogy a felhasználók nincsenek kiépítve, tekintse meg a [kiépítési naplók (előzetes verzió)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) az Azure AD-ben. Adott felhasználó naplóbejegyzéseinek keresése.

Az Azure-portálon a létesítési naplók eléréséhez válassza az **Azure Active Directory** &gt; **Vállalati alkalmazások** &gt; **kiépítése naplók (előzetes verzió)** a **tevékenység** szakaszban. A kiépítési adatok ban a felhasználó neve vagy az azonosító alapján kereshet a forrásrendszerben vagy a célrendszerben. További információt a [Naplók kiépítése (előzetes verzió) című témakörben talál.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

A létesítési naplók rögzítik a létesítési szolgáltatás által végrehajtott összes műveletet, beleértve az Azure AD lekérdezését a hozzárendelt felhasználók számára, amelyek a kiépítés hatókörében vannak, lekérdezi a célalkalmazást a felhasználók létezésére, összehasonlítva a felhasználói objektumokat a rendszer között. Ezután adja hozzá, frissítse vagy tiltsa le a felhasználói fiókot a célrendszerben az összehasonlítás alapján.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Általános problémás területek a kiépítéssel, hogy mérlegeljék
Az alábbi lista azokat az általános problémás területeket tartalmazza, amelyekbe beleáshatja magát, ha van ötlete, hogy hol kezdje.

- [Úgy tűnik, hogy a kiépítési szolgáltatás nem indul el](#provisioning-service-does-not-appear-to-start)
- [A kiépítési naplók szerint a rendszer kihagyja a felhasználókat, és nem van kiépítve, még akkor is, ha hozzá vannak rendelve](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Úgy tűnik, hogy a kiépítési szolgáltatás nem indul el
Ha beállítja a **kiépítési** **állapotot** az **Azure Active &gt; Directory Vállalati alkalmazások &gt; \[alkalmazásnév-kiépítési\] &gt;** szakaszában. Azonban a későbbi újratöltés után az oldalon más állapotrészletek nem jelennek meg, valószínű, hogy a szolgáltatás fut, de még nem fejezte be a kezdeti ciklust. Ellenőrizze a fent leírt **kiépítési naplók (előzetes verzió)** annak megállapításához, hogy milyen műveleteket hajt végre a szolgáltatás, és ha vannak hibák.

>[!NOTE]
>Egy kezdeti ciklus 20 perctől több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítési hatókörben lévő felhasználók számától függően. A kezdeti ciklus utáni későbbi szinkronizálások gyorsabbak, mivel a létesítési szolgáltatás olyan vízjeleket tárol, amelyek a kezdeti ciklus után mindkét rendszer állapotát jelölik. A kezdeti ciklus javítja a későbbi szinkronizálások teljesítményét.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>A kiépítési naplók szerint a rendszer kihagyja a felhasználókat, és nem van kiépítve, még akkor sem, ha hozzá vannak rendelve

Amikor egy felhasználó megjelenik a "kihagyott" a létesítési naplók, fontos, hogy tekintse át a **lépések** lap a napló okának meghatározásához. Az alábbiakban felsoroljuk a gyakori okokat és állásfoglalásokat:

- **Olyan hatókörszűrő van beállítva,** amely egy **attribútumérték alapján szűri ki a felhasználót.** A szűrők hatókörével kapcsolatos további információkért lásd: [hatókörszűrők](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **A felhasználó "nem jogosult ténylegesen".** Ha ez a konkrét hibaüzenet jelenik meg, annak az az oka, hogy probléma van az Azure AD-ben tárolt felhasználói hozzárendelési rekorddal. A probléma megoldásához rendelje vissza a felhasználó (vagy csoport) hozzárendelését az alkalmazásból, majd rendelje hozzá újra. A hozzárendelésről a [Felhasználói vagy csoporthozzáférés hozzárendelése című témakörben](../manage-apps/assign-user-or-group-access-portal.md)talál további információt.
- **Egy szükséges attribútum hiányzik vagy nincs feltöltve egy felhasználóhoz.** A kiépítés beállításakor fontos, hogy tekintse át és konfigurálja az attribútum-leképezések és munkafolyamatok, amelyek meghatározzák, hogy mely felhasználói (vagy csoport) tulajdonságok áramlását az Azure AD az alkalmazásba. Ez a konfiguráció magában foglalja a "megfelelő tulajdonság" beállítását, amely a két rendszer közötti felhasználók/csoportok egyedi azonosítására és egyezésére szolgál. Erről a fontos folyamatról további információt az Azure Active Directoryban található [SaaS-alkalmazások felhasználói kiépítési attribútum-leképezései testreszabása című témakörben talál.](customize-application-attributes.md)
- **Attribútumleképezések csoportokhoz:** A csoport név és a csoport adatainak kiépítése a tagokon kívül, ha bizonyos alkalmazások esetében támogatottak. Ezt a funkciót a **Kiépítés** lapon látható csoportobjektumok **leképezésének** engedélyezésével vagy letiltásával engedélyezheti vagy tilthatja le. Ha a létesítési csoportok engedélyezve van, győződjön meg róla, hogy tekintse át az attribútum leképezések annak érdekében, hogy a megfelelő mező t használja a "megfelelő azonosító". A megfelelő azonosító lehet a megjelenítendő név vagy az e-mail alias. A csoport és annak tagjai nincsenek kiépítve, ha az egyező tulajdonság üres, vagy nem lakott egy csoport az Azure AD-ben.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Az alapértelmezett hozzáférési szerepkörhöz rendelt felhasználók kiépítése
A katalógusból származó alkalmazás alapértelmezett szerepkörét "alapértelmezett hozzáférési" szerepkörnek nevezzük. Korábban a szerepkörhöz rendelt felhasználók nincsenek kiépítve, és a [kiépítési naplókban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) kihagyottként vannak megjelölve, mivel "nem jogosultak ténylegesen". 

**04/16/2020 után létrehozott konfigurációk létesítési viselkedése:** Az alapértelmezett hozzáférési szerepkörhöz rendelt felhasználók kiértékelése megegyezik az összes többi szerepkörrel. Az alapértelmezett hozzáféréssel rendelkező felhasználó nem lesz kihagyva "nem jogosult" néven. 

**04/16/2020 előtt létrehozott konfigurációk létesítési viselkedése:** A következő 3 hónapban a viselkedés úgy folytatódik, ahogy ma. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók kimaradnak, mivel azok nem jogosultak ténylegesen. 2020 júliusa után a viselkedés egységes lesz minden alkalmazás esetében. Nem fogjuk kihagyni az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók kiépítése miatt , hogy "nem hatékonyan jogosult." Ezt a módosítást a Microsoft fogja elvégezni, és nincs szükség vevői beavatkozásra. Ha biztosítani szeretné, hogy ezek a felhasználók továbbra is kimaradnak, még a módosítás után is alkalmazza a megfelelő hatókörszűrőket, vagy vegye figyelembe a felhasználót az alkalmazásból, hogy megbizonyosodjon arról, hogy hatókörön kívül esnek.  

A változásokkal kapcsolatos kérdéseivel forduljonprovisioningfeedback@microsoft.com
## <a name="next-steps"></a>További lépések

[Azure AD Connect szinkronizálás: A deklaratív kiépítés ismertetése](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
