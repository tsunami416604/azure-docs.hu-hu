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
ms.openlocfilehash: 908d42bbecccf4a7c68a6cfde0c8cd960299df89
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213062"
---
## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Egyszerű szolgáltatás létrehozása a tárolóregisztrációs adatbázis-hozzáféréssel rendelkező, futtassa a következő szkriptet a [Azure Cloud Shell](../articles/cloud-shell/overview.md) vagy helyi telepítése az [Azure CLI](/cli/azure/install-azure-cli). A szkript a Bash rendszerhéj van formázva.

A szkript futtatása előtt frissítse a `ACR_NAME` változó a tárolóregisztrációs adatbázis nevére. A `SERVICE_PRINCIPAL_NAME` érték az Azure Active Directory-bérlőn belül egyedinek kell lennie. Ha megjelenik egy "`'http://acr-service-principal' already exists.`" hiba történt, adja meg az egyszerű szolgáltatás egy másik nevet.

Igény szerint módosíthatja a `--role` érték a [az ad sp create-for-rbac][/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac] parancsban, ha különböző engedélyeket szeretne. Szerepkörök teljes listáját lásd: [ACR szerepköröket és engedélyeket](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

A szkript futtatása után jegyezze fel a szolgáltatásnév **azonosító** és **jelszó**. Ha a hitelesítő adatait, konfigurálhatja úgy az alkalmazások és szolgáltatások a tárolóregisztrációs adatbázisba, az egyszerű szolgáltatás hitelesítéséhez.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Meglévő egyszerű szolgáltatás használata

Beállításjegyzék hozzáférést egy létező egyszerű szolgáltatás, új szerepkört kell rendelni a szolgáltatásnévhez. Csakúgy, mint létrehozni egy új szolgáltatásnevet, biztosíthat lekéréses, leküldéses és lekéréses és tulajdonosi hozzáféréssel, többek között.

A következő szkriptet a [az szerepkör-hozzárendelés create][/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create] parancsot használja, biztosítania *lekéréses* szolgáltatásnév engedélyeket ad meg a `SERVICE_PRINCIPAL_ID`változó. Módosítsa a `--role` értékét, ha szeretne egy eltérő szintjét hozzáférés biztosítása.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
