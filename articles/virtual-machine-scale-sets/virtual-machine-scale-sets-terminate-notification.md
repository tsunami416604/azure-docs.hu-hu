---
title: Az Azure virtuálisgép-méretezési csoport példányainak értesítésének leállítása
description: Megtudhatja, hogy miként engedélyezheti a megszüntetési értesítést az Azure virtuálisgép-méretezési csoport példányaihoz
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250750"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Az Azure virtuálisgép-méretezési csoport példányainak értesítésének leállítása
A méretezési csoport példányai engedélyezhetik a példányok megszüntetéséről szóló értesítések fogadását, és előre meghatározott késleltetési időmegadást állíthatnak be a megszakítási művelethez. A megszüntetési értesítés az Azure Metaadat-szolgáltatás – [ütemezett események,](../virtual-machines/windows/scheduled-events.md)amely értesítéseket biztosít, és késlelteti a hatásos műveletek, például az újraindítások és az újratelepítés. A megoldás egy másik eseményt – Leállítja – hozzáad az ütemezett események listájához, és a megszakítási esemény társított késleltetése a felhasználók által a méretezési modell konfigurációiban megadott késleltetési korláttól függ.

Miután regisztrált a szolgáltatásba, a méretezési csoport példányainak nem kell megvárniuk, amíg a megadott időtúltöltés lejár a példány törlése előtt. A Lejárati értesítés kézhezvétele után a példány bármikor törölheti a törlést, mielőtt a leállási időtúllépés lejár.

## <a name="enable-terminate-notifications"></a>Értesítés leállítása
A skálázási beállításpéldányokon többféle módon is engedélyezheti a megszüntetési értesítéseket, ahogy azt az alábbi példák részletezik.

### <a name="azure-portal"></a>Azure portál

A következő lépések új méretezési csoport létrehozásakor engedélyezik a megszakítási értesítést. 

1. Lépjen a **Virtuálisgép-méretezési csoportokra.**
1. Új méretezési csoport létrehozásához válassza a **+ Add** lehetőséget.
1. Nyissa meg a **Kezelés** lapot. 
1. Keresse meg a **példány megszüntetésére** című szakaszt.
1. **A példány megszüntetéséről szóló értesítés**esetén válassza a **Be**lehetőséget.
1. **A Befejezés késleltetése (perc)** esetén állítsa be a kívánt alapértelmezett időhosszabbítást.
1. Ha végzett az új méretezési csoport létrehozásával, válassza a **Véleményezés + létrehozás** gombot. 

> [!NOTE]
> Nem tudja beállítani a megszakítási értesítéseket a meglévő méretezési csoportokon az Azure Portalon

### <a name="rest-api"></a>REST API

A következő példa engedélyezi a méretezési csoport modelljének megszakítási értesítését.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

A fenti blokk 5 perces időtúllépési késleltetést határoz meg (ahogy azt a *PT5M*jelzi) a méretezési csoport összes példányának megszakítási műveletéhez. A *notBeforeTimeout* mező iSO 8601 formátumban 5 és 15 perc között bármilyen értéket kivehet. A megszakítási művelet alapértelmezett időtúltöltését a *notBeforeTimeout* tulajdonság módosításával módosíthatja a fent leírt *terminateNotificationProfile* alatt.

Miután engedélyezte az *scheduledEventsProfile-t* a méretezési csoport modelljén, és beállította a *notBeforeTimeout-ot,* frissítse az egyes példányokat a [legújabb modellre,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) hogy azok tükrözzék a változásokat.

> [!NOTE]
>A méretezési csoport példányain az értesítések leállítása csak a 2019-03-01-es és újabb API-verzióval engedélyezhető

### <a name="azure-powershell"></a>Azure PowerShell
Új méretezési készlet létrehozásakor engedélyezheti a megszüntetési értesítéseket a méretezési készleten a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmag használatával.

