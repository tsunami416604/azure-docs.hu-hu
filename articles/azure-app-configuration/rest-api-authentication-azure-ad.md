---
title: Azure Active Directory REST API – hitelesítés
description: A Azure Active Directory használatával hitelesítheti az Azure-alkalmazás konfigurációját az REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424192"
---
# <a name="azure-active-directory-authentication"></a>Hitelesítés Azure Active Directory-fiókkal

A HTTP-kérések hitelesítését a **tulajdonos** hitelesítési sémája Azure Active Directory (Azure ad) által beszerzett jogkivonat használatával lehet hitelesíteni. Ezeket a kérelmeket TLS protokollon keresztül kell továbbítani.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-token igényléséhez használni kívánt rendszerbiztonsági tag a megfelelő [alkalmazás-konfigurációs szerepkörök](./rest-api-authorization-azure-ad.md) valamelyikéhez rendelhető hozzá

Adja meg az összes kérelmet a hitelesítéshez szükséges összes HTTP-fejléctel. A minimálisan szükséges érték a következők:

|  Kérelem fejléce | Leírás  |
| --------------- | ------------ |
| **Engedélyezés** | A **tulajdonosi** séma által igényelt hitelesítési információk. A formátum és a részletek magyarázata alább látható. |

**Példa**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Azure Active Directory jogkivonat beszerzése

Az Azure AD-jogkivonat beszerzése előtt meg kell határoznia, hogy milyen felhasználót szeretne hitelesíteni, milyen célközönséget kér a jogkivonat számára, és milyen Azure AD-végpontot (Authority) kell használniuk.

### <a name="audience"></a>Célközönség

Az Azure AD-jogkivonatot megfelelő célközönségnek kell megadnia. Az Azure-alkalmazások konfigurálásához a következő célközönségek egyikét kell megadni, amikor jogkivonatot kér. A célközönség neve "erőforrás" is lehet, amelyet a jogkivonat kér.

- {configurationStoreName}. azconfig. IO
- *. azconfig.io

> [!IMPORTANT]
> Ha a kért célközönség {configurationStoreName}. azconfig. IO, pontosan egyeznie kell a kérelem elküldéséhez használt "gazda" kérési fejléctel (a kis-és nagybetűk megkülönböztetésével).

### <a name="azure-ad-authority"></a>Azure AD-szolgáltató

Az Azure AD-szolgáltató az Azure AD-token beszerzéséhez használt végpont. A formátuma a ( `https://login.microsoftonline.com/{tenantId}` ). A `{tenantId}` szegmens arra a Azure Active Directory bérlői azonosítóra hivatkozik, amelyhez a hitelesíteni próbált felhasználó/alkalmazás tartozik.

### <a name="authentication-libraries"></a>Hitelesítési tárak

Az Azure Azure Active Directory Authentication librarys (ADAL) nevű könyvtárakat biztosít az Azure AD-token beszerzésének leegyszerűsítése érdekében. Ezek a kódtárak több nyelvre épülnek. A dokumentáció [itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)található.

## <a name="errors"></a>**Hibák**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**OK:** Nincs megadva az engedélyezési kérelem fejléce a tulajdonosi sémával.
**Megoldás:** Érvényes ```Authorization``` http-kérelem fejlécének megadása

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**OK:** Az Azure AD-jogkivonat érvénytelen.
**Megoldás:** Szerezze be az Azure AD-tokent az Azure AD-szolgáltatótól, és győződjön meg arról, hogy a megfelelő célközönség használatban van.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**OK:** Az Azure AD-jogkivonat érvénytelen.
**Megoldás:** Szerezzen be egy Azure AD-jogkivonatot az Azure AD-szolgáltatótól, és győződjön meg arról, hogy az Azure AD-bérlő az előfizetéshez társítva van, amelyhez a konfigurációs tároló tartozik. Ez a hiba akkor jelenhet meg, ha a résztvevő több Azure AD-bérlőhöz tartozik.
