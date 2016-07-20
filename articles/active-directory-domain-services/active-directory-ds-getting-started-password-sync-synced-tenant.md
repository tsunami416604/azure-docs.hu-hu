<properties
    pageTitle="Azure AD tartományi szolgáltatások: Jelszó-szinkronizálás engedélyezése | Microsoft Azure"
    description="Ismerkedés az Azure Active Directory tartományi szolgáltatásokkal"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="maheshu"/>

# Azure AD tartományi szolgáltatások *(előzetes verzió)* – Jelszó-szinkronizálás engedélyezése az Azure AD tartományi szolgáltatásokra

## 5. feladat: Jelszó-szinkronizálás engedélyezése AAD tartományi szolgáltatásokra szinkronizált Azure AD bérlő esetén
Miután engedélyezte az Azure AD tartományi szolgáltatásokat az Azure AD-címtárhoz, a következő feladat a jelszó-szinkronizálás engedélyezése az Azure AD tartományi szolgáltatásokra. Ez lehetővé teszi a felhasználók számára a tartományba történő bejelentkezést a vállalati hitelesítő adatok használatával.

Az ehhez szükséges lépések eltérőek aszerint, hogy a szervezet csak felhőalapú Azure AD-címtárral rendelkezik-e, vagy be van állítva az Azure AD Connect használatával a helyszíni címtárral történő szinkronizálás.

<br>

> [AZURE.SELECTOR]
- [Csak felhőalapú Azure AD-címtár](active-directory-ds-getting-started-password-sync.md)
- [Szinkronizált Azure AD-címtár](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Szinkronizált bérlők – NTLM és Kerberos hitelesítő kivonatok Azure AD-hoz történő szinkronizálásának engedélyezése
Ha a szervezet Azure AD bérlője be van állítva az Azure AD Connect használatával a helyszíni címtárral történő szinkronizálásra, akkor az Azure AD Connectet úgy kell konfigurálni, hogy szinkronizálja az NTLM és Kerberos hitelesítéshez szükséges hitelesítő kivonatokat. Ezek a kivonatok alapértelmezés szerint nincsenek szinkronizálva az Azure AD-hoz, és az alábbi lépésekkel engedélyezheti a kivonatok szinkronizálását az Azure AD bérlőhöz.

#### Azure AD Connect telepítése vagy frissítése

Telepítse az Azure AD Connect legújabb ajánlott verzióját a tartományhoz csatlakoztatott számítógépre. Ha az Azure AD Connect egy példánya már telepítve van, frissítse úgy, hogy az Azure AD Connect GA változatot használja. A már ismert és esetleg már javított problémák/hibák elkerülése érdekében győződjön meg arról, hogy az Azure AD Connect legújabb verzióját használja.

**[Az Azure AD Connect letöltése](http://www.microsoft.com/download/details.aspx?id=47594)**

Ajánlott verzió: **1.1.130.0** – közzétéve: 2016. április 12.

  > [AZURE.WARNING] Az Azure AD Connect legújabb ajánlott verzióját FELTÉTLENÜL telepítenie kell  ahhoz, hogy az örökölt (NTLM és Kerberos hitelesítéshez szükséges) jelszavas hitelesítő adatokat szinkronizálni lehessen az Azure AD bérlőhöz. Ez a funkció nem érhető el az Azure AD Connect korábbi verzióiban vagy az örökölt DirSync eszközzel.

Az Azure AD Connect telepítési utasításai a következő cikkben érhetők el: [Ismerkedés az Azure AD Connecttel](../active-directory/active-directory-aadconnect.md)


#### Teljes jelszó-szinkronizálás Azure AD-hoz

A teljes jelszó-szinkronizálás és a helyszíni felhasználók jelszókivonatainak (beleértve az NTLM/Kerberos-hitelesítéshez szükséges hitelesítő kivonatokat) Azure AD bérlővel való szinkronizálásának engedélyezése érdekében hajtsa végre a következő PowerShell parancsfájlt minden AD-erdőben.

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

A címtár méretétől (felhasználók, csoportok stb. száma) függ, hogy a hitelesítő adatok Azure AD-hoz történő szinkronizálása mennyi időt vesz igénybe. A jelszavak a hitelesítő kivonatok Azure AD-hoz történő szinkronizálását követően rövid időn belül használhatóvá válnak az Azure AD tartományi szolgáltatások által kezelt tartományban.


<br>

## Kapcsolódó tartalom

- [Jelszó-szinkronizálás engedélyezése AAD tartományi szolgáltatásokra csak felhőalapú Azure AD címtárhoz](active-directory-ds-getting-started-password-sync.md)

- [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)

- [Windows virtuális gép csatlakoztatása Azure AD tartományi szolgáltatások által kezel tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)

- [Red Hat Enterprise Linux-alapú virtuális gép csatlakoztatása Azure AD tartományi szolgáltatások által kezelt tartományhoz](active-directory-ds-admin-guide-join-rhel-linux-vm.md)



<!--HONumber=Jun16_HO2-->


