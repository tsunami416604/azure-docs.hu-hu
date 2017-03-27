---
title: "Azure AD tartományi szolgáltatások: Jelszavak szinkronizálásának engedélyezése | Microsoft Docs"
description: "Első lépések az Azure Active Directory tartományi szolgáltatások használatával"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/17/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 4969b43831a3813a4e76c6447c252a9c458f371a
ms.lasthandoff: 03/17/2017


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Jelszavak szinkronizálásának engedélyezése az Azure AD tartományi szolgáltatásokra
Az előző feladatokban engedélyezte az Azure AD tartományi szolgáltatásokat az Azure AD-bérlő számára. A következő feladat az Azure AD tartományi szolgáltatásokkal való jelszó-szinkronizálás engedélyezése. Ha a bejelentkezési adatok szinkronizálása be van állítva, a felhasználók a vállalati hitelesítői adataikkal jelentkezhetnek be a felügyelt tartományra.

Az ehhez szükséges lépések eltérőek aszerint, hogy a szervezet csak felhőalapú Azure AD-címtárral rendelkezik-e, vagy be van állítva az Azure AD Connect használatával a helyszíni címtárral történő szinkronizálás.

<br>

> [!div class="op_single_selector"]
> * [Kizárólag felhőalapú Azure AD-bérlő](active-directory-ds-getting-started-password-sync.md)
> * [Szinkronizált Azure AD-bérlő](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>5. feladat: Jelszó-szinkronizálás engedélyezése AAD tartományi szolgáltatásokra szinkronizált Azure AD bérlő esetén
A szinkronizált Azure AD-bérlő a szervezet helyi címtárával való szinkronizálásra van beállítva az Azure AD Connect használatával. Az Azure AD Connect alapértelmezés szerint nem szinkronizálja az NTLM és Kerberos hitelesítő adatok kivonatait az Azure AD-val. Az Azure AD tartományi szolgáltatások használatához az Azure AD Connectet úgy kell konfigurálni, hogy szinkronizálja az NTLM- és Kerberos-hitelesítéshez szükséges hitelesítőadat-kivonatokat. 

> [!WARNING]
> Minden alkalommal, amikor engedélyezi az Azure AD tartományi szolgáltatásokat, a jelszó-szinkronizálást is engedélyeznie kell. Előfordulhat, hogy korábban engedélyezte, majd kikapcsolta az Azure AD tartományi szolgáltatásokat az Azure AD-címtárhoz. Ebben az esetben is engedélyeznie kell a jelszó-szinkronizálást a következő alkalommal, amikor engedélyezi az Azure AD tartományi szolgáltatások használatát a címtáron.
>
>

Az alábbi lépésekkel engedélyezheti a kivonatok az Azure AD-bérlővel való szinkronizálását.

### <a name="install-or-update-azure-ad-connect"></a>Azure AD Connect telepítése vagy frissítése
Telepítse az Azure AD Connect legújabb ajánlott kiadását egy tartományhoz csatlakoztatott számítógépre. Ha az Azure AD Connect egy példánya már telepítve van, frissítse az Azure AD Connect legújabb verziójára. A már ismert és esetleg már javított problémák/hibák elkerülése érdekében győződjön meg arról, hogy az Azure AD Connect legújabb verzióját használja.

**[Az Azure AD Connect letöltése](http://www.microsoft.com/download/details.aspx?id=47594)**

Ajánlott verzió: **1.1.281.0** – közzététel dátuma: 2016. szeptember 7.

> [!WARNING]
> Az Azure AD Connect legújabb ajánlott verzióját FELTÉTLENÜL telepítenie kell ahhoz, hogy az örökölt (NTLM és Kerberos hitelesítéshez szükséges) jelszavas hitelesítő adatokat szinkronizálni lehessen az Azure AD bérlőhöz. Ez a funkció nem érhető el az Azure AD Connect korábbi verzióiban vagy az örökölt DirSync eszközzel.
>
>

Az Azure AD Connect telepítési utasításai a következő cikkben érhetők el: [Ismerkedés az Azure AD Connecttel](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>NTLM és Kerberos hitelesítőadat-kivonatok Azure AD-val történő szinkronizálásának engedélyezése
A teljes jelszó-szinkronizálás és a helyszíni felhasználók jelszókivonatainak Azure AD-bérlővel való szinkronizálásának engedélyezéséhez hajtsa végre a következő PowerShell-parancsfájlt minden AD-erdőben. A parancsfájl engedélyezi az NTLM-/Kerberos-hitelesítéshez szükséges hitelesítő adatok szinkronizálását az Azure AD-bérlővel.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

A címtár méretétől (felhasználók, csoportok stb. száma) függ, hogy a jelszókivonatok Azure AD-val történő szinkronizálása mennyi időt vesz igénybe. A jelszavak a hitelesítő kivonatok Azure AD-hoz történő szinkronizálását követően rövid időn belül használhatóvá válnak az Azure AD tartományi szolgáltatások által kezelt tartományban.

<br>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Jelszó-szinkronizálás engedélyezése AAD tartományi szolgáltatásokra csak felhőalapú Azure AD címtárhoz](active-directory-ds-getting-started-password-sync.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [Windows virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Red Hat Enterprise Linux virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

