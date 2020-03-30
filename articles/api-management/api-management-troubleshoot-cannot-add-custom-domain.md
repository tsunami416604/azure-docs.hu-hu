---
title: Nem lehet egyéni tartományt hozzáadni a Key Vault-tanúsítvánnyal
titleSuffix: Azure API Management
description: Ismerje meg, hogyan háríthatja el azt a problémát, amely nek köszönhetően nem adhat hozzá egyéni tartományt az Azure API Management ben egy kulcstartó-tanúsítvány használatával.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430573"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Nem sikerült frissíteni az API Management szolgáltatás állomásneveit

Ez a cikk a "Nem sikerült frissíteni az API Management szolgáltatás állomásnevek" hibaüzenetet, amely az Azure API Management szolgáltatás egyéni tartomány ának hozzáadásakor jelenhet meg. Ez a cikk hibaelhárítási lépéseket tartalmaz a probléma megoldásához.

## <a name="symptoms"></a>Probléma

Amikor az Azure Key Vault tanúsítványával egyéni tartományt próbál hozzáadni az API Management szolgáltatáshoz, a következő hibaüzenet jelenik meg:

- Nem sikerült frissíteni az API Management szolgáltatás állomásneveit. Az erőforráshozhttps://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0' ' kérés a StatusCode: Tiltott kérésazonosítóval: . Kivételüzenet: A művelet érvénytelen állapotkódot adott vissza: "Tiltott".

## <a name="cause"></a>Ok

Az API Management szolgáltatás nem rendelkezik engedéllyel az egyéni tartományhoz használni kívánt kulcstartó eléréséhez.

## <a name="solution"></a>Megoldás

A probléma megoldásához kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalon,](Https://portal.azure.com)válassza ki az API Management-példányt, majd válassza **a Felügyelt identitások**lehetőséget. Győződjön meg arról, hogy a Regisztráció az **Azure Active Directoryval** beállítás igen beállítással **van**beállítva. 
    ![Regisztráció az Azure aktív igazgatójával](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. Az Azure Portalon nyissa meg a **Key vaults** szolgáltatást, és válassza ki az egyéni tartományhoz használni kívánt kulcstartót.
1. Válassza az **Access-házirendek**lehetőséget, és ellenőrizze, hogy van-e olyan egyszerű szolgáltatás, amely megegyezik az API Management szolgáltatáspéldány nevével. Ha van, jelölje ki az egyszerű szolgáltatást, és győződjön meg arról, hogy rendelkezik a Titkos engedélyek listában szereplő **Get** **engedéllyel.**  
    ![Hozzáférési házirend hozzáadása egyszerű szolgáltatáshoz](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Ha az API Management szolgáltatás nem szerepel a listában, válassza a **Hozzáférési házirend hozzáadása**lehetőséget, majd hozza létre a következő hozzáférési szabályzatot:
    - **Konfigurálás sablonból**: Nincs
    - **Egyszerű kiválasztása**: Keressen az API Management szolgáltatás nevében, majd válassza ki a listából
    - **Kulcsengedélyek**: Nincs
    - **Titkos engedélyek**: Beszerezni
    - **Tanúsítványengedélyek**: Nincs
1. A hozzáférési házirend létrehozásához válassza az **OK gombot.**
1. A módosítások mentéséhez válassza a **Mentés** gombot.

Ellenőrizze, hogy megoldódott-e a probléma. Ehhez próbálja meg létrehozni az egyéni tartományt az API Management szolgáltatásban a Key Vault-tanúsítvány használatával.

## <a name="next-steps"></a>További lépések
További információ az API Management szolgáltatásról:

- Tekintse n le az API Managementről szóló további [videókat.](https://azure.microsoft.com/documentation/videos/index/?services=api-management)
* A háttérszolgáltatás védelmének egyéb módjairól a [Kölcsönös tanúsítvány hitelesítése](api-management-howto-mutual-certificates.md)című témakörben van.

* [Hozzon létre egy API Management szolgáltatáspéldányt.](get-started-create-service-instance.md)
* [Az első API kezelése.](import-and-publish.md)