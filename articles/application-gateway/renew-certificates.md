---
title: Azure Application Gateway tanúsítvány megújítása
description: 'Útmutató: az alkalmazás átjáró figyelő társított tanúsítvány megújítása.'
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/18/2018
ms.author: victorh
ms.openlocfilehash: b44a57fe8ebcc985d3ab66ea04936a1558d00863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598266"
---
# <a name="renew-application-gateway-certificates"></a>Alkalmazásátjáró tanúsítványainak megújítása

Bármikor szüksége lesz a tanúsítványok megújítása, ha az alkalmazás átjáró SSL-titkosításra konfigurált.

Egy figyelő vagy az Azure portál használatával, az Azure PowerShell vagy Azure CLI társított tanúsítvány megújítása is:

## <a name="azure-portal"></a>Azure Portal

A portálról figyelő tanúsítvány megújítása, keresse meg az alkalmazás átjáró figyelők. A figyelő, amely rendelkezik egy tanúsítványt, amelyet a megújítani, majd kattintson **megújítási vagy szerkesztése a kiválasztott tanúsítvány**.

![Tanúsítvány megújítása](media/renew-certificate/ssl-cert.png)

Az új PFX-tanúsítvány feltöltése, adjon neki egy nevet, írja be a jelszót, és kattintson **mentése**.

## <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell a tanúsítvány megújításához, használja a következő parancsmagot:

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>További lépések

SSL-Feladatkiszervezést konfigurálása Azure Application Gateway kapcsolatban [SSL-kiszervezés konfigurálása](application-gateway-ssl-portal.md)
