---
title: Az Azure Application Gateway tanúsítvány megújítása
description: Megtudhatja, hogyan egy application gateway-figyelő társított tanúsítvány megújításához.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
origin.date: 08/15/2018
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: 90200f7be6c71346441922365fc4439111dd8701
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133701"
---
# <a name="renew-application-gateway-certificates"></a>Az Application Gateway-tanúsítványok megújítása

Bármikor szüksége lesz a tanúsítványok megújítása, ha az application gateway SSL-titkosításra konfigurált.

Egy figyelő vagy az Azure portal használatával, az Azure PowerShell vagy Azure CLI-vel társított tanúsítvány újíthatja meg:

## <a name="azure-portal"></a>Azure Portal

A portálról figyelő tanúsítvány megújításához, keresse meg az application gateway figyelők. Kattintson a figyelő, amely rendelkezik egy tanúsítványt, amelyet meg kell újítani, és kattintson a **megújítása vagy szerkesztése a kiválasztott tanúsítvány**.

![Tanúsítvány megújítása](./media/renew-certificate/ssl-cert.png)

Az új PFX-tanúsítvány feltöltéséhez, adjon meg egy nevet, írja be a jelszót, és kattintson **mentése**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure PowerShell-lel tanúsítvány megújításához, használja a következő szkriptet:

```azurepowershell
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli
az network application-gateway ssl-cert update `
  -n "<CertName>" `
  --gateway-name "<AppGatewayName>" `
  -g "ResourceGroupName>" `
  --cert-file <PathToCerFile> `
  --cert-password "<password>"
```

## <a name="next-steps"></a>További lépések

SSL-kiürítés konfigurálása az Azure Application Gateway szolgáltatással kapcsolatban lásd: [SSL kiürítési konfigurálása](application-gateway-ssl-portal.md)

<!-- Update_Description: code update -->