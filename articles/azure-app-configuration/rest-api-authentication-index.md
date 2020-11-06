---
title: Azure app Configuration REST API – hitelesítés
description: Az Azure app Configuration REST API használatával történő hitelesítésre szolgáló lapok
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424328"
---
# <a name="authentication"></a>Hitelesítés

Az összes HTTP-kérelmet hitelesíteni kell. A következő hitelesítési sémák támogatottak.

## <a name="hmac"></a>HMAC

A [HMAC-hitelesítés](./rest-api-authentication-hmac.md) véletlenszerűen generált titkot használ a kérelmek hasznos adatainak aláírásához. A hitelesítési módszert használó kérések hitelesítésének részletei a [HMAC-engedélyezés](./rest-api-authorization-hmac.md) szakaszban találhatók.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure ad)-hitelesítés](/azure/active-directory/authentication/overview-authentication) a kérelmek hitelesítéséhez a Azure Active Directorytól kapott tulajdonosi jogkivonatot használ. Az ezen hitelesítési módszert használó kérelmek [engedélyezésének részletei az Azure ad-hitelesítés](./rest-api-authorization-azure-ad.md) szakaszban találhatók.
