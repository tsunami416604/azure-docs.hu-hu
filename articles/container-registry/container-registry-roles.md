---
title: Azure Container Registry – szerepkörök és engedélyek
description: Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az identitás-és hozzáférés-kezelés (IAM) használatával részletes engedélyeket biztosíthat az Azure Container Registry erőforrásaihoz.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: 69104cdaeb4abfc15e2ac4209e1ddbc610656c13
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793984"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Szerepkörök és engedélyek Azure Container Registry

A Azure Container Registry szolgáltatás olyan Azure-szerepköröket támogat, amelyek különböző szintű engedélyeket biztosítanak egy Azure Container Registry-nek. Az Azure [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/index.yml) (RBAC) használatával olyan konkrét engedélyeket rendelhet a felhasználókhoz vagy egyszerű szolgáltatásokhoz, amelyeknek a beállításjegyzékkel kell működniük.

| Szerepkör/engedély       | [Hozzáférés a Resource Managerhez](#access-resource-manager) | [Beállításjegyzék létrehozása/törlése](#create-and-delete-registry) | [Leküldéses rendszerkép](#push-image) | [Lekéréses rendszerkép](#pull-image) | [Rendszerkép-adatok törlése](#delete-image-data) | [Szabályzatok módosítása](#change-policies) |   [Képek aláírása](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Tulajdonos | X | X | X | X | X | X |  |  
| Közreműködő | X | X | X |  X | X | X |  |  
| Olvasó | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Felhasználók és szolgáltatások megkülönböztetése

Minden alkalommal érvényesek az engedélyek, a legjobb megoldás az, ha a feladat elvégzéséhez egy személy vagy szolgáltatás számára a legkorlátozottat kell megadni. A következő engedélyek olyan képességeket jelentenek, amelyeket az emberek és a fej nélküli szolgáltatások használhatnak.

### <a name="cicd-solutions"></a>CI/CD-megoldások

`docker build` parancsok CI/CD-megoldásokból való automatizálásakor `docker push` képességekre van szükség. A fej nélküli szolgáltatási forgatókönyvek esetében javasoljuk, hogy rendelje hozzá a **AcrPush** szerepkört. Ez a szerepkör a szélesebb körben **közreműködő** szerepkörtől eltérően megakadályozza, hogy a fiók más beállításjegyzékbeli műveleteket végezzen, vagy Azure Resource Managerhoz hozzáférjen.

### <a name="container-host-nodes"></a>Tároló-gazdagép csomópontjai

Hasonlóképpen, a tárolókat futtató csomópontoknak a **AcrPull** szerepkörre van szükségük, de nem igényelnek **olvasói** képességeket.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker-bővítmény

Az olyan eszközökhöz, mint a Visual Studio Code [Docker bővítmény](https://code.visualstudio.com/docs/azure/docker), további erőforrás-szolgáltatói hozzáférésre van szükség az elérhető Azure Container-jegyzékek listázásához. Ebben az esetben adja meg a felhasználók hozzáférését az **olvasó** vagy **közreműködő** szerepkörhöz. Ezek a szerepkörök lehetővé teszik `docker pull`, `docker push`, `az acr list`, `az acr build`és egyéb képességeket. 

## <a name="access-resource-manager"></a>Hozzáférés a Resource Managerhez

Az [Azure CLI](/cli/azure/)-vel való Azure Portal és beállításjegyzék-kezeléshez Azure Resource Manager hozzáférés szükséges. Ha például a `az acr list` parancs használatával szeretné lekérni a beállításjegyzékek listáját, erre az engedélyre van szüksége. 

## <a name="create-and-delete-registry"></a>Beállításjegyzék létrehozása és törlése

Azure Container-jegyzékek létrehozásának és törlésének lehetősége.

## <a name="push-image"></a>Leküldéses rendszerkép

Rendszerképek `docker push`ának lehetősége, vagy egy másik [támogatott](container-registry-image-formats.md) összetevő, például egy Helm-diagram leküldése a beállításjegyzékbe. [Hitelesítés](container-registry-authentication.md) szükséges a beállításjegyzékben a hitelesítő adatokkal. 

## <a name="pull-image"></a>Lekéréses rendszerkép

Nem karanténba helyezett rendszerkép `docker pull`ának lehetősége, vagy egy másik [támogatott](container-registry-image-formats.md) összetevő, például egy Helm-diagram lekérése egy beállításjegyzékből. [Hitelesítés](container-registry-authentication.md) szükséges a beállításjegyzékben a hitelesítő adatokkal.

## <a name="delete-image-data"></a>Rendszerkép-adatok törlése

Képes törölni a [tároló lemezképeit](container-registry-delete.md), vagy törölhet más [támogatott](container-registry-image-formats.md) összetevőket, például Helm-diagramokat egy beállításjegyzékből.

## <a name="change-policies"></a>Szabályzatok módosítása

Szabályzatok konfigurálása a beállításjegyzékben. A szabályzatok közé tartozik például a rendszerkép kiürítése, a karanténba helyezés és a képaláírások engedélyezése.

## <a name="sign-images"></a>Képek aláírása

Képes a képek aláírására, általában egy automatizált folyamathoz rendelve, amely egy egyszerű szolgáltatást használ. Ez az engedély általában [leküldéses képpel](#push-image) kombinálva lehetővé teszi a megbízható rendszerképek beállításjegyzékbe való leküldését. Részletekért lásd: [a tartalom megbízhatósága Azure Container Registryban](container-registry-content-trust.md).

## <a name="next-steps"></a>Következő lépések

* További információ a RBAC-szerepkörök Azure-identitáshoz való hozzárendeléséről a [Azure Portal](../role-based-access-control/role-assignments-portal.md), az [Azure CLI](../role-based-access-control/role-assignments-cli.md)vagy más Azure-eszközök használatával.

* További információ a Azure Container Registry [hitelesítési lehetőségeiről](container-registry-authentication.md) .
