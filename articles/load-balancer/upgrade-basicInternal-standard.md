---
title: Frissítés alapszintű belsőről standard belsőre – Azure Load Balancer
description: Ez a cikk bemutatja, hogyan frissítheti az Azure belső Load Balancereit alapszintű SKU-ról standard SKU-ra.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: fe9ae8997e05e4ab99dba66de88976342fbabe56
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858365"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure belső Load Balancer frissítése – nincs szükség kimenő kapcsolatok megtételére
Az [Azure standard Load Balancer](load-balancer-overview.md) számos funkciót és magas rendelkezésre állást kínál a zónák redundancia révén. További információ az Load Balancer SKU-ról: [összehasonlító táblázat](https://docs.microsoft.com/azure/load-balancer/skus#skus).

Ez a cikk egy PowerShell-szkriptet vezet be, amely egy standard Load Balancert hoz létre ugyanazzal a konfigurációval, mint az alapszintű Load Balancer, valamint az alapszintű Load Balancerról standard Load Balancerre történő áttelepítést

## <a name="upgrade-overview"></a>Frissítés áttekintése

Olyan Azure PowerShell-parancsfájl érhető el, amely a következő műveleteket végzi el:

* Létrehoz egy szabványos belső SKU Load Balancer a megadott helyen. Vegye figyelembe, hogy a szabványos belső Load Balancer nem biztosít [kimenő kapcsolatokat](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) .
* Zökkenőmentesen másolja az alapszintű SKU Load Balancer konfigurációit az újonnan létrehozott standard Load Balancerra.
* A magánhálózati IP-címek zökkenőmentes mozgatása az alapszintű Load Balancer az újonnan létrehozott standard Load Balancerig.
* Zökkenőmentesen helyezheti át a virtuális gépeket az alapszintű Load Balancer háttér-készletéből a standard Load Balancer háttér-készletéből.

### <a name="caveatslimitations"></a>Caveats\Limitations

* A parancsfájl csak a belső Load Balancer frissítését támogatja, ha nincs szükség kimenő kapcsolatok használatára. Ha egyes virtuális gépekhez [Kimenő kapcsolatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) szükségesek, tekintse meg ezt az [oldalt](upgrade-InternalBasic-To-PublicStandard.md) . 
* Ha a standard Load Balancer egy másik régióban lett létrehozva, akkor a régi régióban meglévő virtuális gépeket nem lehet az újonnan létrehozott standard Load Balancerhoz rendelni. A korlátozás megkerüléséhez hozzon létre egy új virtuális gépet az új régióban.
* Ha a Load Balancer nem rendelkezik előtér-IP-konfigurációval vagy háttér-készlettel, valószínűleg a parancsfájl futtatásakor hiba lépett fel. Győződjön meg arról, hogy nem üresek.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>IP-kiosztási módszer módosítása statikusra a előtér-IP-konfigurációhoz (hagyja figyelmen kívül ezt a lépést, ha már statikus)

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd válassza ki az alapszintű Load Balancer az erőforrások listából.

2. A **Beállítások**területen válassza ki a **frontend IP-konfiguráció**elemet, majd válassza ki az első előtér-IP-konfigurációt. 

3. A **hozzárendelés**beállításnál válassza a **statikus** lehetőséget.

4. Ismételje meg a 3. lépést az alapszintű Load Balancer összes előtérbeli IP-konfigurációjában.


## <a name="download-the-script"></a>A parancsfájl letöltése

Töltse le az áttelepítési parancsfájlt a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>A szkript használata

A helyi PowerShell-környezet beállításaitól és beállításaitól függően két lehetőség közül választhat:

* Ha nem rendelkezik az Azure az modulok telepítésével, vagy ne feledje eltávolítani az Azure-t, akkor a legjobb lehetőség a szkript futtatására szolgáló `Install-Script` lehetőség használata.
* Ha meg kell őriznie az Azure az modulokat, a legjobb megoldás, ha letölti a szkriptet, és közvetlenül futtatja.

Annak megállapításához, hogy telepítve van-e az Azure az `Get-InstalledModule -Name az`modulok, futtassa a parancsot. Ha a telepített modulok nem láthatók, akkor használhatja a `Install-Script` metódust.

### <a name="install-using-the-install-script-method"></a>Telepítés az install-script metódus használatával

Ha ezt a beállítást szeretné használni, az Azure az modulokat nem kell telepítenie a számítógépre. Ha telepítve vannak, a következő parancs hibát jelez. Távolítsa el az Azure az modulokat, vagy használja a másik lehetőséget a szkript manuális letöltéséhez és futtatásához.
  
Futtassa a szkriptet a következő paranccsal:

`Install-Script -Name AzureILBUpgrade`

Ez a parancs telepíti a szükséges az modulokat is.  

### <a name="install-using-the-script-directly"></a>Telepítés közvetlenül a szkript használatával

Ha van néhány Azure az modulok telepítve, és nem távolítható el (vagy nem szeretné eltávolítani őket), manuálisan letöltheti a szkriptet a parancsfájl letöltése hivatkozás **manuális Letöltés** lapján. A parancsfájl nyers nupkg-fájlként van letöltve. Ha a parancsfájlt erről a nupkg-fájlról szeretné telepíteni, olvassa el a [manuális csomagok letöltése](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)című témakört.

A szkript futtatása:

1. Az `Connect-AzAccount` Azure-hoz való kapcsolódáshoz használható.

1. A `Import-Module Az` használatával importálhatja az az modulokat.

1. Vizsgálja meg a szükséges paramétereket:

   * **rgName: [string]: kötelező** – ez a meglévő alapszintű Load Balancer és az új standard Load Balancerhoz tartozó erőforráscsoport. A karakterlánc értékének megkereséséhez navigáljon Azure Portal, válassza ki az alapszintű Load Balancer forrását, és kattintson a terheléselosztó **áttekintésére** . Az erőforráscsoport ezen az oldalon található.
   * **oldLBName: [string]: kötelező** – ez a meglévő alapszintű, frissíteni kívánt alapbalancer neve. 
   * **newlocation: [string]: kötelező** – ez az a hely, ahol a standard Load Balancer létre lesz hozva. Javasoljuk, hogy a kiválasztott alapszintű Load Balancer ugyanazon helyét örökli a standard Load Balancer a többi meglévő erőforrással való jobb együttműködés érdekében.
   * **newLBName: [string]: kötelező** – ez a létrehozandó standard Load Balancer neve.
1. Futtassa a szkriptet a megfelelő paraméterek használatával. A befejezéshez öt – hét percet is igénybe vehet.

    **Példa**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak korlátozások a Azure PowerShell szkripttel a konfiguráció a v1-ről a v2-re való áttelepíteni?

Igen. Lásd a [figyelmeztetéseket és korlátozásokat](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>A Azure PowerShell szkript az alapszintű Load Balancerról az újonnan létrehozott standard Load Balancerra is átváltja a forgalmat?

Igen, áttelepíti a forgalmat. Ha a forgalmat személyesen szeretné áttelepíteni, használja [ezt a parancsfájlt](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) , amely nem helyezi át a virtuális gépeket.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Néhány probléma merült fel a szkript használatával. Hogyan Kérhetek segítséget?
  
E-mailt küldhet a slbupgradesupport@microsoft.comszolgáltatásnak, megnyithat egy támogatási esetet az Azure-támogatással, vagy mindkettőt.

## <a name="next-steps"></a>További lépések

[Tudnivalók a standard Load Balancer](load-balancer-overview.md)
