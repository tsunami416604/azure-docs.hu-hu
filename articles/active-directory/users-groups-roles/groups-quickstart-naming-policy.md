---
title: Elnevezési szabályzat hozzáadása Office 365-csoportokhoz az Azure Active Directoryban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan vehet fel új felhasználókat, vagy hogyan törölhet meglévő felhasználókat az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/08/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8ebdb22ba5ca04a5c811b3b368055f5f4371c75f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "40208905"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Rövid útmutató: Elnevezési szabályzat az Azure Active Directoryban lévő csoportokhoz

Ebben a rövid útmutatóban elnevezési szabályzatot állíthat be az Azure Active Directory (Azure AD) bérlőjében a felhasználók által létrehozott Office 365-csoportokhoz, hogy a bérlő csoportjainak rendszerezése és keresése egyszerűbbé váljon. Az elnevezési szabályzatot például a következőkre használhatja:

* Tájékoztatást adhat a csoport funkciójáról, tagságáról, földrajzi régiójáról vagy a csoport létrehozójáról.
* Segíthet a csoportok a címtárban való besorolásában.
* Letilthatja bizonyos szavak használatát a csoportnevekben és aliasokban.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="install-powershell-cmdlets"></a>PowerShell-parancsmagok telepítése

A PowerShell-parancsok futtatása előtt mindenképpen távolítsa el a Windows PowerShellhez készült Azure Active Directory PowerShell for Graph modul összes régebbi verzióját, és telepítse az [Azure Active Directory PowerShell for Graph 2.0.0.137-es nyilvános előzetes kiadását](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137). 

1. Nyissa meg a Windows PowerShell alkalmazást rendszergazdaként.
2. Távolítsa el az AzureADPreview korábbi verzióit.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Telepítse az AzureADPreview legújabb verzióját.
  
  ````
  Install-Module AzureADPreview
  ````
Ha a rendszer megerősítését kér a nem megbízható adattár eléréséhez, nyomja le az **Y** billentyűt. Az új modul telepítése igénybe vehet néhány percet.

## <a name="set-up-naming-policy"></a>Elnevezési szabályzat beállítása

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>1. lépés: Jelentkezzen be PowerShell-parancsmagok használatával

1. Nyissa meg a Windows PowerShell alkalmazást. Nincs szükség emelt szintű jogosultságokra.

2. Futtassa a következő parancsokat a parancsmagok futtatásának előkészítéséhez.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  A megjelenő **Bejelentkezés a fiókba** párbeszédpanelen adja meg a rendszergazdai fiókot és jelszót, hogy kapcsolatot létesítsen a szolgáltatással, majd válassza a **Bejelentkezés** lehetőséget.

3. Kövesse az [Azure Active Directory-parancsmagok a csoportbeállítások konfigurálásához](groups-settings-cmdlets.md) című rész lépéseit a bérlő csoportbeállításainak létrehozásához.

### <a name="step-2-view-the-current-settings"></a>2. lépés: Az aktuális beállítások megtekintése

1. Tekintse meg az aktuális elnevezési szabályzat beállításait.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Jelenítse meg az aktuális csoportbeállításokat.
  
  ````
  $Setting.Values
  ````
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>3. lépés: Állítsa be az elnevezési szabályzatot és az egyéni letiltott szavakat

1. Állítsa be a csoportnév előtagjait és utótagjait az Azure AD PowerShellben.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Állítsa be az egyéni letiltott szavakat, amelyek használatát korlátozni szeretné. A következő példa szemlélteti, hogyan adhatja hozzá saját egyéni szavait.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Az új szabályzat alkalmazáshoz mentse a beállításait a következő példának megfelelően.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
Ennyi az egész. Beállította az elnevezési szabályzatot és hozzáadta az egyéni letiltott szavak listáját.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Állítsa be a csoportnév előtagjait és utótagjait az Azure AD PowerShellben.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =""
  ````
  
2. Állítsa be az egyéni letiltott szavakat, amelyek használatát korlátozni szeretné. A következő példa szemlélteti, hogyan adhatja hozzá saját egyéni szavait.
  
  ````
  $Setting["CustomBlockedWordsList"]=""
  ````
  
3. Az új szabályzat alkalmazáshoz mentse a beállításait a következő példának megfelelően.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megismerhette, hogyan állíthat be elnevezési szabályzatot Azure AD-bérlőjéhez a PowerShell-parancsmagok segítségével.

További információért a technikai korlátozásokról, az egyéni letiltott szavak listájának hozzáadásáról és az Office 365-alkalmazások végfelhasználói élményéről tekintse meg a következő cikket, amely az elnevezési szabályzat részleteiről ad tájékoztatást:
> [!div class="nextstepaction"]
> [Elnevezési szabályzat részletei](groups-naming-policy.md)