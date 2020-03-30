---
title: Automatikus felhasználói fiók-kiépítés jelentése SaaS-alkalmazásokba
description: Ismerje meg, hogyan ellenőrizheti az automatikus felhasználói fiók kiépítési feladatok állapotát, és hogyan háríthatja el az egyes felhasználók kiépítésének hibáit.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282188"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Oktatóanyag: A felhasználói fiókok automatikus kiépítéséről szóló jelentés

Az Azure Active Directory (Azure AD) tartalmaz egy [felhasználói fiók kiépítési szolgáltatást,](user-provisioning.md) amely segít automatizálni a felhasználói fiókok kiépítésének kiépítését a SaaS-alkalmazásokban és más rendszerekben, a végpontok között identitáséletciklus-kezelés céljából. Az Azure AD támogatja az előre integrált felhasználói kiépítési összekötők az összes alkalmazás és rendszer a felhasználói kiépítési oktatóanyagok [itt.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

Ez a cikk bemutatja, hogyan ellenőrizheti a kiépítési feladatok állapotát a beállítás uk után, és hogyan háríthatja el az egyes felhasználók és csoportok kiépítésének elhárítását.

## <a name="overview"></a>Áttekintés

Az összekötők kiépítése az Azure [Portal](https://portal.azure.com)használatával van beállítva és konfigurálva, a támogatott alkalmazás [által megadott dokumentáció](../saas-apps/tutorial-list.md) tkövetően. Konfigurálás és futtatás után a létesítési feladatok a következő két módszer egyikével jelenthetők:

* **Azure Portal** – Ez a cikk elsősorban ismerteti a jelentésadatok lekérése az [Azure Portalról,](https://portal.azure.com)amely egy kiépítési összefoglaló jelentést, valamint egy adott alkalmazás részletes kiépítési naplózási naplóit is tartalmazza.
* **Naplózási API** – Az Azure Active Directory egy naplózási API-t is biztosít, amely lehetővé teszi a részletes kiépítési naplózási naplók programozott lekérdezését. Az [Azure Active Directory naplózási API-hivatkozása](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) az API használatára vonatkozó dokumentációt tartalmazza. Bár ez a cikk nem kifejezetten az API használatát, részletesen ismerteti a naplózási események a naplóban rögzített típusú létesítési események.

### <a name="definitions"></a>Meghatározások

Ez a cikk az alábbi kifejezéseket használja:

* **Forrásrendszer** – A felhasználók tárháza, amelyből az Azure AD-kiépítési szolgáltatás szinkronizálja. Az Azure Active Directory az előre integrált létesítési összekötők többségének forrásrendszere, azonban vannak kivételek (például: Munkanap bejövő szinkronizálás).
* **Célrendszer** – A felhasználók tárháza, amelyhez az Azure AD-kiépítési szolgáltatás szinkronizálja. Ez általában egy SaaS-alkalmazás (például: Salesforce, ServiceNow, G Suite, Dropbox for Business), de bizonyos esetekben lehet egy helyszíni rendszer, például az Active Directory (például: Workday Bejövő szinkronizálás az Active Directoryval).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Jelentések beszerzése az Azure Portalról

Egy adott alkalmazás kiépítési jelentésadatainak beszerezéséhez indítsa el az [Azure Portal](https://portal.azure.com) és az **Azure Active Directory** &gt; **vállalati alkalmazások** &gt; **létesítési naplók (előzetes verzió)** **elindításával** a Tevékenység szakaszban. Azt a vállalati alkalmazást is tallózhatja, amelyhez a kiépítés konfigurálva van. Ha például a LinkedIn-elevate szolgáltatásba épít ki felhasználókat, az alkalmazás részleteinek navigációs elérési útja a következő:

**Az Azure Active Directory > Vállalati alkalmazások > az összes > LinkedIn-verzió**

Itt is elérheti a kiépítési folyamatjelző és a létesítési naplók, az alábbiakban ismertetett.

## <a name="provisioning-progress-bar"></a>Kiépítés folyamatjelző

A [kiépítési folyamatjelző látható](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) a **kiépítés** lapon adott alkalmazáshoz. Az Aktuális **állapot** szakaszban található a **Beállítások**alatt, és az aktuális kezdeti vagy növekményes ciklus állapotát mutatja. Ez a rész a következőket is mutatja:

* A szinkronizált és jelenleg hatókörben lévő felhasználók és/csoportok teljes száma a forrásrendszer és a célrendszer között.
* A szinkronizálás legutóbbi futtatásakor. A szinkronizálásáltalában 20-40 percenként történik, miután a [kezdeti ciklus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) befejeződött.
* Azt jelzi, hogy befejeződött-e a [kezdeti ciklus.](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)
* Függetlenül attól, hogy a létesítési folyamat karanténba került-e, és mi az oka a karantén állapotának (például érvénytelen rendszergazdai hitelesítő adatok miatt a célrendszerrel való kommunikáció sikertelen).

Az **aktuális állapot** legyen az első hely, ahol a rendszergazdák a kiépítési feladat működési állapotát ellenőrzik.

 ![Összefoglaló jelentés](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Naplók kiépítése (előzetes verzió)

A kiépítési szolgáltatás által végrehajtott összes tevékenység et az Azure AD [létesítési naplók rögzítik.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Az Azure-portálon a létesítési naplók eléréséhez válassza az **Azure Active Directory** &gt; **Vállalati alkalmazások** &gt; **kiépítése naplók (előzetes verzió)** a **tevékenység** szakaszban. A kiépítési adatok ban a felhasználó neve vagy az azonosító alapján kereshet a forrásrendszerben vagy a célrendszerben. További információt a [Naplók kiépítése (előzetes verzió) című témakörben talál.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) A naplózott tevékenységesemény-típusok a következők:

## <a name="troubleshooting"></a>Hibaelhárítás

A kiépítési összefoglaló jelentés és a kiépítési naplók kulcsszerepet játszanak abban, hogy a rendszergazdák elhárítsák a különböző felhasználói fiókok kiépítési problémáit.

Az automatikus felhasználói kiépítés elhárításával kapcsolatos forgatókönyv-alapú útmutatásért olvassa el [A felhasználók konfigurálása és kiépítése az alkalmazásba című témakört.](../app-provisioning/application-provisioning-config-problem.md)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
