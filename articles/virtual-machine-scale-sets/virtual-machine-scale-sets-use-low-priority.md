---
title: Alacsony prioritású virtuális gépek (előzetes verzió) használó Azure méretezési csoport létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, amely alacsony prioritású virtuális gépek használatával költséget takaríthat Azure-beli virtuálisgép-méretezési csoportok
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: c0b4e3e0a924c1353f7732737670dee7ed45a62a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953871"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Alacsony prioritású virtuális gépek méretezési csoportokon (előzetes verzió)

Alacsony prioritású virtuális gépek használata a méretezési csoportok lehetővé teszi, jelentős költségmegtakarítást a unutilized kapacitásának kihasználásához. Ha az Azure igények vissza a kapacitás időben bármikor az Azure-infrastruktúra zárjon ki alacsony prioritású virtuális gépek. Ezért az alacsony prioritású virtuális gépek, amely képes kezelni a megszakítások számát, például a kötegelt feldolgozás feladatokat, fejlesztési és tesztelési környezetet, nagy méretű számítási feladatok és egyéb feladatokhoz kiváló.

Rendelkezésre álló unutilized kapacitását méret, régió, napi időpontot, és további függően változhat. Amikor beállítja a méretezési csoport alacsony prioritású virtuális gépek üzembe helyezése, Azure foglal le a virtuális gépeket, ha nincs elérhető kapacitás, de ezeknek a virtuális gépeknek nem tartozik SLA. Alacsony prioritású méretezési egyetlen tartalék tartomány van üzembe helyezve, és kínál a nem magas rendelkezésre állást garantál.

## <a name="eviction-policy"></a>Kiürítési szabályzat

Alacsony prioritású méretezési csoportok létrehozásakor beállíthatja kiürítési szabályzatát *felszabadítási* (alapértelmezés) vagy *törlése*. 

A *felszabadítási* házirend lehetővé teszi, hogy eltávolított-példány ismételt üzembe leállítva-(felszabadítva) állapotba helyezi át az eltávolított virtuális gépeket. Azonban nincs garancia arra, hogy a kiosztási sikeres lesz. A felszabadított virtuális gépekre fog számítanak bele a méretezési csoport példánykvóta és fizetni az alapul szolgáló lemezek. 

Ha azt szeretné, hogy a virtuális gépek az alacsony prioritású méretezési törölhető, ha azok ki vannak zárva, a kiürítési szabályzatot és megadható *törlése*. A kiürítési szabályzat törléséhez állítsa be növelje a méretezési csoport példánya száma tulajdonság beállítása új virtuális gépeket hozhat létre. Az eltávolított virtuális gépeket a rendszer törli az alaplemezek együtt, és ezért nem kell fizetni a tárolót. A méretezési csoportok automatikus skálázási funkció automatikusan kipróbálná, és a meghiúsult lépések kompenzációjához eltávolított virtuális gépeket is használható, azonban nincs garancia arra, hogy a kiosztási sikeres lesz. Javasoljuk, Ön csak az automatikus méretezési funkció alacsony prioritású méretezésicsoport-konfigurálásakor kell törölni a lemezeket, és szerezze meg a kvótakorlát elkerülése érdekében a kiürítési szabályzatot. 

