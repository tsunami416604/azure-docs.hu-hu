---
title: "Azure Automation futtató fiókok létrehozása | Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan frissítheti Automation-fiókját és hozhat létre futtató fiókokat a PowerShell-lel vagy a portálról."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/27/2017
ms.author: magoedte
ms.openlocfilehash: 28bfa54c263388d91057118878a3109ec467894a
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>Automation-fiók hitelesítésének frissítése futtató fiókokkal 
Az Azure Portalról vagy a PowerShell-lel frissítheti meglévő Automation-fiókját. Erre a következő esetekben lehet szükség:

* Létrehozott egy Automation-fiókot, de futtató fiókot nem.
* Már használ egy, a Resource Manager-erőforrások felügyeletére szolgáló Automation-fiókot, de szeretné frissíteni, hogy runbookos hitelesítést lehetővé tevő futtató fiókot is tartalmazzon.
* Már használ egy, a klasszikus erőforrások felügyeletére szolgáló Automation-fiókot, de szeretné azt frissíteni, és klasszikus futtató fiókként használni, hogy megtakarítsa az új fiók létrehozásához és a runbookok és objektumok áthelyezéséhez szükséges időt.   

Ez a folyamat a következő elemeket hozza létre az Automation-fiókjában.

**Futtató fiókok esetén:**

* Létrehoz egy önaláírt tanúsítvánnyal ellátott Azure AD-alkalmazást, továbbá létrehoz egy egyszerűszolgáltatás-fiókot az Azure AD-ben lévő alkalmazáshoz, és hozzárendeli a közreműködői szerepkört a jelenlegi előfizetésben lévő fiókhoz. Ezt a beállítást bármikor módosíthatja Tulajdonos értékre vagy bármely egyéb szerepkörre. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
* Létrehoz egy *AzureRunAsCertificate* nevű Automation-tanúsítványobjektumot a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza az Azure AD-alkalmazás által használt titkos tanúsítványkulcsot.
* Létrehoz egy *AzureRunAsConnection* nevű Automation-kapcsolatobjektumot a megadott Automation-fiókban. Ez a kapcsolatobjektum magában foglalja az alkalmazásazonosítót, a bérlőazonosítót, az előfizetés-azonosítót és a tanúsítvány ujjlenyomatát.

**Klasszikus futtatófiókok esetében:**

* Létrehoz egy *AzureClassicRunAsCertificate* nevű Automation-tanúsítványobjektumot a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.
* Létrehoz egy *AzureClassicRunAsConnection* nevű Automation-kapcsolatobjektumot a megadott Automation-fiókban. Ez a kapcsolatobjektum tartalmazza az előfizetés nevét, a subscriptionId paramétert, valamint a tanúsítványobjektum nevét.

