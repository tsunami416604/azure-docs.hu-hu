---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01e1f956270ebbc9b9d5d0ebdfdff52875cafedf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000052"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>A környezet előkészítése az Azure CLI-hez

- Használja az [Azure Cloud Shellt](../articles/cloud-shell/quickstart.md) a Bash-környezet használatával.

   [![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com)   
- A CLI-referenciaparancsok futtatásához [telepítheti](/cli/azure/install-azure-cli) az Azure CLI-t is.
   - Ha helyi telepítést használ, jelentkezzen be az Azure CLI-vel az [az login](/cli/azure/reference-index#az-login) parancs futtatásával.  A hitelesítési folyamat befejezéséhez kövesse a terminálon megjelenő lépéseket.  További bejelentkezési lehetőségek megismeréséhez tekintse meg a [Bejelentkezés az Azure CLI használatával](/cli/azure/authenticate-azure-cli) című szakaszt.
  - Ha a rendszer kéri, az első használatkor telepítse az Azure CLI-bővítményeket.  További információ a bővítményekről: [Bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).
  - Futtassa az [az version](/cli/azure/reference-index?#az_version) parancsot a telepített verzió és a függő kódtárak megkereséséhez. A legújabb verzióra az [az upgrade](/cli/azure/reference-index?#az_upgrade) paranccsal frissíthet.