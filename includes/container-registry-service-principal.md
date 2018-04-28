---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy egyszerű szolgáltatást a tároló beállításjegyzék eléréséhez, az alábbi parancsfájlt is használhat. Frissítés a `ACR_NAME` változó a tárolót a rendszerleíró adatbázis nevét, és szükség esetén a `--role` értéket a [az ad sp létrehozása-az-rbac] [ az-ad-sp-create-for-rbac] parancs különböző engedélyeket adni. Rendelkeznie kell a [Azure CLI](/cli/azure/install-azure-cli) telepítés után használja ezt a parancsfájlt.

A parancsfájl futtatása után tekintse meg a szolgáltatás egyszerű **azonosító** és **jelszó**. Miután a hitelesítő adatait, konfigurálhatja az alkalmazások és szolgáltatások, a tároló beállításjegyzék, mint az egyszerű szolgáltatás felé történő hitelesítésre.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Egy meglévő egyszerű szolgáltatás használata

Beállításjegyzék hozzáférést biztosít egy meglévő egyszerű szolgáltatást, egy új szerepkört kell rendelnie az egyszerű szolgáltatásnév. Csakúgy, mint egy új szolgáltatás létrehozása egyszerű, lekéréses leküldéses és lekéréses és tulajdonosi hozzáférés megadásához.

Az alábbi parancsfájl használ a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] megadását parancs *lekéréses* szolgáltatásnevet engedélyeket ad meg a `SERVICE_PRINCIPAL_ID` változó. Módosítsa a `--role` értéket, ha egy eltérő szintű hozzáférési biztosítani szeretné.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