## <a name="prerequisites"></a>Előfeltételek
Ha [a PowerShell-lel hozza létre a futtató fiókokat](#create-run-as-account-using-powershell), ehhez a folyamathoz a következőkre van szüksége:

* Windows 10 és Azure Resource Manager 3.4.1-es vagy újabb modulokkal rendelkező Windows Server 2016 rendszer. A PowerShell-szkript nem támogatja a Windows korábbi verzióit.
* Az Azure PowerShell 1.0-s és újabb verziói. Információk a PowerShell 1.0-s kiadásáról: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
* Egy Automation-fiók, amelyre a rendszer az *–AutomationAccountName* és az *-ApplicationDisplayName* paraméterek értékeként hivatkozik.

A szkript végrehajtásához feltétlenül szükséges *SubscriptionID*, *ResourceGroup* és *AutomationAccountName* paraméterek értékének lekéréséhez tegye a következőket:

1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be az **Automation** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Automation-fiókok** elemet.
2. Az Automation-fiók oldalon válassza ki az Automation-fiókját, majd a **Fiókbeállítások** területen válassza a **Tulajdonságok** lehetőséget.  
3. Jegyezze fel a **Tulajdonságok** lapon megjelenő értékeket.<br><br> ![Az Automation-fiók „Tulajdonságok” panelje](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>Az Automation-fiók frissítéséhez szükséges engedélyek
Automation-fiók frissítéséhez az alábbi, a jelen témakör végrehajtásához szükséges jogosultságokkal és engedélyekkel kell rendelkeznie.   
 
* Az AD-felhasználói fiókot egy olyan szerepkörhöz kell hozzáadni, amely a Microsoft.Automation-erőforrások közreműködői szerepkörével egyenértékű engedélyekkel rendelkezik a [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md#contributor-role-permissions) című cikkben ismertetett módon.  
* Az Azure AD-bérlő nem rendszergazda jogosultságú felhasználói abban az esetben végezhetik el az [AD-alkalmazások regisztrálását](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions), ha az Alkalmazásregisztrációk beállítás értéke **Igen**.  Ha az Alkalmazásregisztrációk beállítás értéke **Nem**, ezt a műveletet csak az Azure AD globális rendszergazdái hajthatják végre. 

Ha nem tagja az előfizetéshez tartozó Active Directory-példánynak, mielőtt hozzáadják Önt az előfizetés globális rendszergazdai vagy társadminisztrátori szerepköréhez, vendégként lesz hozzáadva az Active Directoryhoz. Ebben az esetben „Nincs engedélye létrehozni…” figyelmeztető üzenetet kap az **Automation-fiók hozzáadása** panelen. A globális rendszergazdai vagy társadminisztrátori szerepkörhöz hozzáadott felhasználók először eltávolíthatók az előfizetéshez tartozó Active Directory-példányból, majd újra hozzáadhatók, így teljes jogú felhasználók lehetnek az Active Directoryban. Ez a helyzet úgy ellenőrizhető, ha az Azure Portal **Azure Active Directory** panelén a **Felhasználók és csoportok** és a **Minden felhasználó** elemre kattint, majd a konkrét felhasználó kiválasztása után a **Profil** elemet választja. A felhasználók profilja alatti **Felhasználó típusa** attribútum értéke ne legyen **Guest** (vendég).

## <a name="create-run-as-account-from-the-portal"></a>Futtató fiók létrehozása a portálról
Az ebben a szakaszban szereplő lépések végrehajtásával frissítheti Azure Automation-fiókját az Azure Portalról.  Egyenként hozza létre a futtató és a klasszikus futtató fiókokat, és ha nem kell klasszikus erőforrásokat felügyelnie, egyszerűen létrehozhatja a futtató fiókot.  

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be az **Automation** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza az **Automation-fiókok** elemet.
3. Az Automation-fiók oldalon válassza ki az Automation-fiókját.  
4. A bal oldali panel **Fiókbeállítások** részén válassza a **Futtató fiókok** lehetőséget.  
5. Attól függően, hogy melyik fiókra van szüksége, válassza az **Azure-alapú futtató fiók** vagy a **Klasszikus Azure-alapú futtató fiók** lehetőséget.  Miután választott, megjelenik az **Azure-alapú futtató fiók felvétele** vagy a **Klasszikus Azure-alapú futtató fiók felvétele** panel. Az áttekintési információk tanulmányozása után kattintson a **Létrehozás** gombra a futtató fiók létrehozásának folytatásához.  
6. Amíg az Azure létrehozza a futtató fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát.  Megjelenik egy szalagcím is azzal az üzenettel, hogy a fiók létrehozása folyamatban van.  A folyamat eltarthat pár percig.  

## <a name="create-run-as-account-using-powershell-script"></a>Futtató fiók létrehozása PowerShell-szkripttel
Ez a PowerShell-szkript a következő konfigurációk támogatását tartalmazza:

* Futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Egy futtató fiók és egy klasszikus futtató fiók létrehozása a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvánnyal.
* Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával.

>[!NOTE]
> Ha klasszikus futtató fiók létrehozását választja, a szkript végrehajtása után töltse fel a nyilvános tanúsítványt (.cer fájlnévkiterjesztéssel) annak az előfizetésnek a felügyeleti tárába, amelyben az Automation-fiókot létrehozták.
> 

1. Mentse el a következő parancsprogramot a számítógépén. Ebben a példában mentse a következő fájlnéven: *New-RunAsAccount.ps1*.

         #Requires -RunAsAdministrator
         Param (
         [Parameter(Mandatory=$true)]
         [String] $ResourceGroup,

         [Parameter(Mandatory=$true)]
         [String] $AutomationAccountName,

         [Parameter(Mandatory=$true)]
         [String] $ApplicationDisplayName,

         [Parameter(Mandatory=$true)]
         [String] $SubscriptionId,

         [Parameter(Mandatory=$true)]
         [Boolean] $CreateClassicRunAsAccount,

         [Parameter(Mandatory=$true)]
         [String] $SelfSignedCertPlainPassword,

         [Parameter(Mandatory=$false)]
         [String] $EnterpriseCertPathForRunAsAccount,

         [Parameter(Mandatory=$false)]
         [String] $EnterpriseCertPlainPasswordForRunAsAccount,

         [Parameter(Mandatory=$false)]
         [String] $EnterpriseCertPathForClassicRunAsAccount,

         [Parameter(Mandatory=$false)]
         [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

         [Parameter(Mandatory=$false)]
         [ValidateSet("AzureCloud","AzureUSGovernment")]
         [string]$EnvironmentName="AzureCloud",

         [Parameter(Mandatory=$false)]
         [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
         )

         function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
                                        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
         $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
             -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
             -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

         $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
         Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
         Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
         }

         function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
         $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
         $keyId = (New-Guid).Guid

         $startDate = Get-Date
         $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
         
         #Create an Azure AD application, AD App Credential, AD ServicePrincipal
         $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
         $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
         $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
         $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

         # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
         Sleep -s 15
         $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
         $Retries = 0;
         While ($NewRole -eq $null -and $Retries -le 6)
         {
             Sleep -s 10
             New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
             $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
             $Retries++;
         }
             return $Application.ApplicationId.ToString();
         }

         function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
         $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
         Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
         New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
         }

         function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
         Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
         New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
         }

         Import-Module AzureRM.Profile
         Import-Module AzureRM.Resources

         $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
         if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3)))
         {
             Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
             return
         }

         Login-AzureRmAccount -Environment $EnvironmentName 
         $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

         # Create a Run As account by using a service principal
         $CertifcateAssetName = "AzureRunAsCertificate"
         $ConnectionAssetName = "AzureRunAsConnection"
         $ConnectionTypeName = "AzureServicePrincipal"

         if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
         $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
         $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
         } else {
            $CertificateName = $AutomationAccountName+$CertifcateAssetName
            $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
            $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
            CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
         }

         # Create a service principal
         $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
         $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

         # Create the Automation certificate asset
         CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

         # Populate the ConnectionFieldValues
         $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
         $TenantID = $SubscriptionInfo | Select TenantId -First 1
         $Thumbprint = $PfxCert.Thumbprint
         $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

         # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
         CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

         if ($CreateClassicRunAsAccount) {
              # Create a Run As account by using a service principal
              $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
              $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
              $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
              $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                      "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                      "Then click Upload and upload the .cer format of #CERT#"

               if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
               $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
               $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
               $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
         } else {
               $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
               $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
               $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
               $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
               $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
               CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
         }

         # Create the Automation certificate asset
         CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

         # Populate the ConnectionFieldValues
         $SubscriptionName = $subscription.Subscription.Name
         $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

         # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
         CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

         Write-Host -ForegroundColor red $UploadMessage
         }

