---
title: Azure Key Vault ügyféloldali kódtárai | Microsoft Docs
description: Azure Key Vault ügyféloldali kódtárai
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 9cc8c1e9406f6b92a445cd52a4ad26e71d83ef5a
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264088"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault ügyféloldali kódtárai

A Azure Key Vault-hez készült ügyféloldali kódtárak különböző nyelveken, többek között a .NET, a Python, a Java és a JavaScript használatával is lehetővé teszik a programozott hozzáférést a Key Vault funkciókhoz.

## <a name="client-libraries-per-language-and-object"></a>Ügyféloldali kódtárak nyelv és objektum alapján

Minden SDK külön ügyféloldali kódtárakat tartalmaz a titkokhoz, kulcsokhoz és tanúsítványokhoz az alábbi táblázatban.

| Nyelv | Titkos kulcsok | Kulcsok | Tanúsítványok |
|--|--|--|--|
| .NET | - [API-referenciák](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [QuickStart](../secrets/quick-create-net.md) | - [API-referenciák](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API-referenciák](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet-csomag](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [API-referenciák](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [QuickStart](../secrets/quick-create-python.md) |- [API-referenciák](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-keys/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [QuickStart](../keys/quick-create-python.md) | - [API-referenciák](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [PyPi-csomag](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [QuickStart](../certificates/quick-create-python.md) |
| Java | - [API-referenciák](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [QuickStart](../secrets/quick-create-java.md) |- [API-referenciák](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API-referenciák](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [API-referenciák](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [NPM-csomag](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [QuickStart](../secrets/quick-create-node.md) |- [API-referenciák](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [NPM-csomag](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API-referenciák](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [NPM-csomag](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>További lépések

- Lásd: [Azure Key Vault fejlesztői útmutató](developers-guide.md)
- További információ a [Azure Key Vault felügyelt identitásáról](managed-identity.md)