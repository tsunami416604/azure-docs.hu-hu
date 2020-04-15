---
title: Azure Application Gateway tanúsítvány megújítása
description: Ismerje meg, hogyan újíthatja meg az alkalmazásátjáró-figyelőhöz társított tanúsítványt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 4c0c0ab84e60335f58ac075459b95cfabb9135ac
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311960"
---
# <a name="renew-application-gateway-certificates"></a>Alkalmazásátjáró-tanúsítványok megújítása

Egy bizonyos ponton meg kell újítania a tanúsítványokat, ha az alkalmazásátjárót TLS/SSL titkosításra konfigurálta.

Megújíthatja a figyelőhöz társított tanúsítványt az Azure Portalon, az Azure PowerShellen vagy az Azure CLI-n keresztül:

## <a name="azure-portal"></a>Azure Portal

Ha meg szeretne újítani egy figyelő tanúsítványt a portálról, keresse meg az alkalmazásátjáró-figyelők. Kattintson arra a figyelőre, aki rendelkezik megújítani kívánt tanúsítvánnyal, majd kattintson **a Kijelölt tanúsítvány megújítása vagy szerkesztése parancsra.**

![Tanúsítvány megújítása](media/renew-certificate/ssl-cert.png)

Töltse fel az új PFX-tanúsítványt, adjon neki nevet, írja be a jelszót, majd kattintson a **Mentés gombra.**

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A tanúsítvány Azure PowerShell használatával történő megújításához használja a következő parancsfájlt:

```azurepowershell-interactive
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

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>További lépések

A TLS-kiszervezés azure-alkalmazásátjáróval történő konfigurálásáról a [TLS-kiszervezés konfigurálása című](application-gateway-ssl-portal.md) témakörben olvashat.
