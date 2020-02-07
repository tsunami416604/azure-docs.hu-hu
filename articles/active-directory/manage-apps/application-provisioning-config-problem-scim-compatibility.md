---
title: Az SCIM 2,0 protokoll megfelelőségével kapcsolatos ismert problémák – Azure AD
description: A SCIM 2,0-et az Azure AD-t támogató nem katalógusbeli alkalmazások hozzáadásakor felmerülő általános protokoll-kompatibilitási problémák megoldása
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0cae5458a9b9456d26363802ee9b06aaa842e72
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063594"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Az Azure AD felhasználói kiépítési szolgáltatás SCIM 2,0-es protokoll-megfelelőségének ismert problémái és megoldásai

Azure Active Directory (Azure AD) automatikusan kiépítheti a felhasználókat és a csoportokat bármely olyan alkalmazásra vagy rendszerre, amelyet egy webszolgáltatásnak kell ellátnia a [rendszeren a tartományok közötti Identitáskezelés (scim) 2,0 protokoll specifikációja](https://tools.ietf.org/html/draft-ietf-scim-api-19)alapján meghatározott felületen. 

A SCIM 2,0 protokoll Azure AD-támogatását a [rendszer a tartományok közötti Identitáskezelés (scim) használatával című témakör ismerteti, amellyel automatikusan kiépítheti a felhasználókat és csoportokat a Azure Active Directoryból az alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md), amelyek a scim 2,0-et támogató alkalmazások számára automatikusan kiépítik a felhasználókat és csoportokat az Azure ad-ből.

Ez a cikk az Azure AD-felhasználó kiépítési szolgáltatásának az SCIM 2,0-es protokollal való betartásával kapcsolatos aktuális és múltbeli problémákat ismerteti, valamint a problémák megoldását.

> [!IMPORTANT]
> Az Azure AD felhasználói kiépítési szolgáltatás SCIM-ügyfelének legújabb frissítése 2018. december 18-án történt. Ez a frissítés az alábbi táblázatban felsorolt ismert kompatibilitási problémákat tárgyalja. A frissítéssel kapcsolatos további információkért tekintse meg az alábbi gyakran ismételt kérdéseket.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2,0 megfelelőségi problémák és állapot

| **SCIM 2,0 megfelelőségi probléma** |  **Rögzített?** | **Javítás dátuma**  |  
|---|---|---|
| Az Azure AD-nek a "/scim" értéknek kell lennie az alkalmazás SCIM végpont URL-címének gyökerében.  | Igen  |  December 18., 2018 | 
| A bővítmény attribútumai a "." jelölést használják az attribútumok neve előtt a kettőspont ":" jelölése helyett |  Igen  | December 18., 2018  | 
|  A többértékű attribútumok javítására vonatkozó kérelmek érvénytelen elérésiút-szűrési szintaxist tartalmaznak. | Igen  |  December 18., 2018  | 
|  A csoportos létrehozási kérelmek érvénytelen séma-URI-t tartalmaznak | Igen  |  December 18., 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>A szolgáltatási javítások automatikusan érvénybe lépnek a meglévő SCIM-alkalmazáson?

Nem. Mivel a régi viselkedéssel való együttműködéshez kódolt SCIM-alkalmazások esetében nem történt változás, a módosítások nem lettek automatikusan alkalmazva a meglévő alkalmazásokra.

A módosítások a javítás után a Azure Portal konfigurált összes új, nem katalógusba [scim](configure-single-sign-on-non-gallery-applications.md) alkalmazásra érvényesek.

A következő szakaszban talál információt arról, hogyan telepítheti át a meglévő felhasználó-kiépítési feladatot a legújabb javítások befoglalására.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Áttelepíthetek egy meglévő SCIM-alapú felhasználói kiépítési feladatot a legújabb szolgáltatás-javítások befoglalására?

Igen. Ha már használja ezt az alkalmazás-példányt az egyszeri bejelentkezéshez, és át kell telepítenie a meglévő kiépítési feladatot a legújabb javítások befoglalásához, kövesse az alábbi eljárást. Ez az eljárás azt ismerteti, hogyan használható a Microsoft Graph API és a Microsoft Graph API Explorer a régi kiépítési feladat meglévő SCIM-alkalmazásból való eltávolításához, valamint egy olyan új művelet létrehozásához, amely az új viselkedést mutatja be.

> [!NOTE]
> Ha az alkalmazás még fejlesztés alatt áll, és még nincs telepítve az egyszeri bejelentkezéshez vagy a felhasználók üzembe helyezéséhez, a legegyszerűbb megoldás az alkalmazás bejegyzésének törlése a Azure Portal **Azure Active Directory > Enterprise Applications** szakaszában, és egyszerűen vegyen fel új bejegyzést az alkalmazáshoz az **alkalmazás létrehozása > nem** katalógus beállítással. Ez egy alternatív megoldás az alábbi eljárás futtatására.
 
1. Jelentkezzen be a Azure Portal https://portal.azure.com.
2. A Azure Portal **Azure Active Directory > vállalati alkalmazások** szakaszában keresse meg és válassza ki a meglévő scim alkalmazást.
3. A meglévő SCIM-alkalmazás **Tulajdonságok** szakaszában másolja át az **objektumazonosítót**.
4. Egy új böngészőablakban nyissa meg a https://developer.microsoft.com/graph/graph-explorert, és jelentkezzen be rendszergazdaként az Azure AD-bérlőhöz, ahol az alkalmazás hozzá van adva.
5. A Graph Explorerben futtassa az alábbi parancsot a kiépítési feladatok AZONOSÍTÓjának megkereséséhez. Cserélje le az "[Object-id]" kifejezést a harmadik lépésből másolt egyszerű szolgáltatásnév (objektumazonosító) helyére.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Feladatok beolvasása](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Feladatok beolvasása") 


6. Az eredmények között másolja a "customappsso" vagy a "scim" kifejezéssel kezdődő teljes "ID" karakterláncot.
7. Futtassa az alábbi parancsot az attribútum-leképezési konfiguráció lekéréséhez, így biztonsági mentést készíthet. Használja ugyanazokat az [Object-id]-t, mint korábban, és cserélje le a [Job-id] elemet az utolsó lépésből másolt kiépítési feladatokhoz tartozó AZONOSÍTÓra.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Séma beolvasása](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Séma beolvasása") 

8. Másolja a JSON-kimenetet az utolsó lépésből, és mentse egy szövegfájlba. Ez tartalmaz minden egyéni attribútumot, amelyet a régi alkalmazáshoz adott hozzá, és körülbelül néhány ezer sornyi JSON-t kell használnia.
9. Futtassa az alábbi parancsot a kiépítési feladatok törléséhez:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Az alábbi parancs futtatásával hozzon létre egy új kiépítési feladatot, amely a legújabb szolgáltatás-javításokkal rendelkezik.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Az utolsó lépés eredményeiben másolja a "scim" karakterlánccal kezdődő teljes "ID" karakterláncot. Szükség esetén újra alkalmazhatja a régi attribútum-hozzárendeléseket az alábbi parancs futtatásával, lecserélve a [New-Job-id] paramétert az imént másolt új AZONOSÍTÓJÚ feladattípusra, majd a kérelem törzsének #7. lépésével adja meg a JSON-kimenetet.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Térjen vissza az első böngészőablakba, és válassza ki az alkalmazás **kiépítési** lapját.
13. Ellenőrizze a konfigurációt, majd indítsa el a kiépítési feladatot. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Hozzáadhatok olyan új, nem katalógusbeli alkalmazást, amely a régi felhasználó-kiépítési viselkedéssel rendelkezik?

Igen. Ha az alkalmazást a javítások előtt meglévő régi viselkedéshez kódolta, és új példányt kell telepítenie, kövesse az alábbi eljárást. Ez az eljárás azt ismerteti, hogyan használható a Microsoft Graph API és a Microsoft Graph API Explorer egy olyan SCIM-létesítési feladat létrehozásához, amely a régi viselkedést mutatja.
 
1. Jelentkezzen be a Azure Portal https://portal.azure.com.
2. a **Azure Active Directory > vállalati alkalmazások > alkalmazás létrehozása** című Azure Portal szakaszában hozzon létre egy új, **nem** katalógusbeli alkalmazást.
3. Az új egyéni alkalmazás **Tulajdonságok** szakaszában másolja ki az **objektumazonosítót**.
4. Egy új böngészőablakban nyissa meg a https://developer.microsoft.com/graph/graph-explorert, és jelentkezzen be rendszergazdaként az Azure AD-bérlőhöz, ahol az alkalmazás hozzá van adva.
5. A Graph Explorerben futtassa az alábbi parancsot az alkalmazás létesítési konfigurációjának inicializálásához.
   Cserélje le az "[Object-id]" kifejezést a harmadik lépésből másolt egyszerű szolgáltatásnév (objektumazonosító) helyére.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Térjen vissza az első böngészőablakba, és válassza ki az alkalmazás **kiépítési** lapját.
7. A szokásos módon végezze el a felhasználó kiépítési konfigurációját.


## <a name="next-steps"></a>Következő lépések
[További információ az SaaS-alkalmazásokhoz való kiépítés és kiépítés ellen](../app-provisioning/user-provisioning.md)

