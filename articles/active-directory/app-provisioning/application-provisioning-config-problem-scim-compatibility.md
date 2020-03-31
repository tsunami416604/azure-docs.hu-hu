---
title: Ismert problémák az SCIM 2.0 protokoll megfelelőségével – Azure AD
description: Az SCIM 2.0 szolgáltatást támogató nem katalógusalkalmazást támogató nem katalógusalkalmazás hozzáadásakor felmerülő gyakori protokollkompatibilitási problémák megoldása az Azure AD-hez
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
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff0d4f8f0062c00351b60174c63d9c19bdfa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522934"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Ismert problémák és megoldások az Azure AD felhasználói kiépítési szolgáltatás SCIM 2.0 protokollmegfelelőségével

Az Azure Active Directory (Azure AD) automatikusan kiépítheti a felhasználókat és csoportokat bármely olyan alkalmazásba vagy rendszerbe, amelyet egy webszolgáltatás a [System for Cross-Domain Identity Management (SCIM) 2.0 protokollspecifikációjában](https://tools.ietf.org/html/draft-ietf-scim-api-19)definiált felülettel előrehelyezett. 

Az Azure AD támogatja az SCIM 2.0 protokoll leírása a [Rendszer használata a tartományok közötti identitáskezelés (SCIM) automatikusan kiépíteni](use-scim-to-provision-users-and-groups.md)a felhasználók és csoportok az Azure Active Directoryból alkalmazások, amely felsorolja a protokoll konkrét részeit, amelyek et valósít meg annak érdekében, hogy automatikusan kiad a felhasználók és csoportok az Azure AD-től az ALKALMAZÁSOK, amelyek támogatják az SCIM 2.0.

Ez a cikk ismerteti az Azure AD felhasználói kiépítési szolgáltatás sal kapcsolatos aktuális és korábbi problémákat, amelyek megfelelnek az SCIM 2.0 protokollnak, és hogyan kerülheti meg ezeket a problémákat.

> [!IMPORTANT]
> 2018. december 18-án készült az Azure AD-felhasználói kiépítési szolgáltatás SCIM-ügyféllegújabb frissítése. A frissítés az alábbi táblázatban felsorolt ismert kompatibilitási problémákat orvosolta. A frissítéssel kapcsolatos további információkért tekintse meg az alábbi gyakori kérdéseket.

## <a name="scim-20-compliance-issues-and-status"></a>Az SCIM 2.0 megfelelőségi problémái és állapota

| **Az SCIM 2.0 megfelelőségi problémája** |  **Rögzített?** | **Rögzítés dátuma**  |  
|---|---|---|
| Az Azure AD megköveteli, hogy a "/scim" az alkalmazás SCIM-végpontjának gyökérkönyvtárában legyen  | Igen  |  2018. december 18.December 18.December 18.December 18,8 | 
| A kiterjesztésattribútumok pont "." jelöléssel rendelkeznek az attribútumnevek előtt a kettőspont ":" jelölés helyett. |  Igen  | 2018. december 18.December 18.December 18.December 18,8  | 
|  A többértékű attribútumok javítási kérelmei érvénytelen elérési útszűrő-szintaxist tartalmaznak | Igen  |  2018. december 18.December 18.December 18.December 18,8  | 
|  A csoportlétrehozási kérelmek érvénytelen sémaURI-t tartalmaznak | Igen  |  2018. december 18.December 18.December 18.December 18,8  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>A leírt szolgáltatások javításai automatikusan alkalmazva voltak a már meglévő SCIM-alkalmazásomra?

Nem. Mivel ez a régebbi viselkedéssel kódolt SCIM-alkalmazások törési módosítását tette volna, a módosítások nem lettek automatikusan alkalmazva a meglévő alkalmazásokra.

A módosítások az Azure Portalon konfigurált összes [új, nem katalógusos SCIM-alkalmazásra](../manage-apps/configure-single-sign-on-non-gallery-applications.md) vonatkoznak a javítás dátuma után.

Arról, hogy miként telepítheti át a meglévő felhasználói kiépítési feladatot a legújabb javításokra, tekintse meg a következő szakaszt.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Áttelepíthetek egy meglévő SCIM-alapú felhasználói kiépítési feladatot a legújabb szolgáltatásjavításokhoz?

Igen. Ha már használja ezt az alkalmazáspéldányt az egyszeri bejelentkezéshez, és át kell telepítenie a meglévő létesítési feladatot a legújabb javítások felvételéhez, kövesse az alábbi eljárást. Ez az eljárás azt ismerteti, hogy miként távolíthatja el a régi kiépítési feladatot a meglévő SCIM-alkalmazásból a Microsoft Graph API és a Microsoft Graph API-kezelő használatával, és hogyan hozhat létre egy újat, amely az új viselkedést mutatja.

> [!NOTE]
> Ha az alkalmazás még fejlesztés alatt áll, és még nem lett telepítve egyetlen bejelentkezéshez vagy a felhasználó kiépítéséhez, a legegyszerűbb megoldás az alkalmazásbejegyzés törlése az **Azure Active Directory > vállalati alkalmazások** szakaszában az Azure Portalon, és egyszerűen adjon hozzá egy új bejegyzést az alkalmazáshoz az alkalmazás létrehozása > nem **galéria** beállítás használatával. Ez az alábbi eljárás futtatásának alternatívája.
 
1. Jelentkezzen be az https://portal.azure.comAzure Portalon.
2. Az **Azure-portál Azure Active Directory > vállalati alkalmazások** szakaszában keresse meg és válassza ki a meglévő SCIM-alkalmazást.
3. A meglévő SCIM-alkalmazás **Tulajdonságok** szakaszában másolja az **Objektumazonosítót**.
4. Egy új böngészőablakban nyissa https://developer.microsoft.com/graph/graph-explorer meg, és jelentkezzen be rendszergazdaként az Azure AD-bérlő, ahol az alkalmazás hozzá van adva.
5. A Graph Explorer, futtassa az alábbi parancsot, hogy keresse meg a létesítési feladat azonosítóját. Cserélje le az "[object-id]" szót a harmadik lépésből másolt egyszerű szolgáltatásazonosítóra (objektumazonosítóra).
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Álláskeresés](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Álláskeresés") 


6. Az eredmények között másolja a teljes "ID" karakterláncot, amely "customappsso" vagy "scim" karakterrel kezdődik.
7. Az alábbi parancs futtatásával lekérheti az attribútumleképezési konfigurációt, hogy biztonsági másolatot készíthessen. Használja ugyanazt az [object-id] , mint korábban, és cserélje le a [job-id] a létesítési feladat azonosítója másolt az utolsó lépés.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Séma bekésezése](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Séma bekésezése") 

8. Másolja a JSON-kimenetet az utolsó lépésből, és mentse egy szövegfájlba. Ez tartalmazza a régi alkalmazáshoz hozzáadott egyéni attribútum-leképezéseket, és körülbelül néhány ezer sorNyi JSON-nak kell lennie.
9. Futtassa az alábbi parancsot a létesítési feladat törléséhez:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Futtassa az alábbi parancsot egy új kiépítési feladat létrehozásához, amely a legújabb szolgáltatásjavításokat tartalmazza.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Az utolsó lépés eredményei között másolja a teljes "ID" karakterláncot, amely "scim" karakterrel kezdődik. Szükség esetén újra alkalmazhatja a régi attribútum-leképezéseket az alábbi parancs futtatásával, az [új-job-id] helyére az imént másolt új feladatazonosítóval, és beírja a JSON kimenetet a #7 lépésből a kérelem törzseként.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Térjen vissza az első webböngésző ablakba, és válassza az alkalmazás **kiépítés** lapját.
13. Ellenőrizze a konfigurációt, majd indítsa el a létesítési feladatot. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Hozzáadhatok egy új, nem katalógusalkalmazást, amely a régi felhasználói kiépítési viselkedéssel rendelkezik?

Igen. Ha egy alkalmazást a javítások előtt létező régi viselkedésre kódolt, és új példányát kell telepítenie, kövesse az alábbi eljárást. Ez az eljárás azt ismerteti, hogy miként hozhat létre a Microsoft Graph API és a Microsoft Graph API-kezelő a régi viselkedést mutató SCIM-kiépítési feladat létrehozásához.
 
1. Jelentkezzen be az https://portal.azure.comAzure Portalon.
2. az **Azure Active Directory > Enterprise Applications > Az** Azure Portal alkalmazás létrehozása szakaszában hozzon létre egy **új, nem katalógusalkalmazást.**
3. Az új egyéni alkalmazás **Tulajdonságok** szakaszában másolja az **Objektumazonosítót**.
4. Egy új böngészőablakban nyissa https://developer.microsoft.com/graph/graph-explorer meg, és jelentkezzen be rendszergazdaként az Azure AD-bérlő, ahol az alkalmazás hozzá van adva.
5. A Graph Explorerben futtassa az alábbi parancsot az alkalmazás létesítési konfigurációjának inicializálásához.
   Cserélje le az "[object-id]" szót a harmadik lépésből másolt egyszerű szolgáltatásazonosítóra (objektumazonosítóra).

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Térjen vissza az első webböngésző ablakba, és válassza az alkalmazás **kiépítés** lapját.
7. Végezze el a felhasználói kiépítési konfigurációt a szokásos módon.


## <a name="next-steps"></a>További lépések
[További információ a SaaS-alkalmazások kiépítéséről és a kiépítés ről](user-provisioning.md)

