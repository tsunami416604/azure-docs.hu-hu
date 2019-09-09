---
title: A hatókörön kívüli felhasználók törlésének kihagyása | Microsoft Docs
description: Megtudhatja, hogyan bírálhatja felül a hatókör felhasználói törlésének alapértelmezett viselkedését.
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
ms.openlocfilehash: a753d8cce3f3b610abab2f78d54d76a05d8bc5cb
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815984"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>A hatókörön kívüli felhasználói fiókok törlésének kihagyása

Alapértelmezés szerint az Azure AD-kiépítési motor törli vagy letiltja a hatókörön kívüli felhasználókat. Azonban előfordulhat, hogy bizonyos forgatókönyvek, például az AD-felhasználó bejövő üzembe helyezése esetén ez a viselkedés nem a várt, és érdemes lehet felülbírálni ezt az alapértelmezett viselkedést.  

Ez az útmutató ismerteti, hogyan használható a Microsoft Graph API és a Microsoft Graph API Explorer a hatókörön kívüli fiókok feldolgozását vezérlő ***SkipOutOfScopeDeletions*** beállításához. 
* Ha a ***SkipOutOfScopeDeletions*** 0 (hamis) értékre van állítva, akkor a hatókörön kívüli fiókok le lesznek tiltva a célhelyen
* Ha a ***SkipOutOfScopeDeletions*** értéke 1 (igaz), akkor a hatókörön kívüli fiókok nem lesznek letiltva a *kiépítési alkalmazás* szintjén, és a Graph API használatával konfigurálhatók. 

Mivel ezt a konfigurációt széles körben használják a *Munkanapokon Active Directory a felhasználók kiépítési* alkalmazásához, az alábbi lépésekben a munkanap alkalmazás képernyőképei szerepelnek. Ez azonban más kiépítési alkalmazásokkal is használható.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1\. lépés: A kiépítési App Service rendszerbiztonsági tag AZONOSÍTÓjának (objektumazonosító) beolvasása

1. Indítsa el a [Azure Portal](https://portal.azure.com), és navigáljon a kiépítési alkalmazás tulajdonságok szakaszába. Például ha a *munkanapokat az ad-felhasználók kiépítési alkalmazás-* hozzárendeléséhez szeretné exportálni, navigáljon az alkalmazás tulajdonságok szakaszába. 
1. A kiépítési alkalmazás tulajdonságok szakaszában másolja az *objektumazonosító* mezőhöz társított GUID értéket. Ezt az értéket az alkalmazás **ServicePrincipalId** is nevezik, és a Graph Explorer műveleteiben fogja használni.

   ![Munkanapok App Service résztvevő azonosítója](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2\. lépés: Bejelentkezés Microsoft Graph Explorerbe

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
1. Kattintson a "Bejelentkezés Microsofttal" gombra, és jelentkezzen be az Azure AD globális rendszergazdai vagy az alkalmazás-rendszergazdai hitelesítő adataival.

    ![Gráf bejelentkezés](./media/export-import-provisioning-mappings/wd_export_02.png)

1. A sikeres bejelentkezés után a bal oldali ablaktáblán megjelenik a felhasználói fiók adatai.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>3\. lépés: Meglévő alkalmazás hitelesítő adatainak és kapcsolati adatainak beolvasása

A Microsoft Graph Explorerben futtassa a következő GET lekérdezést a [servicePrincipalId] helyett az [1. lépésből](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)kinyert **servicePrincipalId** .

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

   ![Feladatok lekérdezésének beolvasása](./media/skip-out-of-scope-deletions/skip-03.png)

Másolja a választ egy szövegfájlba. Az alábbi JSON-szöveghez hasonlóan fog kinézni, amely az üzembe helyezéshez tartozó sárga színnel van kiemelve. Adja hozzá a zöld színnel jelölt vonalakat a végponthoz, és frissítse a munkanapokhoz való kapcsolódási jelszót kék színnel. 

   ![Válasz beolvasása](./media/skip-out-of-scope-deletions/skip-04.png)

Itt látható a leképezéshez hozzáadandó JSON-blokk. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>4\. lépés: A Secrets végpont frissítése a SkipOutOfScopeDeletions jelzővel

A Graph Explorerben futtassa az alábbi parancsot a Secrets végpont ***SkipOutOfScopeDeletions*** jelzővel való frissítéséhez. 

Az alábbi URL-címben cserélje le a [servicePrincipalId] elemet az [1. lépésből](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)kinyert **servicePrincipalId** . 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Másolja a 3. lépésben szereplő frissített szöveget a "kérés törzse" értékre, és állítsa be a "Content-Type" fejlécet "Application/JSON" értékre a "kérések fejléce" kifejezésben. 

   ![PUT kérelem](./media/skip-out-of-scope-deletions/skip-05.png)

Kattintson a "lekérdezés futtatása" gombra. 

A kimenetet "sikeres – állapotkód 204" értékre kell beolvasni. 

   ![Válasz elhelyezése](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>5\. lépés: Annak ellenőrzése, hogy a hatókörön kívüli felhasználók ne legyenek letiltva

Ezt a jelzőt tesztelheti a várt viselkedés alapján, ha frissíti a hatóköri szabályokat egy adott felhasználó kihagyásához. Az alábbi példában egy új hatókör-szabály hozzáadásával kizárja a 21173-as AZONOSÍTÓJÚ alkalmazottat (aki korábban a hatókörben volt): 

   ![Hatókör – példa](./media/skip-out-of-scope-deletions/skip-07.png)

A következő üzembe helyezési ciklusban az Azure AD kiépítési szolgáltatás megállapítja, hogy a 21173-es felhasználó kikerült a hatókörből, és ha a SkipOutOfScopeDeletions tulajdonság engedélyezve van, akkor az adott felhasználó szinkronizálási szabálya az alábbi képen látható üzenetet jeleníti meg: 

   ![Hatókör – példa](./media/skip-out-of-scope-deletions/skip-08.png)