Ez a mintaparancsfájl bemutatja egy méretezési csoport és a kapcsolódó erőforrások létrehozását a konfigurációs fájl használatával: [Teljes virtuálisgép-méretezési csoport létrehozása](./scripts/powershell-sample-create-complete-scale-set.md). Beállíthatja a végződési értesítést úgy, hogy hozzáadja a *TerminateScheduledEvents* és *terminateScheduledEventNotBeforeTimeoutInMinutes* paramétereket a méretezési csoport létrehozásához. A következő példa lehetővé teszi, hogy a szolgáltatás a késleltetési időout 10 perc.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag használatával lehetővé teszi a végződtetési értesítéseket egy meglévő méretezési csoporton.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
A fenti példa lehetővé teszi a megszakítási értesítéseket egy meglévő méretezési csoporton, és 15 perces időtúltöltést állít be a megszakítási eseményhez.

Miután engedélyezte az ütemezett eseményeket a méretezési csoport modelljén, és beállította az időoutot, frissítse az egyes példányokat a [legújabb modellre,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) hogy azok tükrözzék a változásokat.

### <a name="azure-cli-20"></a>Azure CLI 2.0

A következő példa a megszüntetési értesítés engedélyezése új méretezési csoport létrehozása közben.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

A fenti példa először létrehoz egy erőforráscsoportot, majd létrehoz egy új méretezési csoportot, amely10 perces alapértelmezett időtúllépéshez engedélyezve van a leszakított értesítések.

A következő példa a megszüntetési értesítés engedélyezése egy meglévő méretezési csoportban.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Értesítés leállítása

A megszakítási értesítések et [ütemezett eseményeken](../virtual-machines/windows/scheduled-events.md)keresztül küldik, amely egy Azure metaadat-szolgáltatás. Az Azure Metadata szolgáltatás a virtuális gépről elérhető REST-végpont használatával futó virtuális gépek használatával kapcsolatos információkat tesz elérhetővé. Az információ nem irányítható IP-cím, így nem érhető el a virtuális gépen kívül.

Az ütemezett események engedélyezve vannak a méretezési készlethez, amikor először kér eseményeket. Az első hívásban legfeljebb két perces késleltetett válaszra számíthat. Rendszeresen kérdezze le a végpontot a közelgő karbantartási események és a folyamatban lévő karbantartási tevékenységek állapotának észleléséhez.

Ütemezett események le van tiltva a méretezési csoport, ha a méretezési csoport példányai nem kér 24 órán keresztül.

### <a name="endpoint-discovery"></a>Végpont felderítése
Virtuális hálózatra engedélyezett virtuális gépek esetén a metaadat-szolgáltatás statikus, nem irányítható IP-címből érhető el, 169.254.169.254.

Az ütemezett események legújabb verziójának teljes végpontja a következő:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Lekérdezési válasz
A válasz ütemezett események tömbjét tartalmazza. Az üres tömb azt jelenti, hogy jelenleg nincsenek ütemezett események.

Abban az esetben, ha ütemezett események vannak, a válasz események tömbjét tartalmazza. A "Terminate" esemény esetén a válasz a következőképpen fog kinézni:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
A *DocumentInannináció* egy ETag, és egyszerű módja annak vizsgálatának, hogy az események hasznos terhelése megváltozott-e az utolsó lekérdezés óta.

