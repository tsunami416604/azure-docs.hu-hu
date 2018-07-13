---
title: Az Azure-tűzfal nyilvános előzetes verziójának engedélyezése
description: Az Azure PowerShell használatával az Azure-tűzfal nyilvános előzetes verziójának engedélyezése
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 263b16a419b5ff20a9b6d62860385f92c2a18f9c
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991314"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Az Azure-tűzfal nyilvános előzetes verziójának engedélyezése

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Engedélyezze az Azure PowerShell-lel

Az Azure-tűzfal nyilvános előzetes verziójának engedélyezése, használja a következő Azure PowerShell-parancsokat:

```PowerShell
Register-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

A szolgáltatás regisztráció befejezése akár 30 percet vesz igénybe. A regisztrációs állapot a következő Azure PowerShell-parancsok futtatásával ellenőrizheti:

```PowerShell

Get-AzureRmProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzureRmProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
A regisztráció befejezése után futtassa a következő parancsot:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Központi telepítése és konfigurálása az Azure portal segítségével Azure-tűzfal](tutorial-firewall-deploy-portal.md)

