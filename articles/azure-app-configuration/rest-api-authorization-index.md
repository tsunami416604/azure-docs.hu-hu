---
title: Azure app Configuration REST API – engedélyezés
description: Az Azure app Configuration REST API használatával történő engedélyezésre vonatkozó hivatkozási lapok
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932642"
---
# <a name="authorization"></a>Engedélyezés

Az engedélyezés arra az eljárásra vonatkozik, amelyet a hívónak a kérelem elvégzése során alkalmazott engedélyek meghatározására használ. Több engedélyezési modell is létezik. A kérelemhez használt engedélyezési modell a használt [hitelesítési](./rest-api-authentication-index.md) módszertől függ. Az engedélyezési modellek az alábbiakban láthatók.

## <a name="hmac"></a>HMAC

Az HMAC-hitelesítéshez társított [engedélyezési modell](./rest-api-authorization-hmac.md) modellje csak olvasható vagy írható írásra osztja fel az engedélyeket. A részletekért tekintse meg a [HMAC-hitelesítés](./rest-api-authorization-hmac.md) oldalát.

## <a name="azure-active-directory"></a>Azure Active Directory

Az Azure Active Directory (Azure AD) hitelesítéshez társított [engedélyezési modell](./rest-api-authorization-azure-ad.md) az Azure RBAC használatával szabályozza az engedélyeket. A részletekért tekintse meg az [Azure ad-engedélyezési](./rest-api-authorization-azure-ad.md) oldalt.
