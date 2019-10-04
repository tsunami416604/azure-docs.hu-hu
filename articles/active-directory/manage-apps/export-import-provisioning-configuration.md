---
title: Kiépítési konfiguráció exportálása vagy importálása Graph API használatával | Microsoft Docs
description: Megtudhatja, hogyan exportálhatja és importálhatja az üzembe helyezési konfigurációt Graph API használatával.
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
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3e92ee5ffd97174331703b703e811bd1ce5f43
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815841"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Kiépítési konfiguráció exportálása vagy importálása Graph API használatával

A Microsoft Graph API és a Graph Explorer használatával exportálhatja a felhasználók kiépítési attribútumait egy JSON-fájlba, majd importálhatja azt az Azure AD-be. Az itt rögzített lépéseket az üzembe helyezési konfiguráció biztonsági másolatának létrehozásához is használhatja. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1\. lépés: A kiépítési App Service rendszerbiztonsági tag AZONOSÍTÓjának (objektumazonosító) beolvasása

1. Indítsa el a [Azure Portal](https://portal.azure.com), és navigáljon a kiépítési alkalmazás tulajdonságok szakaszába. Például ha a *munkanapokat az ad-felhasználók kiépítési alkalmazás-* hozzárendeléséhez szeretné exportálni, navigáljon az alkalmazás tulajdonságok szakaszába. 
1. A kiépítési alkalmazás tulajdonságok szakaszában másolja az *objektumazonosító* mezőhöz társított GUID értéket. Ezt az értéket az alkalmazás **ServicePrincipalId** is nevezik, és a Graph Explorer műveleteiben fogja használni.

   ![Munkanapok App Service résztvevő azonosítója](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2\. lépés: Bejelentkezés Microsoft Graph Explorerbe

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
1. Kattintson a "Bejelentkezés Microsofttal" gombra, és jelentkezzen be az Azure AD globális rendszergazdai vagy az alkalmazás-rendszergazdai hitelesítő adataival.

    ![Gráf bejelentkezés](./media/export-import-provisioning-mappings/wd_export_02.png)

1. A sikeres bejelentkezés után a bal oldali ablaktáblán megjelenik a felhasználói fiók adatai.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3\. lépés: Kiépítési feladatok AZONOSÍTÓjának beolvasása az üzembe helyezési alkalmazáshoz

A Microsoft Graph Explorerben futtassa a következő GET lekérdezést a [servicePrincipalId] helyett az [1. lépésből](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)kinyert **servicePrincipalId** .

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Az alább látható válasz jelenik meg. Másolja az "id" attribútumot a válaszban. Ez az érték a **ProvisioningJobId** , és a rendszer az alapul szolgáló séma metaadatainak beolvasására szolgál.

   [![Kiépítési feladatok azonosítója](./media/export-import-provisioning-mappings/wd_export_03.png)](./media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>4\. lépés: A kiépítési séma letöltése

A Microsoft Graph Explorerben futtassa a következő GET lekérdezést, és cserélje le a [servicePrincipalId] és a [ProvisioningJobId] kifejezést az előző lépésben lekért ServicePrincipalId és ProvisioningJobId.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Másolja a JSON-objektumot a válaszból, és mentse fájlba a séma biztonsági másolatának létrehozásához.

## <a name="step-5-import-the-provisioning-schema"></a>5\. lépés: A kiépítési séma importálása

> [!CAUTION]
> Ezt a lépést csak akkor hajtsa végre, ha módosítania kell a sémát olyan konfigurációhoz, amely nem módosítható a Azure Portal használatával, vagy ha egy korábban mentett fájl konfigurációját egy érvényes és működő sémával kell visszaállítani.

A Microsoft Graph Explorerben konfigurálja a következő PUT lekérdezést, és cserélje le a [servicePrincipalId] és a [ProvisioningJobId] kifejezést az előző lépésben lekért ServicePrincipalId és ProvisioningJobId.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

A "kérelem törzse" lapon másolja át a JSON-séma fájljának tartalmát.

   [![Kérelem törzse](./media/export-import-provisioning-mappings/wd_export_04.png)](./media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

Az "igénylési fejlécek" lapon adja hozzá a Content-Type fejléc attribútumot az "Application/JSON" értékkel.

   [![Kérések fejlécei](./media/export-import-provisioning-mappings/wd_export_05.png)](./media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Kattintson a "lekérdezés futtatása" gombra az új séma importálásához.
