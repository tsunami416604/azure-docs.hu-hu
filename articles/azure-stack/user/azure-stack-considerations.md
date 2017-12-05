---
title: "Azure és az Azure-verem különbségei kulcs használatakor a szolgáltatások és alkalmazások készítéséhez |} Microsoft Docs"
description: "Teendők, ha a szolgáltatások vagy alkalmazások készítéséhez Azure verem."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: twooley
ms.custom: mvc
ms.openlocfilehash: 7a385ae50ad3403f41bd31f0676e019ee42d181f
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Kulcs szempontok: szolgáltatásokkal vagy az alkalmazások Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Ha szolgáltatások vagy alkalmazások készítéséhez Azure verem, ismernie kell, hogy Azure verem és az Azure közötti különbségek vannak. Ez a cikk a legfontosabb szempont áttekintést nyújt, ha a hibrid felhő fejlesztőkörnyezetet, Azure verem.

## <a name="overview"></a>Áttekintés

A Microsoft Azure Stack egy hibrid felhőplatform, amellyel Azure-szolgáltatásokat használhat a szervezete vagy a szolgáltatója adatközpontjából. Fejlesztőként hozhat létre Azure veremben futó alkalmazások. Azure verem, az Azure-ba, majd telepítheti ezeket az alkalmazásokat vagy, hogy valóban hibrid alkalmazásokat használó Azure verem felhőalapú és az Azure közötti kapcsolat.

Az Azure-verem operátor értesíti, hogy mely szolgáltatások érhetők el, hogy használja, és hogyan kérhet támogatást. Ezek a szolgáltatások egyéni tervek és ajánlatok keresztül kínálnak.

Az Azure műszaki tartalom feltételezi, hogy alkalmazásokat az Azure-verem helyett az Azure-szolgáltatások vannak fejlesztés alatt áll. Ha most felépíti és alkalmazások telepítése Azure verem, látnia kell, fontosabb különbségeket, például:

* Az Azure verem a szolgáltatások és az Azure-ban rendelkezésre álló funkciók egy részét biztosítja.
* A vállalat vagy a service provider kiválaszthatja azokat biztosítani szeretné a szolgáltatásokat. Ez magában foglalja a testreszabott szolgáltatásokat vagy alkalmazásokat. A saját testreszabott dokumentáció is biztosítanak.
* A megfelelő kell használni (például a portálcímre és az Azure Resource Manager-végpont URL-címek) Azure verem-specifikus végpontok.
* PowerShell és API verziók által támogatott Azure verem kell használnia. Ez biztosítja, hogy az alkalmazások Azure verem és az Azure fog működni.

## <a name="cheat-sheet-high-level-differences"></a>Lap cheat: fontos eltérést

A következő táblázat ismerteti az Azure-vermet, és az Azure közötti magas szintű eltéréseket. Tartsa szem előtt ezeket, fejleszthet Azure verem vagy Azure verem szolgáltatásokat használja.

| Terület | Azure (globális) | Azure Stack |
| -------- | ------------- | ----------|
| Akik működik? | Microsoft | A szervezet vagy szolgáltatás szolgáltatónál.|
| Aki lépjen kapcsolatba a támogatási? | Microsoft | Integrált rendszer lépjen kapcsolatba az Azure-verem operátor (ennek a szervezet vagy a service provider) támogatásához.<br><br>Azure verem szoftverfejlesztői készlet támogatásért látogasson el a [Microsoft fórumok](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). A csomag egy kiértékelési környezete, mert nincs érhető el a Microsoft támogatja a szolgáltatások Ügyféltámogatási hivatalos támogatás.
| Az elérhető szolgáltatások | A lista [Azure termékek](https://azure.microsoft.com/services/?b=17.04b). Választható szolgáltatások az Azure-régió lehet. | Azure verem támogatja az Azure-szolgáltatások egy részét. Tényleges szolgáltatások választja a szervezet vagy a service provider kínálnak alapján változhatnak.
| Az Azure Resource Manager végpont * | https://management.azure.com | Egy integrált Azure verem rendszer esetén használja a végpontot, az Azure-verem operátor biztosított.<br><br>A csomagban található, használja: https://management.local.azurestack.external
| Portál URL-cím * | [https://Portal.Azure.com](https://portal.azure.com) | Integrált Azure verem rendszert nyissa meg az Azure-verem operátor megadott URL-címre.<br><br>A csomagban található, használja: https://portal.local.azurestack.external
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
| A következő információkat tekintheti át:<br>-Az azure Resource Manager sablon kapcsolatos szempontok<br>-Hogyan találhatók gyorsindítási sablonok<br>-Segítségével házirendmodul használata Azure Azure verem fejlesztéséhez | [Fejlesztés az Azure Stackhez](azure-stack-developer.md) | 
| Tekintse át, és a bevált gyakorlat a sablonok nem támogatottak. | [Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Verziószámára vonatkozó követelmények

Az Azure verem Azure PowerShell és az Azure service API-k adott verzióit támogatja. Győződjön meg arról, hogy az alkalmazás telepíthető központilag a mindkét Azure verem és az Azure-ba való támogatott verzióját kell használnia.

Győződjön meg arról, hogy használja-e az Azure PowerShell megfelelő verziója, használja a [API-verzió profilok](azure-stack-version-profiles.md). Határozza meg a legújabb API verziót profilját, melyekkel, ismernie kell, amely használata Azure-készlet létrehozása. Ez az információ az Azure verem rendszergazdájától kérheti le.

>[!NOTE]
 Ha szeretne megtudni az Azure verem Development Kit, és rendszergazdai hozzáféréssel rendelkeznek, a "Meghatározni az aktuális" című szakaszában talál [frissítések kezelése](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) az Azure-verem build meghatározásához.

Más API-k esetében futtassa a következő PowerShell-parancsot a névterek, erőforrástípusok és API-verziók által támogatott Azure verem előfizetése kimeneti. Vegye figyelembe, hogy továbbra is lehet különbségek tulajdonság szinten. (A parancs működéséhez, rendelkeznie kell már [telepített](azure-stack-powershell-install.md) és [konfigurált](azure-stack-powershell-configure-user.md) PowerShell Azure verem környezetben. Emellett rendelkeznie kell egy Azure verem ajánlatot-előfizetés.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Példa a kimenetre (csonkolt): ![Get-AzureRmResourceProvider parancs egy példa a kimenetre](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Következő lépések

Szolgáltatás szintű különbségekről további részletes információkért lásd:

* [Virtuális gépek Azure-készletben szempontjai](azure-stack-vm-considerations.md)
* [Tárolás az Azure-készletben szempontjai](azure-stack-acs-differences.md)
* [Azure verem hálózati szempontjai](azure-stack-network-differences.md)
