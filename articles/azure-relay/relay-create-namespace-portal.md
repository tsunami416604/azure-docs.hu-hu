---
title: Továbbítónévtér létrehozása az Azure Portal használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre Relay-névteret a Azure Portal használatával.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 78ab6753-877a-4426-92ec-a81675d62a57
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: b9811ef92aba4891627c20e0269be136582a3304
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211995"
---
# <a name="create-a-relay-namespace-using-the-azure-portal"></a>Továbbítónévtér létrehozása az Azure Portal használatával

A névtér egy hatókörkezelési tároló az Azure Relay valamennyi összetevője számára. Egyetlen névtér több Relayt is tartalmazhat, és a névterek gyakran alkalmazástárolóként is szolgálnak. A Relay-névterek létrehozásának jelenleg két különböző módja van:

1. Azure Portal (ez a cikk).
2. [Azure Resource Manager](../azure-resource-manager/management/overview.md) sablonok.

## <a name="create-a-namespace-in-the-azure-portal"></a>Névtér létrehozása az Azure Portalon

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

Gratulálunk! Ezzel létrehozott egy új Relay-névteret.

## <a name="next-steps"></a>További lépések

* [Relay – gyakori kérdések](relay-faq.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)

