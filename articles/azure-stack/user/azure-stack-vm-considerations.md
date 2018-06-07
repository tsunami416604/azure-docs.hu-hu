---
title: Szempontok a virtuális gépek Azure-készletben és különbségeket |} Microsoft Docs
description: További tudnivalók a különbségek és szempontokat, az Azure-veremben lévő virtuális gépek használatakor.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: brenduns
ms.openlocfilehash: 324fa19aa97cead44f38d07a2fd0765048cd6238
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605388"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Virtuális gépek Azure-készletben használatának szempontjai

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure verem virtuális gépek igény szerinti, méretezhető számítási erőforrások adja meg. Virtuális gépek (VM) telepítése előtt ismernie kell a virtuális gép szolgáltatásához Azure verem és a Microsoft Azure közötti különbségekről. Ez a cikk ismerteti, ezek a különbségek, és azonosítja a virtuális gépek telepítéséhez tervezési fő szempontjait. Azure verem és az Azure közötti magas szintű különbségek kapcsolatos további tudnivalókért lásd: a [szempontok kulcs](azure-stack-considerations.md) cikk.

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
|Virtuális gép rendelkezésre állási csoportok|Több tartalék tartományok (2 vagy 3 régiónként)<br>Több frissítési tartományt<br>Felügyelt támogatása|Több tartalék tartományok (2 vagy 3 régiónként)<br>Több frissítési tartományt (legfeljebb 20)<br>Nem felügyelt támogatása|
|Virtuálisgép-méretezési csoportok|Támogatott automatikus méretezése|Automatikus skálázása nem támogatott.<br>Adja hozzá a méretezési készletben, a portál, a Resource Manager-sablonok vagy a PowerShell használatával további példányokat.

## <a name="virtual-machine-sizes"></a>Virtuálisgép-méretek

Azure verem írja elő az erőforrás-korlátozások keresztül (helyi és a szolgáltatásiszint-kiszolgálón.) erőforrások fogyasztásának elkerülése érdekében Ezek a korlátozások a bérlő élmény javításához, csökkenti a többi bérlő erőforrás-fogyasztásának hatását.

- A virtuális hálózati kimenő sávszélesség caps vezessen be olyan. Azure-készletben a Caps ugyanazok, mint a caps az Azure-ban.
- Tároló-erőforrások, az Azure verem tárolási IOPS-korlátok vonatkoznak a bérlők a tárolók eléréséhez erőforrások alapvető overconsumption elkerülése érdekében valósít meg.
- Csatolt adatok több lemezzel rendelkező virtuális gépekhez a maximális átviteli sebességgel minden adatlemez 500 IOPS HHDs, de 2300 IOPS SSD-k.

A következő táblázat felsorolja a virtuális gépek által támogatott Azure veremben konfigurációjuk együtt:

| Típus           | Méret          | A támogatott méretek tartomány |
| ---------------| ------------- | ------------------------ |
|Általános célú |Alapszintű A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Általános célú |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Általános célú |D-sorozat       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Általános célú |Dv2-sorozat     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Általános célú |DS-sorozat      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Általános célú |DSv2-sorozat    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Memóriaoptimalizált|D-sorozat       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Memóriaoptimalizált|DS-sorozat      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Memóriaoptimalizált|Dv2-sorozat     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Memóriaoptimalizált|DSv2-sorozat-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Virtuálisgép-méretek és a kapcsolódó erőforrás mennyiségek Azure verem és az Azure közötti megegyeznek. Ez magában foglalja az a memóriamennyiség, magok száma, és az adatlemezek hozható létre, szám vagy mérete. Az azonos méretű virtuális gépek teljesítményét azonban egy adott Azure verem környezetben alapul szolgáló jellemzői függ.

## <a name="virtual-machine-extensions"></a>Virtuálisgép-bővítmények

 Azure verem bővítmények egy kis készletét tartalmazza. Frissítések és a további kiterjesztések piactér szindikálási keresztül érhetők el.

A következő PowerShell-parancsfájl segítségével a verem Azure környezetben elérhető virtuálisgép-bővítmények listájának beolvasása:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>API-verziók

Virtuális gépek jellemzői Azure verem a következő API-verzióit támogatja:

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

## <a name="windows-activation"></a>Windows-aktiválás

A Windows-termékeket termékkel használati jogosultságok és a Microsoft feltételeinek megfelelően kell használni. Használja az Azure verem [automatikus Virtuálisgép-aktiválás](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) Windows Server virtuális gépek (VM) aktiválása (AVMA).

- Az Azure-verem gazdagép akkor aktiválódik, a Windows Server 2016 Windows az AVMA kulcsokat. Minden virtuális gép, amely a Windows Server 2012 vagy újabb rendszer automatikusan aktiválja.
- Futtassa a Windows Server 2008 R2 automatikusan nincs aktiválva, és aktiválni kell a virtuális gépek [MAK-aktiválást](https://technet.microsoft.com/library/ff793438.aspx). MAK-aktiválást használ, meg kell adnia a saját termékkulcsot.

Microsoft Azure használatával, hogy a KMS-aktiválás Windows virtuális gépek aktiválása. Ha áthelyezi a virtuális gépek Azure-veremből Azure és az esetlegesen fellépő problémák aktiválása, lásd: [hibaelhárítása a Windows Azure virtuális gép aktiválással kapcsolatos problémák](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). További információk találhatók a [hibaelhárítás Windows aktiválási hibák Azure virtuális gépeken](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) Azure támogatási csapatának blogja post.

## <a name="next-steps"></a>További lépések

[Windows virtuális gép létrehozása Azure-készletben a PowerShell használatával](azure-stack-quick-create-vm-windows-powershell.md)