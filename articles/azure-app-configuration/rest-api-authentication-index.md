---
title: Azure app Configuration REST API – hitelesítés
description: Az Azure app Configuration REST API használatával történő hitelesítésre szolgáló lapok
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183464"
---
# <a name="authentication"></a>Hitelesítés

Az összes HTTP-kérelmet hitelesíteni kell. A következő hitelesítési sémák támogatottak.

## <a name="hmac"></a>HMAC

A [HMAC-hitelesítés](./rest-api-authentication-hmac.md) véletlenszerűen generált titkot használ a kérelmek hasznos adatainak aláírásához. A hitelesítési módszert használó kérések hitelesítésének részletei a [HMAC-engedélyezés](./rest-api-authorization-hmac.md) szakaszban találhatók.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure ad)-hitelesítés](../active-directory/authentication/overview-authentication.md) a kérelmek hitelesítéséhez a Azure Active Directorytól kapott tulajdonosi jogkivonatot használ. Az ezen hitelesítési módszert használó kérelmek [engedélyezésének részletei az Azure ad-hitelesítés](./rest-api-authorization-azure-ad.md) szakaszban találhatók.