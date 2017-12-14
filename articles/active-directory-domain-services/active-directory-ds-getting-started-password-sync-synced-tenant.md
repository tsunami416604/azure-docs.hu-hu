---
title: "Azure AD tartományi szolgáltatások: Jelszavak szinkronizálásának engedélyezése | Microsoft Docs"
description: "Első lépések az Azure Active Directory tartományi szolgáltatások használatával"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 0f6204e8f0f779809cd9c657acbcbcf39d57d481
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Jelszavak szinkronizálásának engedélyezése az Azure Active Directory Domain Services tartományi szolgáltatásokra
Az előző feladatokban engedélyezte az Active Directory Domain Servicest az Azure Active Directory (Azure AD) bérlő számára. A következő feladat az NT LAN Manager (NTLM) és Kerberos hitelesítésiadat-kivonatok Azure AD tartományi szolgáltatásokkal való szinkronizálásának engedélyezése. A bejelentkezési adatok szinkronizálásának beállítását követően a felhasználók a vállalati hitelesítői adataikkal jelentkezhetnek be a felügyelt tartományba.

A folyamat lépései eltérőek a csak felhőalapú felhasználói fiókok és a helyszíni könyvtárból az Azure AD Connect használatával szinkronizált felhasználói fiókok esetében.

<br>
| **A felhasználói fiók típusa** | **A végrehajtandó lépések** |
| --- | --- |
| **Helyszíni címtárból szinkronizált felhasználói fiókok** |**&#x2713;** [Kövesse a cikkben megadott utasításokat](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) | 
| **Az Azure AD-ben létrehozott felhőalapú felhasználói fiókok** |**&#x2713;** [Jelszavak szinkronizálása a felügyelt tartományra csak felhőalapú felhasználói fiókok esetében](active-directory-ds-getting-started-password-sync.md) |
<br>

> [!TIP]
> **Lehetséges, hogy mindkét lépéssorozatot végre kell hajtania.**
> Ha az Azure AD-bérlő csak felhőalapú és a helyszíni AD-ből származó felhasználókkal is rendelkezik, mindkét lépéssorozatot végre kell hajtania.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>5. feladat: jelszavak szinkronizálásának engedélyezése a felügyelt tartományra a helyszíni AD-vel szinkronizált felhasználói fiókok számára
A szinkronizált Azure AD-bérlő a szervezet helyi címtárával való szinkronizálásra van beállítva az Azure AD Connect használatával. Az Azure AD Connect alapértelmezés szerint nem szinkronizálja az NTLM és Kerberos hitelesítő adatok kivonatait az Azure AD-val. Az Azure AD tartományi szolgáltatások használatához az Azure AD Connectet úgy kell konfigurálni, hogy szinkronizálja az NTLM- és Kerberos-hitelesítéshez szükséges hitelesítőadat-kivonatokat. Az alábbi lépésekkel engedélyezheti a helyszíni címtárból származó kivonatok Azure AD-bérlővel való szinkronizálását.

> [!NOTE]
> **Ha a szervezet rendelkezik a helyszíni címtárból szinkronizált felhasználói fiókokkal, akkor a felügyelt tartomány használatához engedélyeznie kell az NTLM- és Kerberos-kivonatok szinkronizálását.** A szinkronizált felhasználói fiókok olyan fiókok, amelyek a helyszíni címtárban lettek létrehozva, és az Azure AD Connect használatával szinkronizálnak az Azure AD-bérlővel.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Azure AD Connect telepítése vagy frissítése
Telepítse az Azure AD Connect legújabb ajánlott kiadását egy tartományhoz csatlakoztatott számítógépre. Ha az Azure AD Connect egy példánya már telepítve van, frissítse az Azure AD Connect legújabb verziójára. A már ismert és esetleg már javított problémák/hibák elkerülése érdekében mindig az Azure AD Connect legújabb verzióját használja.

**[Az Azure AD Connect letöltése](http://www.microsoft.com/download/details.aspx?id=47594)**

Ajánlott verzió: **1.1.614.0** – közzététel dátuma: 2017. szeptember 5.

> [!WARNING]
> Az Azure AD Connect legújabb ajánlott verzióját FELTÉTLENÜL telepítenie kell ahhoz, hogy az örökölt (NTLM és Kerberos hitelesítéshez szükséges) jelszavas hitelesítő adatokat szinkronizálni lehessen az Azure AD bérlőhöz. Ez a funkció nem érhető el az Azure AD Connect korábbi verzióiban vagy az örökölt DirSync eszközzel.
>
>

Az Azure AD Connect telepítési utasításai a következő cikkben érhetők el: [Ismerkedés az Azure AD Connecttel](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>NTLM és Kerberos hitelesítőadat-kivonatok Azure AD-val történő szinkronizálásának engedélyezése
Hajtsa végre a következő PowerShell-szkriptet az összes AD-erdőben. A szkript engedélyezi a helyszíni felhasználók NTLM- és Kerberos-jelszókivonatainak szinkronizálását az Azure AD-bérlővel. A szkript az Azure AD Connectben történő teljes szinkronizálást is kezdeményezi.

```powershell
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
