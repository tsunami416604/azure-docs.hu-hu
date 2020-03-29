---
title: Egyéni méretezési szabályzatok használata az Azure virtuálisgép-méretezési készleteivel
description: Megtudhatja, hogy miként használhatja az egyéni skálázási szabályzatokat az Azure virtuálisgép-méretezési csoportokkal, amelyek automatikus skálázási konfigurációt használnak a példányok számának kezeléséhez
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919838"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Egyéni méretezési szabályzatok használata az Azure virtuálisgép-méretezési készleteivel

A virtuálisgép-méretezési csoport központi telepítése metrikák tömbje alapján skálázható vagy skálázható, beleértve a platformot és a felhasználó által definiált egyéni metrikákat. Míg a horizontális felskálázás új virtuális gépeket hoz létre a méretezési csoport modellje alapján, a horizontális felskálázás hatással van a futó virtuális gépekre, amelyek különböző konfigurációkkal és/vagy funkciókkal rendelkezhetnek a méretezési csoport munkaterhelésének fejlődésével. 

A horizontális felskálázási házirend szolgáltatás lehetővé teszi a felhasználók számára, hogy három méretezési konfigurációval konfigurálják a virtuális gépek méretezési sorrendjét: 

1. Alapértelmezett
2. LegújabbVM
3. Legrégebbi VM

### <a name="default-scale-in-policy"></a>Alapértelmezett horizontális felskálázási házirend

Alapértelmezés szerint a virtuálisgép-méretezési csoport ezt a házirendet alkalmazza annak meghatározására, hogy melyik példány(ok) lesz méretezve. Az *alapértelmezett* házirend del a virtuális gépek a következő sorrendben vannak kiválasztva a méretezéshez:

1. Virtuális gépek egyensúlya a rendelkezésre állási zónák között (ha a méretezési csoport zónaszintű konfigurációban van telepítve)
2. Virtuális gépek egyensúlya a tartalék tartományok között (legjobb erőfeszítés)
3. A legmagasabb példányazonosítóval rendelkező virtuális gép törlése

A felhasználóknak nem kell megadniuk a horizontális felskálázási házirendet, ha csak az alapértelmezett rendezést szeretnék követni.

Vegye figyelembe, hogy a rendelkezésre állási zónák vagy a tartalék tartományok közötti kiegyensúlyozás nem mozgatja a példányokat a rendelkezésre állási zónák vagy a tartalék tartományok között. A kiegyensúlyozás a virtuális gépek nek a kiegyensúlyozatlan rendelkezésre állási zónákból vagy tartalék tartományokból való törlésével érhető el, amíg a virtuális gépek elosztása kiegyensúlyozottká nem válik.

### <a name="newestvm-scale-in-policy"></a>NewestVM horizontális felskálázási házirend

Ez a házirend törli a legújabb létrehozott virtuális gépet a méretezési csoportban, miután kiegyensúlyozza a virtuális gépeket a rendelkezésre állási zónák között (zónaszintű telepítések esetén). A házirend engedélyezéséhez a virtuálisgép méretezési csoport modelljének konfigurációs módosítására van szükség.

### <a name="oldestvm-scale-in-policy"></a>LegrégebbiVM-alapú horizontális felskálázási házirend

Ez a házirend törli a méretezési csoport legrégebbi létrehozott virtuális gépét, miután kiegyensúlyozza a virtuális gépeket a rendelkezésre állási zónák között (zónaszintű telepítések esetén). A házirend engedélyezéséhez a virtuálisgép méretezési csoport modelljének konfigurációs módosítására van szükség.

## <a name="enabling-scale-in-policy"></a>Horizontális felskálázási házirend engedélyezése

A virtualgép-méretezési csoport modellje egy horizontális felskálázási házirendet határoz meg. Amint azt a fenti szakaszokban megjegyeztük, a "NewestVM" és a "OldestVM" házirendek használatakor egy horizontális felskálázási házirend-definícióra van szükség. A virtuálisgép-méretezési csoport automatikusan az "Alapértelmezett" horizontális felskálázási házirendet használja, ha a méretezési csoport modellben nem található méretezési házirend-definíció. 

A virtualgép-méretezési csoport modellje a következő módokon definiálható a méretezési szabályzatban:

### <a name="azure-portal"></a>Azure portál
 
A következő lépések határozzák meg a horizontális felskálázási házirendet új méretezési készlet létrehozásakor. 
 
1. Lépjen a **Virtuálisgép-méretezési csoportokra.**
1. Új méretezési csoport létrehozásához válassza a **+ Add** lehetőséget.
1. Nyissa meg a **Méretezés** lapot. 
1. Keresse meg a **méretezési szabályzat** szakaszt.
1. Válasszon egy horizontális felskálázási szabályzatot a legördülő menüből.
1. Ha végzett az új méretezési csoport létrehozásával, válassza a **Véleményezés + létrehozás** gombot.

### <a name="using-api"></a>Az API használata

