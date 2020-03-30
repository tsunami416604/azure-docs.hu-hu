---
title: Egyéni szabályzatok üzembe helyezése az Azure-folyamatok használatával
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan telepítheti az Azure AD B2C egyéni szabályzatokat egy CI/CD-folyamatban az Azure-folyamatok azure DevOps-szolgáltatásokban való használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188749"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Egyéni szabályzatok üzembe helyezése az Azure-folyamatok használatával

Az [Azure Pipelines-ban][devops-pipelines]beállított folyamatos integrációs és kézbesítési (CI/CD) folyamat használatával az Azure AD B2C egyéni szabályzatait is felveheti a szoftverkézbesítésbe és a kódvezérlés automatizálásába. Különböző Azure AD B2C-környezetekben, például fejlesztési, tesztelési és éles környezetekben való üzembe helyezéskor azt javasoljuk, hogy távolítsa el a manuális folyamatokat, és hajtson végre automatikus tesztelést az Azure-folyamatok használatával.

Három elsődleges lépés szükséges ahhoz, hogy az Azure-folyamatok egyéni szabályzatokat kezelhetjenek az Azure AD B2C-n belül:

1. Hozzon létre egy webalkalmazás-regisztrációt az Azure AD B2C-bérlőben
1. Azure-tártár konfigurálása
1. Azure-folyamat konfigurálása

> [!IMPORTANT]
> Az Azure AD B2C egyéni szabályzatok **preview** kezelése egy Azure Pipeline-nal jelenleg a Microsoft Graph API-végponton `/beta` elérhető előzetes verziójú műveleteket használja. Ezek az API-k éles alkalmazásokban való használata nem támogatott. További információt a [Microsoft Graph REST API bétavégpontra vonatkozó hivatkozásában talál.](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure AD B2C-bérlő](tutorial-create-tenant.md)és a [b2C IEF-házirend-rendszergazdaszerepkörrel](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) rendelkező felhasználó hitelesítő adatai
* A bérlőbe feltöltött [egyéni házirendek](custom-policy-get-started.md)
* [A felügyeleti alkalmazás](microsoft-graph-get-started.md) regisztrálva van a bérlőben a Microsoft Graph API-engedélyekkel.ReadWrite.TrustFramework *Policy.ReadWrite.TrustFramework*
* [Az Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)és az [Azure DevOps Services projekthez][devops-create-project] való hozzáférés

## <a name="client-credentials-grant-flow"></a>Ügyfélhitelesítő adatok megadása

Az itt ismertetett forgatókönyv az Azure Pipelines és az Azure AD B2C közötti szolgáltatás-szolgáltatás hívásokat használja az OAuth 2.0 [ügyfélhitelesítő adatok megadása szolgáltatásnyújtási folyamat](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)használatával. Ez a támogatási folyamat lehetővé teszi, hogy egy webszolgáltatás, például az Azure Pipelines (a bizalmas ügyfél) saját hitelesítő adatait használja ahelyett, hogy megszemélyesítené a felhasználót, hogy hitelesítse magát egy másik webszolgáltatás (ebben az esetben a Microsoft Graph API) hívásakor. Az Azure Pipelines nem interaktív módon szerez be egy jogkivonatot, majd kéréseket küld a Microsoft Graph API-hoz.

## <a name="register-an-application-for-management-tasks"></a>Alkalmazás regisztrálása kezelési feladatokhoz

