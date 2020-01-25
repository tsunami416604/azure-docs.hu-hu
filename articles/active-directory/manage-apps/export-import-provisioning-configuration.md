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
ms.openlocfilehash: 609031bfad23a14a954a09a447e363e89a9d29d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711744"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Kiépítési konfiguráció exportálása vagy importálása Graph API használatával

A Microsoft Graph API és a Graph Explorer használatával exportálhatja a felhasználók kiépítési attribútumait egy JSON-fájlba, majd importálhatja azt az Azure AD-be. Az itt rögzített lépéseket az üzembe helyezési konfiguráció biztonsági másolatának létrehozásához is használhatja. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1\. lépés: a kiépítési App Service rendszerbiztonsági tag AZONOSÍTÓjának beolvasása (objektumazonosító)

1. Indítsa el a [Azure Portal](https://portal.azure.com), és navigáljon a kiépítési alkalmazás tulajdonságok szakaszába. Például ha a *munkanapokat az ad-felhasználók kiépítési alkalmazás-* hozzárendeléséhez szeretné exportálni, navigáljon az alkalmazás tulajdonságok szakaszába. 
1. A kiépítési alkalmazás tulajdonságok szakaszában másolja az *objektumazonosító* mezőhöz társított GUID értéket. Ezt az értéket az alkalmazás **ServicePrincipalId** is nevezik, és a Graph Explorer műveleteiben fogja használni.

   ![Munkanapok App Service résztvevő azonosítója](media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2\. lépés: bejelentkezés Microsoft Graph Explorerbe

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
1. Kattintson a "Bejelentkezés Microsofttal" gombra, és jelentkezzen be az Azure AD globális rendszergazdai vagy az alkalmazás-rendszergazdai hitelesítő adataival.

    ![Gráf bejelentkezés](media/export-import-provisioning-mappings/wd_export_02.png)

1. A sikeres bejelentkezés után a bal oldali ablaktáblán megjelenik a felhasználói fiók adatai.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3\. lépés: a kiépítési alkalmazáshoz tartozó kiépítési feladatok AZONOSÍTÓjának beolvasása

A Microsoft Graph Explorerben futtassa a következő GET lekérdezést a [servicePrincipalId] helyett az [1. lépésből](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)kinyert **servicePrincipalId** .

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Az alább látható válasz jelenik meg. Másolja az "id" attribútumot a válaszban. Ez az érték a **ProvisioningJobId** , és a rendszer az alapul szolgáló séma metaadatainak beolvasására szolgál.

   [![kiépítési feladatot azonosító](media/export-import-provisioning-mappings/wd_export_03.png)](media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>4\. lépés: a kiépítési séma letöltése

A Microsoft Graph Explorerben futtassa a következő GET lekérdezést, és cserélje le a [servicePrincipalId] és a [ProvisioningJobId] kifejezést az előző lépésben lekért ServicePrincipalId és ProvisioningJobId.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Másolja a JSON-objektumot a válaszból, és mentse fájlba a séma biztonsági másolatának létrehozásához.

## <a name="step-5-import-the-provisioning-schema"></a>5\. lépés: a kiépítési séma importálása

> [!CAUTION]
> Ezt a lépést csak akkor hajtsa végre, ha módosítania kell a sémát olyan konfigurációhoz, amely nem módosítható a Azure Portal használatával, vagy ha egy korábban mentett fájl konfigurációját egy érvényes és működő sémával kell visszaállítani.

A Microsoft Graph Explorerben konfigurálja a következő PUT lekérdezést, és cserélje le a [servicePrincipalId] és a [ProvisioningJobId] kifejezést az előző lépésben lekért ServicePrincipalId és ProvisioningJobId.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

A "kérelem törzse" lapon másolja át a JSON-séma fájljának tartalmát.

   [![kérelem törzse](media/export-import-provisioning-mappings/wd_export_04.png)](media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

Az "igénylési fejlécek" lapon adja hozzá a Content-Type fejléc attribútumot az "Application/JSON" értékkel.

   [![kérések fejlécei](media/export-import-provisioning-mappings/wd_export_05.png)](media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Kattintson a "lekérdezés futtatása" gombra az új séma importálásához.