Put végrehajtása a virtuálisgép-méretezési csoporton az API 2019-03-01 használatával:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Hozzon létre egy erőforráscsoportot, majd hozzon létre egy új méretezési csoportot, amelynek méretezési házirendje legrégebbi vm-ként van *beállítva.*

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

A következő példa egy horizontális felskálázási házirendet ad hozzá, miközben új méretezési készletet hoz létre. Először hozzon létre egy erőforráscsoportot, majd hozzon létre egy új méretezési csoportot *a legrégivm-es*méretezési házirenddel. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Sablon használata

A sablonban a "Tulajdonságok" területen adja hozzá a következőket:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

A fenti blokkok határozzák meg, hogy a virtuális gép méretezési készlettörli a legrégebbi virtuális gép egy zóna-kiegyensúlyozott méretezési készlet, amikor egy horizontális felskálázás aktiválódik (automatikus skálázás vagy manuális törlés).

Ha egy virtuálisgép-méretezési csoport nem zóna kiegyensúlyozott, a méretezési csoport először törli a virtuális gépeket a kiegyensúlyozatlan zóna(k) között. A kiegyensúlyozatlan zónákon belül a méretezési készlet a fent megadott skálázási házirendet fogja használni annak meghatározásához, hogy melyik virtuális géphez kell skálázni. Ebben az esetben egy kiegyensúlyozatlan zónán belül a méretezési csoport kiválasztja a legrégebbi virtuális gép ebben a zónában törölni kell.

A nem zónaszintű virtuálisgép-méretezési csoport, a szabályzat kiválasztja a legrégebbi virtuális gép a méretezési csoport ban törlésre.

Ugyanez a folyamat vonatkozik a "NewestVM" használata a fenti horizontális felskálázási házirendben.

## <a name="modifying-scale-in-policies"></a>Méretezési házirendek módosítása

A horizontális felskálázási házirend módosítása ugyanazt a folyamatot követi, mint a horizontális felskálázási házirend alkalmazása. Ha például a fenti példában a házirendet "OldestVM"-ről "NewestVM"-re szeretné módosítani, ezt a következő módon teheti meg:

### <a name="azure-portal"></a>Azure portál

Módosíthatja egy meglévő méretezési csoport méretezési szabályzatát az Azure Portalon keresztül. 
 
1. Egy meglévő virtuálisgép-méretezési csoportban válassza a **méretezés** lehetőséget a bal oldali menüben.
1. Válassza a **Méretezési házirend** lapot.
1. Válasszon egy horizontális felskálázási szabályzatot a legördülő menüből.
1. Amikor elkészült, válassza a **Mentés** gombot. 

### <a name="using-api"></a>Az API használata

Put végrehajtása a virtuálisgép-méretezési csoporton az API 2019-03-01 használatával:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Meglévő méretezési csoport méretezési házirendjének frissítése:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Az alábbi példa egy meglévő méretezési csoport méretezési házirendjének frissítésére szól: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Sablon használata

A sablonban a "Tulajdonságok" területen módosítsa a sablont az alábbi módon, és telepítse újra: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Ugyanez a folyamat vonatkozik, ha úgy dönt, hogy a "NewestVM" beállítást "Alapértelmezett" vagy "OldestVM" értékre módosítja.

## <a name="instance-protection-and-scale-in-policy"></a>Példányvédelem és horizontális felskálázási házirend

A virtuálisgép-méretezési csoportok kétféle [példányvédelmet](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)biztosítanak:

1. Védelem a méretezéstől
2. Védelem a méretezési műveletekkel szemben

A védett virtuális gép nem törlődik egy horizontális felskálázási művelet, függetlenül attól, hogy a méretezési házirend alkalmazott. Például ha VM_0 (a méretezési készlet legrégebbi virtuális gépe) védett a méretezési ponttól, és a méretezési készlet "LegrégebbiVM" méretezési szabályzattal rendelkezik, VM_0 nem veszi figyelembe a méretezési, annak ellenére, hogy a méretezési készlet legrégebbi virtuális gép. 

A védett virtuális gépeket a felhasználó bármikor manuálisan törölheti, függetlenül a ttól, hogy a méretezési csoportban engedélyezett-e a horizontális felskálázási házirend. 

## <a name="usage-examples"></a>Használati példák 

Az alábbi példák bemutatják, hogy egy virtuálisgép-méretezési csoport hogyan választja ki a virtuális gépeket, amelyeket törölni kell egy méretezési esemény aktiválásakor. A legmagasabb példányazonosítókkal rendelkező virtuális gépek a méretezési csoport legújabb virtuális gépei, a legkisebb példányazonosítókkal rendelkező virtuális gépek a méretezési csoport legrégebbi virtuális gépei. 

### <a name="oldestvm-scale-in-policy"></a>LegrégebbiVM-alapú horizontális felskálázási házirend