Az előfeltételek ben [említettek](#prerequisites)szerint szüksége van egy alkalmazásregisztrációra, amelyet az Azure Pipelines által végrehajtott PowerShell-parancsfájlok használhatnak a bérlő erőforrásainak eléréséhez.

Ha már rendelkezik egy olyan alkalmazásregisztrációval, amelyet az automatizálási feladatokhoz használ, győződjön meg arról, hogy megkapta a **Microsoft Graph** > **Policy** > **Policy.ReadWrite.TrustFramework** engedélyt az alkalmazásregisztráció **API-engedélyein** belül.

A felügyeleti alkalmazások regisztrálásával kapcsolatos tudnivalókat az [Azure AD B2C kezelése a Microsoft Graph segítségével](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Azure-tártár konfigurálása

A felügyeleti alkalmazás regisztrálva, készen áll a házirend-fájlok tárházának konfigurálására.

1. Jelentkezzen be az Azure DevOps Services szervezetébe.
1. [Hozzon létre egy új projektet,][devops-create-project] vagy jelöljön ki egy meglévőt.
1. A projektben keresse meg az **Újrapos-okat,** és válassza a **Fájlok** lapot. Jelöljön ki egy meglévő tárházat, vagy hozzon létre egyet ehhez a gyakorlathoz.
1. Hozzon létre egy *B2CAssets*nevű mappát. Nevezze el a szükséges helyőrző fájlt *README.md* és **véglegesítse** a fájlt. Ezt a fájlt később is eltávolíthatja, ha szeretné.
1. Adja hozzá az Azure AD B2C szabályzatfájlokat a *B2CAssets* mappába. Ez magában foglalja a *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*, és minden más, létrehozott házirendet. Rögzítse az egyes Azure AD B2C-házirendfájlok fájlnevét egy későbbi lépésben való használatra (powershell-parancsfájl-argumentumként használatosak).
1. Hozzon létre egy *Parancsfájlok* nevű mappát a tárház gyökérkönyvtárában, nevezze el a Helyőrző fájlt *DeployToB2c.ps1*. Ne véglegesítse a fájlt ezen a ponton, akkor ezt egy későbbi lépésben.
1. Illessze be a következő PowerShell-parancsfájlt *a DeployToB2c.ps1*fájlba, majd **véglegesítse** a fájlt. A parancsfájl beszerez egy jogkivonatot az Azure AD-ből, és meghívja a Microsoft Graph API-t, hogy töltse fel a szabályzatokat a *B2CAssets* mappában az Azure AD B2C bérlőbe.

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

A tárház inicializált és az egyéni házirend-fájlok at, készen áll a kiadási folyamat beállítása.

### <a name="create-pipeline"></a>Folyamat létrehozása

1. Jelentkezzen be az Azure DevOps Services szervezetébe, és keresse meg a projektet.
1. A projektben válassza **a Folyamatok** > **új** > **folyamatkiadása lehetőséget.**
1. A **Sablon kiválasztása**csoportban válassza az Üres feladat **lehetőséget.**
1. Adjon meg egy **szakasznevet**, például *DeployCustomPolicies*, majd zárja be az ablaktáblát.
1. Válassza **a Műtermék hozzáadása**lehetőséget, és a **Forrástípus csoportban**válassza az **Azure Repository**lehetőséget.
    1. Válassza ki a PowerShell-parancsfájllal feltöltött *Parancsfájl-mappát* tartalmazó forrástárt.
    1. Válasszon **alapértelmezett ágat**. Ha új tárházat hozott létre az előző szakaszban, az alapértelmezett ág a *master*.
    1. Hagyja meg a Legújabb **alapértelmezett verzióbeállítást** *az alapértelmezett ágból*.
    1. Adja meg a tárház **forrásaliasát.** Például, *policyRepo*. Ne adjon meg szóközt az aliasnévben.
1. Válassza a **Hozzáadás lehetőséget**
1. Nevezze át a folyamatot, hogy tükrözze a szándékát. Például *az egyéni házirend-folyamat telepítése.*
1. A **folyamatkonfiguráció** mentéséhez válassza a Mentés lehetőséget.

### <a name="configure-pipeline-variables"></a>Folyamatváltozók konfigurálása

1. Válassza a **Változók** lapot.
1. Adja hozzá a következő változókat a **Folyamatváltozók** csoportban, és állítsa be a megadott értékeket:

    | Név | Érték |
    | ---- | ----- |
    | `clientId` | A korábban regisztrált alkalmazás **alkalmazásazonosítója (ügyfélazonosítója).** |
    | `clientSecret` | A korábban létrehozott **ügyféltitok** értéke. <br /> Módosítsa a változó típusát **titkosra** (válassza a lakat ikont). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, ahol *a b2c-bérlő* az Azure AD B2C-bérlő neve. |

1. A változók mentéséhez válassza a **Mentés** gombot.

### <a name="add-pipeline-tasks"></a>Folyamatfeladatok hozzáadása

Ezután adjon hozzá egy feladatot egy házirendfájl központi telepítéséhez.

1. Válassza a **Feladatok** lapot.
1. Válassza **az Ügynök feladat**lehetőséget ,**+** majd a pluszjel ( ) lehetőséget, ha feladatot szeretne hozzáadni az Ügynök feladathoz.
1. Keresse meg és válassza a **PowerShell**lehetőséget. Ne válassza az "Azure PowerShell", a "PowerShell a célgépeken" vagy egy másik PowerShell-bejegyzést.
1. Válassza ki az újonnan hozzáadott **PowerShell-parancsfájl-feladatot.**
1. Adja meg a következő értékeket a PowerShell-parancsfájl feladathoz:
    * **Feladat verziója**: 2.*
    * **Megjelenítendő név**: A feladat által feltöltendő házirend neve. Például *B2C_1A_TrustFrameworkBase*.
    * **Szöveg**: Fájl elérési útja
    * **Parancsfájl elérési útja**: Jelölje ki a három pontot (***...***), keresse meg a *Parancsfájlok mappát,* majd válassza *a DeployToB2C.ps1* fájlt.
    * **Érvek:**

        Adja meg az **Arguments argumentumok következő értékeit.** Cserélje `{alias-name}` le az előző szakaszban megadott aliasra.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Ha például a megadott alias *a policyRepo,* az argumentumsornak a következőnek kell lennie:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Az Ügynökfeladat mentéséhez válassza a **Mentés** lehetőséget.

Az imént hozzáadott feladat *feltölt egy* szabályzatfájlt az Azure AD B2C-be. A folytatás előtt manuálisan indítsa el a feladatot (**Kiadás létrehozása**) annak érdekében, hogy a művelet sikeresen befejeződjön, mielőtt további feladatokat hozna létre.

Ha a feladat sikeresen befejeződött, adja hozzá a telepítési feladatokat az egyes egyéni házirendfájlok előző lépéseinek végrehajtásával. Módosítsa `-PolicyId` az `-PathToFile` egyes házirendek argumentumértékeit.

Az `PolicyId` a TrustFrameworkPolicy csomóponton belüli XML-házirendfájl elején található érték. A következő `PolicyId` házirend XML-fájljában például *B2C_1A_TrustFrameworkBase:*

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

Az ügynökök futtatásakor és a házirendfájlok feltöltésekén ellenőrizze, hogy a rendszer a következő sorrendben tölti fel őket:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfilSzerkesztés.xml*
1. *PasswordReset.xml fájl*

Az identitáskezelési keretrendszer kényszeríti ezt a sorrendet, mivel a fájlstruktúra hierarchikus láncra épül.

## <a name="test-your-pipeline"></a>A folyamat tesztelése

A kiadási folyamat tesztelése:

1. Válassza **a Folyamatok,** majd **a Felszabadulás lehetőséget.**
1. Válassza ki a korábban létrehozott folyamatot, például *deployCustompolicies.*
1. Válassza **a Kiadás létrehozása**lehetőséget, majd a **Létrehozás** gombot a kiadás várólistára helyezéséhez.

Meg kell jelennie egy értesítési szalagcímnek, amely azt mondja, hogy a kiadás várólistára került. Állapotának megtekintéséhez jelölje ki a hivatkozást az értesítési szalagcímben, vagy jelölje ki a **Felszabadulás ok** lapon lévő listában.

## <a name="next-steps"></a>További lépések

További információk:

* [Szolgáltatás-szolgáltatás hívások ügyfélhitelesítő adatokhasználatával](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
