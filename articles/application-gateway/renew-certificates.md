---
title: Azure Application Gateway-tanúsítvány megújítása
description: Útmutató az Application Gateway-figyelőhöz társított tanúsítvány megújításához.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277997"
---
# <a name="renew-application-gateway-certificates"></a>Application Gateway tanúsítványok megújítása

Ekkor meg kell újítania a tanúsítványokat, ha az Application Gateway-t az SSL-titkosításhoz konfigurálta.

A figyelőhöz társított tanúsítvány megújítása a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával történhet:

## <a name="azure-portal"></a>Azure Portal

A figyelő tanúsítványának a portálról való megújításához navigáljon az Application Gateway-figyelőkhöz. Kattintson arra a figyelőre, amelynek meg kell újítania a tanúsítványát, majd kattintson a **megújítás vagy a kijelölt tanúsítvány szerkesztése**elemre.

![Tanúsítvány megújítása](media/renew-certificate/ssl-cert.png)

Töltse fel az új PFX-tanúsítványt, adjon meg egy nevet, írja be a jelszót, majd kattintson a **Save (Mentés**) gombra.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha Azure PowerShell használatával szeretné megújítani a tanúsítványt, használja a következő parancsfájlt:

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
## <a name="azure-cli"></a>Azure parancssori felület (CLI)

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Következő lépések

Az SSL-alapú kiszervezésnek az Azure Application Gateway használatával történő konfigurálásával kapcsolatos további információkért lásd: [SSL-kiszervezés konfigurálása](application-gateway-ssl-portal.md)