| Esemény                 | Példányazonosítók a 1.  | Példányazonosítók a 2.  | Példányazonosítók a 3.  | Méretezési beállítások kiválasztása                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Kezdeti               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Beskálázás              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Válasszon az 1-es és a 2-es zóna közül, még akkor is, ha a 3-as zónában van a legrégebbi virtuális gép. Törölje a Virtuálisgép2-t a 2-es zónából, mivel ez a zóna legrégebbi virtuális gépe.   |
| Beskálázás              | ***3,*** 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Válassza az 1-es zónát, annak ellenére, hogy a 3-as zónában van a legrégebbi virtuális gép. Törölje a VM3-at az 1-es zónából, mivel ez a zóna legrégebbi virtuális gépe.                  |
| Beskálázás              | 4, 5, 10               | 6, 9, 11               | ***1,*** 7, 8          | A zónák kiegyensúlyozottak. Törölje a Virtuálisgép1-et a 3-as zónában, mivel ez a méretezési csoport legrégebbi virtuális gépe.                                               |
| Beskálázás              | ***4,*** 5, 10         | 6, 9, 11               | 7, 8                   | Válasszon az 1-es és a 2-es zóna között. Törölje a VM4-et az 1-es zónában, mivel ez a legrégebbi virtuális gép a két zónában.                              |
| Beskálázás              | 5, 10                  | ***6,*** 9, 11         | 7, 8                   | Válassza a 2-es zónát, annak ellenére, hogy az 1-es zóna rendelkezik a legrégebbi virtuális gép. Törölje a Virtuálisgép 6-ot az 1-es zónában, mivel ez a zóna legrégebbi virtuális gépe.                    |
| Beskálázás              | ***5,*** 10            | 9, 11                  | 7, 8                   | A zónák kiegyensúlyozottak. Törölje a VM5-öt az 1-es zónában, mivel ez a méretezési csoport legrégebbi virtuális gépe.                                                |

A nem zónaszintű virtuálisgép-méretezési csoportok, a szabályzat kiválasztja a legrégebbi virtuális gép a méretezési csoport ban törlésre. Minden "védett" virtuális gép törlésre kerül kimarad.

### <a name="newestvm-scale-in-policy"></a>NewestVM horizontális felskálázási házirend

| Esemény                 | Példányazonosítók a 1.  | Példányazonosítók a 2.  | Példányazonosítók a 3.  | Méretezési beállítások kiválasztása                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Kezdeti               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Beskálázás              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Válasszon az 1-es és a 2-es zóna közül. Törölje a VM11-et a 2-es zónából, mivel ez a két zóna legújabb virtuális gépe.                                |
| Beskálázás              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Válassza az 1-es zónát, mert több virtuális gép, mint a másik két zóna. Törölje a VM10-et az 1-es zónából, mivel ez a zóna legújabb virtuális gépe.          |
| Beskálázás              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | A zónák kiegyensúlyozottak. Törölje a VM9-et a 2-es zónában, mivel ez a méretezési csoport legújabb virtuális gépe.                                                |
| Beskálázás              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Válasszon az 1-es és a 3-as zóna között. Törölje a VM8-at a 3-as zónában, mivel ez a zóna legújabb virtuális gépe.                                      |
| Beskálázás              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Válassza az 1-es zónát annak ellenére, hogy a 3-as zónában található a legújabb virtuális gép. Törölje a VM5-öt az 1-es zónában, mivel ez a zóna legújabb virtuális gépe.                    |
| Beskálázás              | 3, 4                   | 2, 6                   | 1, ***7***             | A zónák kiegyensúlyozottak. Törölje a Virtuálisgép7-et a 3-as zónában, mivel ez a méretezési csoport legújabb virtuális gépe.                                                |

A nem zónaszintű virtuálisgép-méretezési csoportok, a szabályzat kiválasztja a legújabb virtuális gép a méretezési csoport ban törlésre. Minden "védett" virtuális gép törlésre kerül kimarad. 

## <a name="troubleshoot"></a>Hibaelhárítás

1. A scaleInPolicy engedélyezésének elmulasztása Ha "BadRequest" hibaüzenetet kap, amely a következő hibaüzenetet tartalmazza: "Nem található a "scaleInPolicy" tag a "properties" típusú objektumon, majd ellenőrizze a virtuálisgép-méretezési készlethez használt API-verziót. Ehhez a funkcióhoz a 2019-03-01-es vagy újabb API-verzió szükséges.

2. A virtuális gépek nem megfelelő kiválasztása a méretezési ponthoz Tekintse meg a fenti példákat. Ha a virtuális gép méretezési készletegy zonális központi telepítés, a méretezési házirend először a kiegyensúlyozatlan zónák, majd a méretezési csoportban, ha a zóna kiegyensúlyozott. Ha a horizontális felskálázás sorrendje nem felel meg a fenti példáknak, hozzon létre egy lekérdezést a virtuálisgép méretezési csoport csapat hibaelhárításhoz.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [telepítheti az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuális gép méretezési csoportok.