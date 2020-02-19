---
title: Egyéni szabályzatok üzembe helyezése az Azure-folyamatokkal
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan helyezhet üzembe Azure AD B2C egyéni házirendeket CI/CD-folyamatokban az Azure DevOps Services Azure-folyamatainak használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 21fde69f404ee535bfe0019a91843297b1752a92
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463140"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Egyéni szabályzatok üzembe helyezése az Azure-folyamatokkal

Az [Azure-folyamatokban][devops-pipelines]beállított folyamatos integrációs és kézbesítési (CI/CD) folyamat használatával a szoftveres kézbesítés és a kód-vezérlés automatizálása során a Azure ad B2C egyéni szabályzatokat is megadhatja. A különböző Azure AD B2C környezetekben történő üzembe helyezéskor, például a fejlesztési, tesztelési és éles üzemi környezetben javasoljuk, hogy távolítsa el a manuális folyamatokat, és végezzen automatikus tesztelést az Azure-folyamatok használatával.

Három fő lépés szükséges ahhoz, hogy lehetővé tegye az Azure-folyamatok számára az egyéni házirendek felügyeletét Azure AD B2Con belül:

1. Webalkalmazás-regisztráció létrehozása a Azure AD B2C-bérlőben
1. Azure-tárház konfigurálása
1. Azure-folyamat konfigurálása

