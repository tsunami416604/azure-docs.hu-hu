---
title: SCIM 2.0 protokoll megfelelőség az Azure AD-felhasználó kiépítési szolgáltatás ismert problémák és megoldásuk |} A Microsoft Docs
description: Egy, amely támogatja az SCIM 2.0 az Azure AD katalógusban nem szereplő alkalmazás hozzáadása során szembesülnek közös protokollt kompatibilitási problémák megoldása
services: active-directory
documentationcenter: ''
author: asmalser
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asmalser
ms.openlocfilehash: d67132989910434cd6b5d178ee44425992525496
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475951"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Ismert problémák és megoldásaik az SCIM 2.0 protokoll megfelelőség az Azure AD-felhasználó kiépítési szolgáltatás

Az Azure Active Directory (Azure AD) automatikusan létesítsen felhasználók és csoportok bármilyen alkalmazás vagy a rendszer, amely egy webszolgáltatás felhasználói felülettel fronted van definiálva a [rendszer tartományok közötti Identity Management (SCIM) 2.0-s protokoll specifikáció](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Az SCIM 2.0 protokoll támogatása az Azure AD leírt [használatával a rendszer a tartományok közötti Identity Management (SCIM) automatikus kiépítésére a felhasználók és csoportok alkalmazásokhoz az Azure Active Directoryból](use-scim-to-provision-users-and-groups.md), amely felsorolja a a protokoll, amely megvalósítja az annak érdekében, hogy a felhasználók és csoportok Azure AD-ből, amelyek támogatják az SCIM 2.0 alkalmazások automatikusan üzembe meghatározott részeit.

Ez a cikk a kiépítési szolgáltatás megfeleljen az SCIM 2.0 protokollt, és hogyan működnek ezek a hibák elhárítása Azure AD-felhasználót az aktuális és korábbi kapcsolatos problémákat ismerteti.

> [!IMPORTANT]
> Az Azure AD felhasználói kiépítési szolgáltatás SCIM ügyfélre a legújabb frissítés történt a 2018. December 18. Ez a frissítés az alábbi táblázatban felsorolt ismert kompatibilitási problémákkal foglalkozik. További információt a frissítés alatt a gyakori kérdések listája.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 megfelelőségi problémák megoldása és állapota

| **SCIM 2.0 házirendnek való megfeleléssel** |  **Rögzített?** | **Javítsa ki a dátum**  |  
|---|---|---|
| Az Azure AD szükséges "/ scim" kell lennie az alkalmazás által az SCIM-végpont URL-címe  | Igen  |  2018. december 18. | 
| A bővítményattribútumok használata pont "."jelöléssel attribútum neve helyett kettőspont előtt":" jelöléssel |  Igen  | 2018. december 18.  | 
|  A Patch kéréseknek többértékű attribútumok érvénytelen elérési út szűrési szintaxist tartalmaz | Igen  |  2018. december 18.  | 
|  Létrehozási kérelmek csoportosítása tartalmaz egy érvénytelen séma URI | Igen  |  2018. december 18.  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>A szolgáltatások javítások leírt automatikusan alkalmazza a már meglévő SCIM alkalmazásomat?

Nem. Ahogy azt kellene alkotnak, az SCIM-alkalmazásoknak a régebbi működése működéséhez kódolni is használhatatlanná tévő változást, a módosítások lettek nem alkalmazza automatikusan a meglévő alkalmazások.

A módosítások érvénybe lépnek minden új [katalógusban nem szereplő SCIM alkalmazások](configure-single-sign-on-non-gallery-applications.md) a javítást követően az Azure Portalon konfigurálni.

Hogyan telepíthet át egy meglévő felhasználó fürtkiépítési feladat tartalmazza a legújabb javításokkal információkért lásd: a következő szakaszban.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Áttelepíthetek egy meglévő SCIM-alapú felhasználó fürtkiépítési feladat a szolgáltatás legújabb javításokkal felvenni?

Igen. Ha már használja az alkalmazáspéldány egyszeri bejelentkezést, és szeretne áttelepíteni a meglévő üzembe helyezési feladat tartalmazza a legújabb javításokat, kövesse az alábbi eljárást. Ez az eljárás ismerteti, hogyan használhatja a Microsoft Graph API és a Microsoft Graph API explorer távolítsa el a régi létesítési feladat a meglévő SCIM-alkalmazást, és hozzon létre egy újat, amely az új viselkedés érvényes.

> [!NOTE]
> Ha az alkalmazás még fejlesztés alatt áll, és egyszeri bejelentkezés vagy a felhasználók átadásának még nem vezette, a legegyszerűbb megoldás, ami törli az alkalmazás bejegyzést a **Azure Active Directory > Vállalati alkalmazások**szakaszában az Azure Portalon, és egyszerűen adjon hozzá egy új bejegyzést az alkalmazás használatával a **-alkalmazás létrehozása > katalógusban nem szereplő** lehetőséget. Ez az alábbi eljárás futtatása helyett használhatók.
 
1. Jelentkezzen be az Azure Portalra a https://portal.azure.com.
2. Az a **Azure Active Directory > Vállalati alkalmazások** szakaszban az Azure Portalon keresse meg és válassza ki a meglévő SCIM-alkalmazást.
3.  Az a **tulajdonságok** szakaszában a meglévő SCIM-alkalmazás, példány a **Objektumazonosító**.
4.  Egy új böngészőablakban, lépjen a https://developer.microsoft.com/en-us/graph/graph-explorer , és jelentkezzen be az Azure AD-bérlővel, ahol az alkalmazás bekerül a rendszergazdájaként.
5. A Graph Explorer keresse meg az üzembe helyezési feladat Azonosítóját az alábbi parancs futtatásával. Cserélje le a szolgáltatást a harmadik lépésben másolt résztvevő-azonosító (objektumazonosító:) "[object-id]".
 
 `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

 ![Első feladatok](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "feladatok beolvasása") 


6. Az eredmények között másolja a teljes "ID" karakterlánccal kezdődik "customappsso" vagy "scim".
7. Futtassa az alábbi parancsot a attribútumleképezés konfiguráció, lekérése érdekében, hogy biztonsági mentést. A azonos [-objektumazonosító], mielőtt használja, és cserélje le az előző lépésben másolt kiépítési Feladatazonosítót [feladatazonosító].
 
 `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
 ![Séma beolvasása](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "séma beolvasása") 

8. Másolja az utolsó lépés a JSON-kimenetet, és mentse egy szövegfájlba. Ez tartalmazza a bármely egyéni attribútum-leképezések, hogy a régi alkalmazáshoz hozzáadott, és körülbelül több ezer sornyi JSON kell lennie.
9. Futtassa az alábbi parancsot az üzembe helyezési feladat törléséhez:
 
 `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Futtassa az alábbi parancsot egy új létesítési feladat létrehozása, amely rendelkezik a szolgáltatás legújabb javításokkal.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs `
 `{   templateId: "scim"   } `
   
11. Az utolsó lépés az eredmények között másolja a teljes "ID" karakterlánccal kezdődik "scim". Igény szerint alkalmazza újra a régi attribútumleképezések az alábbi [Új – feladat-id] cserélje le az imént másolt új feladat azonosítója, és írja be a JSON kimenete kérelemtörzsként való #7. lépés: a parancs futtatásával.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema `
 `{   <your-schema-json-here>   }`

12. Az első böngészőablakban adja vissza, és válassza ki a **kiépítési** az alkalmazás lapon.
13. Ellenőrizze a konfigurációt, és indítsa el az üzembe helyezési feladat. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Adhat hozzá egy új katalógusban nem szereplő alkalmazást, amely rendelkezik a régi felhasználói viselkedés kiépítés?

Igen. Ha az alkalmazás régi viselkedés előtti a javításokat, és üzembe helyezése egy új példányát kell kellett kódolni, kövesse az alábbi utasításokat. Ez az eljárás ismerteti, hogyan lehet egy, a régi viselkedés érvényes kiépítési SCIM-feladat létrehozása a Microsoft Graph API és a Microsoft Graph API explorer használatával.
 
1.  Jelentkezzen be az Azure Portalra a https://portal.azure.com.
2. az a **Azure Active Directory > Vállalati alkalmazások > alkalmazás létrehozása** szakaszban az Azure Portal, hozzon létre egy új **katalógusban nem szereplő** alkalmazás.
3.  Az a **tulajdonságok** az új egyéni alkalmazás, példány szakaszában a **Objektumazonosító**.
4.  Egy új böngészőablakban, lépjen a https://developer.microsoft.com/en-us/graph/graph-explorer , és jelentkezzen be az Azure AD-bérlővel, ahol az alkalmazás bekerül a rendszergazdájaként.
5. A Graph Explorer futtassa az alábbi parancsot az alkalmazás kiépítési konfigurációjának inicializálása.
Cserélje le a szolgáltatást a harmadik lépésben másolt résztvevő-azonosító (objektumazonosító:) "[object-id]".

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "customappsso"   }`
 
6. Az első böngészőablakban adja vissza, és válassza ki a **kiépítési** az alkalmazás lapon.
7. Fejezze be a felhasználó konfigurációs kiépítése, ahogy azt szokásosan tenné.


## <a name="next-steps"></a>További lépések
[További információ az üzembe helyezést és megszüntetést SaaS-alkalmazásokhoz](user-provisioning.md)

