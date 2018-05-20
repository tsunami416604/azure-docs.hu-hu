---
title: Azure verem API-verzió profilok kezelése |} Microsoft Docs
description: További információk a API verziója profilok Azure-készletben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: e568ffd2c3adb97ed0b727b85e7888fb797db1f9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Azure verem API-verzió profilok kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

API-profilok adja meg, az Azure erőforrás-szolgáltató és a végpontok Azure REST API-verzió. Egyéni ügyfelek különböző nyelveken API-profilok használatával hozhat létre. Minden ügyfél az API-profilt használja a helyes erőforrás-szolgáltató és API-verziót kérjen Azure verem.

Létrehozhat egy alkalmazást az Azure erőforrás-szolgáltató nem kell korlátoznia a pontosan mely mindegyik erőforrás-szolgáltató API verziója kompatibilis Azure verem használható. Az alkalmazás-profilhoz; csak igazítása Az SDK-t a megfelelő API-verzió vált át.

Ez a témakör nyújt segítséget:

 - Ismerje meg, az Azure-verem API-profilok.
 - Ismerje meg, hogyan használhatja API-profilok a megoldások kialakításához.
 - Hol található kód jellemző útmutatást talál.

## <a name="summary-of-api-profiles"></a>API-profilok összegzése

- API-profilok használatosak az Azure erőforrás-szolgáltatók és az API-verziók.
- API-profilok jöttek létre a fejlesztők számára, így több Azure-felhőkben sablonokat hozhatnak létre. A kompatibilis és állandó felület szükség teljesítéséhez készültek.
- Profilok évente négyszer kiadásakor.
- Három profil elnevezési konvenciókat használhatók:
    - **legújabb**  
        A globális Azure-ban, amely a legújabb API verzióját tartalmazza.
    - **yyyy-mm-dd-hybrid**  
    Kiadás dátuma: egy róla ütemben történik, ebben a kiadásban összpontosít konzisztencia és stabilitását több felhőkben. Ez a profil optimális Azure verem kompatibilitási célozza.
    - **éééé-hh-nn-profil** optimális stabilitás és a legújabb funkciók között helyezkedik el.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Az Azure API-profilok és az Azure verem kompatibilitási

A legújabb Azure API-profilok az Azure-verem nem kompatibilisek. A következő tanúsítványelnevezési módszerek segítségével azonosíthatja a Azure verem megoldások használandó profilok.

**legújabb**  
A profil rendelkezik-e a legújabb API-verziók található a globális Azure-ban Azure verem nem fognak működni. **Legújabb** jelentős változásokat legnagyobb száma. A profil a stabilitás és a többi felhőből kompatibilis fenntartott helyezi. Ha a legfrissebb API-verziót használnak, próbál **legújabb** profilt kell használnia.

**Éééé-hh-nn-hibrid**  
Ez a profil március és szeptember felszabadul évente. Ez a profil optimális stabilitás és a különböző felhők kompatibilitás rendelkezik. **Éééé-hh-nn-hibrid** célja, hogy a globális Azure és az Azure-verem. Az ezen profilban szereplő Azure API-verziók lesz azonos Azure veremben felsorolt megfelelően. Ez a profil segítségével kódot a hibrid felhő megoldások fejlesztése.

**yyyy-mm-dd-profile**  
Ehhez a profilhoz. június és December globális Azure szabadul fel. A profil elleni Azure verem; nem fognak működni. általában nem lesznek számos jelentős változásokat. Bár közötti optimális stabilitás és a legújabb funkciókat, a különbség a között csatlakozót **legújabb** , és ezt a profilt, amely **legújabb** mindig a legújabb API-verziók, függetlenül attól, hogy mikor fog állni az API-t jelent meg. Például ha egy új API-verzió holnap a számítási API-t készül, hogy API-verzió megjelenik a **legújabb**, de nem a a **éééé-hh-nn-profil** , mert már létezik ehhez a profilhoz.  **éééé-hh-nn-profil** magában foglalja a legfrissebb. június vagy December előtt kiadott verziók.

