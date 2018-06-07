---
title: Virtuálisgép-bővítmény OMS Azure Linux |} Microsoft Docs
description: A Linux virtuális gépet egy virtuálisgép-bővítmény használatával az OMS-ügynök telepítése.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/21/2018
ms.author: danis
ms.openlocfilehash: f0d8224e5578a5ae46245e6c70792e962a44c933
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652855"
---
# <a name="oms-virtual-machine-extension-for-linux"></a>Linux OMS virtuálisgép-bővítmény

## <a name="overview"></a>Áttekintés

A Naplóelemzési figyelési riasztási és riasztási szervizelési képességeket biztosít a felhő között és a helyszíni eszközök. Az OMS-ügynököt Linux virtuálisgép-bővítmény közzétett és a Microsoft támogatja. A bővítmény, az OMS-ügynököt telepít Azure virtuális gépeken, és regisztrálja a virtuális gépek be egy meglévő Naplóelemzési munkaterület. Ez a dokumentum részletesen a támogatott platformok, a konfigurációk és a Linux virtuálisgép-bővítmény OMS vonatkozó telepítési lehetőségeket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az OMS-ügynököt bővítmény futtatható a Linux terjesztéseket ellen.

| Disztribúció | Verzió |
|---|---|
| CentOS Linux | 5, 6 és 7 (x86/x64) |
| Oracle Linux | 5, 6 és 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 5, 6, 7 (x86/x64) |
| Debian GNU/Linux | 6, 7, 8 és 9 (x86/x64) |
| Ubuntu | 12.04 LTS, 14.04 LTS 16.04 LTS (x86/x64) |
| SUSE Linux Enterprise Server | 11 és 12 (x86/x64) |

### <a name="agent-and-vm-extension-version"></a>Ügynök és Virtuálisgép-bővítmény verzió
A következő táblázat az OMS-VM verziójának leképezéseket bővítményt, és az OMS-ügynököt csomagot minden egyes kiadásához. A kibocsátási megjegyzések az OMS-ügynök csomag verziószámát mutató hivatkozás szerepel. Kibocsátási megjegyzések a hibajavításokat és egy adott ügynök kiadásban elérhető új szolgáltatások részleteket tartalmazza.  

| OMS Linux virtuális gép kiterjesztés verziója | OMS-ügynököt csomag verziója | 
|--------------------------------|--------------------------|
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

Az Azure Security Center automatikusan látja el az OMS-ügynököt, és csatlakozik a egy alapértelmezett Naplóelemzési munkaterület ASC hozta létre az Azure-előfizetésben. Ha az Azure Security Center használ, ne futtassa végig a lépéseken, ebben a dokumentumban. Ezzel felülírja a konfigurált munkaterületet, és megszakítja a kapcsolatot az Azure Security Center.

### <a name="internet-connectivity"></a>Internetkapcsolat

Az OMS-ügynököt bővítmény Linux megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON jeleníti meg az OMS-ügynököt bővítmény sémáját. A bővítmény szükséges a munkaterület azonosítója és a cél a Naplóelemzési munkaterület; kulcsát Ezek az értékek lehetnek [a Naplóelemzési munkaterület található](../../log-analytics/log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) az Azure portálon. A munkaterület-kulcs bizalmas adatokat kell kezelni, mert azt egy védett beállítás konfigurációban kell tárolni. Az Azure Virtuálisgép-bővítmény védett beállítás adatokat titkosít, és csak visszafejti a cél virtuális gépen. Vegye figyelembe, hogy **workspaceId** és **workspaceKey** -és nagybetűk.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.6",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.6 |
| workspaceId (például) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (például) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Sablonok is épp ezért tökéletes választás, ha egy vagy több feladás egy vagy több konfigurációs szolgáltatáshoz bevezetési például igénylő virtuális gépek telepítése. Az OMS-ügynök Virtuálisgép-bővítmény tartalmazó minta Resource Manager sablon megtalálható a [Azure Quick Start gyűjtemény](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A JSON-konfiguráció a virtuálisgép-bővítmény a virtuálisgép-erőforrás ágyazhatók egymásba, vagy elhelyezve, a gyökér vagy a legfelső szintű erőforrás-kezelő JSON-sablon. A JSON-konfiguráció elhelyezési erőforrás nevét és típusát. értéke befolyásolja. További információkért lásd: [nevét és típusát gyermekerőforrásait beállítása](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

Az alábbi példa azt feltételezi, hogy a virtuális gépi bővítményt a virtuálisgép-erőforrás van beágyazva. A bővítmény erőforrás beágyazási, amikor bekerül a JSON a `"resources": []` objektum a virtuális gép.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.6",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

A bővítmény JSON elhelyezésekor a sablon gyökerében, az erőforrás nevét a szülő virtuális gép egy hivatkozást tartalmaz, és a típus beágyazott konfigurációját tükrözi.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.6",
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

Az Azure CLI segítségével az OMS-ügynök Virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre. Cserélje le a *workspaceId* és *workspaceKey* származó Naplóelemzési munkaterületet. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.6 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure parancssori felület használatával. A megadott virtuális gépek bővítmények központi telepítési állapotának megtekintéséhez a következő parancsot az Azure parancssori felület használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A következő fájl kerül a bővítmény végrehajtás kimenetének:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Hibakódok és azok jelentését

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 9 | Túl korán nevű engedélyezése | [Az Azure Linux ügynök frissítése](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) az elérhető legújabb verzióra. |
| 10 | Virtuális gép már csatlakoztatva van a Naplóelemzési munkaterület | A virtuális gép csatlakozik a bővítmény sémában megadott munkaterület, stopOnMultipleConnections értéke HAMIS, a nyilvános beállításai, vagy távolítsa el ezt a tulajdonságot. Ez a virtuális gép lekérdezi számlázva után az egyes munkaterületeken van csatlakoztatva. |
| 11 | A bővítmény megadott Érvénytelen konfiguráció | Kövesse a fenti példákban beállítása a telepítéshez szükséges minden tulajdonság értékével. |
| 12 | A dpkg package manager zárolva van | Győződjön meg arról, hogy minden dpkg frissítési műveletek a számítógépen végzett, és próbálkozzon újra. |
| 17 | OMS csomag telepítési hiba | 
| 19 | OMI csomag telepítési hiba | 
| 20 | Az SCX csomag telepítési hiba |
| 51 | Ezt a bővítményt a virtuális gép operációs rendszer nem támogatott | |
| 55 | Nem lehet kapcsolódni a Microsoft Operations Management Suite szolgáltatással | Ellenőrizze, hogy a rendszernek van-e Internet-hozzáféréssel, vagy adtak meg, hogy érvényes HTTP-proxy. Ezenkívül ellenőrizze a helyességét a munkaterület azonosítóját. |

További hibaelhárítási információért található meg a [OMS-ügynök-az-Linux hibaelhárítási útmutatója](../../log-analytics/log-analytics-azure-vmext-troubleshoot.md).

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).
