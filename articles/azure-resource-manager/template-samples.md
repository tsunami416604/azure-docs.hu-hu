---
title: Azure Resource Manager sablon mintái
description: A felügyeleti szolgáltatások, például a szerepkörök és a zárolások üzembe helyezéséhez Azure Resource Manager sablon mintákat.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 11/16/2018
ms.author: tomfitz
ms.openlocfilehash: e342507b584a405637fc6728dfcd6e49199a154f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390179"
---
# <a name="azure-resource-manager-templates-for-management-features"></a>A felügyeleti funkciók Azure Resource Manager sablonjai

A következő táblázat a Resource Manager által biztosított szolgáltatások Azure Resource Manager sablonjaira mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Szerepkör-hozzárendelések**||
| [Erőforrás-csoport szerepkörének társítása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)| Beépített szerepkört rendel egy felhasználóhoz egy meglévő erőforráscsoporthoz. |
| [Szerepkör társítása a meglévő virtuális géphez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)| Beépített szerepkört rendel egy felhasználóhoz egy meglévő virtuális géphez. |
| [Szerepkör társítása több virtuális géphez](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)| Beépített szerepköröket rendel a felhasználókhoz több virtuális géphez. |
| [Szerepkör társítása az Azure-előfizetéshez](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/subscription-role-assigment)| Szerepkört rendel egy felhasználóhoz egy Azure-előfizetéshez. |
|**Szerepkör-definíció**||
| [Egyéni szerepkör-definíció létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-role-def)| Új szerepkör-definíciót hoz létre egy Azure-előfizetésben. |
|**Erőforrás-zárolás**||
| [Erőforráscsoport zárolása](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)| Létrehoz egy erőforráscsoportot, és egy **DoNotDelete** -zárolást alkalmaz az erőforráscsoporthoz. Hozzárendeli a közreműködői szerepkört egy felhasználóhoz. |
| | |
