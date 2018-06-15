---
title: Azure és az Azure-verem közötti fő különbségek megismeréséhez, szolgáltatások és alkalmazások épület használatakor |} Microsoft Docs
description: Mit kell tudni, hogy szolgáltatásokat használja, vagy olyan alkalmazások fordítása, Azure verem.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/16/2018
ms.author: mabrigg
ms.openlocfilehash: eab208175f7eb3b761ec7266483a7cd5268198e8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31512928"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Kulcs szempontok: szolgáltatásokkal vagy az alkalmazások Azure verem

Mielőtt szolgáltatásokat használja, vagy olyan alkalmazások fordítása, Azure verem, ismernie kell az Azure verem és az Azure közötti különbségeket. Ez a cikk a legfontosabb szempont azonosítja, ha az Azure-verem használja a hibrid felhőalapú fejlesztési környezet.

## <a name="overview"></a>Áttekintés

A Microsoft Azure Stack egy hibrid felhőplatform, amellyel Azure-szolgáltatásokat használhat a szervezete vagy a szolgáltatója adatközpontjából. Az alkalmazás elkészítésére Azure veremben, és majd telepítse azt Azure verem, az Azure-ba, vagy az Azure hibrid felhő.

Az Azure-verem operátor értesíti, hogy mely szolgáltatások érhetők el, hogy használja, és hogyan kérhet támogatást. Ezek a szolgáltatások egyéni tervek és ajánlatok keresztül kínálnak.

Az Azure műszaki tartalom feltételezi, hogy alkalmazásokat az Azure-verem helyett az Azure-szolgáltatások vannak fejlesztés alatt áll. Ha most felépíti és alkalmazások telepítése Azure verem, látnia kell, fontosabb különbségeket, például:

* Az Azure verem a szolgáltatások és az Azure-ban rendelkezésre álló funkciók egy részét biztosítja.
* A vállalat vagy a service provider kiválaszthatja azokat biztosítani szeretné a szolgáltatásokat. Az elérhető lehetőségek közé tartozik a testreszabott szolgáltatásokat vagy alkalmazásokat. A saját testreszabott dokumentáció is biztosítanak.
* A megfelelő kell használni (például a portálcímre és az Azure Resource Manager-végpont URL-címek) Azure verem-specifikus végpontok.
* PowerShell és API verziók által támogatott Azure verem kell használnia. Támogatott verziók a biztosítja, hogy az alkalmazások Azure verem és Azure is működni fog-e.

## <a name="cheat-sheet-high-level-differences"></a>Lap cheat: fontos eltérést

A következő táblázat ismerteti az Azure-vermet, és az Azure közötti magas szintű eltéréseket. Ezek a különbségek figyelembe megtarthatják fejleszthet Azure verem vagy Azure verem szolgáltatásokat használja.
*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