## <a name="azure-resource-manager-api-profiles"></a>Az Azure Resource Manager API-profilok

Azure verem nem található a globális Azure API-verziók a legújabb verzióját használja. A megoldás létrehozásakor kell mindegyik az Azure erőforrás-szolgáltató, amely kompatibilis a Azure verem API-verzió található.

Ahelyett, hogy minden erőforrás-szolgáltató és az Azure-verem által támogatott verziót kutatás, mint használhat egy API-profilt. A profilja olyan erőforrás-szolgáltatók és API-verziók. Az SDK-t vagy egy eszközt, az SDK-val készült visszaáll a cél api-verzió van megadva a profilban. Az API-profiljaival megadhat egy Profilverzió egy teljes sablonra, és futásidőben, az Azure erőforrás-kezelő kiválasztja a megfelelő verziót az erőforrás.

API-profilok Azure Resource Manager, például a PowerShell, az Azure parancssori felület, az SDK és a Microsoft Visual Studio code használó eszközök működik. Eszközök és SDK-k segítségével profilok olvassa el a modulok és a szalagtárak, amikor egy alkalmazás felépítése verziójának.

**A profilt használó fejlesztési forgatókönyv**  
Tegyük fel, hogy létrehozásához PowerShell használja:

* A storage-fiók, amely használja a **Microsoft.Storage** erőforrás-szolgáltató, amely támogatja az api-version 2016-03-30.
* A virtuális gép által használt a **Microsoft.Compute** erőforrás-szolgáltató, amely 2015-12-01 api-verzió támogatja.

Keresése és telepítése a PowerShell-modul, amely támogatja a api-verzióit, tárolási és számítási van szüksége, helyett egy profilt is használhatja. A parancsmag ** Install-profil * profilnév x és PowerShell betölti a modulok megfelelő verziójával.

Hasonlóképpen a Python SDK segítségével összeállíthat egy Python-alapú alkalmazást, ha egy profil használhatja. Az SDK-t a megfelelő modulok az erőforrás-szolgáltató a parancsfájlban megadott tölt be.

Fejlesztőként összpontosíthat írása a megoldás. Használhatja egy profilt, hogy tudnák, hogy a kód működni fog-e a profil támogató összes felhő között.

## <a name="api-profile-code-samples"></a>API-profil mintakódok

Kódminták segítséget nyújtanak a megoldás integrálása az Azure veremnek megfelelő a választott nyelv profilok segítségével megtalálhatja. Jelenleg található útmutatást és minták a következő nyelveken:

- **PowerShell**  
Használhatja a **AzureRM.Bootstrapper** modul a PowerShell-galériában API-verzió profilokkal működéséhez szükséges PowerShell-parancsmagok segítségével keresztül érhető el. További információ: [PowerShell használata API-verzió profilok](azure-stack-version-profiles-powershell.md).
- **Azure CLI 2.0**  
Frissítheti a környezet konfigurációjának Azure verem meghatározott API verziója profilt kell használnia. További információ: [használata API verziója profilok az Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
NYISSA meg SDK egy profil a különféle szolgáltatások eltérő verziójú különböző típusú kombinációja. a profilok alatt érhetők el profilok / elérési utat, a verziót a **éééé-hh-nn** formátumban. További információ: [használata API verziója profilokat NYISSA meg a](azure-stack-version-profiles-go.md).
- **Ruby**  
A Ruby SDK az Azure verem erőforrás-kezelőhöz biztosít eszközök segítségével felépítéséhez és az infrastruktúra kezelését. Erőforrás-szolgáltató az SDK tartalmazza a számítási, a virtuális hálózatok és a tárolási Ruby nyelveket. További információ: [Ruby profilokkal használata API verziója](azure-stack-version-profiles-ruby.md)

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](azure-stack-powershell-configure-user.md)
* [Erőforrás-szolgáltató API verziókat támogatja a profilok kapcsolatos adatokat](azure-stack-profiles-azure-resource-manager-versions.md).
