---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9e4f2e355240ba8682cbe9f86f2be94e7dd0d92d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70032348"
---
## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ha egyszerű szolgáltatást szeretne létrehozni a tároló beállításjegyzékéhez való hozzáféréssel, futtassa a következő parancsfájlt az [Azure Cloud Shellben](../articles/cloud-shell/overview.md) vagy az Azure CLI helyi [telepítésében.](/cli/azure/install-azure-cli) A parancsfájl a Bash rendszerhéjhoz van formázva.

A parancsfájl futtatása `ACR_NAME` előtt frissítse a változót a tároló beállításjegyzékének nevével. Az `SERVICE_PRINCIPAL_NAME` értéknek egyedinek kell lennie az Azure Active Directory-bérlőn belül. Ha " "`'http://acr-service-principal' already exists.`hibaüzenetet kap, adjon meg egy másik nevet a szolgáltatásnévnek.

Az `--role` [azad sp create-for-rbac][az-ad-sp-create-for-rbac] parancs ban módosíthatja az értéket, ha különböző engedélyeket szeretne megadni. A szerepkörök teljes listáját az [ACR-szerepkörök és engedélyek című témakörben tetszetős ekben lehet látni.](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md)

A parancsfájl futtatása után vegye figyelembe az egyszerű szolgáltatás **azonosítóját** és **jelszavát.** Miután rendelkezik a hitelesítő adatokkal, konfigurálhatja az alkalmazások at és szolgáltatásokat, hogy hitelesítse magát a tároló rendszerleíró adatbázisában, mint a szolgáltatás névjegyzék.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Meglévő egyszerű szolgáltatás használata

Ahhoz, hogy rendszerleíró adatbázis-hozzáférést adjon egy meglévő egyszerű szolgáltatásnak, új szerepkört kell hozzárendelnie az egyszerű szolgáltatáshoz. Az új szolgáltatásnév létrehozásához is többek között lekéréses, leküldéses és lekéréses és tulajdonosi hozzáférést is biztosíthat.

A következő parancsfájl az [az szerepkör-hozzárendelés létrehozási][az-role-assignment-create] parancssegítségével *lekéréses* engedélyeket ad a `SERVICE_PRINCIPAL_ID` változóban megadott egyszerű szolgáltatásnak. Állítsa `--role` be az értéket, ha más szintű hozzáférést szeretne adni.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
