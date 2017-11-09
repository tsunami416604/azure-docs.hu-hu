---
title: "Ismerkedés az Azure Active Directory azonosító adatok védelmét és a Microsoft Graph |} Microsoft Docs"
description: "Bevezető a lekérdezés Microsoft Graph kockázati eseményekről és a kapcsolódó információk listáját az Azure Active Directoryból."
services: active-directory
keywords: "az Azure active directory azonosító adatok védelmét, a kockázat esemény, a biztonsági rés, a biztonsági házirendet, a Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 568cad4e394dfedddce1bfce66ddf627947d7568
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Ismerkedés az Azure Active Directory azonosító adatok védelmét és a Microsoft Graph
A Microsoft Graph a Microsoft unified API-végpont és az otthoni [Azure Active Directory Identity Protection](active-directory-identityprotection.md) API-k. Az első API **identityRiskEvents**, lehetővé teszi a Microsoft Graph listájának lekérdezési [kockázati események](active-directory-identityprotection-risk-events-types.md) és a kapcsolódó adatokat. Ez a cikk lekérdezi az API lekérdezése lépésekhez. Egy részletes bemutatása, teljes dokumentációját, és a Graph Explorer elérésére, tekintse meg a [Microsoft Graph hely](https://graph.microsoft.io/).

> [!IMPORTANT]
> A Microsoft javasolja, hogy az Azure Portalon található [Azure AD felügyeleti központból](https://aad.portal.azure.com) kezelje az Azure AD-t az ebben a cikkben javasolt klasszikus Azure portál helyett.

A Microsoft Graph el az azonosító adatok védelmét az adatok három lépésben történik:

1. Adja hozzá az alkalmazás egy ügyfélkulcsot. 
2. A Microsoft Graph, amikor egy hitelesítési jogkivonatot kap hitelesítéshez használ a titkos kulcs és néhány egyéb adatot. 
3. Ez a token használatával kéréseket küld az API-végpontot, és vissza Identity Protection-adatok beolvasása.

Mielőtt hozzáfogna, szüksége lesz:

* Az alkalmazás létrehozása az Azure AD rendszergazdai jogosultságokat
* A bérlői tartomány (például contoso.onmicrosoft.com) neve

## <a name="add-an-application-with-a-client-secret"></a>Egy ügyfél titokkal alkalmazás hozzáadása
1. [Jelentkezzen be a](https://manage.windowsazure.com) rendszergazdaként a klasszikus Azure portálra. 
2. A bal oldali navigációs panelén kattintson **Active Directory**. 
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
4. Kattintson a felső menüben **alkalmazások**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **a szerveztem által fejlesztett alkalmazás hozzáadása**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. Az a **adja meg azt az alkalmazás** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    a. Az a **neve** szövegmező, írja be az alkalmazás nevét (pl.: AADIP kockázat esemény API-alkalmazás).
   
    b. Mint **típus**, jelölje be **webalkalmazás és / vagy webes API**.
   
    c. Kattintson a **Tovább** gombra.
8. Az a **alkalmazás tulajdonságainak** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    a. Az a **bejelentkezési URL-cím** szövegmezőhöz típus `http://localhost`.
   
    b. Az a **App ID URI** szövegmezőhöz típus `http://localhost`.
   
    c. Kattintson a **Befejezés** gombra.

Ön most már konfigurálhatja az alkalmazást.

![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>Adja meg az alkalmazás számára az API-val
1. Az alkalmazás lapján, a felső menüben kattintson a **konfigurálása**. 
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. Az a **egyéb alkalmazások engedélyei** kattintson **alkalmazás hozzáadása**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. Az a **egyéb alkalmazások engedélyei** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    a. Válassza ki **Microsoft Graph**.
   
    b. Kattintson a **Befejezés** gombra.
4. Kattintson a **Alkalmazásengedélyek: 0**, majd válassza ki **olvassa el az összes identity kockázati események adatait**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. Kattintson a lap alján található **Mentés** gombra.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>Hívóbetű lekérése
1. Az alkalmazás lapon a a **kulcsok** területen válassza ki a 1 év, időtartam.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. Kattintson a lap alján található **Mentés** gombra.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. a kulcsok szakaszában másolja az újonnan létrehozott kulcs értékét, és illessze be egy biztonságos helyre.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > Ha elveszti a kulcsot, akkor ez a szakasz való visszatéréshez, és hozzon létre egy új kulcsot. Tartsa a kulcs titkos kulcs: bárki, aki hozzáférhet az adatokhoz.
   > 
   > 
4. Az a **tulajdonságok** szakaszban, másolja a **ügyfél-azonosító**, majd illessze be egy biztonságos helyre. 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Microsoft Graph hitelesítést és az identitás kockázati események API lekérdezése
Ezen a ponton rendelkeznie kell:

* A fenti másolt ügyfél-azonosító
* A fenti másolt kulcsot
* A bérlői tartomány neve

A hitelesítéshez a feladás egy vagy több vonatkozó kérés küldése `https://login.microsoft.com` törzsében a következő paraméterekkel:

* grant_type: "**client_credentials**"
* erőforrás: "**https://graph.microsoft.com**"
* client_id:<your client ID>
* client_secret:<your key>

> [!NOTE]
> Meg kell adni az értékeket a **client_id** és a **client_secret** paraméter.
> 
> 

Ha sikeres, ez egy hitelesítési jogkivonatot ad vissza.  
Hívja az API-t, hogy hozzon létre egy fejléc a következő paraméter:

    `Authorization`=”<token_type> <access_token>"


Hitelesítésekor található a jogkivonat típusa és a hozzáférési jogkivonat a visszaküldött jogkivonat.

Ezt a fejlécet a kérés küldése a következő API URL-címe:`https://graph.microsoft.com/beta/identityRiskEvents`

A válasz, ha sikeres, gyűjteménye identitás kockázati eseményekről és kapcsolódó adatait, amely elemzése és kezelése, ahogyan OData JSON formátumban.

Itt látható mintakód a hitelesítés és a Powershell használatával API felület meghívásakor.  
Az ügyfél-azonosító csak hozzáadása kulcs, és a bérlői tartományhoz.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Következő lépések
Gratulálunk, a Microsoft Graph első hívás most végzett!  
Most identitás kockázati események és igényei azonban az adatok felhasználásával.

A Microsoft Graph és hogyan hozhat létre alkalmazásokat a Graph API-val kapcsolatos további tudnivalókért tekintse meg a [dokumentáció](https://graph.microsoft.io/docs) és sok más, a a [Microsoft Graph hely](https://graph.microsoft.io/). Bizonyosodjon meg arról, hogy lássa el könyvjelzővel a [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) oldal, amely felsorolja a identitás védelmi API-k gráfban. Azt hozzáadja az új módon történő együttműködésre Identity Protection API-n keresztül, adott oldalon láthatja azokat.

## <a name="additional-resources"></a>További források
* [Az Azure Active Directory azonosító adatok védelmét](active-directory-identityprotection.md)
* [Azure Active Directory Identity Protection által észlelt kockázati események típusai](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [A Microsoft Graph áttekintése](https://graph.microsoft.io/docs)
* [Az Azure AD Identity Protection gyökerén](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

