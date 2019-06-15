---
title: Az Azure-tűzfal nyilvános előzetes verziójának engedélyezése
description: Az Azure PowerShell használatával az Azure-tűzfal nyilvános előzetes verziójának engedélyezése
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: fe1b8f9d56b0f4faa0baa25463b2aa29a59715cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193056"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Az Azure-tűzfal nyilvános előzetes verziójának engedélyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Engedélyezés az Azure PowerShell-lel

Az Azure-tűzfal nyilvános előzetes verziójának engedélyezése, használja a következő Azure PowerShell-parancsokat:

```PowerShell
Register-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

A szolgáltatás regisztráció befejezése akár 30 percet vesz igénybe. A regisztrációs állapot a következő Azure PowerShell-parancsok futtatásával ellenőrizheti:

```powershell

Get-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
A regisztráció befejezése után futtassa a következő parancsot:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure-tűzfal](tutorial-firewall-deploy-portal.md)

