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
ms.openlocfilehash: 1ea65c9c1f69c8eec77eb498a5963b0d77ce57f1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Azure verem API-verzió profilok kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

API-profilok adja meg, az Azure erőforrás-szolgáltató és a végpontok Azure REST API-verzió. Egyéni ügyfelek különböző nyelveken API-profilok használatával hozhat létre. Minden ügyfél az API-profilt használja a megfelelő erőforrás-szolgáltató és API-verziót kérjen Azure verem. 

Létrehozhat egy alkalmazást az Azure erőforrás-szolgáltató nem kell korlátoznia a pontosan mely mindegyik erőforrás-szolgáltató API verziója kompatibilis Azure verem használható. Az alkalmazás-profilhoz; csak igazítása Az SDK-t a megfelelő API-verzió vált át.


Ez a témakör nyújt segítséget:
 - Ismerje meg, az Azure-verem API-profilok.
 - Hogyan az API-profilok segítségével a megoldások fejlesztése.
 - Hol kód jellemző útmutatást.

## <a name="summary-of-api-profiles"></a>API-profilok összegzése

- API-profilok használatosak az Azure erőforrás-szolgáltatók és az API-verziók.
- API-profilokat a fejlesztők számára a sablonok létrehozása több Azure-felhőkben létre. A célja, hogy a kompatibilis és állandó kapcsolat az igényeknek megfelelő.
- Profilok évente négyszer kiadásakor.
- Három profil elnevezési szabályai a következők:
    - **legújabb**  
        Legutóbbi API-verziók, amely az Azure-ban.
    - **yyyy-mm-dd-hybrid**  
    Kiadás dátuma: egy róla ütemben történik, ebben a kiadásban összpontosított konzisztencia és stabilitását több felhőkben. Ez a profil optimális Azure verem kompatibilitási célozza. 
    - **yyyy-mm-dd-profile**  
    Optimális stabilitás és a legújabb funkciók között helyezkedik el.

### <a name="api-profiles-and-azure-stack-compatibility"></a>API-profilokat és Azure verem kompatibilitási

A legújabb API-profilok az Azure-verem nem kompatibilisek. Az elnevezési konvenciókat segítségével azonosíthatók a profilok használatához Azure verem megoldásaiban.

**legújabb**  
Ez a profil a legújabb API-verziók található a globális Azure-ban Azure verem nem fog működni. A profil rendelkezik-e jelentős változásokat legnagyobb száma. A profil a stabilitás és a többi felhőből kompatibilis fenntartott helyezi. Ha a kívánt a legújabb API-verziót használnak, akkor a profilt kell használnia.

**Éééé-hh-nn-hibrid**  
Ehhez a profilhoz. március és szeptember felszabadul évente. Ez a profil optimális stabilitás és a különböző felhők kompatibilitás rendelkezik. Ez a profil globális Azure és Azure verem célja. Az ezen profilban szereplő Azure API-verziók lesz azonos Azure veremben felsorolt megfelelően. Ez a profil segítségével kódot a hibrid felhő megoldások fejlesztése.

**yyyy-mm-dd-profile**  
Ehhez a profilhoz. június és December globális Azure szabadul fel. Ez a profil nem fog működni a szemben Azure verem; számos jelentős változásokat lesz. Optimális stabilitás és a legújabb funkciókat mögött helyezkedik el, amíg a legújabb és közötti ehhez a profilhoz különbség, hogy legújabb mindig a legújabb API-verziók attól függetlenül, ha jelent meg az API-t olyan állnak. Egy új API-verzió hoz létre a számítási API holnap, ha az adott API-verzió a legújabb profil megjelenik, de nem profilban a éééé-hh-nn-profilt a profil az előre meghatározott. A legfrissebb. június vagy December előtt kiadott verziók magában foglalja.

## <a name="azure-resource-manager-api-profiles"></a>Az Azure Resource Manager API-profilok

Azure verem nem található a globális Azure API-verziók a legújabb verzióját használja. A saját megoldás készítése kell mindegyik erőforrás-szolgáltató API-verzió található, amely kompatibilis a Azure verem Azure-ban.

Ahelyett, hogy mindegyik erőforrás-szolgáltató és az Azure-verem által támogatott verziót kutatás, mint használhat egy API-profilt. A profilja olyan erőforrás-szolgáltatók és API-verziók. Az SDK-t vagy egy eszközt, az SDK-val készült visszaáll a cél api-verzió van megadva a profilban. Az API-profiljaival megadhat egy Profilverzió egy teljes sablonra, és futásidőben, az Azure erőforrás-kezelő kiválasztja a megfelelő verziót az erőforrás.

API-profilok Azure Resource Manager, például a PowerShell, az Azure parancssori felület, az SDK és a Microsoft Visual Studio code használó eszközök működik. Eszközök és SDK-k segítségével profilok olvassa el a modulok és a szalagtárak, amikor egy alkalmazás felépítése verziójának.

Például, ha a PowerShell egy tároló létrehozásához használja a fiók a **Microsoft.Storage** erőforrás-szolgáltató, amely támogatja az api-version 2016-03-30 és a virtuális gépek a Microsoft.Compute erőforrás-szolgáltató 2015-12-01 api-version paraméterrel , keressen fel, amely PowerShell-modul támogatja 2016-03-30 tárolási kell, és melyik modul 2015-02-01 támogatja a számítás és a telepítést. Ehelyett egy profilt is használhatja. A parancsmag ** Install-profil * profilnév x és PowerShell betölti a modulok a megfelelő verziója.

Hasonlóképpen ha a Python SDK segítségével összeállíthat egy Python-alapú alkalmazást, megadhatja a profil. Az SDK-t a megfelelő modulok az erőforrás-szolgáltató a parancsfájlban megadott tölt be.

Fejlesztőként összpontosíthat írása a megoldás. Ahelyett, hogy mely api-verzióit, erőforrás-szolgáltató vizsgálja, és melyik felhőalapú működik együtt, egy profil és tudja, hogy a kód, amely támogatja a profilt minden felhőkben fognak működni.

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
