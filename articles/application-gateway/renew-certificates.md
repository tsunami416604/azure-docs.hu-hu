---
title: Azure Application Gateway tanúsítvány megújítása
description: 'Útmutató: az alkalmazás átjáró figyelő társított tanúsítvány megújítása.'
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="renew-application-gateway-certificates"></a>Alkalmazásátjáró tanúsítványainak megújítása

Bármikor szüksége lesz a tanúsítványok megújítása, ha az alkalmazás átjáró SSL-titkosításra konfigurált.

Az Azure-portálon vagy az Azure PowerShell figyelő társított tanúsítvány megújítása is:

## <a name="azure-portal"></a>Azure Portal

A portálról figyelő tanúsítvány megújítása, keresse meg az alkalmazás átjáró figyelők. A figyelő, amely rendelkezik egy tanúsítványt, amelyet a megújítani, majd kattintson **megújítási vagy szerkesztése a kiválasztott tanúsítvány**.

![Tanúsítvány megújítása](media/renew-certificate/ssl-cert.png)

Az új PFX-tanúsítvány feltöltése, adjon neki egy nevet, írja be a jelszót, és kattintson **mentése**.

## <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell a tanúsítvány megújításához, használja a következő parancsmagot:

```PowerShell
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

## <a name="next-steps"></a>További lépések

SSL-Feladatkiszervezést konfigurálása Azure Application Gateway kapcsolatban [SSL-kiszervezés konfigurálása](application-gateway-ssl-portal.md)
