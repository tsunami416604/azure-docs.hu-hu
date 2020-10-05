---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482128"
---
1. Győződjön meg arról, hogy rendelkezik [aktív előfizetéssel rendelkező Azure-fiókkal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Telepítse a [Python 2.7 + vagy a 3.5.3 +](https://www.python.org/downloads)programot.

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli)-t.

1. Kövesse a [hitelesítés konfigurálása helyi fejlesztéshez](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication)című útmutatót, amellyel létrehoz egy helyi szolgáltatásnevet, és elérhetővé teheti a Python Azure Key Vault-ügyfél számára környezeti változók használatával. 

    Ha a kódot közvetlenül az Azure-on futtatja, akkor nem kell külön egyszerű szolgáltatást használni, ha az alkalmazás felügyelt identitást használ.

1. Egy terminálon vagy parancssorban hozzon létre egy megfelelő Project-mappát, majd hozzon létre és aktiváljan egy Python virtuális környezetet a [Python virtuális környezetek használata](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) című cikkben leírtak szerint.

1. Az Azure Active Directory Identity Library telepítése:

    ```terminal
    pip install azure.identity
    ```
