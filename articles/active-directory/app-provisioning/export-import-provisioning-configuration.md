---
title: Exportálja a létesítési konfigurációt, és állítsa vissza egy ismert jó állapotba a vész-helyreállításhoz.| Microsoft dokumentumok
description: Ismerje meg, hogyan exportálhatja a létesítési konfigurációt, és állítsa vissza egy ismert jó állapotba vész-helyreállítási.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051314"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>A létesítési konfiguráció exportálása és az ismert jó állapotvisszaállítása

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>A kiépítési konfiguráció exportálása és importálása az Azure Portalról

### <a name="how-can-i-export-my-provisioning-configuration"></a>Hogyan exportálhatom a kiépítési konfigurációmat?
A konfiguráció exportálása:
1. Az [Azure Portalon](https://portal.azure.com/)a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.
2. Az **Azure Active Directory** ablaktáblában válassza a **Nagyvállalati alkalmazások** lehetőséget, és válassza ki az alkalmazást.
3. A bal oldali navigációs ablakban válassza a **kiépítés lehetőséget.** A kiépítési konfigurációs lapon kattintson **az attribútumleképezések**elemre, majd **mutassa meg a speciális beállításokat,** és végül **tekintse át a sémát.** Ez elviszi a séma szerkesztő. 
5. Kattintson a letöltésre az oldal tetején található parancssávon a séma letöltéséhez.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Katasztrófa utáni helyreállítás - visszaállítva egy ismert jó állapotba
A konfiguráció exportálása és mentése lehetővé teszi a konfiguráció egy korábbi verziójának visszaállítását. Javasoljuk, hogy exportálja a létesítési konfigurációt, és mentse későbbi használatra, amikor módosítja az attribútum-hozzárendeléseket vagy a hatókörszűrőket. Mindössze annyit kell tennie, hogy megnyitja a fenti lépésekben letöltött JSON fájlt, másolja a JSON fájl teljes tartalmát, cserélje le a JSON tartalom teljes tartalmát a sémaszerkesztőben, majd mentse. Ha van egy aktív kiépítési ciklus, befejeződik, és a következő ciklus a frissített sémát fogja használni. A következő ciklus egy kezdeti ciklus is lesz, amely minden felhasználót és csoportot újraértékel az új konfiguráció alapján. Az előző konfigurációra való visszaállításkor vegye figyelembe a következőket:
* A rendszer újra kiértékeli a felhasználókat, hogy megállapítsa, hatókörüknek kell-e lennie. Ha a hatókörszűrők megváltoztak, a felhasználó már nincs hatókörben, le lesztiltva. Bár ez a kívánt viselkedés a legtöbb esetben, vannak esetek, amikor érdemes megakadályozni ezt, és használhatja a [skip out of scope törlések](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) funkciót. 
* A létesítési konfiguráció módosítása újraindítja a szolgáltatást, és elindítja a [kezdeti ciklust.](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental)


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>A kiépítési konfiguráció exportálása és importálása a Microsoft Graph API használatával
A Microsoft Graph API és a Microsoft Graph Explorer segítségével exportálhatja a felhasználói kiépítési attribútum-hozzárendeléseket és sémát egy JSON-fájlba, és importálhatja azt az Azure AD-be. Az itt rögzített lépések segítségével biztonsági másolatot készíthet a létesítési konfigurációról. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1. lépés: A kiépítési alkalmazás egyszerű szolgáltatásazonosítójának (objektumazonosító) lekérése

1. Indítsa el az [Azure Portalt](https://portal.azure.com), és keresse meg a kiépítési alkalmazás Tulajdonságok szakaszát. Ha például a Workday-t az *AD-felhasználó kiépítési alkalmazásleképezési alkalmazásba* szeretné exportálni, keresse meg az alkalmazás Tulajdonságok szakaszát. 
1. A kiépítési alkalmazás Tulajdonságok szakaszábamásolja az *Objektumazonosító* mezőhöz társított GUID értéket. Ezt az értéket az alkalmazás **ServicePrincipalId-jának** is nevezik, és a Microsoft Graph Explorer műveleteiben fogja használni.

   ![Workday alkalmazásszolgáltatás egyszerű azonosítója](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>2. lépés: Bejelentkezés a Microsoft Graph Explorer programba

1. A [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
1. Kattintson a "Bejelentkezés a Microsofttal" gombra, és jelentkezzen be az Azure AD globális rendszergazdai vagy alkalmazásfelügyeleti hitelesítő adataival.

    ![Microsoft Graph bejelentkezés](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Sikeres bejelentkezés után a felhasználói fiók részletei a bal oldali ablaktáblában jelennek meg.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3. lépés: A kiépítési feladat azonosítójának lekérése a kiépítési alkalmazásból

A Microsoft Graph Explorer programban futtassa a következő GET-lekérdezést, amely a [servicePrincipalId] helyére **az** [1.](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Választ kap az alábbiak szerint. Másolja a válaszban található "id attribútumot". Ez az érték a **ProvisioningJobId,** és az alapul szolgáló séma metaadatok beolvasására szolgál.

   [![Kiépítési feladat azonosítója](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>4. lépés: A kiépítési séma letöltése

A Microsoft Graph Explorerben futtassa a következő GET-lekérdezést, és cserélje le a [servicePrincipalId] és a [ProvisioningJobId] programot a ServicePrincipalId és az előző lépésekben beolvasott ProvisioningJobId helyett.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Másolja a JSON-objektumot a válaszból, és mentse egy fájlba a séma biztonsági másolatának létrehozásához.

### <a name="step-5-import-the-provisioning-schema"></a>5. lépés: A kiépítési séma importálása

> [!CAUTION]
> Ezt a lépést csak akkor hajtsa végre, ha módosítania kell a konfigurációs sémát, amely nem módosítható az Azure Portalhasználatával, vagy ha vissza kell állítania a konfigurációt egy korábban érvényes és működő sémával rendelkező biztonsági másolatot.

A Microsoft Graph Explorer konfigurálja a következő PUT lekérdezést, a [servicePrincipalId] és a [ProvisioningJobId] helyett a ServicePrincipalId és a ProvisioningJobId az előző lépésekben beolvasott.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

A "Kérelem törzse" lapon másolja a JSON-sémafájl tartalmát.

   [![A kérelem törzse](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

A "Fejlécek kérése" lapon adja hozzá a Content-Type fejléc attribútumot "application/json" értékkel.

   [![Fejlécek kérése](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Az új séma importálásához kattintson a "Lekérdezés futtatása" gombra.
