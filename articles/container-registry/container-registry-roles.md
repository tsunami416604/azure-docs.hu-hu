---
title: Az Azure Container Registry - szerepkörök és engedélyek
description: Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az identitás és hozzáférés-kezelés (IAM) használatával adja meg a részletes engedélyeket az erőforrásokhoz az Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: a4b9b382755e73b6218432624c471346e9698752
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193390"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Az Azure Container Registry-szerepkörök és engedélyek

Az Azure Container Registry szolgáltatás támogat egy Azure-szerepkörök, engedélyek és egy Azure container registry különböző szinteken biztosít. Az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/index.yml) (RBAC) adott engedélyek hozzárendelése a felhasználókhoz vagy egyszerű szolgáltatások, amelyek a beállításjegyzék kommunikáljon.

| Szerepkör vagy engedély       | [Resource Manager elérése](#access-resource-manager)| [Beállításjegyzék létrehozása/törlése](#create-and-delete-registry) | [Rendszerkép leküldése](#push-image) | [Rendszerkép lekérése](#pull-image) | [Szabályzatok módosítása](#change-policies) |   [Bejelentkezési képek](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- |
| Tulajdonos | X | X | X | X | X |  |  
| Közreműködő | X | X | X | X | X |  |  
| Olvasó | X |  |  | X |  |  | 
| AcrPush |  |  | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  
| ACR-lemezképaláíró |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Különbséget tenni a felhasználók és szolgáltatások

Idő engedélyek vonatkoznak, az ajánlott eljárás, hogy egy személy vagy szolgáltatás, feladat végrehajtására vonatkozó legtöbb korlátozott számú. A következő engedélycsoportok képességek az emberek és távfelügyelt szolgáltatások által esetleg használt csoportját képviselik.

### <a name="cicd-solutions"></a>CI/CD-megoldások

Amikor automatizálása `docker build` szükséges parancsokat a CI/CD-megoldások, `docker push` képességeket. Távfelügyelt szolgáltatás ebben az esetben javasoljuk, hogy hozzárendelése a **AcrPush** szerepkör. Ezzel a szerepkörrel ellentétben a szélesebb körű **közreműködői** szerepkör, megakadályozza, hogy a fiók más beállításjegyzék műveleteket végez, vagy Azure Resource Manager elérése.

### <a name="container-host-nodes"></a>Tároló csomópontokra

Hasonlóképpen, a tárolók futtató csomópontot kell a **AcrPull** szerepkör elvégzéséhez nem szükséges, de **olvasó** képességeket.

### <a name="visual-studio-code-docker-extension"></a>A Visual Studio Code Docker-bővítmény

Az eszközök, mint például a Visual Studio Code [Docker-bővítmény](https://code.visualstudio.com/docs/azure/docker), listázhatja a rendelkezésre álló Azure tároló-beállításjegyzékek további erőforrás-szolgáltató hozzáférés szükséges. Ebben az esetben adja meg a felhasználók számára a hozzáférést a **olvasó** vagy **közreműködői** szerepkör. Ezek a szerepkörök engedélyezése `docker pull`, `docker push`, `az acr list`, `az acr build`, és egyéb funkciókat. 

## <a name="access-resource-manager"></a>Resource Manager elérése

Az Azure Resource Manager-hozzáférésre szüksége az Azure portal és a beállításjegyzék kezelése a a [Azure CLI-vel](/cli/azure/). Például a beállításjegyzékek listájának lekérése a `az acr list` parancsot, erre az engedélyre van szüksége beállítása. 

## <a name="create-and-delete-registry"></a>Hozzon létre, és a regisztrációs adatbázis törlése

Hozzon létre vagy töröljön az Azure container registryk lehetővé teszi.

## <a name="push-image"></a>Rendszerkép leküldése

Lehetővé teszi `docker push` képet, vagy egy másik leküldéses [összetevő támogatott](container-registry-image-formats.md) például egy Helm-diagram, egy regisztrációs adatbázisba. Szükséges [hitelesítési](container-registry-authentication.md) a beállításjegyzék használatával a meghatalmazott identitását. 

## <a name="pull-image"></a>Rendszerkép lekérése

Lehetővé teszi `docker pull` egy nem-karanténba képfájl, illetve egy másik lekéréses [összetevő támogatott](container-registry-image-formats.md) például egy Helm-diagramot a beállításjegyzékből. Szükséges [hitelesítési](container-registry-authentication.md) a beállításjegyzék használatával a meghatalmazott identitását.

## <a name="change-policies"></a>Szabályzatok módosítása

Szabályzatok konfigurálása a beállításjegyzék lehetővé teszi. Házirendek tartalmazzák a lemezkép-ürítést, karanténba helyezése, és a lemezkép aláírási engedélyezése.

## <a name="sign-images"></a>Bejelentkezési képek

Lehetővé teszi képek, általában egy automatizált folyamattal, amely egy egyszerű szolgáltatást kell használnia rendelt aláírásához. Ez az engedély általában kombinálva [leküldéses kép](#push-image) , hogy egy megbízható rendszerkép leküldése egy beállításjegyzéket. További információkért lásd: [tartalom az Azure Container Registry megbízhatósági](container-registry-content-trust.md).

## <a name="next-steps"></a>További lépések

* További információ az RBAC-szerepkörök hozzárendelése az Azure-identitás használatával a [az Azure portal](../role-based-access-control/role-assignments-portal.md), a [Azure CLI-vel](../role-based-access-control/role-assignments-cli.md), vagy más Azure-eszközök.

* Ismerje meg [hitelesítési beállítások](container-registry-authentication.md) az Azure Container Registry.