| Terület | Azure (globális) | Azure Stack |
| -------- | ------------- | ----------|
| Akik működik? | Microsoft | A szervezet vagy szolgáltatás szolgáltatónál.|
| Aki lépjen kapcsolatba a támogatási? | Microsoft | Integrált rendszer lépjen kapcsolatba az Azure-verem operátor (ennek a szervezet vagy a service provider) támogatásához.<br><br>Azure verem szoftverfejlesztői készlet támogatásért látogasson el a [Microsoft fórumok](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). A csomag egy kiértékelési környezete, mert nincs érhető el a Microsoft támogatja a szolgáltatások Ügyféltámogatási hivatalos támogatás.
| Az elérhető szolgáltatások | A lista [Azure termékek](https://azure.microsoft.com/services/?b=17.04b). Választható szolgáltatások az Azure-régió lehet. | Azure verem támogatja az Azure-szolgáltatások egy részét. Tényleges szolgáltatások választja a szervezet vagy a service provider kínálnak alapján változhatnak.
| Az Azure Resource Manager végpont * | https://management.azure.com | Egy integrált Azure verem rendszer esetén használja a végpontot, az Azure-verem operátor biztosított.<br><br>A csomagban található használja: https://management.local.azurestack.external
| Portál URL-cím * | [https://portal.azure.com](https://portal.azure.com) | Integrált Azure verem rendszert nyissa meg az Azure-verem operátor megadott URL-címre.<br><br>A csomagban található használja: https://portal.local.azurestack.external
| Régió | Kiválaszthatja, hogy melyik régióban telepíteni szeretné. | Egy integrált Azure verem rendszer a rendszeren elérhető régiót kell használnia.<br><br>A csomag, a régiót mindig lesz **helyi**.
| Erőforráscsoportok | Erőforráscsoport régiókban is kiterjedhetnek. | Integrált rendszerek és a csomag nincs csak egy régió tartozik.
|Támogatott névterek erőforrástípusok és API-verziók | A legújabb (vagy korábbi verziójú, amely még nem elavultak). | Az Azure verem adott verzióit támogatja. Az "Verzióra vonatkozó követelmények" című cikkben.
| | |

Ha a rendszer Azure verem operátor, lásd: [felügyeleti portálján](../azure-stack-manage-portals.md) és [Adminisztráció alapjai](../azure-stack-manage-basics.md) további információt.

## <a name="helpful-tools-and-best-practices"></a>Hasznos eszközök és ajánlott eljárások
 
 A Microsoft biztosít több eszközöket és útmutatást tartalmaz, amelyek megkönnyítik az Azure-verem fejlesztése.

| Ajánlás | Referencia | 
| -------- | ------------- | 
| A megfelelő telepíti a fejlesztői munkaállomáson. | - [Telepítse a PowerShell](azure-stack-powershell-install.md)<br>- [Eszközök](azure-stack-powershell-download.md)<br>- [PowerShell konfigurálása](azure-stack-powershell-configure-user.md)<br>- [A Visual Studio telepítése](azure-stack-install-visual-studio.md) 
| Tekintse át a következő elemek:<br>-Az azure Resource Manager sablon kapcsolatos szempontok<br>-Hogyan találhatók gyorsindítási sablonok<br>-Segítségével házirendmodul használata Azure Azure verem fejlesztéséhez | [Fejlesztés az Azure Stackhez](azure-stack-developer.md) | 
| Tekintse át, és a bevált gyakorlat a sablonok nem támogatottak. | [Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Verziószámára vonatkozó követelmények

Az Azure verem Azure PowerShell és az Azure service API-k adott verzióit támogatja. Támogatott verziók segítségével győződjön meg arról, hogy az alkalmazás telepíthető központilag a mindkét Azure verem és az Azure-bA.

Győződjön meg arról, hogy használja-e az Azure PowerShell megfelelő verziója, használja a [API-verzió profilok](azure-stack-version-profiles.md). Területen megállapíthatja, hogy a legújabb API verziót profil használható Azure verem használata esetén a build megállapítása. Ez az információ az Azure verem rendszergazdájától kérheti le.

>[!NOTE]
 Ha szeretne megtudni az Azure verem Development Kit, és rendszergazdai hozzáféréssel rendelkeznek, a "Meghatározni az aktuális" című szakaszában talál [frissítések kezelése](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) az Azure-verem build meghatározásához.

Más API-k esetében futtassa a következő PowerShell-parancsot a névterek, erőforrástípusok és API-verziók által támogatott Azure verem előfizetése kimeneti. Vegye figyelembe, hogy továbbra is lehet különbségek tulajdonság szinten. (A parancs működéséhez, rendelkeznie kell már [telepített](azure-stack-powershell-install.md) és [konfigurált](azure-stack-powershell-configure-user.md) PowerShell Azure verem környezetben. Emellett rendelkeznie kell egy Azure verem ajánlatot-előfizetés.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Example output (truncated): ![Example output of Get-AzureRmResourceProvider command](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>További lépések

Szolgáltatás szintű különbségekről további részletes információkért lásd:

* [Virtuális gépek Azure-készletben szempontjai](azure-stack-vm-considerations.md)
* [Tárolás az Azure-készletben szempontjai](azure-stack-acs-differences.md)
* [Azure verem hálózati szempontjai](azure-stack-network-differences.md)
