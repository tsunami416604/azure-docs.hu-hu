---
title: Automatikus SaaS-alkalmazásfelhasználói kiépítés az Azure AD-ben | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja az Azure AD-t a felhasználói fiókok automatikus kiépítésére, deprovision- és folyamatos frissítésére több külső SaaS-alkalmazásban.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454533"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Az Azure Active Directoryval automatizálhatja a felhasználók kiépítését és az alkalmazásokhoz való mentesítését

Az Azure Active Directoryban (Azure AD) az **alkalmazáskiépítés** kifejezés a felhasználói identitások és szerepkörök automatikus létrehozására utal a felhőben ([SaaS)](https://azure.microsoft.com/overview/what-is-saas/)alkalmazások, amelyekhez a felhasználóknak hozzáférésre van szükségük. A felhasználói identitások létrehozása mellett az automatikus kiépítés magában foglalja a felhasználói identitások karbantartását és eltávolítását az állapot vagy a szerepkörök változásakor. Gyakori forgatókönyvek közé tartozik egy Azure AD-felhasználó kiépítése olyan alkalmazásokba, mint a [Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) [a Salesforce,](../saas-apps/salesforce-provisioning-tutorial.md) [a ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)és egyebek.

![Kiépítés áttekintő diagramja](./media/user-provisioning/provisioning-overview.png)

Ez a funkció lehetővé teszi:

- **Kiépítése automatizálása:** Automatikusan hozzon létre új fiókokat a megfelelő rendszerekben az új emberek számára, amikor csatlakoznak a csapathoz vagy a szervezethez.
- **A megszüntetés automatizálása:** Automatikusan inaktiválja a fiókokat a megfelelő rendszerekben, amikor az emberek elhagyják a csapatot vagy a szervezetet.
- **Adatok szinkronizálása rendszerek között:** Győződjön meg arról, hogy az alkalmazásokban és rendszerekben lévő identitások a címtárban vagy a humánerőforrás-rendszerben bekövetkezett változások alapján naprakészek.
- **Kiépítési csoportok:** Csoportok kiépítése az őket támogató alkalmazásokhoz.
- **Hozzáférés szabályozása:** Figyelheti és naplózhatja, hogy ki van-e kiépítve az alkalmazásokba.
- **Zökkenőmentes üzembe helyezés barna mezőben:** Egyezhet a rendszerek közötti meglévő identitásokkal, és lehetővé teszi az egyszerű integrációt, még akkor is, ha a felhasználók már léteznek a célrendszerben.
- **Gazdag testreszabáshasználata:** Használja ki a testre szabható attribútumleképezések előnyeit, amelyek meghatározzák, hogy milyen felhasználói adatok nak kell a forrásrendszerből a célrendszerbe áramlaniuk.
- **Riasztások beszereznie a kritikus eseményekről:** A kiépítési szolgáltatás riasztásokat biztosít a kritikus eseményekhez, és lehetővé teszi a Log Analytics-integrációt, ahol egyéni riasztásokat határozhat meg az üzleti igények kielégítéséhez.

## <a name="benefits-of-automatic-provisioning"></a>Az automatikus beépítés előnyei

Ahogy a modern szervezetekben használt alkalmazások száma folyamatosan növekszik, az informatikai rendszergazdák feladata a hozzáférés-kezelés nagy méretekben. Szabványok, például a biztonsági állítások markup language (SAML) vagy az Open ID Connect (OIDC) lehetővé teszia a rendszergazdák számára, hogy gyorsan beállít egy bejelentkezés (SSO), de a hozzáférés is megköveteli a felhasználók számára, hogy ki kell építeni az alkalmazásba. Sok rendszergazda számára a kiépítés azt jelenti, hogy minden felhasználói fiókot manuálisan hoz létre, vagy hetente feltölti a CSV-fájlokat, de ezek a folyamatok időigényesek, költségesek és hibalehetőségek. Megoldások, például saml just-in-time (JIT) fogadták el a kiépítés automatizálására, de a vállalatok is szükség van egy megoldás, hogy kilépjen a felhasználók, amikor elhagyják a szervezetet, vagy már nem kell hozzáférést bizonyos alkalmazások szerepkör-változás alapján.

Az automatikus kiépítés néhány gyakori motivációja a következő:

- A létesítési folyamatok hatékonyságának és pontosságának maximalizálása.
- Az egyéni fejlesztésű kiépítési megoldások és parancsfájlok üzemeltetésével és karbantartásával kapcsolatos költségek megtakarítója.
- A szervezet biztonságossá tétele a felhasználók identitásának azonnali eltávolításával a legfontosabb SaaS-alkalmazásokból, amikor elhagyják a szervezetet.
- Nagyszámú felhasználó egyszerű importálása egy adott SaaS-alkalmazásba vagy rendszerbe.
- Egyetlen szabályzatkészlet, amely meghatározza, hogy ki van építve, és ki jelentkezhet be egy alkalmazásba.

Az Azure AD-felhasználók kiépítése segíthet kezelni ezeket a kihívásokat. Ha többet szeretne megtudni arról, hogy az ügyfelek hogyan használják az Azure AD-felhasználók kiépítését, olvassa el az [ASOS esettanulmányt.](https://aka.ms/asoscasestudy) Az alábbi videó áttekintést nyújt a felhasználói kiépítésről az Azure AD-ben:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Milyen alkalmazásokat és rendszereket használhatok az Azure AD automatikus felhasználói kiépítésével?

Az Azure AD számos népszerű SaaS-alkalmazás és humánerőforrás-rendszer előre integrált támogatását, valamint az [SCIM 2.0 szabvány](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)meghatározott részeit megvalósító alkalmazások általános támogatását támogatja.

* **Előre integrált alkalmazások (galéria SaaS alkalmazások)**. Az összes olyan alkalmazást, amelyhez az Azure AD támogatja az előre integrált kiépítési összekötőt, megtalálhatja a [felhasználói kiépítéshez szükséges alkalmazásoktatóanyagok listájában.](../saas-apps/tutorial-list.md) A katalógusban felsorolt előintegrált alkalmazások általában SCIM 2.0-alapú felhasználói felügyeleti API-kat használnak a kiépítéshez. 

   ![A Salesforce emblémája](./media/user-provisioning/gallery-app-logos.png)

   Ha új kiépítési alkalmazást szeretne kérni, [kérheti, hogy az alkalmazást integrálja az alkalmazásgalériánkba.](../develop/howto-app-gallery-listing.md) A felhasználói létesítési kérelem hez az alkalmazásnak scim-kompatibilis végponttal kell rendelkeznie. Kérjük, kérje, hogy az alkalmazás szállítója kövesse az SCIM szabványt, hogy gyorsan betudjuk rakni az alkalmazást a platformunkra.

* **Az SCIM 2.0-t támogató alkalmazások**. Az SCIM 2.0 alapú felhasználói felügyeleti API-kat megvalósító alkalmazások általános csatlakoztatásáról az [SCIM-végpont létrehozása és a felhasználói kiépítés konfigurálása című](use-scim-to-provision-users-and-groups.md)témakörben talál.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Mi a tartományok közötti identitáskezelés rendszere?

A kiépítés és a megszüntetés automatizálása érdekében az alkalmazások saját felhasználói és csoportAPI-kat fednek fel. Azonban bárki, aki több alkalmazásban is próbált felhasználókat kezelni, azt fogja mondani, hogy minden alkalmazás ugyanazokat az egyszerű műveleteket próbálja végrehajtani, például felhasználókat létrehozni vagy frissíteni, felhasználókat hozzáadni a csoportokhoz, vagy kiépíteni a felhasználókat. Mégis, ezek az egyszerű műveletek csak egy kicsit másképp valósítják meg, különböző végpontelérési utak, különböző módszerek et a felhasználói adatok megadására, és egy másik séma, hogy képviselje az egyes információelemeket.

Ezeknek a kihívásoknak a kezelése érdekében az SCIM-specifikáció egy közös felhasználói sémát biztosít, amely segít a felhasználóknak az alkalmazásokba való beköltözésben, az alkalmazásokon kívül és azok körül. Az SCIM a kiépítés de facto szabványává válik, és ha olyan összevonási szabványokkal együtt használják, mint az SAML vagy az OpenID Connect, a rendszergazdák számára végpontok között szabványalapú megoldást biztosít a hozzáférés-kezeléshez.

A felhasználók és csoportok alkalmazáshoz való kiépítésének és megszüntetésének automatizálásához az SCIM-végpont fejlesztésével kapcsolatos részletes útmutatásért olvassa el [az SCIM-végpont létrehozása és a felhasználói kiépítés konfigurálása](use-scim-to-provision-users-and-groups.md)című témakört. A galériában (Slack, Azure Databricks, Snowflake stb.) lévő, előre integrált alkalmazások esetén kihagyhatja a fejlesztői dokumentációt, és használhatja az [itt](../saas-apps/tutorial-list.md)található oktatóanyagokat.

## <a name="manual-vs-automatic-provisioning"></a>Manuális és automatikus átadás

Az Azure AD-katalógusban lévő alkalmazások két kiépítési mód egyikét támogatják:

* **A manuális** kiépítés azt jelenti, hogy még nincs automatikus Azure AD-kiépítési összekötő az alkalmazáshoz. A felhasználói fiókokat manuálisan kell létrehozni, például úgy, hogy a felhasználókat közvetlenül az alkalmazás felügyeleti portáljára veszik fel, vagy feltöltik a felhasználói fiók adatait tartalmazó számolótáblát. Tekintse meg az alkalmazás által biztosított dokumentációt, vagy lépjen kapcsolatba az alkalmazás fejlesztőjével, hogy megtudja, milyen mechanizmusok állnak rendelkezésre.

* **Automatikus** azt jelenti, hogy egy Azure AD-kiépítési összekötő továbbfejlesztett ék ehhez az alkalmazáshoz. Kövesse a telepítő oktatóanyag specifikus az alkalmazás kiépítés beállításához. Az alkalmazásoktatóanyagok a [SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](../saas-apps/tutorial-list.md)oldalon találhatók.

Az Azure AD-gyűjteményben az automatikus kiépítést támogató alkalmazásokat egy **kiépítési** ikon jelöli ki. Váltson át az új galéria előnézeti élményére, hogy láthassa ezeket az ikonokat (az **Alkalmazás hozzáadása oldal**tetején található szalagcímben válassza ki a Kattintson ide feliratot az új és **továbbfejlesztett alkalmazásgaléria kipróbálásához).**

![Kiépítés ikon az alkalmazásgyűjteményben](./media/user-provisioning/browse-gallery.png)

Az alkalmazás által támogatott létesítési mód is látható a **Kiépítés** lapon, miután hozzáadta az alkalmazást a **vállalati alkalmazásokhoz.**

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hogyan állíthatom be az automatikus kiépítést egy alkalmazásba?

A katalógusban felsorolt, előre integrált alkalmazások, lépésről-lépésre elérhető az automatikus kiépítés beállításához. Tekintse meg [az integrált galériaalkalmazások oktatóanyagainak listáját.](../saas-apps/tutorial-list.md) Az alábbi videó bemutatja, hogyan állíthatja be a SalesForce automatikus felhasználói kiépítését.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Az SCIM 2.0-s t támogató egyéb alkalmazások esetében kövesse az SCIM-végpont létrehozása című cikkben ismertetett lépéseket, és konfigurálja a [felhasználói kiépítést.](use-scim-to-provision-users-and-groups.md)


## <a name="related-articles"></a>Kapcsolódó cikkek

- [Az SaaS-alkalmazások integrálására vonatkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
- [Attribútumleképezések testreszabása a felhasználók kiépítéséhez](customize-application-attributes.md)
- [Kifejezések írása attribútumleképezéshez](../app-provisioning/functions-for-customizing-application-data.md)
- [Hatókörszűrők a felhasználók kiépítéshez](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM-végpont létrehozása és a felhasználói kiépítés konfigurálása](use-scim-to-provision-users-and-groups.md)
- [Az Azure AD szinkronizálási API áttekintése](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
