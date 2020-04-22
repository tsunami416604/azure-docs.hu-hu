---
title: Frissítés alapszintű belsőről standard belső - Azure load balancer
description: Ez a cikk bemutatja, hogyan frissítheti az Azure belső terheléselosztóját alapszintű termékváltozatról standard termékváltozatra
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770389"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Az Azure belső terheléselosztó jának frissítése – nincs szükség kimenő kapcsolatra
[Az Azure Standard Load Balancer](load-balancer-overview.md) a funkciók és a magas rendelkezésre állás széles készletét kínálja a zónaredundancia révén. Ha többet szeretne megtudni a terheléselosztó termékváltozatáról, tekintse meg az [összehasonlító táblázatot.](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus)

Ez a cikk egy PowerShell-parancsfájlt vezet be, amely az alapszintű terheléselosztóval azonos konfigurációval hoz létre standard terheléselosztót, valamint az alapszintű terheléselosztóról a standard terheléselosztóra irányuló forgalmat.

## <a name="upgrade-overview"></a>Frissítés áttekintése

Egy Azure PowerShell-parancsfájl érhető el, amely a következőket teszi:

* Szabványos belső termékváltozat-terheléselosztót hoz létre a megadott helyen. Vegye figyelembe, hogy a standard belső terheléselosztó nem biztosít [kimenő kapcsolatot.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)
* Zökkenőmentesen másolja az alaptermékváltozat terheléselosztó konfigurációit az újonnan létrehozott standard terheléselosztóba.
* Zökkenőmentesen helyezze át a privát IP-eket az alapszintű terheléselosztóról az újonnan létrehozott standard terheléselosztóra.
* Zökkenőmentesen helyezze át a virtuális gépeket az alapszintű terheléselosztó háttérkészletéből a standard terheléselosztó háttérkészletébe

### <a name="caveatslimitations"></a>Kikötések\Korlátozások

* A parancsfájl csak akkor támogatja a belső terheléselosztó frissítését, ahol nincs szükség kimenő kapcsolatra. Ha néhány virtuális géphez [kimenő kapcsolatra](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) van szüksége, olvassa el ezt az [oldalt](upgrade-InternalBasic-To-PublicStandard.md) az utasításokért. 
* Ha a standard terheléselosztó egy másik régióban jön létre, nem fogja tudni társítani a régi régióban meglévő virtuális gépeket az újonnan létrehozott standard terheléselosztóhoz. A korlátozás megkerülése, győződjön meg róla, hogy hozzon létre egy új virtuális gép az új régióban.
* Ha a terheléselosztó nem rendelkezik előtér-IP-konfigurációval vagy háttérkészlettel, valószínűleg hibát talál el a parancsfájl futtatásakor. Győződjön meg róla, hogy nem üres.

## <a name="download-the-script"></a>A szkript letöltése

Töltse le az áttelepítési parancsfájlt a [PowerShell-galériából.](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0)
## <a name="use-the-script"></a>A szkript használata

A helyi PowerShell-környezet beállításától és beállításaitól függően két lehetőség közül választhat:

* Ha nincs telepítve az Azure Az-modulok, vagy nem bánja az Azure Az-modulok eltávolítását, a `Install-Script` legjobb megoldás a parancsfájl futtatásához.
* Ha meg kell tartania az Azure Az modulokat, a legjobb, ha letölti a parancsfájlt, és közvetlenül futtatja.

Annak megállapításához, hogy telepítve vannak-e `Get-InstalledModule -Name az`az Azure Az modulok, futtassa a futtassa a futtassa a futtassa a programot. Ha nem látja a telepített Az modulokat, akkor `Install-Script` használhatja a módszert.

### <a name="install-using-the-install-script-method"></a>Telepítés a Install-Script metódussal

A beállítás használatához nem kell az Azure Az modulok telepítve a számítógépre. Ha telepítve vannak, a következő parancs hibát jelenít meg. Eltávolíthatja az Azure Az modulokat, vagy a másik lehetőséggel manuálisan letöltheti a parancsfájlt, és futtathatja azt.
  
Futtassa a parancsfájlt a következő paranccsal:

`Install-Script -Name AzureILBUpgrade`

Ez a parancs a szükséges Az modulokat is telepíti.  

### <a name="install-using-the-script-directly"></a>Telepítés közvetlenül a parancsfájl használatával

Ha néhány Azure Az-modul telepítve van, és nem tudja eltávolítani őket (vagy nem szeretné eltávolítani őket), manuálisan letöltheti a parancsfájlt a parancsfájl **letöltési hivatkozásának Kézi letöltés** elapjával. A szkript letölthető, mint egy nyers nupkg fájlt. Ha erről a nupkg fájlból szeretné telepíteni a parancsfájlt, olvassa el a Kézi csomag letöltése című [témakört.](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)

A szkript futtatása:

1. Az `Connect-AzAccount` Azure-hoz való csatlakozáshoz használható.

1. Az `Import-Module Az` Az modulok importálására használható.

1. Vizsgálja meg a szükséges paramétereket:

   * **rgName: [String]: Kötelező** – Ez a meglévő alapszintű terheléselosztó és az új standard terheléselosztó erőforráscsoportja. A karakterlánc-érték megkereséséhez keresse meg az Azure Portalon, válassza ki az alapszintű terheléselosztó forrását, és kattintson a terheléselosztó **áttekintése** elemre. Az erőforráscsoport azon a lapon található.
   * **oldLBName: [String]: Kötelező** – Ez a frissíteni kívánt meglévő alapszintű kiegyensúlyozó neve. 
   * **newlocation: [String]: Kötelező** – Ez az a hely, ahol a standard terheléselosztó létrejön. Javasoljuk, hogy a kiválasztott alapszintű terheléselosztó ugyanazon helyét örökölje a standard terheléselosztóval a más meglévő erőforrásokkal való jobb társítás érdekében.
   * **newLBName: [String]: Kötelező** – Ez a létrehozandó standard terheléselosztó neve.
1. Futtassa a parancsfájlt a megfelelő paraméterek kel. 5-7 percig is eltarthat.

    **Példa**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak-e korlátozások az Azure PowerShell-parancsfájl áttelepítése a konfiguráció v1 v2?

Igen. Lásd [a kifogásokat/korlátozásokat.](#caveatslimitations)

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Az Azure PowerShell-parancsfájl is átvált az alapszintű terheléselosztóról az újonnan létrehozott standard terheléselosztóra?

Igen, áttelepíti a forgalmat. Ha személyesen szeretné áttelepíteni a forgalmat, használja [ezt a parancsfájlt,](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) amely nem telepíti át a virtuális gépeket.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Belefutottam néhány probléma segítségével ezt a forgatókönyvet. Hogyan kaphatok segítséget?
  
E-mailt küldhet slbupgradesupport@microsoft.coma, megnyithat egy támogatási esetet az Azure-támogatással, vagy mindkettőt megteheti.

## <a name="next-steps"></a>További lépések

[További információ a standard terheléselosztóról](load-balancer-overview.md)
