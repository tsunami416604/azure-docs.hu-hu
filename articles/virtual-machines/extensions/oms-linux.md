---
title: Log Analytics virtuálisgép-bővítmény linuxhoz
description: A Linux rendszerű virtuális gép, virtuálisgép-bővítmények használatával a Log Analytics-ügynök telepítése.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250555"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics virtuálisgép-bővítmény linuxhoz

## <a name="overview"></a>Áttekintés

Azure Monitor naplók a Felhőbeli és a helyszíni eszközökön végzett figyelési, riasztási és riasztási lehetőségeket biztosítanak. A Linux rendszerhez készült Log Analytics virtuálisgép-bővítményt a Microsoft közzétette és támogatja. A bővítmény a Log Analytics-ügynököket telepíti az Azure-beli virtuális gépeken, és regisztrálja a virtuális gépek egy meglévő Log Analytics-munkaterületet. Ez a dokumentum részletesen, a támogatott platformok, konfigurációk és a Log Analytics virtuálisgép-bővítmény linuxhoz üzembe helyezési lehetőségeit.

>[!NOTE]
>Az Azure Monitor folyamatos Váltás a Microsoft Operations Management Suite (OMS) részeként az OMS ügynök a Windows vagy Linux rendszerű fog példányaként a Log Analytics-ügynököket Windows és a Log Analytics-ügynök Linux rendszeren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A támogatott Linux-disztribúciókkal kapcsolatos részletekért tekintse meg a [log Analytics ügynök áttekintését](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) ismertető cikket.

### <a name="agent-and-vm-extension-version"></a>Az ügynök és a Virtuálisgép-bővítmény verziója
Az alábbi táblázat tartalmazza a Log Analytics Virtuálisgép-bővítmény és a Log Analytics ügynök csomag minden kiadott verziójáról. A Log Analytics csomag verziója kibocsátási megjegyzései mutató hivatkozás megtalálható. Kibocsátási megjegyzések a hibajavítások és a egy adott ügynök kiadásban elérhető új szolgáltatások közé tartozik.  

| Log Analytics Linux VM-bővítmény verziója | Log Analytics-ügynököket csomag verziója | 
|--------------------------------|--------------------------|
| 1.12.25 | [1.12.15 – 0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0 – 9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0 – 1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0 – 0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1 – 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0-as | [1.8.0 – 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1 – 3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0 – 42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4 – 210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3 – 174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2 – 125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2 – 124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1 – 123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1 – 45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0 – 45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5 – 127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5 – 127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4 – 15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center automatikusan látja el a Log Analytics-ügynököket, és csatlakoztatja az Azure-előfizetésében az ASC által létrehozott alapértelmezett Log Analytics-munkaterületet. Ha az Azure Security Center használ, ne futtassa a jelen dokumentumban leírt lépések segítségével. Ezzel felülírja a konfigurált munkaterületével, és megszakítja a kapcsolatot az Azure Security Centerrel.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Log Analytics-ügynököket-bővítmény linuxhoz megköveteli, hogy a céloldali virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics-ügynök bővítmény sémáját jeleníti meg. A kiterjesztéshez a munkaterület-azonosító és a munkaterület kulcsa szükséges a cél Log Analytics munkaterületről; Ezek az értékek a Azure Portal [log Analytics munkaterületén találhatók](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) . A munkaterület kulcsát kényes adatként kell kezelni, mert azt egy védett beállítás konfigurációjának kell tárolni. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen. Vegye figyelembe, hogy a **munkaterület azonosítója** és a **workspaceKey** a kis-és nagybetűk megkülönböztetése.

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
>A fenti séma azt feltételezi, hogy ez a sablon gyökér szintű kerülnek. Ha a sablonban elhelyezi a virtuális gép erőforrásában, a `type` és az `name` tulajdonságokat módosítani kell, a [lejjebb](#template-deployment)leírtak szerint.

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| munkaterület azonosítója (például:) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (például:) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amely az üzembe helyezés utáni konfigurációt igényli, például Azure Monitor naplók bevezetését. A Log Analytics Agent virtuálisgép-bővítményt tartalmazó példa Resource Manager-sablon az [Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)rövid útmutatójában található. 

Virtuálisgép-bővítmények JSON konfigurációjának a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON konfigurációs elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információ: [a gyermek erőforrások nevének és típusának beállítása](../../azure-resource-manager/templates/child-resource-name-type.md). 

Az alábbi példa azt feltételezi, hogy a Virtuálisgép-bővítményt a virtuális gép típusú erőforrást van beágyazva. A bővítmény erőforrásának beágyazásakor a rendszer a JSON-t a virtuális gép `"resources": []` objektumára helyezi.

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

Helyezi el a JSON-bővítmény a sablonban gyökérmappájában, amikor az erőforrás neve a szülő virtuális gép egy hivatkozást tartalmaz, és a típus a beágyazott konfigurációját tükrözi.  

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

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével a Log Analytics ügynök Virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez. Cserélje le az alábbi *myWorkspaceKey* értéket a munkaterület kulcsával és a *myWorkspaceId* értékkel a munkaterület-azonosítójával. Ezek az értékek a Azure Portal *Speciális beállítások*területén található log Analytics munkaterületén találhatók. 

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

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és azok jelentését

| Hibakód | Jelentés | A művelet lehetséges |
| :---: | --- | --- |
| 9 | Túl korán nevű engedélyezése | [Frissítse az Azure Linux-ügynököt](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) a legújabb elérhető verzióra. |
| 10 | Virtuális gép már csatlakoztatva van egy Log Analytics-munkaterület | A virtuális gép csatlakozik a bővítmény sémában megadott munkaterület, stopOnMultipleConnections "false" értékűre a nyilvános beállításait, vagy távolítsa el ezt a tulajdonságot. Ez a virtuális gép egyes munkaterületekre vonatkozó való csatlakozás után díjat számítjuk fel. |
| 11 | A bővítmény megadott Érvénytelen konfiguráció | Kövesse az előző példákból való üzembe helyezéshez szükséges összes tulajdonság értéket állítsa be. |
| 17 | Log Analytics-csomag telepítési hiba | 
| 19 | OMI csomag telepítési hiba | 
| 20 | Az SCX-csomag telepítési hiba |
| 51 | Ezt a bővítményt a virtuális gép operációs rendszer nem támogatott | |
| 55 | Nem lehet csatlakozni a Azure Monitor szolgáltatáshoz, vagy hiányzik a szükséges csomagok, vagy a dpkg Package Manager zárolva van| Ellenőrizze, hogy a rendszer rendelkezik Internet-hozzáféréssel, vagy az, hogy érvényes HTTP proxy lett megadva. Ezenkívül ellenőrizze a munkaterület azonosítójának helyességét, és a curl és tar segédprogram telepítve van. |

További hibaelhárítási tudnivalókat a [log Analytics-Agent-Linux hibaelhárítási útmutatójában](../../azure-monitor/platform/vmext-troubleshoot.md)talál.

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
