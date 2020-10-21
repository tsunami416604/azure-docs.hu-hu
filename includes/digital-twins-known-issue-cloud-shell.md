---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-hoz – ismert probléma Cloud Shell hitelesítéssel
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320972"
---
>[!NOTE]
>Jelenleg **ismert probléma** történt a következő parancsokkal kapcsolatos Cloud Shellban *https://shell.azure.com* : `az dt route` , `az dt model` , `az dt twin` .
>
>A megoldáshoz a következők bármelyikét végezheti el:
> * `az login`A parancs futtatása előtt futtassa Cloud shell.
> * Nyissa meg a Cloud Shell ablaktáblát a Azure Portalban, és fejezze be a Cloud Shell munkát.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="A &quot;Cloud Shell&quot; ikonnal jelölt Azure Portal megjelenítése, valamint a portál ablakának alján megjelenő Cloud Shell":::
> * Cloud Shell helyett a [helyi CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t használja.
>
>A problémáról további részleteket a [*Hibaelhárítás: az Azure digitális Twins ismert problémái*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)című témakörben talál.