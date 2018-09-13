---
title: Megismerheti az Azure és az Azure Stack közötti lényeges különbségeket, használatakor, szolgáltatások és alkalmazások fejlesztése |} A Microsoft Docs
description: Mit kell tudni, hogy szolgáltatásokat vagy alkalmazásokat hozhat létre az Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/12/2018
ms.author: sethm
ms.openlocfilehash: 9f229caa76059db403ced5b74c7a35ec2f0b8e9d
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721891"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Kulcs szempontok: services használatával, vagy alkalmazások készítéséhez az Azure Stackhez

Mielőtt szolgáltatások használatára, vagy alkalmazásokat hozhat létre az Azure Stack, ismernie kell az Azure Stack- és Azure közötti különbségeket. Ez a cikk azonosítja a legfontosabb szempont, amikor az Azure Stack használatával a hibrid felhőalapú fejlesztési környezet.

## <a name="overview"></a>Áttekintés

A Microsoft Azure Stack egy hibrid felhőplatform, amellyel Azure-szolgáltatásokat használhat a szervezete vagy a szolgáltatója adatközpontjából. Alkalmazás készítése az Azure Stacken, és ezután telepítheti az Azure Stack, az Azure-bA vagy az Azure hibrid felhőalapú.

Az Azure Stack-operátorokról lehetővé teszi, hogy mely szolgáltatások érhetők el, amelyet használhat, és hogyan kérhet támogatást. Ezek a szolgáltatások saját testre szabott csomagokat és ajánlatokat keresztül kínálnak.

Az Azure műszaki tartalom azt feltételezi, hogy alkalmazásokat fejlesztenek ki az Azure Stack helyett az Azure-szolgáltatások. Amikor hozhat létre, és az alkalmazások üzembe helyezése az Azure Stack, néhány fontos eltérés, például kell ismernie:

* Az Azure Stack biztosít egy **részhalmazát** szolgáltatásokat és az Azure-ban elérhető szolgáltatásokat.
* A vállalat vagy a service provider kiválaszthatja, hogy mely szolgáltatásokat, azok kíván kíván felajánlani. A rendelkezésre álló beállítások testre szabott szolgáltatások vagy alkalmazások lehetnek. Előfordulhat, hogy a saját testre szabott dokumentáció kínálnak.
* A megfelelő kell használni (például a portál címet és az Azure Resource Manager-végpont URL-címek) az Azure Stack-specifikus végpontok.
* PowerShell és API-verziók az Azure Stack által támogatott kell használnia. Támogatott verziók használata biztosítja, hogy az alkalmazások működni fog-e az Azure Stack és az Azure.

## <a name="cheat-sheet-high-level-differences"></a>Hasznos tanácsok: fontos eltérést

A következő táblázat ismerteti az Azure Stack és az Azure magas szintű különbségeit. Fejlesztés az Azure Stackhez vagy az Azure Stack-szolgáltatások használatára, tartsa szem előtt a különbségeket.
*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

