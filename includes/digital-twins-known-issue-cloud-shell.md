---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-hoz – ismert probléma Cloud Shell hitelesítéssel
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290097"
---
>[!NOTE]
>Jelenleg egy **ismert probléma** van a Cloud shellt érintő következő parancsokkal: `az dt route` , `az dt model` , `az dt twin` .
>
>A probléma megoldásához futtassa a `az login` parancsot Cloud Shell a parancs futtatása előtt, vagy használja a [helyi](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) parancssori felületet Cloud Shell helyett. Erről további részleteket a [*Hibaelhárítás: az Azure digitális Twins ismert problémái*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)című témakörben talál.