> [!NOTE]
> Az előzetes verzióban lesz a kiürítési szabályzat beállítása használatával a [az Azure portal](#use-the-azure-portal) és [Azure Resource Manager-sablonok](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Beállítja a méretezési csoport alacsony prioritású virtuális gépek üzembe helyezése

Alacsony prioritás virtuális gépeket a méretezési csoportok üzembe helyezéséhez beállíthatja az új *prioritású* jelzőt *alacsony*. A méretezési csoportban lévő összes virtuális gépet alacsony prioritású állítja be. Hozzon létre egy méretezési csoportot az alacsony prioritású virtuális gépek, használja a következő módszerek egyikét:
- [Azure Portal](#use-the-azure-portal)
- [Azure CLI](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Az Azure Resource Manager-sablonok](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Egy méretezési csoportot, amely használja az alacsony prioritású virtuális gépek létrehozásának folyamata megegyezik a részletes a [első lépések a cikk](quick-create-portal.md). Amikor egy méretezési csoportot helyez üzembe, az alacsony prioritású jelzőt és a kiürítési szabályzatot választhat: ![hozzon létre egy méretezési csoportot az alacsony prioritású virtuális gépek](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Egy méretezési csoportot az alacsony prioritású virtuális gépek létrehozásának folyamata megegyezik a részletes a [első lépések a cikk](quick-create-cli.md). Adja hozzá a a "--prioritás" paraméter a parancssori felület hívás, illetve beállíthatja *alacsony* az alábbi példában látható módon:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

Egy méretezési csoportot az alacsony prioritású virtuális gépek létrehozásának folyamata megegyezik a részletes a [első lépések a cikk](quick-create-powershell.md).
Adja hozzá a "-prioritás" paramétert a [New-azurermvmssconfig parancsmaghoz](/powershell/module/azurerm.compute/new-azurermvmssconfig) , beállíthatja azt a *alacsony* az alábbi példában látható módon:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

Egy méretezési csoportot, amely használja az alacsony prioritású virtuális gépek létrehozásának folyamata megegyezik a az első lépéseket bemutató cikkben leírt módon [Linux](quick-create-template-linux.md) vagy [Windows](quick-create-template-windows.md). Adja hozzá a "priority" tulajdonságot a *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* erőforrás írja be a sablonban, és adja meg *alacsony* értékeként. Ügyeljen arra, hogy *2018-03-01* API-verzió vagy újabb verziója. 

Annak érdekében, hogy a törlési beállítása a kiürítési szabályzatot, adja hozzá a "evictionPolicy" paramétert, és állítsa be *törlése*.

Az alábbi példa létrehoz egy Linux alacsony prioritású nevű méretezési *myScaleSet* a *USA nyugati középső Régiója*, ahol *törlése* a virtuális gépeket a méretezési csoportban lévő kiürítési:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="faq"></a>GYIK

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Áttérhetek meglévő méretezési csoportok az alacsony prioritású méretezési csoportok?
Nem, az alacsony prioritású jelző csak támogatott létrehozáskor.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Hozhat létre egy méretezési csoportot a hagyományos virtuális gépekhez és az alacsony prioritású virtuális gépek?
Nem, egy méretezési csoportot nem támogatja egynél több prioritás.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Hogyan kezeli az alacsony prioritású virtuális gépek kvóta?
Alacsony prioritású virtuális gépek és a normál virtuális gépeket ossza meg ugyanazt a kvóta készletet. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Használható alacsony prioritású méretezési csoportok automatikus skálázási?
Igen, az automatikus skálázási szabályok adhatók meg az alacsony prioritású méretezési csoportot. Ha a virtuális gépek ki vannak zárva, automatikus méretezés próbálkozzon új alacsony prioritású virtuális gépek létrehozása. Ne feledje, azonban a kapacitások nem garantáltan. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>Automatikus skálázási működik az mindkét kiürítési szabályzatokat is (felszabadítása és törlése)?
Javasoljuk, hogy beállította-e törölni az automatikus méretezés használatakor a kiürítési szabályzatot. Ennek az az oka a példányok felszabadítása sikeresen megtörtént a méretezési csoportban lévő a kapacitás száma is beleszámít. Az automatikus méretezés használatakor valószínűleg eléri a cél-példányok száma a felszabadítása sikeresen megtörtént, eltávolított példányok gyorsan miatt. 

## <a name="next-steps"></a>További lépések
Most, hogy egy alacsony prioritású virtuális gépeket tartalmazó méretezési csoportot hozott létre, próbáljon üzembe helyezni a [automatikus méretezési csoport sablon az alacsony prioritású](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Tekintse meg a [díjszabását ismertető lapon virtuálisgép-méretezési](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) a díjszabás részleteiért.