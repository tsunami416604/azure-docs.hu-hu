---
title: Az Azure verem datacenter integrációs - identitás
description: 'Útmutató: Azure verem AD FS integrálása az AD FS adatközpont'
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: dc6c8ef2953b7495c734ec8b16530cdd812ac792
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Az Azure verem datacenter integrációs - identitás
Az identitás-szolgáltatóktól Azure verem Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) használatával telepítheti. Azure verem központi telepítése előtt meg kell nyitnia a választás. AD FS segítségével központi telepítés is nevezzük Azure verem kapcsolat nélküli módban való telepítése.

A következő táblázatban a két azonosító lehetőségek közötti eltéréseket:

||Az internethez|Az internethez csatlakozó|
|---------|---------|---------|
|Számlázás|Kapacitás kell lennie.<br> Nagyvállalati Szerződés (EA) csak|A kapacitás vagy a fizetési,-akkor-használható<br>EA vagy Felhőszolgáltató (CSP)|
|Identitás|Az AD FS kell lennie.|Azure AD vagy AD FS|
|Piactér szindikálási|Támogatott<br>BYOL licencelés|Támogatott<br>BYOL licencelés|
|Regisztráció|Ajánlott, cserélhető adathordozóra van szükség<br> és egy különálló csatlakoztatott eszközön.|Automatikus|
|Javítás és frissítés|Szükséges, cserélhető adathordozóra van szükség<br> és egy különálló csatlakoztatott eszközön.|Frissítési csomag közvetlenül tölthető le<br> az Azure verem az internetről.|

> [!IMPORTANT]
> Az identitásszolgáltató nem lehet átállítani a teljes Azure verem megoldás üzembe helyezésével.

## <a name="active-directory-federation-services-and-graph"></a>Az Active Directory összevonási szolgáltatások és a Graph

Az AD FS telepítése lehetővé teszi, hogy egy meglévő Active Directory-erdőben való hitelesítéshez szükséges erőforrások Azure verem identitásokat. A meglévő Active Directory-erdő az AD FS és az AD FS összevonási megbízhatósági kapcsolat létrehozásának engedélyezése egy központi telepítés szükséges.

Hitelesítés az identitás egy része. Kezelése szerepköralapú hozzáférés vezérlés (RBAC) Azure-készletben, be kell állítani a diagram-összetevőt. Delegált hozzáférést egy erőforráshoz, amikor a diagram összetevő keres a felhasználói fiók a meglévő Active Directory-erdőben, az LDAP protokoll használatával.

![Az Azure Active Directory összevonási szolgáltatások verem architektúrája](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

A meglévő AD FS a fiók biztonságijogkivonat-szolgáltatás (STS) által küldött jogcímek a verem Azure Active Directory összevonási szolgáltatások (az erőforrás STS). Azure-készletben automation hoz létre a jogcím-szolgáltatói megbízhatóság a metaadat-végpontjához a meglévő Active Directory összevonási szolgáltatások.

A meglévő AD FS-ben, be kell állítani egy megbízható függő entitás. Ez a lépés nem végezhető el az automation, és az üzemeltető be kell állítani. Az Azure-verem metaadat-végpontjához dokumentálja a AzureStackStampDeploymentInfo.JSON fájlban, vagy a kiemelt végpont segítségével a parancs futtatásával `Get-AzureStackInfo`.

A függő entitás szintű bizalmi kapcsolatainak konfigurációja is szükséges a Microsoft által biztosított jogcím-átalakítási szabályok konfigurálása.

A Graph-konfigurációhoz a szolgáltatásfióknak kell lennie, feltéve, hogy rendelkezik-e olvasási engedéllyel a meglévő Active Directoryban. A fiókra akkor szükség ahhoz, hogy az RBAC forgatókönyvek automatizálását bemenetként.

Az utolsó lépést az új tulajdonos az alapértelmezett szolgáltató előfizetés van konfigurálva. Ez a fiók összes erőforrásokhoz, amikor a verem Azure felügyeleti portálra bejelentkezve teljes hozzáféréssel rendelkezik.

Követelmények:


|Összetevő|Követelmény|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012 vagy 2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Graph-integráció beállítása

Graph csak egyetlen Active Directory-erdőben való integráció támogatja. Ha több erdő, csak a konfigurációban megadott erdő beolvasási felhasználók és csoportok lesz használható.

A következő információkra szükség az automation paraméterek bemeneteként:


|Paraméter|Leírás|Példa|
|---------|---------|---------|
|CustomADGlobalCatalog|A cél az Active Directory-erdő teljes Tartományneve<br>Biztosan el kívánja integrálni|Contoso.com|
|CustomADAdminCredentials|Egy LDAP olvasási engedéllyel rendelkező felhasználó|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Felhasználói fiók létrehozása, ha a meglévő Active Directoryban (nem kötelező)

Szükség esetén a meglévő Active Directory Graph szolgáltatás fiók hozhat létre. Hajtsa végre ezt a lépést, ha még nem rendelkezik egy használni kívánt fiókot.

1. A meglévő Active Directoryban hozzon létre a következő felhasználói fiókot (ajánlott):
   - **Felhasználónév**: graphservice
   - **Jelszó**: erős jelszó használata<br>Konfigurálja a jelszó soha nem jár le.

   Nincsenek különleges engedélyekhez és a tagság megadása kötelező.

#### <a name="trigger-automation-to-configure-graph"></a>Eseményindító automatizálás graph konfigurálása

Ez az eljárás az adatközponti hálózat, amely képes kommunikálni a kiemelt végpont Azure-készletben lévő számítógépet használni.

2. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és kapcsolódjon a kiemelt végpont IP-címét. A hitelesítő adatok használata **CloudAdmin** hitelesítéséhez.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Most, hogy a kiemelt végpont csatlakozik, a következő parancsot: 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Amikor a rendszer kéri, adja meg a Graph-szolgáltatáshoz (például graphservice) használni kívánt felhasználói fiók hitelesítő adatait. A Register-DirectoryService parancsmag bemenete kell lennie az erdő neve / legfelső szintű tartomány az erdőben, nem pedig az erdő más tartományokba.

   > [!IMPORTANT]
   > Várja meg a hitelesítő adatok előugró (Get-Credential parancsmag nem támogatott a rendszerjogosultságú végpont) és a Graph-szolgáltatásfiók hitelesítő adatokat.

#### <a name="graph-protocols-and-ports"></a>Graph protokollok és portok

Azure verem Graph szolgáltatás a következő protokollok és portok használatával kommunikálni egy írható globális katalógus kiszolgáló (GC) és a kulcsszolgáltató (KDC), amely képes a bejelentkezési kérések a cél az Active Directory-erdőben.

Graph szolgáltatás Azure-készletben a következő protokollokat és portokat használ a cél az Active Directory folytatott kommunikációhoz:

|Típus|Port|Protokoll|
|---------|---------|---------|
|LDAP|389|TCP ÉS UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Az AD FS-integráció beállítása úgy, hogy letölti az összevonási metaadatok

A következő információkra szükség az automation paraméterek bemenetként:

|Paraméter|Leírás|Példa|
|---------|---------|---------|
|CustomAdfsName|A jogcím-szolgáltató neve. <cr>Úgy tűnik, így az AD FS kezdőlapja.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Összevonási metaadatok hivatkozás|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Eseményindító automatizálási jogcím-szolgáltatói megbízhatóság Azure verem konfigurálása

Az eljárás használatához Azure-készletben a rendszerjogosultságú végponttal, amely képes kommunikálni a számítógépet használni. Várható, hogy a partner által használt tanúsítvány **STS AD FS** Azure verem megbízhatónak.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és a kiemelt végponthoz kapcsolódni.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy a kiemelt végpont csatlakozik, a következő parancsot a környezetének megfelelő paraméterekkel:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. A következő parancsot a környezetének megfelelő paraméterekkel az alapértelmezett szolgáltató előfizetés tulajdonosának frissítése:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Az AD FS integrációjának beállításában, adja meg a összevonási metaadatait tartalmazó fájl

Ezt a módszert használja, ha a következő feltételek valamelyike teljesül:

- A tanúsítványlánc nem egyezik az AD FS végpontja Azure verem képest.
- Nincs hálózati kapcsolat a meglévő AD FS-kiszolgálón az Azure verem AD FS-példányt.

A következő információkra szükség az automation paraméterek bemenetként:


|Paraméter|Leírás|Példa|
|---------|---------|---------|
|CustomAdfsName|A jogcím-szolgáltató neve. Így az AD FS kezdőlapja jelenik meg.|Contoso|
|CustomADFSFederationMetadataFile|Összevonási metaadatait tartalmazó fájl|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>Hozzon létre összevonási metaadatait tartalmazó fájl

A következő eljáráshoz lesz a fiók STS meglévő AD FS-telepítés hálózati kapcsolattal rendelkező számítógépen kell használnia. Is telepítenie kell a szükséges tanúsítványokat.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsot, a környezetnek megfelelő paraméterekkel:

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. A metaadat-fájlt másolja egy megosztáshoz, amely a jogosultsági szintű végpont elérhető.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Eseményindító automatizálási jogcím-szolgáltatói megbízhatóság Azure verem konfigurálása

Az eljárás használatához Azure-készletben a rendszerjogosultságú végponttal, amely képes kommunikálni a számítógépet használni.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és a kiemelt végponthoz kapcsolódni.

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Most, hogy a kiemelt végpont csatlakozik, a következő parancsot a környezetének megfelelő paraméterekkel:

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. A következő parancsot a környezetének megfelelő paraméterekkel az alapértelmezett szolgáltató előfizetés tulajdonosának frissítése:

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Függő entitás konfigurálja a meglévő AD FS üzembe helyezése (fiók STS)

A Microsoft egy parancsfájlt tartalmaz, a függő entitás megbízhatóságaként, beleértve a jogcím-átalakítási szabályok konfigurálása. A parancsfájl használata nem kötelező, a parancsok manuálisan is futtatható.

Letöltheti a segítő parancsfájl [Azure verem eszközök](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) a Githubon.

Ha úgy dönt, hogy manuálisan futtassa a parancsokat, kövesse az alábbi lépéseket:

1. A következő tartalmat átmásolja egy .txt fájlt (például c:\ClaimRules.txt mentett) az Adatközpont AD FS-példány vagy a farm tag:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Windows Forms-alapú hitelesítés engedélyezéséhez nyissa meg a Windows PowerShell-munkamenetet emelt szintű felhasználóként, és futtassa a következő parancsot:

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. A függő entitás megbízhatóságának hozzáadásához futtassa a következő Windows PowerShell-parancsot az AD FS-példányt vagy egy farm tagja. Győződjön meg arról, hogy az AD FS végpont frissítése, és az 1. lépésben létrehozott fájlt.

   **Az AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **AD FS 2012/2012 R2-höz**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Kell használnia az AD FS beépülő MMC-modulban a kiállítás-engedélyezési szabályok konfigurálása a Windows Server 2012 vagy 2012 R2 AD FS használata esetén.

4. Internet Explorer vagy használatakor az Edge böngésző Azure verem eléréséhez, akkor kell figyelmen kívül a token kötések. Ellenkező esetben a bejelentkezési kísérlet sikertelen. Az AD FS-példányt vagy egy farm tagja a következő parancsot:

   > [!note]  
   > Ez a lépés használata esetén nem alkalmazható a Windows Server 2012 vagy 2012 R2 AD FS. Biztonságos, hagyja ki ezt a parancsot, és folytassa az integrációt is.

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. Ahhoz, hogy a frissítési jogkivonatokat, nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsot:

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>Egyszerű szolgáltatásnév létrehozása

Nincsenek számos forgatókönyv, amely egy egyszerű szolgáltatásnév (SPN) hitelesítés használata szükséges. Az alábbiakban néhány példát:

- Az AD FS telepítésének Azure verem CLI használata
- A System Center felügyeleti csomag az AD FS telepítésekor Azure verem
- Az AD FS telepítésekor Azure verem erőforrás-szolgáltató
- Számos olyan alkalmazás
- A nem interaktív bejelentkezési van szüksége

Egyszerű Szolgáltatásnevet létrehozásával kapcsolatos további információkért lásd: [egyszerű szolgáltatásnév létrehozása az AD FS](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="configuration-rollback"></a>Konfigurációs visszaállítása

Ha hiba lép fel, hogy a környezet olyan állapotban, amelyben már nem hitelesítheti, egy visszaállítási beállítás érhető el.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsokat:

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ezután futtassa a következő parancsmagot:

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   Miután a visszaállítási művelet, az összes konfigurációs módosítás visszavonásra kerül. Csak a beépített hitelesítési **CloudAdmin** felhasználói lehetőség.

   > [!IMPORTANT]
   > Konfigurálnia kell az alapértelmezett szolgáltató előfizetés eredeti tulajdonosa

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>További naplófájlok összegyűjtése

Ha nem sikerül, a parancsmagokat használatával hozhatja létre további naplók a `Get-Azurestacklogs` parancsmag.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsokat:

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ezután futtassa a következő parancsmagot:

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>További lépések

[Külső figyelési megoldások integrálása](azure-stack-integrate-monitor.md)
