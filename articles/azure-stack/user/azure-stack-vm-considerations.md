---
title: "Szempontok a virtuális gépek Azure-készletben és különbségeket |} Microsoft Docs"
description: "További tudnivalók a különbségek és szempontokat, az Azure-veremben lévő virtuális gépek használatakor."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: mabrigg
ms.openlocfilehash: fe655facf4da99d951a430db8ce603cc0ec7f224
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Virtuális gépek Azure-készletben szempontjai

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Virtuális gépek az igény szerinti, méretezhető számítási erőforrások Azure verem által kínált. Virtuális gépek használatakor ismernie kell, hogy vannak-e az Azure-ban elérhető funkciókat és Azure verem közötti különbséget. Ez a cikk áttekintést a virtuális gépek és a szolgáltatások, Azure verem egyedi szempontjai. Azure verem és az Azure közötti magas szintű különbségek kapcsolatos további tudnivalókért lásd: a [szempontok kulcs](azure-stack-considerations.md) cikk.

## <a name="cheat-sheet-virtual-machine-differences"></a>Lap cheat: virtuális gép különbségek

| Szolgáltatás | Azure (globális) | Azure Stack |
| --- | --- | --- |
| Virtuálisgép-rendszerképek | Az Azure piactéren lemezképeket hozhat létre virtuális gépet tartalmaz. Tekintse meg a [Azure piactér](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) lap használatával jeleníthetők meg az Azure piactéren elérhető képeket listáját. | Alapértelmezés szerint nincs képeket elérhető a verem Azure piactéren. Az Azure-verem felhő rendszergazdájának kell közzétételére, vagy töltse le a lemezképet a verem Azure piactéren előtt a felhasználók használni tudják őket. |
| Virtuálisgép-méretek | Azure virtuális gépek méretű széles választékát támogatja. Az elérhető méretek és a lehetőségek kapcsolatos információkért tekintse meg a [Windows virtuális gépek méretét](../../virtual-machines/virtual-machines-windows-sizes.md) és [Linux virtuálisgép-méretek](../../virtual-machines/linux/sizes.md) témaköröket. | Az Azure verem támogatja az Azure-ban rendelkezésre álló virtuálisgép-méretek egy részét. A támogatott méretek listájának megtekintéséhez, tekintse meg a [virtuálisgép-méretek](#virtual-machine-sizes) című szakaszát. |
| Virtuális gép kvóták | [Kvótakorlát](../../azure-subscription-service-limits.md#service-specific-limits) a Microsoft által beállított | Az Azure verem felhő rendszergazdájának kell megadnia a kvóták, ahhoz, azok a felhasználók számára virtuális gépek kínálnak. |
| Virtuálisgép-bővítmények |Azure virtuálisgép-bővítmények széles választékát támogatja. Az elérhető bővítmények kapcsolatos információkért tekintse meg a [virtuálisgép-bővítmények és a szolgáltatások](../../virtual-machines/windows/extensions-features.md) cikk.| Az Azure verem támogatja az Azure-ban rendelkezésre álló bővítmények egy részét, és a bővítmény rendelkező különböző verziókat. Az Azure verem felhő rendszergazda megadhatja, hogy mely bővítmények, amelyeket a felhasználók számára elérhetővé kell tenni. A támogatott bővítmények listájának megtekintéséhez, tekintse meg a [virtuálisgép-bővítmények](#virtual-machine-extensions) című szakaszát. |
| Virtuálisgép-hálózat | Nyilvános IP-címek hozzárendelését a virtuális gép az interneten keresztül érhetők el.<br><br><br>Az Azure virtuális gépek egy rögzített DNS-névvel rendelkezik. | A bérlői virtuális géphez rendelt nyilvános IP-címek csak Azure verem szoftverfejlesztői készlet környezetben érhetők el. A felhasználóknak rendelkezniük kell az Azure verem szoftverfejlesztői készlet keresztül hozzáférést [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) vagy [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) csatlakozni a virtuális gép, amely Azure-készletben jön létre.<br><br>Egy Azure verem példány belül létrehozott virtuális gépek rendelkezik egy DNS-nevet, a felhő rendszergazdája által beállított értéke alapján. |
| Virtuálisgép-tároló | Támogatja a [által kezelt lemezeken.](../../virtual-machines/windows/managed-disks-overview.md) | Felügyelt lemezek még nem támogatottak Azure-készletben. |
| API-verziók | Azure mindig a legújabb API-verziók a virtuális gép minden funkció rendelkezik. | Azure verem adott Azure-szolgáltatások és az adott API-verziók támogatja ezeket a szolgáltatásokat. A támogatott API-verziók listáját megtekintéséhez tekintse meg a [API-verziók](#api-versions) című szakaszát. |
|Virtuális gép rendelkezésre állási csoportok|Több tartalék tartományok (2 vagy 3 régiónként)<br>Több frissítési tartományt<br>Felügyelt támogatása|Egyetlen tartalék tartomány<br>Egyetlen frissítési tartományi<br>Nem felügyelt támogatása|
|Virtuálisgép-méretezési csoportok|Támogatott automatikus méretezése|Automatikus skálázása nem támogatott.<br>Adja hozzá a méretezési készletben, a portál, a Resource Manager-sablonok vagy a PowerShell használatával további példányokat.

## <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek 

Az Azure verem szoftverfejlesztői készlet a következő méretek támogatja: 

| Típus | Méret | A támogatott méretek tartomány |
| --- | --- | --- |
|Általános célú |Alapszintű A|A0 - A4|
|Általános célú |Standard A|A0 - A7|
|Általános célú |D-sorozat|D1 - D4|
|Általános célú |Dv2-sorozat|D1_v2 - D5_v2|
|Általános célú |DS-sorozat|DS1 - DS4|
|Általános célú |DSv2-sorozat|DS1_v2 - DS5_v2|
|Memóriaoptimalizált|DS-sorozat|DS11 - DS14|
|Memóriaoptimalizált |DSv2-sorozat|DS11_v2 - DS14_v2|

Virtuálisgép-méretek és a kapcsolódó erőforrás mennyiségek Azure verem és az Azure közötti megegyeznek. Például ez magában foglalja a memóriamennyiség, magok száma és a szám vagy méretének az adatlemezek hozható létre. Az ugyanazon Virtuálisgép-méretet Azure verem teljesítményét azonban egy adott Azure verem környezetben alapul szolgáló jellemzői függ.

## <a name="virtual-machine-extensions"></a>Virtuálisgép-bővítmények 

 Az Azure verem szoftverfejlesztői készlet a következő virtuális gép bővítmény verzióit támogatja:

![Virtuálisgép-bővítmények](media/azure-stack-vm-considerations/vm-extensions.png)

A következő PowerShell-parancsfájl segítségével a verem Azure környezetben elérhető virtuálisgép-bővítmények listájának beolvasása:

```powershell 
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize 
```

## <a name="api-versions"></a>API-verziók 

Virtuális gépek jellemzői az Azure verem szoftverfejlesztői készlet a következő API-verzióit támogatja:

![Virtuális gép típusú erőforrások](media/azure-stack-vm-considerations/vm-resoource-types.png)

A következő PowerShell-parancsfájl segítségével az API verziójának lekérése elérhető Azure verem környezetében a virtuális gépek jellemzői:

```powershell 
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
A támogatott erőforrástípusai és API-verziók listáját változhat, ha a felhő üzemeltetője a Azure verem környezet frissíti egy újabb verzióra.

## <a name="next-steps"></a>Következő lépések

[Windows virtuális gép létrehozása Azure-készletben a PowerShell használatával](azure-stack-quick-create-vm-windows-powershell.md)