| Terület | Azure (globális) | Azure Stack |
| -------- | ------------- | ----------|
| Akik működik ez? | Microsoft | A szervezet vagy a service provider.|
| Akik tegye meg a kapcsolatot? | Microsoft | Egy integrált rendszer lépjen kapcsolatba az Azure Stack-operátorokról (a saját szervezet vagy szolgáltatás szolgáltató) támogatást.<br><br>Azure Stack Development Kit támogatásért látogasson el a [Microsoft fórumok](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Mivel a csomag egy kiértékelési környezete, rendszer nem érhető el – a Microsoft támogatja a szolgáltatások (CSS) hivatalos támogatja.
| Elérhető szolgáltatások | Listájának megtekintéséhez [Azure-termékek](https://azure.microsoft.com/services/?b=17.04b). Az Azure-régióban elérhető szolgáltatások változhat. | Az Azure Stack egy Azure-szolgáltatások részét támogatja. Tényleges szolgáltatások terméktípustól függ, hogy a szervezet vagy a service provider választ kínálnak.
| Az Azure Resource Manager endpoint * | https://management.azure.com | Az Azure Stackkel integrált rendszer a végpont, amelyet az Azure Stack-operátorokról megadott használja.<br><br>A fejlesztői készlet használja: https://management.local.azurestack.external
| Portál URL-címe * | [https://portal.azure.com](https://portal.azure.com) | Az Azure Stackkel integrált rendszer nyissa meg az Azure Stack-operátorokról megadott URL-CÍMÉT.<br><br>A fejlesztői készlet használja: https://portal.local.azurestack.external
| Régió | Kiválaszthatja, hogy melyik régiót szeretné telepíteni. | Az Azure Stackkel integrált rendszer használja a régiót, amelyben a rendszeren érhető el.<br><br>A fejlesztői csomag régió mindig lesz **helyi**.
| Erőforráscsoportok | Egy erőforráscsoport régióban is kiterjedhetnek. | Integrált rendszerek és a csomag nincs csak egy régiót.
|Támogatott névtereket, erőforrástípusok és API-verziók | A legújabb (vagy régebbi verziójú, amely még nem avult). | Az Azure Stack támogatja a verziókkal. Ez a cikk "Verziójával kapcsolatos követelmények" című szakaszában talál.
| | |

Ha a áll az Azure Stack operátorait, lásd: [a felügyeleti portál használatával](../azure-stack-manage-portals.md) és [Adminisztráció alapjai](../azure-stack-manage-basics.md) további információt.

## <a name="helpful-tools-and-best-practices"></a>Hasznos eszközök és ajánlott eljárások
 
 A Microsoft biztosít több eszközöket és útmutatást, amellyel fejlesztése az Azure Stackhez.

| Ajánlás | Referencia | 
| -------- | ------------- | 
| Telepítse a megfelelő eszközöket a fejlesztői munkaállomásán. | - [A PowerShell telepítése](azure-stack-powershell-install.md)<br>- [Eszközök letöltése](azure-stack-powershell-download.md)<br>- [A PowerShell konfigurálása](azure-stack-powershell-configure-user.md)<br>- [A Visual Studio telepítése](azure-stack-install-visual-studio.md) 
| Tekintse át a következő elemek:<br>– Az azure Resource Manager-sablon kapcsolatos szempontok<br>-Hogyan gyorsindítási sablonok keresése<br>-Házirendmodul használata segít az Azure-fejlesztés az Azure Stackhez | [Fejlesztés az Azure Stackhez](azure-stack-developer.md) | 
| Tekintse át és kövesse az ajánlott eljárások a sablonokhoz. | [Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Verzióra vonatkozó követelmények

Az Azure Stack az Azure PowerShell és az Azure-szolgáltatás API-k adott verzióit támogatja. Támogatott verziók használatával győződjön meg arról, hogy az alkalmazás telepíthető központilag a két Azure stack, és az Azure-bA.

Győződjön meg arról, hogy az Azure PowerShell megfelelő verzióját használja, használja a [API-verzióprofilok](azure-stack-version-profiles.md). Annak megállapításához, a legújabb API verzió-profilt, amelyet használhat, megtudhatja a build, az Azure Stack használata esetén. Ezt az információt kaphat az Azure Stack rendszergazdájától.

>[!NOTE]
 Ha az Azure Stack Development Kit használ, és rendelkezik rendszergazdai hozzáféréssel, "Az aktuális verzió meghatározása" című szakaszában talál [frissítéseinek kezelése](../azure-stack-updates.md#determine-the-current-version) meghatározni az Azure Stack-build.

A más API-k futtassa a következő PowerShell-parancsot a kimenetben a névterek, erőforrástípusok és API-verziók által támogatott az Azure Stack-előfizetéssel. Vegye figyelembe, hogy továbbra is lehet különbségek tulajdonság szinten. (A parancs működéséhez rendelkeznie kell már [telepített](azure-stack-powershell-install.md) és [konfigurált](azure-stack-powershell-configure-user.md) PowerShell az Azure Stack-környezet. Akkor is rendelkeznie kell az ajánlatot az Azure Stack-előfizetést.)

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Example output (truncated): ![Example output of Get-AzureRmResourceProvider command](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>További lépések

Szolgáltatási szinten különbségek kapcsolatos részletesebb információkért lásd:

* [Szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md)
* [Az Azure Stackben tárolási szempontjai](azure-stack-acs-differences.md)
* [Azure Stack hálózati szempontjai](azure-stack-network-differences.md)
