---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a0a9bc29c3e20a025fb2c46a71c2f134c37bee04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465120"
---
A következő PowerShell-parancs letiltja a Storage-fiók nyilvános végpontján lévő összes forgalmat. Vegye figyelembe, hogy a parancshoz a `-Bypass` paraméter van beállítva `AzureServices` . Ez lehetővé teszi, hogy a megbízható, első féltől származó szolgáltatások, például a Azure File Sync hozzáférhessenek a Storage-fiókhoz a nyilvános végponton keresztül.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```