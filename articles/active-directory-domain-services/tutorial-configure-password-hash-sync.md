---
title: Jelszókivonat-szinkronizálás engedélyezése az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan engedélyezheti a jelszókivonat-szinkronizálást az Azure AD Connect használatával egy Azure Active Directory tartományi szolgáltatások felügyelt tartományához.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 93e5ee9b46fb3387b70dd5092f72efcaa8a2bc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239167"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Oktatóanyag: Jelszó-szinkronizálás engedélyezése az Azure Active Directory tartományi szolgáltatásokban hibrid környezetekben

Hibrid környezetekben egy Azure Active Directory (Azure AD) bérlő konfigurálható úgy, hogy szinkronizáljon egy helyszíni Active Directory tartományi szolgáltatások (AD DS) környezetben az Azure AD Connect használatával. Alapértelmezés szerint az Azure AD Connect nem szinkronizálja az Azure Active Directory tartományi szolgáltatásokhoz (Azure AD DS) szükséges örökölt NT LAN-kezelőt (NTLM) és a Kerberos-jelszókivéteket.

Az Azure AD DS használatához a helyszíni ActiveÁd-ds-környezetből szinkronizált fiókok használatával konfigurálnia kell az Azure AD Connect szolgáltatást az NTLM- és Kerberos-hitelesítéshez szükséges jelszókivétek szinkronizálásához. Az Azure AD Connect konfigurálása után egy helyszíni fiók létrehozása vagy jelszómódosítási esemény is szinkronizálja az örökölt jelszókitagokat az Azure AD-vel.

Nem kell végrehajtania ezeket a lépéseket, ha csak a helyszíni AD DS-környezet nélküli felhőalapú fiókokat használ.

Ebben az oktatóanyagban a következőket tanulhatja meg:

> [!div class="checklist"]
> * Miért van szükség régebbi NTLM- és Kerberos-jelszókivétre?
> * A régi jelszókivonat-szinkronizálás konfigurálása az Azure AD Connecthez

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő erőforrásokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az Azure Active Directory-bérlő az előfizetéshez társítva, amely szinkronizálva van egy helyszíni címtárral az Azure AD Connect használatával.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
    * Ha szükséges, [engedélyezze az Azure AD Connect et a jelszókivonat-szinkronizáláshoz.][enable-azure-ad-connect]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Jelszókivonat-szinkronizálás az Azure AD Connect használatával

Az Azure AD Connect segítségével szinkronizálhatja az objektumokat, például a felhasználói fiókokat és csoportokat egy helyszíni ActiveAngeles-szolgáltatásokból egy Azure AD-bérlőbe. A folyamat részeként a jelszókivonat-szinkronizálás lehetővé teszi, hogy a fiókok ugyanazt a jelszót használják az on-prem AD DS környezetben és az Azure AD-ben.

A felügyelt tartomány felhasználóinak hitelesítéséhez az Azure AD DS-nek jelszókivonatokra van szüksége az NTLM és a Kerberos-hitelesítéshez megfelelő formátumban. Az Azure AD nem tárolja a jelszókivonatokat az NTLM- vagy Kerberos-hitelesítéshez szükséges formátumban, amíg nem engedélyezi az Azure AD DS-t a bérlő számára. Biztonsági okokból az Azure AD is nem tárolja a jelszó hitelesítő adatokat a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan generálni ezeket az NTLM- vagy Kerberos-jelszókivét a felhasználók meglévő hitelesítő adatai alapján.

Az Azure AD Connect konfigurálható úgy, hogy szinkronizálja az Azure AD DS szükséges NTLM- vagy Kerberos-jelszó-kivétit. Győződjön meg arról, hogy elvégezte az [Azure AD Connect jelszókivonat-szinkronizáláshoz való engedélyezéséhez][enable-azure-ad-connect]szükséges lépéseket. Ha az Azure AD Connect egy meglévő példányával rendelkezett, [töltse le és frissítse a legújabb verzióra,][azure-ad-connect-download] hogy szinkronizálhassa az NTLM és a Kerberos örökölt jelszókivétit. Ez a funkció nem érhető el az Azure AD Connect korai kiadásaiban vagy az örökölt DirSync eszközzel. Az Azure AD Connect *1.1.614.0-s* vagy újabb verziója szükséges.

> [!IMPORTANT]
> Az Azure AD Connect csak telepíteni és konfigurálni a helyszíni AD DS-környezetekkel való szinkronizáláshoz. Nem támogatott az Azure AD Connect telepítése egy Azure AD DS által felügyelt tartományban az objektumok Azure AD-vel való szinkronizálásához.

## <a name="enable-synchronization-of-password-hashes"></a>Jelszókivétek szinkronizálásának engedélyezése

Az Azure AD Connect telepítve van, és úgy van konfigurálva, hogy szinkronizáljon az Azure AD-vel, most konfigurálja az örökölt jelszókivonat-szinkronizálást az NTLM és a Kerberos számára. A PowerShell-parancsfájl a szükséges beállítások konfigurálására szolgál, majd elindítja a teljes jelszó-szinkronizálást az Azure AD-vel. Ha az Azure AD Connect jelszókivonat-szinkronizálási folyamata befejeződött, a felhasználók bejelentkezhetnek az alkalmazásokba az Azure AD DS-en keresztül, amelyek örökölt NTLM- vagy Kerberos-jelszókivonatokat használnak.

1. Azokon a számítógépen, amelyen telepítve van az Azure AD Connect, a Start menüből nyissa meg az **Azure AD Connect > szinkronizálási szolgáltatást.**
1. Válassza az **Összekötők** lapot. A helyszíni AD DS-környezet és az Azure AD közötti szinkronizálás létrehozásához használt kapcsolati adatok fel vannak sorolva.

    A **típus** vagy *a Windows Azure Active Directoryt (Microsoft)* jelzi az Azure AD-összekötőhöz, vagy az *Active Directory tartományi szolgáltatások* a helyszíni ActiveD DS-összekötőhöz. Jegyezze fel a PowerShell-parancsfájlban a következő lépésben használandó összekötőneveket.

    ![Az összekötőnevek listázása a Szinkronizálási szolgáltatáskezelőben](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    Ebben a példában a képernyőkép a következő összekötőket használja:

    * Az Azure AD-összekötő neve *aaddscontoso.onmicrosoft.com - AAD*
    * A helyszíni AD DS-összekötő neve *onprem.contoso.com*

1. Másolja és illessze be a következő PowerShell-parancsfájlt a számítógépre az Azure AD Connect telepítésével. A parancsfájl elindítja a teljes jelszószinkronizálást, amely örökölt jelszókiírásokat tartalmaz. Frissítse `$azureadConnector` a `$adConnector` változókat az előző lépés összekötőneveivel.

    Futtassa ezt a parancsfájlt minden AD-erdőben az NTLM- és Kerberos-jelszókivétek Azure AD-vel való szinkronizálásához.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Attól függően, hogy a címtár a fiókok száma és a csoportok számát tekintve, az örökölt jelszókivétek szinkronizálása az Azure AD-hez eltarthat egy ideig. A jelszavakat ezután szinkronizálja az Azure AD DS felügyelt tartomány, miután szinkronizálja az Azure AD-vel.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket tanulta meg:

> [!div class="checklist"]
> * Miért van szükség régebbi NTLM- és Kerberos-jelszókivétre?
> * A régi jelszókivonat-szinkronizálás konfigurálása az Azure AD Connecthez

> [!div class="nextstepaction"]
> [A szinkronizálás működése az Azure AD tartományi szolgáltatások által kezelt tartományban](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
