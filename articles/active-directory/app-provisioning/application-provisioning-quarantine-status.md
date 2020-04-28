---
title: Karanténba helyezés alkalmazás-létesítési állapota | Microsoft Docs
description: Amikor konfigurált egy alkalmazást a felhasználók automatikus kiépítési felállításához, megtudhatja, mi a karanténba helyezési állapot, és hogyan törölheti azt.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154627"
---
# <a name="application-provisioning-in-quarantine-status"></a>Alkalmazás üzembe helyezése a karantén állapotában

Az Azure AD kiépítési szolgáltatás figyeli a konfiguráció állapotát, és nem kifogástalan állapotú alkalmazásokat helyez el a "karantén" állapotban. Ha a megcélzott rendszerre irányuló hívások többsége vagy mindegyike egy hiba miatt nem sikerül, például érvénytelen rendszergazdai hitelesítő adatok, a kiépítési feladat karanténként van megjelölve.

A karanténba helyezve a növekményes ciklusok gyakorisága naponta egyszer csökken. A kiépítési feladatot a rendszer eltávolítja a karanténból az összes hiba javítása és a következő szinkronizálási ciklus elindítása után. Ha a kiépítési feladatok több mint négy hétig maradnak karanténban, a kiépítési feladatok le vannak tiltva (a rendszer leáll).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hogyan tudni, hogy az alkalmazás karanténban van-e?

Három módon ellenőrizhető, hogy egy alkalmazás karanténba helyezve van-e:
  
- A Azure Portal keresse meg **Azure Active Directory** > **Enterprise Applications** > &lt;*Application Name*&gt; > **kiépítés** lehetőséget, majd görgessen a folyamatjelző sáv alján.  

  ![Üzembe helyezési állapotsor, amely a karantén állapotát mutatja](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- A Azure Portal navigáljon **Azure Active Directory** > **naplók** > szűrés **tevékenység: karanténba helyezés** , és tekintse át a karanténba helyezési előzményeket. Bár a fentiekben ismertetett folyamatjelzőn látható nézet azt mutatja, hogy a kiépítés jelenleg karanténban van-e, a naplók lehetővé teszik az alkalmazások karanténba helyezési előzményeinek megtekintését. 

- Használja a Microsoft Graph kérelmet a [synchronizationJob beszerzéséhez](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) a kiépítési feladatok állapotának programozott beszerzéséhez:

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Nézze meg az e-mailjeit. Ha egy alkalmazás Karanténba kerül, egy egyszeri értesítő e-mailt kap. Ha a karanténba helyezés oka megváltozik, a rendszer egy frissített e-mailt küld, amely a karanténba helyezés új okát mutatja. Ha nem lát e-mailt:

  - Győződjön meg arról, hogy érvényes **értesítési e-mailt** adott az alkalmazás kiépítési konfigurációjában.
  - Győződjön meg róla, hogy nincs levélszemét-szűrés az értesítő e-mailek mappájában.
  - Győződjön meg arról, hogy nincs leiratkozott az e-mailekről.

## <a name="why-is-my-application-in-quarantine"></a>Miért van a karanténba helyezett alkalmazásom?

A kiépítési feladatok állapotának beolvasására Microsoft Graph kérelem a karanténba helyezés következő okát mutatja be:

- `EncounteredQuarantineException`azt jelzi, hogy érvénytelen hitelesítő adatok lettek megadva. A kiépítési szolgáltatás nem tud kapcsolatot létesíteni a forrásrendszer és a célként megadott rendszer között.

- `EncounteredEscrowProportionThreshold`azt jelzi, hogy a kiépítés túllépte a letéti küszöbértéket. Ez az állapot akkor fordul elő, ha a kiépítési események több mint 60%-a nem sikerült.

- `QuarantineOnDemand`azt jelenti, hogy az alkalmazással kapcsolatos problémát észlelt, és manuálisan beállította a karanténba helyezését.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hogyan az alkalmazás karanténba helyezését?

Először javítsa ki azt a problémát, amely az alkalmazás karanténba helyezését okozta.

- Ellenőrizze az alkalmazás kiépítési beállításait, és győződjön meg arról, hogy [érvényes rendszergazdai hitelesítő adatokat](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)adott meg. Az Azure AD-nek képesnek kell lennie megbízhatóság létrehozására a célalkalmazás használatával. Győződjön meg arról, hogy érvényes hitelesítő adatokat adott meg, és a fiókja rendelkezik a szükséges engedélyekkel.

- Tekintse át a [kiépítési naplókat](../reports-monitoring/concept-provisioning-logs.md) , és vizsgálja meg, hogy milyen hibák okozzák a karanténba helyezést, és oldja meg a hibát. Nyissa meg a Azure Portal kiépítési naplóit a **tevékenység** szakaszban **Azure Active Directory** &gt; **vállalati alkalmazások** &gt; **kiépítési naplóiban (előzetes verzió)** .

A probléma megoldása után indítsa újra a kiépítési feladatot. Az alkalmazás üzembe helyezési beállításainak (például az attribútum-hozzárendelések vagy a hatóköri szűrők) bizonyos módosításai automatikusan újraindulnak a kiépítés során. Az alkalmazás **üzembe** helyezési lapjának folyamatjelző sávján a kiépítés utolsó indításakor látható. Ha manuálisan kell újraindítani a kiépítési feladatot, használja az alábbi módszerek egyikét:  

- A kiépítési feladatok újraindításához használja a Azure Portal. Az alkalmazás **kiépítési** lapján a **Beállítások**területen válassza az **állapot törlése és a szinkronizálás újraindítása** és a **kiépítési állapot** beállítása **a**következőre lehetőséget. Ez a művelet teljesen újraindítja a kiépítési szolgáltatást, amely hosszabb időt is igénybe vehet. A teljes kezdeti ciklus újra futni fog, ami törli a letéteket, eltávolítja az alkalmazást a karanténból, és törli az összes vízjelet.

- [A kiépítési feladatok újraindításához használja a](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)Microsoft Graph. Az újraindítást teljes mértékben szabályozhatja. Dönthet úgy, hogy törli a letéteket (a karantén állapotának újraindításához), a karantén törlését (az alkalmazás karanténból való eltávolítását) vagy a vízjelek törlését. Használja az alábbi kérelmet:
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`
