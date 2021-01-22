---
title: Azure Firewall aktív FTP-támogatás
description: Alapértelmezés szerint az aktív FTP le van tiltva Azure Firewallon. A PowerShell, a CLI és az ARM sablon használatával engedélyezheti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: victorh
ms.openlocfilehash: 2ff61d06885c182454c99ee7e982a3a1a1f5013c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690459"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall aktív FTP-támogatás

Az aktív FTP szolgáltatással az FTP-kiszolgáló kezdeményezi az adatkapcsolatot a kijelölt FTP-ügyfél adatportjával. Az ügyféloldali hálózat tűzfala általában blokkolja a belső ügyfél portján kívüli kapcsolódási kéréseket. További információ: [aktív FTP és passzív FTP, végleges magyarázat](https://slacksite.com/other/ftp.html).

Alapértelmezés szerint az aktív FTP-támogatás le van tiltva a Azure Firewallon az FTP-parancs használatával történő FTP-visszafordulási támadásokkal szembeni védelem érdekében `PORT` . A Azure PowerShell, az Azure CLI vagy egy Azure ARM-sablon használatával történő üzembe helyezéskor azonban engedélyezheti az aktív FTP-t.

## <a name="azure-powershell"></a>Azure PowerShell

A Azure PowerShell használatával történő üzembe helyezéshez használja a `AllowActiveFTP` paramétert. További információ: [tűzfal létrehozása az Active FTP engedélyezésével](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-).

## <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával történő üzembe helyezéshez használja a `--allow-active-ftp` paramétert. További információ: [az Network Firewall Create](/cli/azure/ext/azure-firewall/network/firewall?view=azure-cli-latest#ext_azure_firewall_az_network_firewall_create-optional-parameters). 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager- (ARM-) sablon

ARM-sablon használatával történő üzembe helyezéshez használja a következő `AdditionalProperties` mezőt:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
További információ: [Microsoft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="next-steps"></a>Következő lépések

A Azure Firewall telepítésének megismeréséhez tekintse meg a [Azure Firewall telepítése és konfigurálása a Azure PowerShell használatával](deploy-ps.md)című témakört.