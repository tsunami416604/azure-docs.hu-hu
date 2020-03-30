---
title: Probléma a felhasználók Azure AD Gallery-alkalmazásba való kiépítésének konfigurálásakor
description: Az Azure AD alkalmazásgalériában már felsorolt alkalmazásokba történő felhasználói kiépítés konfigurálásakor felmerülő gyakori problémák elhárítása
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
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3955b96e4a7edfc79a229d927523bdd4473409d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522764"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Probléma a felhasználók Azure AD Gallery-alkalmazásba való kiépítésének konfigurálásakor

Az alkalmazás [automatikus felhasználói kiépítésének](user-provisioning.md) konfigurálásához (ahol támogatott) szükség van arra, hogy az alkalmazás automatikus kiépítésre való előkészítéséhez meghatározott utasításokat kell követni. Ezután az Azure Portal segítségével konfigurálhatja a létesítési szolgáltatás felhasználói fiókok szinkronizálása az alkalmazással.

Mindig először keresse meg a telepítő oktatóanyag kifejezetten az alkalmazás kiépítési beállításához. Ezután kövesse ezeket a lépéseket az alkalmazás és az Azure AD konfigurálásához a létesítési kapcsolat létrehozásához. Az alkalmazásoktatóanyagok listája az [SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listájában](../saas-apps/tutorial-list.md)található.

## <a name="how-to-see-if-provisioning-is-working"></a>Hogyan lehet tudni, hogy működik-e a kiépítés? 

A szolgáltatás konfigurálása után a szolgáltatás működésébe betekintő legtöbb információ két helyről nyerhető ki:

-   **Kiépítési naplók (előzetes verzió)** – A [létesítési naplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) rögzítik a létesítési szolgáltatás által végrehajtott összes műveletet, beleértve az Azure AD lekérdezését a hozzárendelt felhasználók számára, amelyek a kiépítés hatókörében vannak. Lekérdezi a célalkalmazást a felhasználók létezésére, összehasonlítva a felhasználói objektumokat a rendszer között. Ezután adja hozzá, frissítse vagy tiltsa le a felhasználói fiókot a célrendszerben az összehasonlítás alapján. Az Azure-portálon a létesítési naplók eléréséhez válassza az **Azure Active Directory** &gt; **Vállalati alkalmazások** &gt; **kiépítése naplók (előzetes verzió)** a **tevékenység** szakaszban.

