---
title: Karantén kérelmezési állapota | Microsoft dokumentumok
description: Ha konfigurált egy alkalmazást az automatikus felhasználói kiépítéshez, ismerje meg, hogy mit jelent a karantén kiépítési állapota, és hogyan törölheti azt.
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
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563c049bf3d1606e87db54e3b003dac987594610
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154627"
---
# <a name="application-provisioning-in-quarantine-status"></a>Alkalmazás-kiépítés karanténállapotban

Az Azure AD-kiépítési szolgáltatás figyeli a konfiguráció állapotát, és a nem megfelelő állapotú alkalmazásokat "karantén" állapotba helyezi. Ha a célrendszeren végrehajtott hívások többsége vagy egésze egy hiba miatt következetesen sikertelen, például érvénytelen rendszergazdai hitelesítő adatok miatt, a létesítési feladat karanténként van megjelölve.

Míg a karantén, a gyakorisága növekményes ciklusok fokozatosan csökken naponta egyszer. A kiépítési feladat törlődik a karanténból, miután az összes hibát kijavították, és a következő szinkronizálási ciklus elindul. Ha a létesítési feladat több mint négy hétig karanténban marad, a létesítési feladat le van tiltva (leáll).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Honnan tudhatom, hogy az alkalmazásom karanténban van-e?

Háromféleképpen ellenőrizheti, hogy egy alkalmazás karanténban van-e:
  
- Az Azure Portalon keresse meg az **Azure Active Directory** > **Vállalati alkalmazások** > &lt;*alkalmazásneve*&gt; > **Kiépítés,** és görgessen a folyamatjelző alján.  

  ![Karanténállapotot megjelenítő állapotsor kiépítése](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Az Azure Portalon keresse meg az **Azure Active Directory** > **naplózási naplók** > a **tevékenység: karantén és** tekintse át a karantén előzményeket. Míg a nézet a folyamatjelzőn a fent leírt azt mutatja, hogy a kiépítés jelenleg karanténban van, a naplók lehetővé teszik, hogy egy alkalmazás karantén előzményeit. 

- Használja a Microsoft Graph kérés [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) programozott állapotának lekéréséhez a létesítési feladat:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Nézze meg az e-mailjeit. Amikor egy alkalmazás karanténba kerül, a rendszer egyszeri értesítési e-mailt küld. Ha a karantén oka megváltozik, a rendszer egy frissített e-mailt küld, amely a karantén új okát mutatja. Ha nem lát e-mailt:

  - Győződjön meg arról, hogy adott meg egy érvényes **értesítési e-mailt** az alkalmazás létesítési konfigurációjában.
  - Győződjön meg arról, hogy nincs spamszűrés az értesítő e-mail postafiókjában.
  - Győződjön meg róla, hogy nem iratkozott le az e-mailekről.

## <a name="why-is-my-application-in-quarantine"></a>Miért van karanténban a jelentkezésem?

A kiépítési feladat állapotának lekérésére irányuló Microsoft Graph-kérelem a karantén következő okát mutatja:

- `EncounteredQuarantineException`azt jelzi, hogy érvénytelen hitelesítő adatokat adott meg. A létesítési szolgáltatás nem tud kapcsolatot létesíteni a forrásrendszer és a célrendszer között.

- `EncounteredEscrowProportionThreshold`azt jelzi, hogy a kiépítés meghaladta a letéti küszöbértéket. Ez a feltétel akkor fordul elő, ha a kiépítési események több mint 60%-a sikertelen volt.

- `QuarantineOnDemand`azt jelenti, hogy hibát észleltünk az alkalmazással kapcsolatban, és manuálisan karanténba állítottuk.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hogyan tudom kivinni a jelentkezésemet a karanténból?

Először oldja meg azt a problémát, amely miatt az alkalmazás karanténba került.

- Ellenőrizze az alkalmazás létesítési beállításait, és győződjön meg arról, hogy [érvényes rendszergazdai hitelesítő adatokat adott meg.](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning) Az Azure AD-nek képesnek kell lennie egy bizalmi kapcsolat létrehozásához a célalkalmazással. Győződjön meg arról, hogy érvényes hitelesítő adatokat adott meg, és fiókja rendelkezik a szükséges engedélyekkel.

- Tekintse át a [létesítési naplókat,](../reports-monitoring/concept-provisioning-logs.md) hogy tovább vizsgálja, milyen hibák okoznak karantént, és a hiba megoldása. Az Azure Active Directory Vállalati alkalmazások kiépítési naplóinak (előzetes verzió) a **Tevékenység** szakaszban elérhető a létesítési naplók elérése az **Azure Active Directory** &gt; **vállalati alkalmazások** &gt; **kiépítési naplóihoz (előzetes verzióban).**

Miután megoldotta a problémát, indítsa újra a létesítési feladatot. Az alkalmazás létesítési beállításainak bizonyos módosításai, például az attribútumleképezések vagy a hatókörszűrők, automatikusan újraindítják a kiépítést. Az alkalmazás **kiépítéslapján** a folyamatjelző jelzi, hogy mikor kezdődött el utoljára a kiépítés. Ha manuálisan kell újraindítania a létesítési feladatot, használja az alábbi módszerek egyikét:  

- Az Azure Portal használatával indítsa újra a létesítési feladatot. Az alkalmazás **Kiépítés** lapján a **Beállítások**csoportban válassza a Törlés állapot lehetőséget, **majd indítsa újra a szinkronizálást,** és állítsa **a Kiépítés állapotát** **Be**beállításra. Ez a művelet teljesen újraindítja a kiépítési szolgáltatást, ami eltarthat egy ideig. A teljes kezdeti ciklus újra lefut, amely törli az escrows-t, eltávolítja az alkalmazást a karanténból, és törli a vízjeleket.

- A [Kiépítési feladat újraindításához](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)használja a Microsoft Graph programot. Teljes mértékben szabályozhatja, hogy mit indít újra. Választhat, hogy törölje a jelzőpontokat (a karantén állapot felé felhalmozódó letéti számláló újraindításához), a karantén kiürítését (az alkalmazás karanténból való eltávolításához) vagy a vízjelek törlését. Használja az alábbi kérelmet:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
