---
title: Log Analytics virtuális gépi bővítmény Linuxhoz
description: Telepítse a Log Analytics-ügynököt Linux-rendszerű virtuális gépen egy virtuálisgép-bővítmény használatával.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: 9ddac229fc38a91a8b97b24dc2807080b2295758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250555"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics virtuális gépi bővítmény Linuxhoz

## <a name="overview"></a>Áttekintés

Az Azure Monitor Logs figyelési, riasztási és riasztási javítási képességeket biztosít a felhőbeli és helyszíni eszközökön. A Log Analytics virtuálisgép-bővítmény titulálta és támogatja a Microsoft. A bővítmény telepíti a Log Analytics-ügynököt az Azure virtuális gépeken, és virtuális gépeket foglal be egy meglévő Log Analytics-munkaterületre. Ez a dokumentum részletezi a támogatott platformok, konfigurációk és üzembe helyezési lehetőségek a Log Analytics virtuálisgép-bővítmény Linux.

>[!NOTE]
>A Microsoft Operations Management Suite (OMS) szolgáltatásról az Azure Monitorra való folyamatos áttérés részeként a Windows vagy Linux OMS-ügynök a Windows és a Log Analytics Linux loganalytics-ügynökeként lesz hivatkozva.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A támogatott Linux-disztribúciók ról a [Log Analytics-ügynök áttekintéséről](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) szóló cikkben olvashat.

### <a name="agent-and-vm-extension-version"></a>Ügynök és vm-bővítmény verziója
Az alábbi táblázat a Log Analytics virtuálisgép-bővítmény és a Log Analytics-ügynökcsomag verziójának leképezését tartalmazza az egyes kiadásokhoz. A Log Analytics-ügynökcsomag verziójának kiadási megjegyzésekre mutató hivatkozása is megtalálható. A kibocsátási megjegyzések tartalmazzák a hibajavítások részleteit és az adott ügynökkiadáshoz elérhető új funkciókat.  

| Log Analytics Linux VM bővítmény verziója | A Log Analytics Ügynök csomag verziója | 
|--------------------------------|--------------------------|
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center automatikusan leköti a Log Analytics-ügynököt, és csatlakoztatja azt az ASC által az Azure-előfizetésben létrehozott alapértelmezett Log Analytics-munkaterülethez. Ha az Azure Security Centert használja, ne futtassa a jelen dokumentum lépéseit. Ezzel felülírja a konfigurált munkaterületet, és megszakítja a kapcsolatot az Azure Security Centerrel.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Log Analytics Ügynök linuxos bővítmény megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics-ügynök bővítmény sémáját jeleníti meg. A bővítmény hez a munkaterület-azonosító és a munkaterületkulcs szükséges a célnapló-elemzési munkaterületről; ezek az értékek az Azure Portalon található [Log Analytics-munkaterületen találhatók.](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) Mivel a munkaterületi kulcsot bizalmas adatként kell kezelni, védett beállítási konfigurációban kell tárolni. Az Azure VM-bővítmény védett beállítási adatai titkosítva vannak, és csak a cél virtuális gépen fejtik vissza. Vegye figyelembe, hogy **a workspaceId** és **a workspaceKey** a kis- és nagybetűket is figyelembe veszi.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>A fenti séma feltételezi, hogy a sablon gyökérszintjén lesz elhelyezve. Ha a sablonban a virtuálisgép-erőforrásba `type` `name` helyezi, a és a tulajdonságokat módosítani kell, a hogy a lentlejjebb leírt módon [levan írva.](#template-deployment)

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| közzétevő | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| munkaterület-azonosító (pl. | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (pl. | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek üzembe helyezés utáni konfigurációt igényelnek, például az Azure Monitor naplókba való bevezetést. A Log Analytics Ügynök virtuálisgép-bővítményt tartalmazó mintaerőforrás-kezelősablon az [Azure gyorsútmutató-galériában](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)található. 

A virtuálisgép-bővítmény JSON-konfigurációja beágyazható a virtuálisgép-erőforrásba, vagy elhelyezhető egy Erőforrás-kezelő JSON-sablon gyökér- vagy legfelső szintjén. A JSON-konfiguráció elhelyezése hatással van az erőforrás nevének és típusának értékére. További információt a [Név és a gyermekerőforrások típusának beállítása](../../azure-resource-manager/templates/child-resource-name-type.md)című témakörben talál. 

A következő példa feltételezi, hogy a virtuális gép bővítmény e-kiszolgáló a virtuális gép erőforrás. A bővítmény erőforrás beágyazásakor a `"resources": []` JSON a virtuális gép objektumába kerül.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Amikor a JSON bővítményt helyezi a sablon gyökeréhez, az erőforrás neve tartalmaz egy hivatkozást a szülő virtuális gépre, és a típus a beágyazott konfigurációt tükrözi.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

Az Azure CLI használható a Log Analytics Ügynök virtuálisgép-bővítmény üzembe helyezéséhez egy meglévő virtuális gépre. Cserélje le az alábbi *myWorkspaceKey* értéket a munkaterületi kulcsra és a *myWorkspaceId* értéket a munkaterület-azonosítóra. Ezek az értékek az Azure PortalOn található Log Analytics-munkaterületen találhatók a *Speciális beállítások csoportban.* 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény-végrehajtási kimenet a következő fájlba kerül:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és jelentésük

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 9 | Korai meghívni engedélyezése | [Frissítse az Azure Linux-ügynök](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) a legújabb elérhető verzióra. |
| 10 | A virtuális gép már csatlakozik egy Log Analytics-munkaterülethez | Ha a virtuális gép a bővítménysémában megadott munkaterülethez szeretne csatlakozni, állítsa a stopOnMultipleConnections beállítást hamis helyre a nyilvános beállításokban, vagy távolítsa el ezt a tulajdonságot. Ez a virtuális gép minden olyan munkaterületért egyszer kerül számlázásra, amelyhez kapcsolódik. |
| 11 | Érvénytelen konfiguráció, amelyet a bővítmény nek adtak | Kövesse az előző példákat a telepítéshez szükséges összes tulajdonságérték beállításához. |
| 17 | A Log Analytics csomag telepítési hibája | 
| 19 | OMI csomag telepítési hiba | 
| 20 | Az SCX csomag telepítési hibája |
| 51 | Ez a bővítmény nem támogatott a virtuális gép operációs rendszerében | |
| 55 | Nem lehet csatlakozni az Azure Monitor szolgáltatáshoz, vagy a hiányzó csomagok vagy a dpkg csomagkezelő zárolva van| Ellenőrizze, hogy a rendszer rendelkezik-e internet-hozzáféréssel, vagy hogy van-e érvényes HTTP-proxy. Ellenőrizze továbbá a munkaterület-azonosító helyességét, és ellenőrizze, hogy telepítve vannak-e a kaszkadár- és kátrányos segédprogramok. |

További hibaelhárítási információk a [Log Analytics-Agent-for-Linux hibaelhárítási útmutatóban](../../azure-monitor/platform/vmext-troubleshoot.md)találhatók.

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.
