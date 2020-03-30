---
title: Hatókören kívüli felhasználók törlésének kihagyása | Microsoft dokumentumok
description: Ismerje meg, hogyan bírálhatja felül a hatókörből kieső felhasználókon kívüli kiépítés alapértelmezett viselkedését.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522449"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>A hatókörön kívül eső felhasználói fiókok törlésének kihagyása

Alapértelmezés szerint az Azure AD kiépítési motor ideiglenesen törli vagy letiltja a hatókörön kívül eső felhasználókat. Bizonyos esetekben azonban, mint a Workday az AD-felhasználó bejövő kiépítése ez a viselkedés nem lehet a várt, és érdemes felülírni ezt az alapértelmezett viselkedést.  

Ez az útmutató bemutatja, hogyan használhatja a Microsoft Graph API-t és a Microsoft Graph API-kezelőt a ***SkipOutOfScopeDeletions*** jelző beállításához, amely a hatókörön kívül eső fiókok feldolgozását szabályozza. 
* Ha ***a SkipOutOfScopeDeletions*** értéke 0 (hamis), akkor a hatókörön kívül eső fiókok le lesznek tiltva a célban
* Ha ***skipOutOfScopeDeletions*** van beállítva 1 (igaz), majd a fiókok, amelyek kikerülnek a hatókörnem lesz letiltva a cél Ez a jelző van beállítva a *kiépítési alkalmazás* szintjén, és konfigurálható a Graph API használatával. 

Mivel ez a konfiguráció széles körben használják a *Workday az Active Directory felhasználói kiépítési* alkalmazás, az alábbi lépések közé képernyőképek a Workday alkalmazás. Azonban ez is használható **az összes többi alkalmazással,** mint például (ServiceNow, Salesforce, Dropbox stb.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1. lépés: A kiépítési alkalmazás egyszerű szolgáltatásazonosítójának (objektumazonosító) lekérése

1. Indítsa el az [Azure Portalt](https://portal.azure.com), és keresse meg a kiépítési alkalmazás Tulajdonságok szakaszát. Például ha a *Workday-t a Workday alkalmazáskiépítési alkalmazásleképezésbe* szeretné exportálni, keresse meg az alkalmazás Tulajdonságok szakaszát. 
1. A kiépítési alkalmazás Tulajdonságok szakaszábamásolja az *Objektumazonosító* mezőhöz társított GUID értéket. Ezt az értéket az alkalmazás **ServicePrincipalId-jának** is nevezik, és a Graph Explorer műveleteiben fogja használni.

   ![Workday alkalmazásszolgáltatás egyszerű azonosítója](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2. lépés: Bejelentkezés a Microsoft Graph Explorer programba

1. A [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
1. Kattintson a "Bejelentkezés a Microsofttal" gombra, és jelentkezzen be az Azure AD globális rendszergazdai vagy alkalmazásfelügyeleti hitelesítő adataival.

    ![Graph bejelentkezés](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Sikeres bejelentkezés után a felhasználói fiók részletei a bal oldali ablaktáblában jelennek meg.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>3. lépés: Meglévő alkalmazás-hitelesítő adatok és kapcsolódási részletek beszerezni

A Microsoft Graph Explorer programban futtassa a következő GET-lekérdezést, amely a [servicePrincipalId] helyére **az** [1.](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Feladatlekérdezés beszése](./media/skip-out-of-scope-deletions/skip-03.png)

Másolja a választ egy szöveges fájlba. Úgy fog kinézni, mint az alábbi JSON-szöveg, a központi telepítésre jellemző sárga színnel kiemelve lévő értékekkel. Adja hozzá a zöldszínnel kiemelt vonalakat a végére, és frissítse a kék színnel kiemelt Workday-kapcsolat jelszavát. 

   ![Get állásválasz](./media/skip-out-of-scope-deletions/skip-04.png)

Itt van a JSON blokk hozzáadni a leképezéshez. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>4. lépés: Frissítse a titkos kulcsok végpontját a SkipOutOfScopeDeletions jelzővel

A Graph Explorer, futtassa az alábbi parancsot, hogy frissítse a titkos kulcsok végponta a ***SkipOutOfScopeDeletions*** jelző. 

Az alábbi URL-címben cserélje le a [servicePrincipalId] elemet **az** [1.](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Másolja a frissített szöveget a 3. 

   ![PUT kérelem](./media/skip-out-of-scope-deletions/skip-05.png)

Kattintson a "Lekérdezés futtatása" gombra. 

Önnek kellene kap a termelés mint " siker – helyzet Kód 204". 

   ![PUT válasz](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>5. lépés: Ellenőrizze, hogy a hatókörön kívül a felhasználók nem lesznek letiltva

Tesztelheti ezt a jelzőt a várt viselkedés, ha frissíti a hatókörszabályokat, hogy kihagyja egy adott felhasználó. Az alábbi példában a 21173-as azonosítóval rendelkező alkalmazottat (aki korábban hatókörben volt) egy új hatóköri szabály hozzáadásával kizárjuk: 

   ![Példa hatókör-hatókörre](./media/skip-out-of-scope-deletions/skip-07.png)

A következő kiépítési ciklusban az Azure AD létesítési szolgáltatás azonosítja, hogy a felhasználó 21173 kikerült a hatókör, és ha a SkipOutOfScopeDeletions tulajdonság engedélyezve van, majd a szinkronizálási szabály az adott felhasználó megjelenik egy üzenet az alábbiak szerint: 

   ![Példa hatókör-hatókörre](./media/skip-out-of-scope-deletions/skip-08.png)