> [!IMPORTANT]
> Azure AD B2C egyéni szabályzatok Azure-folyamattal való kezelése jelenleg az Microsoft Graph API `/beta` végpontján elérhető **előzetes** műveleteket használja. Az API-k üzemi alkalmazásokban való használata nem támogatott. További információ: [Microsoft Graph REST API Beta-végpont referenciája](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Előfeltételek

* [Azure ad B2C bérlő](tutorial-create-tenant.md)és a címtárbeli felhasználó hitelesítő adatai a [B2C IEF házirend-rendszergazdai](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) szerepkörrel
* A bérlőre feltöltött [Egyéni szabályzatok](custom-policy-get-started.md)
* [Azure-folyamat](https://azure.microsoft.com/services/devops/pipelines/)és hozzáférés egy [Azure DevOps Services-projekthez][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Ügyfél-hitelesítő adatok megadása folyamat

Az itt leírt forgatókönyv az Azure-folyamatok és a Azure AD B2C közötti, a OAuth 2,0 [ügyfél-hitelesítő adatok engedélyezési folyamatát](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)használó szolgáltatások közötti hívások használatát teszi lehetővé. Ez a engedélyezési folyamat lehetővé teszi a webszolgáltatások, például az Azure-folyamatok (a bizalmas ügyfél) számára, hogy a saját hitelesítő adatait használják, ahelyett, hogy a felhasználó megszemélyesítését egy másik webszolgáltatás (ebben az esetben a Microsoft Graph API) hívásakor hitelesítse. Az Azure-folyamatok nem interaktív módon szereznek be tokent, majd kérelmeket tesznek a Microsoft Graph API-nak.

## <a name="register-an-application-for-management-tasks"></a>Alkalmazás regisztrálása felügyeleti feladatokhoz

Először hozzon létre egy alkalmazás-regisztrációt, amelyet az Azure-folyamatok által futtatott PowerShell-parancsfájlok a Azure AD B2C való kommunikációra használnak majd. Ha már rendelkezik az automatizálási feladatokhoz használt alkalmazás-regisztrációval, ugorjon az [engedélyek megadása](#grant-permissions) szakaszra.

### <a name="register-application"></a>Alkalmazás regisztrálása

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-permissions"></a>Engedélyek megadása

Ezután adja meg az alkalmazás számára, hogy a Microsoft Graph API használatával olvassa és írja be az egyéni házirendeket a Azure AD B2C-bérlőben.

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. A **regisztrált alkalmazás** áttekintése lapon válassza a **Beállítások**lehetőséget.
1. Az **API-hozzáférés**területen válassza a **szükséges engedélyek**lehetőséget.
1. Válassza a **Hozzáadás**lehetőséget, majd **válasszon ki egy API**-t.
1. Válassza a **Microsoft Graph**, majd a **lehetőséget**.
1. Az **alkalmazás engedélyei**területen válassza **a szervezeti megbízhatósági keretrendszer szabályzatának olvasása és írása**lehetőséget.
1. Válassza a **kiválasztás**, majd a **kész**lehetőséget.
1. Válassza az **engedélyek megadása**lehetőséget, majd válassza az **Igen**lehetőséget. Az engedélyek teljes propagálása eltarthat néhány percig.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza ki azt a webalkalmazást, amelynek hozzáféréssel kell rendelkeznie a Microsoft Graph API-hoz. Például: *managementapp1*.
1. A **kezelés**területen válassza az **API-engedélyek**lehetőséget.
1. A **konfigurált engedélyek**területen válassza **az engedély hozzáadása**elemet.
1. Válassza a **Microsoft API** -k fület, majd válassza a **Microsoft Graph**lehetőséget.
1. Válassza ki az **alkalmazás engedélyeit**.
1. Bontsa ki a **házirend** elemet, és válassza a **Policy. ReadWrite. TrustFramework**lehetőséget.
1. Válassza az **engedélyek hozzáadása**lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblép a következő lépésre.
1. Válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy olyan fiókkal a Azure AD B2C-bérlőben, amely legalább a *Cloud Application Administrator* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **frissítés**lehetőséget, majd ellenőrizze, hogy a "engedélyezve..." az **állapot**területen jelenik meg. Eltarthat néhány percig, amíg az engedélyek propagálása megtörténik.

* * *

### <a name="create-client-secret"></a>Ügyfél titkos kulcsának létrehozása

A Azure AD B2C való hitelesítéshez a PowerShell-parancsfájlnak meg kell adnia az alkalmazáshoz létrehozott ügyfél-titkos kulcsot.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-an-azure-repo"></a>Azure-tárház konfigurálása

Ha a felügyeleti alkalmazás regisztrálva van, készen áll a házirend-fájlok tárházának konfigurálására.

1. Jelentkezzen be az Azure DevOps Services-szervezetbe.
1. [Hozzon létre egy új projektet][devops-create-project] , vagy válasszon ki egy meglévő projektet.
1. A projektben navigáljon a **repók** lapra, és válassza a **fájlok** lapot. Válasszon ki egy meglévő tárházat, vagy hozzon létre egyet ehhez a gyakorlathoz.
1. Hozzon létre egy *B2CAssets*nevű mappát. Nevezze el a szükséges helyőrző fájlt *readme.MD* , és **véglegesítse** a fájlt. Ezt a fájlt később is eltávolíthatja, ha szeretné.
1. Adja hozzá Azure AD B2C házirend-fájljait a *B2CAssets* mappához. Ebbe beletartozik a *TrustFrameworkBase. XML*, a *TrustFrameWorkExtensions. XML*, a *SignUpOrSignin. XML*, a *ProfileEdit. XML*, a *PasswordReset. XML*és minden más létrehozott szabályzat. Jegyezze fel az egyes Azure AD B2C házirend-fájlok fájlnevét egy későbbi lépésben való használatra (PowerShell-parancsfájl argumentumaként használják).
1. Hozzon létre egy *Scripts* nevű mappát az adattár gyökérkönyvtárában, nevezze el a helyőrző *DeployToB2c. ps1*fájlt. Ne véglegesítse a fájlt ezen a ponton, egy későbbi lépésben.
1. Illessze be a következő PowerShell-szkriptet a *DeployToB2c. ps1*fájlba, majd **véglegesítse** a fájlt. A parancsfájl jogkivonatot kér az Azure AD-től, és meghívja a Microsoft Graph API-t, hogy feltöltse a szabályzatokat a *B2CAssets* mappában a Azure ad B2C bérlőre.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Az Azure-folyamat konfigurálása

A tárház inicializálása és az egyéni házirend-fájlok feltöltése után készen áll a kiadási folyamat beállítására.

### <a name="create-pipeline"></a>Folyamat létrehozása

1. Jelentkezzen be az Azure DevOps Services-szervezetbe, és navigáljon a projekthez.
1. A projektben válassza a **folyamatok** > **kiadások** > **új folyamat**elemet.
1. **A sablon kiválasztása**területen válassza az **üres feladatot**.
1. Adja meg a **fázis nevét**, például *DeployCustomPolicies*, majd a panel bezárásához.
1. Válassza **az összetevő hozzáadása**lehetőséget, majd a **forrás típusa**területen válassza az **Azure adattár**lehetőséget.
    1. Válassza ki a PowerShell-parancsfájllal feltöltött *parancsfájlok* mappát tartalmazó forrás tárházat.
    1. Válasszon ki egy **alapértelmezett ágat**. Ha az előző szakaszban létrehozott egy új tárházat, az alapértelmezett ág a *Master*.
    1. Hagyja meg a legutóbbi **alapértelmezett verzió** beállítását *az alapértelmezett ág alapján*.
    1. Adja meg a tárház **forrás-aliasát** . Például: *policyRepo*. Ne tartalmazzon szóközt az alias nevében.
1. Válassza a **Hozzáadás** lehetőséget
1. Nevezze át a folyamatot, hogy tükrözze a szándékát. Például az *egyéni házirend-folyamat telepítése*.
1. A folyamat konfigurációjának mentéséhez válassza a **Mentés** lehetőséget.

### <a name="configure-pipeline-variables"></a>Folyamat változóinak konfigurálása

1. Válassza a **változók** fület.
1. Adja hozzá a következő változókat a **folyamat változói** alatt, és állítsa be az értékeket a megadott módon:

    | Name (Név) | Érték |
    | ---- | ----- |
    | `clientId` | A korábban regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** . |
    | `clientSecret` | A korábban létrehozott **ügyfél-titok** értéke. <br /> Módosítsa a változó típusát **titkosra** (válassza a zárolás ikont). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, ahol *a-B2C-bérlő* a Azure ad B2C bérlő neve. |

1. A változók mentéséhez válassza a **Mentés** lehetőséget.

### <a name="add-pipeline-tasks"></a>Folyamattal kapcsolatos feladatok hozzáadása

Ezután adjon hozzá egy feladatot a házirendfájl telepítéséhez.

1. Válassza a **feladatok** fület.
1. Válassza az **ügynök feladata**lehetőséget, majd válassza ki a pluszjelet ( **+** ) egy feladat hozzáadásához az ügynök feladatához.
1. Keresse meg és válassza ki a **PowerShellt**. Ne válassza a "Azure PowerShell," "PowerShell a célszámítógépen" vagy egy másik PowerShell-bejegyzést.
1. Válassza ki az újonnan hozzáadott **PowerShell-parancsfájl** feladatot.
1. Adja meg a következő értékeket a PowerShell-parancsfájl feladatához:
    * **Feladat verziója**: 2. *
    * **Megjelenítendő név**: annak a szabályzatnak a neve, amelyet a feladatnak fel kell töltenie. Például *B2C_1A_TrustFrameworkBase*.
    * **Típus**: fájl elérési útja
    * **Parancsfájl elérési útja**: válassza a három pontot (***..***.), keresse meg a *parancsfájlok* mappát, majd válassza ki a *DeployToB2C. ps1* fájlt.
    * **Argumentumok**

        Adja meg a következő értékeket az **argumentumokhoz**. Cserélje le a `{alias-name}`t az előző szakaszban megadott aliasra.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Ha például a megadott alias *policyRepo*, az argumentumnak a következőnek kell lennie:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/contosob2cpolicies/B2CAssets/TrustFrameworkBase.xml
        ```

1. Válassza a **Mentés** lehetőséget az ügynök feladatainak mentéséhez.

Az imént hozzáadott feladat feltölt *egy* házirend-fájlt Azure ad B2Cba. A továbblépés előtt manuálisan aktiválja a feladatot (**kiadás létrehozása**), és győződjön meg arról, hogy a feladat sikeresen befejeződik, mielőtt további feladatokat hozna létre.

Ha a feladat sikeresen befejeződik, adja hozzá a telepítési feladatokat az egyes egyéni házirend-fájlok előző lépéseinek végrehajtásával. Módosítsa a `-PolicyId` és `-PathToFile` argumentum értékeit az egyes házirendekhez.

A `PolicyId` a TrustFrameworkPolicy csomóponton belül egy XML-házirend fájljának elején található érték. A következő házirend-XML-ben szereplő `PolicyId` például *B2C_1A_TrustFrameworkBase*:

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Az ügynökök futtatásakor és a házirend-fájlok feltöltésekor ügyeljen arra, hogy a következő sorrendben töltse fel őket:

1. *TrustFrameworkBase. XML*
1. *TrustFrameworkExtensions. XML*
1. *SignUpOrSignin. XML*
1. *ProfileEdit. XML*
1. *PasswordReset. XML*

Az Identity Experience Framework ezt a sorrendet kényszeríti, mivel a fájl szerkezete hierarchikus láncra épül.

## <a name="test-your-pipeline"></a>A folyamat tesztelése

A kiadási folyamat tesztelése:

1. Válassza a **folyamatok** , majd a **kiadások**lehetőséget.
1. Válassza ki a korábban létrehozott folyamatot, például *DeployCustomPolicies*.
1. Válassza a **kiadás létrehozása**elemet, majd válassza a **Létrehozás** lehetőséget a kiadás várólistára állításához.

Egy értesítési szalagcím jelenik meg, amely szerint a kiadás várólistára került. Az állapot megtekintéséhez válassza ki a hivatkozást az értesítési szalagcímben, vagy válassza ki a listából a **kiadások** lapon.

## <a name="next-steps"></a>Következő lépések

További információk:

* [Ügyfél-hitelesítő adatokat használó szolgáltatások közötti hívások](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps-szolgáltatások](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines