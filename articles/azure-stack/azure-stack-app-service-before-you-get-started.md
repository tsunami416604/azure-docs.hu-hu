---
title: "App Service Azure veremben központi telepítése előtt |} Microsoft Docs"
description: "App Service Azure veremben központi telepítése előtt szükséges lépések"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: 18a671fe49b57dda3df33b58a464b300e574376f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Az App Service Azure veremben megkezdése előtt
*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure App Service Azure veremben telepítése előtt el kell végeznie az ebben a cikkben szereplő előfeltételek.

## <a name="download-the-installer-and-helper-scripts"></a>A telepítő és a segítő parancsfájlok letöltése

1. Töltse le a [Azure verem üzembe helyezési segítő parancsfájlok az App Service](https://aka.ms/appsvconmashelpers).
2. Töltse le a [Azure verem telepítő az App Service](https://aka.ms/appsvconmasinstaller).
3. Csomagolja ki a fájlokat a segítő parancsfájlok .zip fájl. A következő fájlok, a gyökérmappa-szerkezetében jelennek meg:
   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Modulok
     - GraphAPI.psm1

## <a name="prepare-for-high-availability"></a>Készítse elő a magas rendelkezésre állás

Az Azure App Service Azure veremben jelenleg nem magas rendelkezésre állású kínálnak, mert Azure verem munkaterheléseinek csak egy tartalék tartomány történő központi telepítését.

Azure App Service Azure veremben előkészítése magas rendelkezésre állású, telepítse a szükséges fájl server és SQL Server-példány egy magas rendelkezésre állású konfigurációban. Ha Azure verem támogatja több tartalék tartományok, azt pedig engedélyezése az Azure App Service Azure veremben magas rendelkezésre állású konfigurációban útmutatást nyújtanak.


## <a name="get-certificates"></a>Tanúsítványok beszerzése

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Az Azure verem szoftverfejlesztői készlet szükséges tanúsítványok

Az első parancsfájl együttműködik az Azure-verem hitelesítésszolgáltató, amelyet az App Service négy tanúsítványok létrehozásához:

| Fájlnév | Használat |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | App Service alapértelmezett SSL-tanúsítvány |
| Api.appservice.local.azurestack.external.pfx | App Service API SSL-tanúsítvány |
| ftp.appservice.local.azurestack.external.pfx | App Service publisher SSL-tanúsítvány |
| Sso.appservice.local.azurestack.external.pfx | App Service alkalmazás identitástanúsítvány |

Futtassa a parancsfájlt a Azure verem szoftverfejlesztői készlet gazdagépen, és győződjön meg arról, hogy futtat, azurestack\CloudAdmin PowerShell:

1. A PowerShell-munkamenetben azurestack\AzureStackAdmin futtató futtassa a létrehozás-AppServiceCerts.ps1 parancsfájlt a mappát, amelyikbe kibontotta a segítő parancsfájlok. A parancsfájl négy tanúsítványokat hoz létre a parancsfájlt, amelyet az App Service tanúsítványok létrehozásához ugyanabban a mappában.
2. Adjon meg egy jelszót a .pfx fájlt biztonságos, és jegyezze fel a azt. Meg kell adni azt az App Service a Azure verem installer.

#### <a name="create-appservicecertsps1-parameters"></a>Hozzon létre AppServiceCerts.ps1 paraméterek

| Paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| pfxPassword | Szükséges | NULL értékű | Jelszó, amely segít megvédeni a tanúsítvány titkos kulcsa |
| DomainName | Szükséges | local.azurestack.external | Az Azure verem régió és a tartományi utótag |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Az Azure App Service Azure veremben éles üzembe helyezéséhez szükséges tanúsítványok

Az erőforrás-szolgáltató éles környezetben működteti, meg kell adni a következő négy tanúsítványokat.

#### <a name="default-domain-certificate"></a>Alapértelmezett tartományi tanúsítvány

Az alapértelmezett tartományi tanúsítvány el van helyezve az előtér-szerepkört. Az Azure App Service helyettesítő vagy az alapértelmezett tartomány kérelmek felhasználói alkalmazás ezt a tanúsítványt használja. Ezzel a tanúsítvánnyal is forrás vezérlő műveletekhez (Kudu).

A tanúsítvány .pfx formátumban kell lennie, és a két-tulajdonos helyettesítő tanúsítványt kell lennie. Ez lehetővé teszi, hogy egy tanúsítványt az alapértelmezett tartomány és a forrás-ellenőrzési műveletek SCM végpontja.

| Formátum | Példa |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>API-tanúsítvány

Az API-tanúsítványt a felügyeleti szerepkör helyezkedik el. Az erőforrás-szolgáltató a biztonságos API-hívások használja azt. A tanúsítványnak a közzétételt tartalmaznia kell a tulajdonosa megegyezik a API DNS-bejegyzés.

| Formátum | Példa |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Közzététel tanúsítvány

A tanúsítvány a közzétevő szerepkört a FTPS-forgalmat az alkalmazástulajdonosok biztonságossá tételére hogy tartalom feltöltése során. A tanúsítványnak a közzétételt tartalmaznia kell a tulajdonosa megegyezik a FTPS DNS-bejegyzés.

| Formátum | Példa |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Identitás tanúsítványa

Lehetővé teszi a tanúsítványt az identitás-alkalmazáshoz:
- Az Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) directory, Azure verem és az App Service integráció a számítási erőforrás-szolgáltató támogatásához közötti integráció.
- Egyszeri bejelentkezés forgatókönyvek speciális fejlesztői eszközök Azure App Service Azure veremben belül.

Az identitás tanúsítványának tartalmaznia kell a tulajdonosa megegyezik a következő formátumban:

| Formátum | Példa |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Az Azure erőforrás-kezelő legfelső szintű tanúsítvány Azure verem

A PowerShell-munkamenetben azurestack\CloudAdmin futtató futtassa a Get-AzureStackRootCert.ps1 parancsfájlt a mappát, amelyikbe kibontotta a segítő parancsfájlok. A parancsfájl négy tanúsítványokat hoz létre a parancsfájlt, amelyet az App Service tanúsítványok létrehozásához ugyanabban a mappában.

| Get-AzureStackRootCert.ps1 parameter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Szükséges | AzS-ERCS01 | Kiemelt végpont |
| CloudAdminCredential | Szükséges | AzureStack\CloudAdmin | Tartományi fiók hitelesítő adatait a Azure verem felhő rendszergazdák számára |


## <a name="prepare-the-file-server"></a>A fájlkiszolgáló előkészítése

Az Azure App Service egy fájlkiszolgáló használatát igényli. Éles központi telepítése esetén a fájlkiszolgálón kell állítani a lehet magas rendelkezésre állású és hibák kezelésére képes.

Csak Azure verem szoftverfejlesztői készlet központi telepítése esetén használhatja a [példa Azure Resource Manager központi telepítési sablont](https://aka.ms/appsvconmasdkfstemplate) konfigurált egy csomópontos fájlkiszolgáló központi telepítéséhez. Az egy csomópontos fájlkiszolgáló munkacsoport lesz.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Kiépítés csoportokat és fiókokat az Active Directoryban


1. Hozza létre a következő Active Directory globális biztonsági csoportokat:
   - FileShareOwners
   - FileShareUsers
2. Hozza létre a következő Active Directory-fiókokat szolgáltatásfióknak:
   - FileShareOwner
   - FileShareUser

   Biztonság ajánlott eljárás, a felhasználók ezeket a fiókokat (és az összes webes szerepkör) legyen egymástól és erős felhasználóneveket és jelszavakat. Állítsa be a jelszavakat, a következő feltételeknek:
   - Engedélyezése **jelszó sohasem jár le**.
   - Engedélyezése **felhasználó nem módosíthatja a jelszót**.
   - Tiltsa le a **kell változtatni a jelszót a következő bejelentkezéskor**.
3. Adja hozzá a fiókokat a csoporttagságokat az alábbiak szerint:
   - Adja hozzá **FileShareOwner** számára a **FileShareOwners** csoport.
   - Adja hozzá **FileShareUser** számára a **FileShareUsers** csoport.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Csoportok és fiókok munkacsoportban kiépítése

>[!NOTE]
> Amikor konfigurálja a fájlkiszolgáló, futtassa a következő parancsokat egy rendszergazdai parancssort. *Ne használjon PowerShell.*

Az Azure Resource Manager-sablon használatakor a felhasználóknak már létre vannak hozva.

1. A következő parancsokat a FileShareOwner és FileShareUser fiókokat létrehozni. Cserélje le `<password>` saját értékekkel.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Állítsa be a jelszó soha nem lejár a következő WMIC-parancsok futtatásával:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. A helyi csoportok FileShareUsers és FileShareOwners létrehozása, és adja hozzá a fiókokat az első lépésben őket:
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>A tartalommegosztás kiépítése

A tartalommegosztás bérlői webhely tartalmát tartalmazza. A tartalom könyvtármegosztáson található a fájlkiszolgáló telepítéséhez az eljárás megegyezik a Active Directory és a munkacsoport-környezeteket. De másik feladatátvevő fürt az Active Directoryban.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>A tartalom kiszolgálómegosztás egyetlen fájlba (Active Directory vagy munkacsoport) kiépítése

Az önálló fájlkiszolgálóról futtassa a következő parancsokat egy rendszergazda jogú parancssorba. Cserélje le a következő `C:\WebSites` a megfelelő útvonalaira a környezetben.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>Adja hozzá a FileShareOwners csoportot a helyi Rendszergazdák csoport

Rendszerfelügyeleti webszolgáltatások megfelelően működjön fel kell vennie a FileShareOwners csoportot a helyi rendszergazdák csoporthoz.

#### <a name="active-directory"></a>Active Directory

A következő parancsokat egy rendszergazda jogú parancssorba a fájlkiszolgálón, vagy minden fájlkiszolgálón, amely különbséglemezként funkcionál egy feladatátvételi fürt csomópontja. Cserélje le a következő `<DOMAIN>` a használni kívánt tartomány nevét.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Munkacsoport

A fájlkiszolgálón futtassa a következő parancsot egy rendszergazda jogú parancssorba:

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>A megosztás hozzáférés-vezérlés konfigurálása

A következő parancsokat egy rendszergazda jogú parancssorba a fájlkiszolgálón vagy a feladatátvevő fürtcsomóponton, amely az aktuális fürt erőforrás tulajdonosa. Cserélje le dőlt értékek értékek, amelyek adott környezetre.

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Munkacsoport
```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Az SQL Server-példány előkészítése

Az Azure App Service Azure verem futtató és a mérési adatbázisok elő kell készítenie az App Service-adatbázisok tárolásához SQL Server-példányt.

Az Azure verem szoftverfejlesztői készlet történő telepítések esetén használhatja az SQL Server Express 2014 SP2 vagy újabb.

Éles és magas rendelkezésre állású céljára kell teljes verziót az SQL Server 2014 SP2 vagy újabb, kevert módú hitelesítés engedélyezése, és telepítenie a egy [magas rendelkezésre állású konfigurációban](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Az SQL Server-példány az Azure App Service Azure veremben levő összes App Service-szerepkörből elérhetőnek kell lennie. Az alapértelmezett szolgáltató előfizetésben Azure verem SQL Server telepítése. Lehetőség a szervezeten belül a meglévő infrastruktúra használata (feltéve, nincs kapcsolat az Azure-verem). Azure piactér lemezkép használata, ne felejtse el ennek megfelelően konfigurálja a tűzfalat.

Az SQL Server szerepkörök bármelyikéhez egy alapértelmezett vagy megnevezett példányt is használhatja. Ha egy megnevezett példányt használ, ügyeljen arra, hogy az manuálisan indítsa el az SQL Server Browser szolgáltatást, és nyissa meg a portot 1434.

## <a name="create-an-azure-active-directory-application"></a>Egy Azure Active Directory-alkalmazás létrehozása

Adja meg az Azure AD szolgáltatás egyszerű az alábbiak támogatásához:
- Virtuálisgép-méretezési integrációs be munkavégző rétegek
- Egyszeri bejelentkezés az Azure Functions portálon és speciális fejlesztői eszközök

Ezeket a lépéseket csak az Azure AD által biztosított Azure verem környezetek vonatkozik.

A rendszergazdák az SSO kell konfigurálni:
- Engedélyezze a speciális fejlesztői eszközök, az App Service (Kudu) belül.
- Az Azure Functions portálon élmény használatának engedélyezése.

Kövesse az alábbi lépéseket:

1. Nyisson meg egy PowerShell-példány azurestack\AzureStackAdmin.
2. Nyissa meg a letöltött és kibontott a parancsfájlok helyét a [előfeltétel-ellenőrzési lépés](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Telepítse a PowerShell Azure verem](azure-stack-powershell-install.md).
4. Futtassa a **létrehozás-AADIdentityApp.ps1** parancsfájl. Amikor a rendszer kéri, adja meg, amely az Azure-verem üzembe helyezéshez használata az Azure AD-bérlő azonosítója. Adja meg például **myazurestack.onmicrosoft.com**.
5. Az a **Credential** ablak, írja be a Azure AD szolgáltatás-rendszergazdai fiókot és jelszót. Kattintson az **OK** gombra.
6. Adja meg a tanúsítvány elérési útja és a tanúsítvány jelszavát az [korábban létrehozott tanúsítvány](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). A tanúsítványt, az alapértelmezés szerint ez a lépés létrehozott **sso.appservice.local.azurestack.external.pfx**.
7. A parancsfájl létrehoz egy új alkalmazást a bérlő az Azure AD-példányban. Jegyezze fel az alkalmazás Azonosítóját, amelynek PowerShell kimenet ad vissza. A telepítés során tájékoztatásra van szüksége.
8. Nyisson meg egy új böngészőablakot, és jelentkezzen be a [Azure-portálon](https://portal.azure.com) az Azure Active Directory szolgáltatás-rendszergazdaként
9. Nyissa meg az Azure AD erőforrás-szolgáltató.
10. Válassza ki **App regisztrációk**.
11. Keresse meg az Alkalmazásazonosítót a 7. lépés részeként. Egy App Service alkalmazás szerepel.
12. Válassza ki **alkalmazás** a listában.
13. Válassza ki **szükséges engedélyek** > **engedélyeket** > **Igen**.

| Hozzon létre AADIdentityApp.ps1 paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| DirectoryTenantName | Szükséges | NULL értékű | Az Azure AD-bérlő azonosítója. Adja meg a GUID vagy karakterlánc. Példa: myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Szükséges | NULL értékű | Felügyeleti Azure Resource Manager-végpont. Példa: adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Szükséges | NULL értékű | A bérlői Azure Resource Manager-végpont. Példa: management.local.azurestack.external. |
| AzureStackAdminCredential | Szükséges | NULL értékű | Az Azure AD szolgáltatás rendszergazdai hitelesítő adatait. |
| CertificateFilePath | Szükséges | NULL értékű | A korábban létrehozott identitás alkalmazás tanúsítvány fájl elérési útja. |
| CertificatePassword | Szükséges | NULL értékű | Olyan jelszót, amely segít megvédeni a tanúsítvány titkos kulcsa. |

## <a name="create-an-active-directory-federation-services-application"></a>Active Directory összevonási szolgáltatások alkalmazás létrehozása

AD FS által védett Azure verem környezetben konfigurálnia kell egy Active Directory összevonási szolgáltatások szolgáltatás egyszerű támogatásához a következő:
- Virtuálisgép-méretezési integrációs be munkavégző rétegek
- Egyszeri bejelentkezés az Azure Functions portálon és speciális fejlesztői eszközök

A rendszergazdák az SSO kell konfigurálni:
- Konfigurálása egyszerű virtuális gép méretezési készlet integrációs szolgáltatás munkavégző rétegen.
- Engedélyezze a speciális fejlesztői eszközök, az App Service (Kudu) belül.
- Az Azure Functions portálon élmény használatának engedélyezése.

Kövesse az alábbi lépéseket:

1. Nyisson meg egy PowerShell-példány azurestack\AzureStackAdmin.
2. Nyissa meg a letöltött és kibontott a parancsfájlok helyét a [előfeltétel-ellenőrzési lépés](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Telepítse a PowerShell Azure verem](azure-stack-powershell-install.md).
4.  Futtassa a **létrehozás-ADFSIdentityApp.ps1** parancsfájl.
5.  Az a **hitelesítő adat** ablak, írja be az AD FS felhőalapú rendszergazdai fiókot és jelszót. Kattintson az **OK** gombra.
6.  Adja meg a tanúsítvány elérési útja és a tanúsítvány jelszavát az [korábban létrehozott tanúsítvány](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). A tanúsítványt, az alapértelmezés szerint ez a lépés létrehozott **sso.appservice.local.azurestack.external.pfx**.

| Hozzon létre ADFSIdentityApp.ps1 paraméter | Kötelező vagy választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| AdminArmEndpoint | Szükséges | NULL értékű | Felügyeleti Azure Resource Manager-végpont. Példa: adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Szükséges | NULL értékű | Kiemelt végpont. Példa: AzS-ERCS01. |
| CloudAdminCredential | Szükséges | NULL értékű | Tartományi fiók hitelesítő adatait a Azure verem felhő rendszergazdák számára. Példa: Azurestack\CloudAdmin. |
| CertificateFilePath | Szükséges | NULL értékű | Az identitás alkalmazás tanúsítvány PFX-fájljának elérési útja. |
| CertificatePassword | Szükséges | NULL értékű | Olyan jelszót, amely segít megvédeni a tanúsítvány titkos kulcsa. |


## <a name="next-steps"></a>További lépések

[Az App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md)
