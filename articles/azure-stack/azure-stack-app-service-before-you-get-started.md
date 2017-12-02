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
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: 17967131853d4334ae2c0ba3c0aa01089b7f3b61
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Az App Service Azure veremben megkezdése előtt

Azure veremben az Azure App Service rendelkezik, amely a központi telepítés előtt el kell végezni az előfeltételként lépésből áll:

- Töltse le az Azure App Service az Azure verem segítő parancsfájlok
- Magas rendelkezésre állás
- Az Azure App Service-Azure veremben szükséges tanúsítványok
- Készítse elő a fájlkiszolgálón
- SQL-kiszolgáló előkészítése
- Egy Azure Active Directory-alkalmazás létrehozása
- Active Directory összevonási szolgáltatások alkalmazás létrehozása

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Töltse le az Azure App Service Azure verem telepítő és segítő parancsfájlok

1. Töltse le a [Azure verem üzembe helyezési segítő parancsfájlok az App Service](https://aka.ms/appsvconmashelpers).
2. Töltse le a [Azure verem telepítő az App Service](https://aka.ms/appsvconmasinstaller).
3. Csomagolja ki a fájlokat a segítő parancsfájlok zip-fájl. A következő fájlok, a gyökérmappa-szerkezetében jelennek meg:
  - Common.ps1
  - Hozzon létre AADIdentityApp.ps1
  - Hozzon létre ADFSIdentityApp.ps1
  - Hozzon létre AppServiceCerts.ps1
  - Get-AzureStackRootCert.ps1
  - Remove-AppService.ps1
  - Modulok
    - GraphAPI.psm1
    
## <a name="high-availability"></a>Magas rendelkezésre állás

Az Azure App Service Azure veremben nincs jelenleg képes biztosítani a magas rendelkezésre állású, mert Azure verem csak egy egyetlen tartalék tartomány munkaterhelések telepíti.

Azure App Service Azure veremben előkészítése magas rendelkezésre állású, ügyeljen arra, hogy a szükséges fájl vagy az SQL Server telepítése egy magas rendelkezésre állású konfigurációban. Ha Azure verem támogatja több tartalék tartományok, azt pedig engedélyezése az Azure App Service Azure veremben magas rendelkezésre állású konfigurációban útmutatást nyújtanak.


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Az Azure App Service-Azure veremben szükséges tanúsítványok

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Az Azure verem szoftverfejlesztői készlet szükséges tanúsítványok

Ez a parancsfájl első négy App Service által használt tanúsítványok létrehozása az Azure verem hitelesítésszolgáltató működik.

| Fájlnév | Használat |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | App Service alapértelmezett SSL-tanúsítvány |
| API.appservice.local.azurestack.external.pfx | Az App Service API SSL-tanúsítvány |
| FTP.appservice.local.azurestack.external.pfx | Az App Service Publisher SSL-tanúsítvány |
| Sso.appservice.local.azurestack.external.pfx | Az App Service alkalmazás Identitástanúsítvány |

Futtassa a parancsfájlt a Azure verem szoftverfejlesztői készlet gazdagépen, és győződjön meg arról, hogy PowerShell mint azurestack\CloudAdmin futtat.

1. Egy PowerShell-munkamenetben azurestack\AzureStackAdmin futtató hajtsa végre a Create-AppServiceCerts.ps1 parancsfájlt a mappában, amelyikbe kibontotta a segítő parancsfájlok. A parancsfájl ugyanabban a mappában, amelyet az App Service létrehozása tanúsítványok parancsfájlként négy tanúsítványokat hoz létre.
2. Adjon meg egy jelszót a .pfx fájlt biztonságos, és jegyezze fel a azt. Meg kell adni azt az App Service a Azure verem installer.

#### <a name="create-appservicecertsps1-parameters"></a>Hozzon létre AppServiceCerts.ps1 paraméterek

| Paraméter | Kötelező/választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| PfxPassword | Szükséges | NULL értékű | A tanúsítvány titkos kulcsának védelmére használt jelszót |
| Tartománynév | Szükséges | Local.azurestack.external | Az Azure verem régió és a tartományi utótag |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Az Azure App Service Azure veremben éles üzembe helyezéséhez szükséges tanúsítványok

Az erőforrás-szolgáltató éles környezetben működteti, meg kell adni a következő négy tanúsítványok:

#### <a name="default-domain-certificate"></a>Alapértelmezett tartományi tanúsítvány

Az alapértelmezett tartományi tanúsítvány el van helyezve az előtér-szerepkört. A helyettesítő karakteres vagy az alapértelmezett tartomány kéréseket az Azure App Service felhasználói alkalmazások szolgál. Ezzel a tanúsítvánnyal is forrás vezérlő műveletekhez (KUDU).

A tanúsítvány .pfx formátumban kell lennie, és a két-tulajdonos helyettesítő tanúsítványt kell lennie. Ez lehetővé teszi, hogy az alapértelmezett tartomány és a forrás-ellenőrzési műveletek kell fedi le egy tanúsítvány az scm végpont.

| Formátum | Példa |
| --- | --- |
| \*.appservice. \<régió\>.\< Tartománynév\>.\< bővítmény\> | \*. appservice.redmond.azurestack.external |
| \*. scm.appservice. <region>. <DomainName>.<extension> | \*. appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>API-tanúsítvány

Az API-tanúsítvány helyezkedik el a felügyeleti szerepkör, és az erőforrás-szolgáltató api-hívások biztonságossá tételére szolgál. A tanúsítvány közzététele a tulajdonosa megegyezik a API DNS-bejegyzést kell tartalmaznia:

| Formátum | Példa |
| --- | --- |
| API.appservice. \<régió\>.\< Tartománynév\>.\< bővítmény\> | API.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Közzététel tanúsítvány

A tanúsítvány a közzétevő szerepkört a FTPS-forgalmat az alkalmazástulajdonosok biztonságossá tételére hogy tartalom feltöltése során.  A tanúsítvány közzététele a tulajdonosa megegyezik a FTPS DNS-bejegyzés tartalmaznia kell.

| Formátum | Példa |
| --- | --- |
| FTP.appservice. \<régió\>.\< Tartománynév\>.\< bővítmény\> | API.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Identitás tanúsítványa

Lehetővé teszi a tanúsítványt az identitás-alkalmazáshoz:
- az AAD/ADFS directory, az Azure verem és az App Service-nek támogatja a integrálását a számítási erőforrás-szolgáltató közötti integráció
- Az egyszeri bejelentkezés a forgatókönyvek az Azure App Service Azure veremben belül speciális fejlesztői eszközök.
Az identitás tanúsítványának tartalmaznia kell a tulajdonosa megegyezik a következő formátumban:

| Formátum | Példa |
| --- | --- |
| sso.appservice. \<régió\>.\< Tartománynév\>.\< bővítmény\> | sso.appservice.redmond.azurestack.external |

### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Bontsa ki az Azure verem Azure Resource Manager főtanúsítvány

A PowerShell-munkamenetben azurestack\CloudAdmin fut hajtsa végre a Get-AzureStackRootCert.ps1 parancsfájlt a mappában, amelyikbe kibontotta a segítő parancsfájlok. A parancsfájl ugyanabban a mappában, amelyet az App Service létrehozása tanúsítványok parancsfájlként négy tanúsítványokat hoz létre.

| Get-AzureStackRootCert.ps1 paraméter | Kötelező/választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| PrivelegedEndpoint | Szükséges | AzS-ERCS01 | Kiemelt végpont. |
| CloudAdminCredential | Szükséges | AzureStack\CloudAdmin | Az Azure verem felhő rendszergazda tartományi fiók hitelesítő adatai |


## <a name="prepare-the-file-server"></a>A fájlkiszolgáló előkészítése

Az Azure App Service egy fájlkiszolgáló használatát igényli. Az üzemi környezetek a fájlkiszolgáló magas rendelkezésre állású legyen konfigurálva és hibák kezelésére képes kell lennie.

Csak az Azure verem szoftverfejlesztői készlet telepítésekben való használatra, az ebben a példában Azure Resource Manager központi telepítési sablon segítségével egy konfigurált egycsomópontos fájlkiszolgáló telepítéséhez: https://aka.ms/appsvconmasdkfstemplate. Az egyetlen csomópont fájlkiszolgáló munkacsoport lesz.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Kiépítés csoportokat és fiókokat az Active Directoryban


1. Hozza létre a következő Active Directory globális biztonsági csoportokat:
    - FileShareOwners
    - FileShareUsers
2. Hozza létre a következő Active Directory-fiókokat szolgáltatásfióknak:
    - FileShareOwner
    - FileShareUser

    Biztonság ajánlott eljárás, a felhasználók ezeket a fiókokat (és az összes webes szerepkör) legyen egymástól és erős felhasználói neveket és jelszavakat.
    Állítsa be a jelszavakat, a következő feltételeknek:
     - Engedélyezése **jelszó sohasem jár le**.
     - Engedélyezése **felhasználó nem módosíthatja a jelszót**.
     - Tiltsa le a **kell változtatni a jelszót a következő bejelentkezéskor**.
3. Adja hozzá a fiókokat a csoporttagságokat az alábbiak szerint:
    - Adja hozzá **FileShareOwner** számára a **FileShareOwners** csoport.
    - Adja hozzá **FileShareUser** számára a **FileShareUsers** csoport.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Csoportok és fiókok munkacsoportban kiépítése

>[!NOTE]
> Hajtsa végre a következő parancsokat minden, a fájlkiszolgáló egy rendszergazdai jogú parancssort munkamenetben konfigurálásakor.  **Ne használjon PowerShell.**

A fenti Azure Resource Manager-sablon használatakor a felhasználóknak már létre vannak hozva.

1. A következő parancsokat a FileShareOwner és FileShareUser fiókokat létrehozni. Cserélje le <password> saját értékekkel.
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. Állítsa be a jelszó soha nem lejár a következő WMIC-parancsok futtatásával:
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. A helyi csoportok FileShareUsers és FileShareOwners létrehozása, és adja hozzá a fiókokat az első lépésben őket.
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>A tartalommegosztás kiépítése

A tartalommegosztás bérlői webhely tartalmát tartalmazza. A tartalom könyvtármegosztáson található a fájlkiszolgáló telepítéséhez az eljárás megegyezik a Active Directory és a munkacsoport környezetekben, de különböző feladatátvevő fürt az Active Directoryban.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>A fájlkiszolgáló a tartalommegosztás kiépítése (AD vagy munkacsoporthoz)

Az önálló fájlkiszolgálóról futtassa a következő parancsokat egy rendszergazda jogú parancssorba. Cserélje le az érték a "C:\WebSites" megfelelő útvonalaira a környezetben.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>Ahhoz, hogy a Rendszerfelügyeleti webszolgáltatások, adja hozzá a FileShareOwners csoportot a helyi Rendszergazdák csoport

Ahhoz, hogy a Rendszerfelügyeleti webszolgáltatások megfelelően működjön fel kell vennie a FileShareOwners csoportot a helyi rendszergazdák csoporthoz.

#### <a name="active-directory"></a>Active Directory

A következő parancsokat egy rendszergazda jogú parancssorba a fájlkiszolgálón, vagy minden fájlkiszolgáló feladatátvevő fürt csomóponton. Cserélje le a következő <DOMAIN> a használni kívánt tartomány nevét.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>Munkacsoport

A következő parancsot egy rendszergazda jogú parancssorba a fájlkiszolgálón.

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>A megosztás hozzáférés-vezérlés konfigurálása

A következő parancsokat egy rendszergazda jogú parancssorba a fájlkiszolgálón vagy a fájlkiszolgáló feladatátvevő fürt csomóponton, amely az aktuális fürt erőforrás tulajdonosa. Cserélje le a dőlt értékek adott környezetre értékekkel.

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

## <a name="prepare-the-sql-server"></a>Az SQL-kiszolgáló előkészítése

Az Azure App Service Azure verem futtató és a mérési adatbázisok elő kell készítenie egy SQL Server az Azure App Service-adatbázisok tárolásához.

Az Azure verem szoftverfejlesztői készlet való használatra, használhatja az SQL Express 2014 SP2 vagy újabb.

Éles és magas rendelkezésre állás érdekében, kell teljes verziót az SQL 2014 SP2 vagy újabb, kevert módú hitelesítés engedélyezése, és telepítenie a egy [magas rendelkezésre állású konfigurációban](https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Az Azure App Service Azure verem SQL-kiszolgálón az összes App Service-szerepkörből elérhetőnek kell lennie. SQL Server az alapértelmezett szolgáltató előfizetésben Azure verem is telepíthető. Lehetőség a szervezeten belül a meglévő infrastruktúra használata (feltéve, nincs kapcsolat az Azure-verem). Ha Azure piactér lemezkép használ, ne felejtse el ennek megfelelően konfigurálja a tűzfalat. 

Az SQL Server szerepkörök bármelyikéhez egy alapértelmezett vagy megnevezett példányt is használhatja. Ha egy megnevezett példányt használ, ne feledje, hogy manuális módszerrel indítsa el az SQL Browser szolgáltatás, és nyissa meg a portot 1434.

## <a name="create-an-azure-active-directory-application"></a>Egy Azure Active Directory-alkalmazás létrehozása

Adja meg az Azure AD szolgáltatás egyszerű az alábbiak támogatásához:
- Virtuális gép méretezési integrációs munkavégző rétegen.
- Egyszeri bejelentkezés az Azure Functions portálon és speciális fejlesztői eszközök.

Ezeket a lépéseket az Azure AD-Azure verem környezetekben csak védett alkalmazni.

A rendszergazdák az SSO kell konfigurálni:
- Engedélyezze a speciális fejlesztői eszközök, az App Service (Kudu) belül.
- Az Azure Functions portálon élmény használatának engedélyezése.

Kövesse az alábbi lépéseket:

1. Nyisson meg egy PowerShell-példány azurestack\AzureStackAdmin.
2. Nyissa meg a letöltött és kibontott a parancsfájlok a helyét a [előfeltétel-ellenőrzési lépés](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [A verem az Azure PowerShell telepítése](azure-stack-powershell-install.md).
4. Futtassa a **létrehozás-AADIdentityApp.ps1** parancsfájl. Amikor az Azure AD-bérlőazonosító beszerzése kéri, adja meg a sablonverziót Azure verem, például myazurestack.onmicrosoft.com használata az Azure AD bérlői azonosító.
5. Az a **Credential** ablak, írja be a Azure AD szolgáltatás-rendszergazdai fiókot és jelszót. Kattintson az **OK** gombra.
6. Adja meg a tanúsítvány elérési útja és a tanúsítvány jelszavát az [korábban létrehozott tanúsítvány](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). A tanúsítványt, az alapértelmezés szerint ez a lépés létrehozott **sso.appservice.local.azurestack.external.pfx**.
7. A parancsfájl létrehoz egy új alkalmazást az Azure AD-bérlőben. Jegyezze fel az alkalmazás Azonosítóját, amelynek PowerShell kimenet ad vissza. A telepítés során tájékoztatásra van szüksége.
8. Nyisson meg egy új böngészőablakot, és jelentkezzen be az Azure portálon (portal.azure.com) felhasználóként a **Azure Active Directory szolgáltatás-rendszergazda**.
9. Nyissa meg az Azure AD erőforrás-szolgáltató.
10. Kattintson a **App regisztrációk**.
11. Keresse meg a **Alkalmazásazonosító** 7. lépés részeként. Egy App Service alkalmazás szerepel.
12. Kattintson a **alkalmazás** a listában
13. Kattintson a **szükséges engedélyek** > **engedélyeket** > **Igen**.

| Hozzon létre AADIdentityApp.ps1 paraméter | Kötelező/választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| DirectoryTenantName | Szükséges | NULL értékű | Az Azure AD-bérlő azonosítója. Adja meg a globálisan egyedi Azonosítót vagy karakterlánc, például myazureaaddirectory.onmicrosoft.com |
| AdminArmEndpoint | Szükséges | NULL értékű | A rendszergazda Azure Resource Manager végpont, például adminmanagement.local.azurestack.external |
| TenantARMEndpoint | Szükséges | NULL értékű | A bérlői Azure Resource Manager-végpontot, például: management.local.azurestack.external |
| AzureStackAdminCredential | Szükséges | NULL értékű | Az Azure AD szolgáltatás rendszergazdai hitelesítő adatait |
| A CertificateFilePath | Szükséges | NULL értékű | A korábban létrehozott identitás alkalmazás tanúsítvány fájl elérési útja. |
| CertificatePassword | Szükséges | NULL értékű | A tanúsítvány titkos kulcsának védelmére használt jelszót. |

## <a name="create-an-active-directory-federation-services-application"></a>Active Directory összevonási szolgáltatások alkalmazás létrehozása

AD FS által védett Azure verem környezetben konfigurálnia kell egy Active Directory összevonási szolgáltatások szolgáltatás egyszerű támogatásához a következő:
- Virtuális gép méretezési integrációs munkavégző rétegen.
- Egyszeri bejelentkezés az Azure Functions portálon és speciális fejlesztői eszközök.

A rendszergazdáknak kell az egyszeri bejelentkezés konfigurálása:
- Konfigurálása egyszerű virtuális gép méretezési készlet integrációs szolgáltatás munkavégző rétegen.
- Engedélyezze a speciális fejlesztői eszközök, az App Service (Kudu) belül.
- Az Azure Functions portálon élmény használatának engedélyezése.

Kövesse az alábbi lépéseket:

1. Nyisson meg egy PowerShell-példány azurestack\azurestackadmin.
2. Nyissa meg a letöltött és kibontott a parancsfájlok a helyét a [előfeltétel-ellenőrzési lépés](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [A verem az Azure PowerShell telepítése](azure-stack-powershell-install.md).
4.  Futtassa a **létrehozás-ADFSIdentityApp.ps1** parancsfájl.
5.  Az a **hitelesítő adat** ablak, írja be az AD FS felhőalapú rendszergazdai fiókot és jelszót. Kattintson az **OK** gombra.
6.  Adja meg a tanúsítvány elérési útja és a tanúsítvány jelszavát az [korábban létrehozott tanúsítvány](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). Alapértelmezés szerint ebben a lépésben létrehozott tanúsítvány sso.appservice.local.azurestack.external.pfx.

| Hozzon létre ADFSIdentityApp.ps1 paraméter | Kötelező/választható | Alapértelmezett érték | Leírás |
| --- | --- | --- | --- |
| AdminArmEndpoint | Szükséges | NULL értékű | Az Azure Resource Manager admin végpont. Például adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Szükséges | NULL értékű | Kiemelt végpont. Például AzS-ERCS01. |
| CloudAdminCredential | Szükséges | NULL értékű | Az Azure verem cloudadmin tartományi fiók hitelesítő adatait. Például Azurestack\CloudAdmin. |
| A CertificateFilePath | Szükséges | NULL értékű | Identitás alkalmazás tanúsítvány PFX-fájljának elérési útja. |
| CertificatePassword | Szükséges | NULL értékű | A tanúsítvány titkos kulcsának védelmére használt jelszót. |


## <a name="next-steps"></a>Következő lépések

[Az App Service erőforrás-szolgáltató telepítéséhez](azure-stack-app-service-deploy.md).
