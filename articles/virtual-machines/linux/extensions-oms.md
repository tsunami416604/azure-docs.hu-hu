---
title: "Virtuálisgép-bővítmény OMS Azure Linux |} Microsoft Docs"
description: "A Linux virtuális gépet egy virtuálisgép-bővítmény használatával az OMS-ügynök telepítése."
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 138fc8c98ea6f409b28407b20851c96ecc618b09
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="oms-virtual-machine-extension-for-linux"></a>Linux OMS virtuálisgép-bővítmény

## <a name="overview"></a>Áttekintés

Az Operations Management Suite (OMS) figyelési riasztási és riasztási szervizelési képességeket biztosít a felhő között és a helyszíni eszközök. Az OMS-ügynököt Linux virtuálisgép-bővítmény közzétett és a Microsoft támogatja. A bővítmény, az OMS-ügynököt telepít Azure virtuális gépeken, és regisztrálja a virtuális gépek be egy meglévő OMS-munkaterület. Ez a dokumentum részletesen a támogatott platformok, a konfigurációk és a Linux virtuálisgép-bővítmény OMS vonatkozó telepítési lehetőségeket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az OMS-ügynököt bővítmény futtatható a Linux terjesztéseket ellen.

| Disztribúció | Verzió |
|---|---|
| CentOS Linux | 5, 6 és 7 |
| Oracle Linux | 5, 6 és 7 |
| Red Hat Enterprise Linux Server | 5, 6, 7 |
| Debian GNU/Linux | 8, 6, 7 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 és 12 |

### <a name="internet-connectivity"></a>Internetkapcsolat

Az OMS-ügynököt bővítmény Linux megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>A séma kiterjesztése

A következő JSON jeleníti meg az OMS-ügynököt bővítmény sémáját. A bővítmény szükséges a munkaterület azonosítója és a cél OMS-munkaterület; kulcsát Ezeket az értékeket az OMS-portálon található. A munkaterület-kulcs bizalmas adatokat kell kezelni, mert azt egy védett beállítás konfigurációban kell tárolni. Az Azure Virtuálisgép-bővítmény védett beállítás adatokat titkosít, és csak visszafejti a cél virtuális gépen. Vegye figyelembe, hogy **workspaceId** és **workspaceKey** -és nagybetűk.

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
    "typeHandlerVersion": "1.4",
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

| Név | Érték / – példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Közzétevő | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| workspaceId (például) | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (például) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ == |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Sablonok bevezetése az OMS-be például a feladás egy vagy több központi telepítési beállításokra van szükség egy vagy több virtuális gépek telepítése során ideális. Az OMS-ügynök Virtuálisgép-bővítmény tartalmazó minta Resource Manager sablon megtalálható a [Azure Quick Start gyűjtemény](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

A JSON-konfiguráció a virtuálisgép-bővítmény a virtuálisgép-erőforrás ágyazhatók egymásba, vagy elhelyezve, a gyökér vagy a legfelső szintű erőforrás-kezelő JSON-sablon. A JSON-konfiguráció elhelyezési erőforrás nevét és típusát. értéke befolyásolja. További információkért lásd: [nevét és típusát gyermekerőforrásait beállítása](../../azure-resource-manager/resource-manager-template-child-resource.md). 

Az alábbi példa azt feltételezi, hogy a virtuálisgép-erőforrást az OMS-bővítmény van beágyazva. A bővítmény erőforrás beágyazási, amikor bekerül a JSON a `"resources": []` objektum a virtuális gép.

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
    "typeHandlerVersion": "1.4",
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
    "typeHandlerVersion": "1.4",
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

Az Azure CLI segítségével az OMS-ügynök Virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre. Cserélje le az OMS-kulcsot és az OMS-azonosító az OMS-munkaterület szerintiek. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
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

| Hibakód: | Jelentése | Lehetséges művelet |
| :---: | --- | --- |
| 10 | Virtuális gép már csatlakoztatva van egy OMS-munkaterület | A virtuális gép csatlakozik a bővítmény sémában megadott munkaterület, stopOnMultipleConnections értéke HAMIS, a nyilvános beállításai, vagy távolítsa el ezt a tulajdonságot. Ez a virtuális gép lekérdezi számlázva után az egyes munkaterületeken van csatlakoztatva. |
| 11 | A bővítmény megadott Érvénytelen konfiguráció | Kövesse a fenti példákban beállítása a telepítéshez szükséges minden tulajdonság értékével. |
| 12 | A dpkg package manager zárolva van | Győződjön meg arról, hogy minden dpkg frissítési műveletek a számítógépen végzett, és próbálkozzon újra. |
| 20 | Túl korán nevű engedélyezése | [Az Azure Linux ügynök frissítése](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) az elérhető legújabb verzióra. |
| 51 | Ezt a bővítményt a virtuális gép operációs rendszer nem támogatott | |
| 55 | Nem lehet kapcsolódni a Microsoft Operations Management Suite szolgáltatással | Ellenőrizze, hogy a rendszernek van-e Internet-hozzáféréssel, vagy adtak meg, hogy érvényes HTTP-proxy. Ezenkívül ellenőrizze a helyességét a munkaterület azonosítóját. |

További hibaelhárítási információért található meg a [OMS-ügynök-az-Linux hibaelhárítási útmutatója](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#).

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/en-us/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/en-us/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/en-us/support/faq/).