-   **Jelenlegi állapot –** Egy adott alkalmazás utolsó kiépítési futtatásának összegzése az **Azure &gt; Active &gt; \[Directory\] &gt;Vállalati alkalmazások alkalmazásnév-kiépítés** szakaszában, a képernyő alján, a szolgáltatásbeállítások alatt látható. Az Aktuális állapot szakasz azt mutatja, hogy egy kiépítési ciklus megkezdte-e a felhasználói fiókok kiépítését. Megtekintheti a ciklus előrehaladását, megtekintheti, hogy hány felhasználó és csoport lett kiépítve, és megtekintheti, hogy hány szerepkör jön létre. Ha bármilyen hiba merül fel, a részletek a [Kiépítési naplókban (.. /reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Általános problémás területek a kiépítéssel,

Az alábbi lista azokat az általános problémás területeket tartalmazza, amelyekbe beleáshatja magát, ha van ötlete, hogy hol kezdje.

* [Úgy tűnik, hogy a kiépítési szolgáltatás nem indul el](#provisioning-service-does-not-appear-to-start)
* Nem lehet menteni a konfigurációt, mert az alkalmazás hitelesítő adatai nem működnek
* [A kiépítési naplók szerint a felhasználók "kimaradnak", és nem vannak kiépítve, még akkor is, ha hozzá vannak rendelve](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Úgy tűnik, hogy a kiépítési szolgáltatás nem indul el

Ha beállítja a **kiépítési** **állapotot** az **Azure Active &gt; Directory Vállalati alkalmazások &gt; \[alkalmazásnév-kiépítési\] &gt;** szakaszában. A későbbi újratöltésután azonban az adott oldalon más állapotrészletek nem jelennek meg. Valószínű, hogy a szolgáltatás fut, de még nem fejezte be a kezdeti ciklust. Ellenőrizze a fent leírt **kiépítési naplókat,** hogy megtudja, milyen műveleteket hajt végre a szolgáltatás, és ha vannak hibák.

>[!NOTE]
>Egy kezdeti ciklus 20 perctől több óráig is eltarthat, az Azure AD-címtár méretétől és a kiépítési hatókörben lévő felhasználók számától függően. A kezdeti ciklus utáni későbbi szinkronizálások gyorsabbak lesznek, mivel a létesítési szolgáltatás olyan vízjeleket tárol, amelyek a kezdeti ciklus után mindkét rendszer állapotát jelölik, javítva a későbbi szinkronizálások teljesítményét.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Nem lehet menteni a konfigurációt, mert az alkalmazás hitelesítő adatai nem működnek

Annak érdekében, hogy a kiépítés működjön, az Azure AD érvényes hitelesítő adatokat igényel, amelyek lehetővé teszik, hogy csatlakozzon az alkalmazás által biztosított felhasználói felügyeleti API-hoz. Ha ezek a hitelesítő adatok nem működnek, vagy nem tudja, mik azok, tekintse át az alkalmazás beállításával korábban ismertetett oktatóanyagot.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>A kiépítési naplók szerint a rendszer kihagyja a felhasználókat, és nem van kiépítve, még akkor sem, ha hozzá vannak rendelve

Amikor egy felhasználó megjelenik a "kihagyott" a létesítési naplók, nagyon fontos, hogy olvassa el a kiterjesztett részleteket a naplóüzenetben, hogy meghatározza az okát. Az alábbiakban felsoroljuk a gyakori okokat és állásfoglalásokat:

- **Olyan hatókörszűrő van beállítva,** amely egy **attribútumérték alapján szűri ki a felhasználót.** További információ: [Attribútumalapú alkalmazáskiépítés hatókörszűrőkkel.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

- **A felhasználó "nem jogosult ténylegesen".** Ha ez a konkrét hibaüzenet jelenik meg, annak az az oka, hogy probléma van az Azure AD-ben tárolt felhasználói hozzárendelési rekorddal. A probléma megoldásához rendelje hozzá a felhasználót (vagy csoportot) az alkalmazásból, majd rendelje hozzá újra újra. További információt a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz című témakörben talál.](../manage-apps/assign-user-or-group-access-portal.md)

- **Egy szükséges attribútum hiányzik vagy nincs feltöltve egy felhasználóhoz.** A kiépítés beállításakor fontos, hogy tekintse át és konfigurálja az attribútum-leképezések és munkafolyamatok, amelyek meghatározzák, hogy mely felhasználói (vagy csoport) tulajdonságok áramlását az Azure AD az alkalmazásba. Ez magában foglalja a "megfelelő tulajdonság" beállítását, amely a felhasználók/csoportok egyedi azonosítására és egyeztetésére szolgál a két rendszer között. Erről a fontos folyamatról további információt a [Felhasználókiépítési attribútum-hozzárendelések testreszabása című témakörben talál.](../app-provisioning/customize-application-attributes.md)

  * **Attribútumleképezések csoportokhoz:** A csoport név és a csoport adatainak kiépítése a tagokon kívül, ha bizonyos alkalmazások esetében támogatottak. Ezt a funkciót a **Kiépítés** lapon látható csoportobjektumok **leképezésének** engedélyezésével vagy letiltásával engedélyezheti vagy tilthatja le. Ha a létesítési csoportok engedélyezve van, győződjön meg róla, hogy tekintse át az attribútum leképezések annak érdekében, hogy a megfelelő mező t használja a "megfelelő azonosító". Ez lehet a megjelenítendő név vagy e-mail alias), mivel a csoport és annak tagjai nem létesíthetők, ha a megfelelő tulajdonság üres, vagy nem lakott egy csoport az Azure AD-ben.

## <a name="next-steps"></a>További lépések
[A felhasználói kiépítés és a SaaS-alkalmazásokba való megszüntetés automatizálása az Azure Active Directoryval](user-provisioning.md)
