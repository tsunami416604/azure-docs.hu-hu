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
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465054"
---
A következő CLI-parancs letiltja a Storage-fiók nyilvános végpontján lévő összes forgalmat. Vegye figyelembe, hogy a parancshoz a `-bypass` paraméter van beállítva `AzureServices` . Ez lehetővé teszi, hogy a megbízható, első féltől származó szolgáltatások, például a Azure File Sync hozzáférhessenek a Storage-fiókhoz a nyilvános végponton keresztül.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```