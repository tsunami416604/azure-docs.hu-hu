---
title: RBAC-szerepkörök és engedélyek
description: Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az identitás- és hozzáférés-kezelés (IAM) használatával részletes engedélyeket biztosítaz Azure-tároló beállításjegyzékében lévő erőforrásokhoz.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74893484"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry szerepkörök és engedélyek

Az Azure Container Registry szolgáltatás támogatja a [beépített Azure-szerepkörök,](../role-based-access-control/built-in-roles.md) amelyek különböző szintű engedélyeket egy Azure-tároló beállításjegyzék. Az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/index.yml) (RBAC) használatával adott engedélyeket rendelhet a felhasználókhoz, egyszerű szolgáltatástagokhoz vagy más identitásokhoz, amelyeknek egy beállításjegyzékkel kell együttműködniük. 

| Szerepkör/engedély       | [Access erőforrás-kezelő](#access-resource-manager) | [Beállításjegyzék létrehozása/törlése](#create-and-delete-registry) | [Lenyomáskép](#push-image) | [Kép lehúzása](#pull-image) | [Képadatok törlése](#delete-image-data) | [Házirendek módosítása](#change-policies) |   [Képek aláírása](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Tulajdonos | X | X | X | X | X | X |  |  
| Közreműködő | X | X | X |  X | X | X |  |  
| Olvasó | X |  |  | X |  |  |  |
| AcrPush között |  |  | X | X | |  |  |  
| AcrPull között |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Felhasználók és szolgáltatások megkülönböztetése

Az engedélyek alkalmazásakor ajánlott az, hogy egy személy vagy szolgáltatás számára a lehető legkorlátozottabb engedélyeket adja meg egy feladat elvégzéséhez. Az alábbi engedélykészletek olyan képességeket jelölnek, amelyeket az emberek és a fej nélküli szolgáltatások használhatnak.

### <a name="cicd-solutions"></a>CI/CD megoldások

A CI/CD-megoldások parancsainak automatizálásakor `docker build` képességekre van szüksége. `docker push` Ezekhez a fej nélküli szolgáltatásforgatókönyvek hez azt javasoljuk, hogy az **AcrPush** szerepkör hozzárendelése. Ez a szerepkör, ellentétben a szélesebb **közreműködői** szerepkör, megakadályozza, hogy a fiók más beállításjegyzék-műveletek et hajtson végre, vagy az Azure Resource Manager eléréséhez.

### <a name="container-host-nodes"></a>Tároló gazdacsomópontjai

Hasonlóképpen a tárolókat futtató csomópontoknak szükségük van az **AcrPull** szerepkörre, de nem igényelnek **Olvasóképességeket.**

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker bővítmény

Az olyan eszközök esetében, mint például a Visual Studio Code [Docker bővítmény,](https://code.visualstudio.com/docs/azure/docker)további erőforrás-szolgáltatói hozzáférés szükséges a rendelkezésre álló Azure-tároló-beállításjegyzékek listázásához. Ebben az esetben biztosítson hozzáférést a felhasználóknak az **Olvasó** vagy **közreműködőszerepkörhöz.** Ezek a `docker pull` `docker push`szerepkörök lehetővé teszik , , `az acr list`, `az acr build`és egyéb képességeket. 

## <a name="access-resource-manager"></a>Access erőforrás-kezelő

Az Azure Resource Manager-hozzáférés szükséges az Azure Portalhoz és a beállításjegyzék-kezeléshez az [Azure CLI-vel.](/cli/azure/) Ha például a parancs használatával szeretne listát `az acr list` kapni a nyilvántartásokról, szüksége van erre az engedélykészletre. 

## <a name="create-and-delete-registry"></a>Rendszerleíró adatbázis létrehozása és törlése

Az Azure-tároló-jegyzékek létrehozása és törlése.

## <a name="push-image"></a>Lenyomáskép

Egy `docker push` lemezkép, vagy leküldése egy másik [támogatott műtermék,](container-registry-image-formats.md) például egy Helm diagram, a rendszerleíró adatbázisba. [Hitelesítést](container-registry-authentication.md) igényel a rendszerleíró adatbázissal az engedélyezett identitás használatával. 

## <a name="pull-image"></a>Kép lehúzása

Nem karanténba helyezett lemezkép lekérése, vagy egy másik [támogatott műtermék,](container-registry-image-formats.md) például egy Helm-diagram lekérése a rendszerleíró adatbázisból. `docker pull` [Hitelesítést](container-registry-authentication.md) igényel a rendszerleíró adatbázissal az engedélyezett identitás használatával.

## <a name="delete-image-data"></a>Képadatok törlése

A [tárolóképek törlése](container-registry-delete.md)vagy más [támogatott összetevők,](container-registry-image-formats.md) például a Helm-diagramok törlése a rendszerleíró adatbázisból.

## <a name="change-policies"></a>Házirendek módosítása

Házirendek konfigurálása a beállításjegyzékben. A házirendek közé tartozik a kép tisztítása, a karantén engedélyezése és a kép aláírása.

## <a name="sign-images"></a>Képek aláírása

Az a képesség, hogy a lemezképek, általában egy automatikus folyamathoz rendelt, amely egy egyszerű szolgáltatás használata. Ez az engedély általában együtt [push image](#push-image) lehetővé teszi a megbízható lemezkép leküldése a rendszerleíró adatbázisba. További információt az [Azure Container Registry tartalommegbízhatósági kapcsolata.](container-registry-content-trust.md)

## <a name="custom-roles"></a>Egyéni szerepkörök

Más Azure-erőforrásokhoz is létrehozhat saját [egyéni szerepköröket](../role-based-access-control/custom-roles.md) az Azure Container Registry részletes engedélyekkel. Ezután rendelje hozzá az egyéni szerepköröket a felhasználókhoz, a szolgáltatásnévi tagokhoz vagy más identitásokhoz, amelyeknek a beállításjegyzékkel kell együttműködniük. 

Annak megállapításához, hogy mely engedélyeket kell alkalmazni egy egyéni szerepkörre, tekintse meg a Microsoft.ContainerRegistry [műveletek](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)listáját, tekintse át a [beépített ACR szerepkörök](../role-based-access-control/built-in-roles.md)engedélyezett műveleteket, vagy futtassa a következő parancsot:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Egyéni szerepkör definiálása a [Lépések egyéni szerepkör létrehozásához](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)című témakörben található.

> [!IMPORTANT]
> Egyéni szerepkörben az Azure Container Registry jelenleg nem `Microsoft.ContainerRegistry/*` támogatja `Microsoft.ContainerRegistry/registries/*` a helyettesítő karaktereket, például vagy amelyek hozzáférést biztosítanak az összes egyező művelethez. Adja meg a szükséges műveletet egyenként a szerepkörben.

## <a name="next-steps"></a>További lépések

* További információ az RBAC-szerepkörök Azure-identitáshoz való hozzárendeléséről az [Azure Portal](../role-based-access-control/role-assignments-portal.md), az [Azure CLI](../role-based-access-control/role-assignments-cli.md)vagy más Azure-eszközök használatával.

* Ismerje meg az Azure Container Registry [hitelesítési beállításait.](container-registry-authentication.md)

* Megtudhatja, hogy miként engedélyezi [a tárház hatókörrel rendelkező engedélyeket](container-registry-repository-scoped-permissions.md) (előzetes verziót) egy tároló beállításjegyzékében.