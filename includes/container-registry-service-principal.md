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
ms.openlocfilehash: 69951693f9d3bacb556453aba954620815884d43
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56334052"
---
## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Egyszerű szolgáltatás létrehozása a tárolóregisztrációs adatbázis-hozzáféréssel rendelkező, futtassa a következő szkriptet a [Azure Cloud Shell](../articles/cloud-shell/overview.md) vagy helyi telepítése az [Azure CLI](/cli/azure/install-azure-cli). A szkript a Bash rendszerhéj van formázva.

A szkript futtatása előtt frissítse a `ACR_NAME` változó a tárolóregisztrációs adatbázis nevére. A `SERVICE_PRINCIPAL_NAME` érték az Azure Active Directory-bérlőn belül egyedinek kell lennie. Ha megjelenik egy "`'http://acr-service-principal' already exists.`" hiba történt, adja meg az egyszerű szolgáltatás egy másik nevet.

Igény szerint módosíthatja a `--role` értékét a [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] parancsot, hogy szeretné-e másik engedélyeket. Szerepkörök teljes listáját lásd: [ACR szerepköröket és engedélyeket](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

A szkript futtatása után jegyezze fel a szolgáltatásnév **azonosító** és **jelszó**. Ha a hitelesítő adatait, konfigurálhatja úgy az alkalmazások és szolgáltatások a tárolóregisztrációs adatbázisba, az egyszerű szolgáltatás hitelesítéséhez.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Meglévő egyszerű szolgáltatás használata

Beállításjegyzék hozzáférést egy létező egyszerű szolgáltatás, új szerepkört kell rendelni a szolgáltatásnévhez. Csakúgy, mint létrehozni egy új szolgáltatásnevet, biztosíthat lekéréses, leküldéses és lekéréses és tulajdonosi hozzáféréssel, többek között.

Az alábbi szkript a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot adhat *lekéréses* szolgáltatásnév engedélyeket ad meg a `SERVICE_PRINCIPAL_ID` változó. Módosítsa a `--role` értékét, ha szeretne egy eltérő szintjét hozzáférés biztosítása.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