A fenti mezőkről további információt a [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) és Linux ütemezett események dokumentációjában [talál.](../virtual-machines/linux/scheduled-events.md#event-properties)

### <a name="respond-to-events"></a>Válasz eseményekre
Miután tudomást szerzett egy közelgő eseményről, és befejezte a szabályos leállítás logikáját, jóváhagyhatja a kiemelkedő eseményt azáltal, hogy postahívást kezdeményez a metaadat-szolgáltatáshoz az EventId del. A POST-hívás azt jelzi az Azure-nak, hogy folytathatja a virtuális gép törlését.

Az alábbiakban a json várható a POST kérelem szerv. A kérelemnek tartalmaznia kell a StartRequests listáját. Minden StartRequest tartalmazza a meggyorsítani kívánt esemény eseményazonosítót:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Győződjön meg arról, hogy a méretezési készlet minden virtuális gép csak az adott virtuális gépre vonatkozó EventID jóváhagyása. A virtuális gép saját virtuálisgép-nevet kaphat [a példány metaadatain keresztül.](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name) Ez a név a "{scale-set-name}_{instance-id}" formában jelenik meg, és a fent leírt lekérdezési válasz "Erőforrások" szakaszában jelenik meg.

A [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) és a [Python](../virtual-machines/linux/scheduled-events.md#python-sample)használatával lekérdezésre és az eseményekre válaszoló mintákparancsfájljaira is hivatkozhat.

## <a name="tips-and-best-practices"></a>Tippek és ajánlott eljárások
-   Az értesítések leállítása csak a "delete" műveletek – Minden törlési művelet (manuális törlés vagy automatikus skálázás által kezdeményezett méretezési alapú) létrehoz események leállítása, ha a méretezési csoport *scheduledEventsProfile* engedélyezve van. Más műveletek, például újraindítás, újratelepítés, újratelepítés és stop/deallocate nem hozlétre leállítási eseményeket. Az értesítések leállítása nem engedélyezhető az alacsony prioritású virtuális gépeken.
-   Nincs kötelező várakozás az időtúloldalon – Az esemény fogadása után és az esemény *NotBefore* idő lejárta előtt bármikor elindíthatja a megszakítási műveletet.
-   Kötelező törlés időtúlidőpontban – nincs lehetőség az időtúlodási érték meghosszabbítására egy esemény létrehozása után. Az időbeli elévülése után a függőben lévő megszüntetési esemény feldolgozásra kerül, és a virtuális gép törlődik.
-   Módosítható időtúllépést érték – Az időtúllépést bármikor módosíthatja, mielőtt egy példány törlődik, a *nemBeforeTimeout* tulajdonság módosításával a méretezési csoport modell és a virtuális gép példányainak frissítése a legújabb modellre.
-   Az összes függőben lévő törlés jóváhagyása – Ha van egy függőben lévő törlés VM_1, amely nincs jóváhagyva, és VM_2-én egy másik megszüntetési eseményt hagyott jóvá, akkor VM_2 nem törlődik, amíg a VM_1 végződési esemény jóváhagyása vagy az időtúlidőpontja el nem telik. Miután jóváhagyta a VM_1-es megszakítási eseményt, akkor mind VM_1, mind VM_2 törlődnek.
-   Az összes egyidejű törlés jóváhagyása – A fenti példa kiterjesztése, ha VM_1 és VM_2 azonos *NotBefore* idő, akkor mindkét megszakítási eseményeket jóvá kell hagyni, vagy egyik virtuális gép sem törlődik az időtúllépés lejárta előtt.

## <a name="troubleshoot"></a>Hibaelhárítás
### <a name="failure-to-enable-scheduledeventsprofile"></a>Nem sikerült engedélyezni az scheduledEventsProfile-t
Ha "BadRequest" hibaüzenetjelenik meg a "VirtualMachineProfile" típusú objektumon található "ScheduledEventsProfile" taggal, ellenőrizze a méretezési csoport műveleteihez használt API-verziót. Számítási API-verzió **2019-03-01** vagy újabb szükséges. 

### <a name="failure-to-get-terminate-events"></a>Nem sikerült leadni a Terminate eseményeket
Ha nem kap **meg terminate** események et ütemezett eseményeken keresztül, majd ellenőrizze az API-verzió az események beszerzése. Metaadat-szolgáltatás API-verziója **2019-01-01** vagy újabb szükséges az események leállítása.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>A Terminate esemény beszerzése helytelen NotBefore idővel  
Miután engedélyezte az *scheduledEventsProfile-t* a méretezési csoport modelljén, és beállította a *notBeforeTimeout-ot,* frissítse az egyes példányokat a [legújabb modellre,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) hogy azok tükrözzék a változásokat.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [telepítheti az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuális gép méretezési csoportok.
