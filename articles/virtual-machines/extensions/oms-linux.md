---
title: Az Azure Log Analytics virtuálisgép-bővítmény linuxhoz |} A Microsoft Docs
description: A Linux rendszerű virtuális gép, virtuálisgép-bővítmények használatával a Log Analytics-ügynök telepítése.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/30/2018
ms.author: roiyz
ms.openlocfilehash: 93e64aaa436b23390de328a9268dcfcb773c279f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52728161"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics virtuálisgép-bővítmény linuxhoz

## <a name="overview"></a>Áttekintés

A log Analytics monitorozási, riasztási és riasztási szervizelési képességeket biztosít a felhőben és helyszíni eszközökön. A Log Analytics-ügynököket a virtuálisgép-bővítmény linuxhoz közzétett és a Microsoft támogatja. A bővítmény a Log Analytics-ügynököket telepíti az Azure-beli virtuális gépeken, és regisztrálja a virtuális gépek egy meglévő Log Analytics-munkaterületet. Ez a dokumentum részletesen, a támogatott platformok, konfigurációk és a Log Analytics virtuálisgép-bővítmény linuxhoz üzembe helyezési lehetőségeit.

>[!NOTE]
>Az Azure Monitor folyamatos Váltás a Microsoft Operations Management Suite (OMS) részeként az OMS ügynök a Windows vagy Linux rendszerű fog példányaként a Log Analytics-ügynököket Windows és a Log Analytics-ügynök Linux rendszeren.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Log Analytics-ügynök bővítményt a Linux-disztribúciók is futtatni.

| Disztribúció | Verzió |
|---|---|
| CentOS Linux | 6 (x86/x64) és 7 (x 64) |
| Amazon Linux | 2017.09 (x64) | 
| Oracle Linux | 6. és 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 6 (x86/x64) és 7 (x 64) |
| Debian GNU/Linux | 8. és 9 (x86/x64) |
| Ubuntu | 14.04 (x86/x64) LTS, 16.04 LTS (x86/x64) és 18.04 LTS (x64) |
| SUSE Linux Enterprise Server | 12 (x 64) |

>[!NOTE]
>OpenSSL-verzió kisebb 1.x bármilyen platformon nem támogatott, és csak támogatott verzió 1.10 x86_64 platformok (64 bites).  
>

### <a name="agent-and-vm-extension-version"></a>Az ügynök és a Virtuálisgép-bővítmény verziója
Az alábbi táblázat tartalmazza a Log Analytics Virtuálisgép-bővítmény és a Log Analytics ügynök csomag minden kiadott verziójáról. A Log Analytics csomag verziója kibocsátási megjegyzései mutató hivatkozás megtalálható. Kibocsátási megjegyzések a hibajavítások és a egy adott ügynök kiadásban elérhető új szolgáltatások közé tartozik.  

| Log Analytics Linux VM-bővítmény verziója | Log Analytics-ügynököket csomag verziója | 
|--------------------------------|--------------------------|
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0-as | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
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

Az Azure Security Center automatikusan látja el a Log Analytics-ügynököket, és csatlakoztatja az Azure-előfizetésében az ASC által létrehozott alapértelmezett Log Analytics-munkaterületet. Ha az Azure Security Center használ, ne futtassa a jelen dokumentumban leírt lépések segítségével. Ezzel felülírja a konfigurált munkaterületével, és megszakítja a kapcsolatot az Azure Security Centerrel.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Log Analytics-ügynököket-bővítmény linuxhoz megköveteli, hogy a céloldali virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics-ügynök bővítmény sémáját jeleníti meg. A bővítmény telepítéséhez a munkaterület-Azonosítót és a cél a Log Analytics-munkaterület; munkaterület kulcsa Ezek az értékek lehetnek [található a Log Analytics-munkaterület](../../log-analytics/log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) az Azure Portalon. A munkaterület kulcsát kényes adatként kell kezelni, mert azt egy védett beállítás konfigurációjának kell tárolni. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen. Vegye figyelembe, hogy **munkaterület azonosítója** és **workspaceKey** kis-és nagybetűket.

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
>A fenti séma azt feltételezi, hogy ez a sablon gyökér szintű kerülnek. Ha a virtuális gép típusú erőforrást a sablonban található helyezi azt a `type` és `name` tulajdonságokat kell módosítani, amint [kicsit lentebb](#template-deployment).
>

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

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Sablonok ideálisak, egy vagy több Log Analytics üzembe helyezése például üzembe helyezést követő konfiguráció igénylő virtuális gépek üzembe helyezésekor. Egy mintául szolgáló Resource Manager-sablon, amely tartalmazza a Log Analytics ügynök Virtuálisgép-bővítmény találhatók a [Azure gyors üzembe helyezési katalógus](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Virtuálisgép-bővítmények JSON konfigurációjának a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON konfigurációs elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

Az alábbi példa azt feltételezi, hogy a Virtuálisgép-bővítményt a virtuális gép típusú erőforrást van beágyazva. A bővítmény erőforrás beágyazása, ha a JSON az kerül a `"resources": []` objektum a virtuális gép.

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

Az Azure CLI segítségével a Log Analytics ügynök Virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez. Cserélje le a *munkaterület azonosítója* és *workspaceKey* azokkal a Log Analytics-munkaterület. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.7 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
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
| 9 | Túl korán nevű engedélyezése | [Az Azure Linux-ügynök frissítése](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) elérhető legújabb verzióra. |
| 10 | Virtuális gép már csatlakoztatva van egy Log Analytics-munkaterület | A virtuális gép csatlakozik a bővítmény sémában megadott munkaterület, stopOnMultipleConnections "false" értékűre a nyilvános beállításait, vagy távolítsa el ezt a tulajdonságot. Ez a virtuális gép egyes munkaterületekre vonatkozó való csatlakozás után díjat számítjuk fel. |
| 11 | A bővítmény megadott Érvénytelen konfiguráció | Kövesse az előző példákból való üzembe helyezéshez szükséges összes tulajdonság értéket állítsa be. |
| 17 | Log Analytics-csomag telepítési hiba | 
| 19 | OMI csomag telepítési hiba | 
| 20 | Az SCX-csomag telepítési hiba |
| 51 | Ezt a bővítményt a virtuális gép operációs rendszer nem támogatott | |
| 55 | Nem lehet kapcsolódni a Log Analytics szolgáltatással vagy a szükséges csomagokat a hiányzó vagy dpkg Csomagkezelő zárolva van| Ellenőrizze, hogy a rendszer rendelkezik Internet-hozzáféréssel, vagy az, hogy érvényes HTTP proxy lett megadva. Ezenkívül ellenőrizze a munkaterület azonosítójának helyességét, és a curl és tar segédprogram telepítve van. |

További hibaelhárítási információk találhatók a [Log Analytics –--Linux-ügynök hibaelhárítási útmutatója](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