2. A számítógépén indítsa el a **Windows PowerShell** alkalmazást a **Kezdőlap** képernyőről emelt szintű felhasználói jogokkal.
3. A rendszergazda jogú parancssori felületből lépjen abba a mappába, amely az 1. lépésben létrehozott szkriptet tartalmazza.  
4. Futtassa a szkriptet a kívánt konfigurációhoz szükséges paraméterértékekkel.

    **Futtató fiók létrehozása önaláírt tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Futtató fiók és klasszikus futtató fiók létrehozása vállalati tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > A rendszer az Azure-ral történő hitelesítést fogja kérni a szkript futtatása után. Jelentkezzen be egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörnek, és emellett az előfizetés társadminisztrátorának is számít.
    >
    >

A szkript sikeres futtatása után jegyezze fel a következőket:
* Ha önaláírt nyilvános tanúsítvánnyal (.cer fájl) ellátott klasszikus futtató fiókot hoz létre, a szkript létrehozza és menti a tanúsítványt a számítógépen a PowerShell-munkamenet végrehajtásához használt *%USERPROFILE%\AppData\Local\Temp* felhasználói profilhoz tartozó ideiglenes mappába.
* Ha egy (.cer formátumú) vállalati tanúsítvánnyal rendelkező klasszikus futtató fiókot hozott létre, használja ezt a tanúsítványt. Kövesse az utasításokat, amelyek bemutatják [a felügyeleti API-tanúsítványok klasszikus Azure portálra való feltöltését](../azure-api-management-certs.md), majd ellenőrizze a hitelesítő adatok konfigurációját a klasszikus üzembe helyezési erőforrásokkal. Ehhez használja a [klasszikus Azure üzembe helyezési modell erőforrásaival való hitelesítésre szolgáló mintakódot](automation-verify-runas-authentication.md#classic-run-as-authentication). 
* Ha *nem* klasszikus futtató fiókot hozott létre, állítsa be a hitelesítést a Resource Manager-erőforrásokkal, valamint ellenőrizze a hitelesítő adatok konfigurációját. Ehhez használja a [Service Management-erőforrásokkal való hitelesítéshez használt mintakódot.](automation-verify-runas-authentication.md#automation-run-as-authentication)

## <a name="next-steps"></a>Következő lépések
* Az Egyszerű szolgáltatásokkal kapcsolatos további információkért lásd: [Alkalmazásobjektumok és egyszerű szolgáltatási objektumok](../active-directory/active-directory-application-objects.md).
* A tanúsítványokkal és az Azure-szolgáltatásokkal kapcsolatos részletes információkért lásd: [Tanúsítványok áttekintése az Azure Cloud Servicesben](../cloud-services/cloud-services-certs-create.md).
