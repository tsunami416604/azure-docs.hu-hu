---
title: Nem adható hozzá egyéni tartomány Key Vault-tanúsítvány használatával az Azure-ban API Management | Microsoft Docs
description: Ismerje meg, hogy miként lehet elhárítani a problémát, amelyben a Key Vault-tanúsítvánnyal nem adhat hozzá egyéni tartományt az Azure API Managementban.
services: api-management
documentationcenter: ''
author: genlin
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: 30dfd864c6c237024c72325da813691ac5043bd0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072091"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Nem sikerült frissíteni API Management Service-gazdagépeket

Ez a cikk a "nem sikerült frissíteni API Management Service hostnames" hibát mutatja be, amely akkor fordulhat elő, ha egyéni tartományt ad hozzá az Azure API Management szolgáltatáshoz. Ez a cikk a probléma megoldásához nyújt hibaelhárítási lépéseket.

## <a name="symptoms"></a>Probléma

Ha a API Management szolgáltatáshoz egyéni tartományt próbál felvenni egy Azure Key Vault tanúsítványának használatával, a következő hibaüzenet jelenik meg:

- Nem sikerült frissíteni API Management Service-gazdagépeket. A (z)https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0"" erőforrásra irányuló kérelem sikertelen a következő statuscode: Kérelemazonosító esetében tiltott:. Kivétel üzenete: A művelet érvénytelen állapotkódot adott vissza: "tiltott".

## <a name="cause"></a>Ok

A API Management szolgáltatásnak nincs engedélye arra, hogy hozzáférjen az egyéni tartományhoz használni kívánt kulcstartóhoz.

## <a name="solution"></a>Megoldás

A probléma megoldásához kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Portal](Https://portal.azure.com), válassza ki a API Management példányt, majd válassza a **felügyelt identitások**lehetőséget. Győződjön meg arról, hogy a **regisztráció a Azure Active Directory** beállítás értéke **Igen**. 
    ![Regisztrálás az Azure Active Director-ban](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. A Azure Portal nyissa meg a **Key Vaults** szolgáltatást, és válassza ki az egyéni tartományhoz használni kívánt kulcstartót.
1. Válassza a **hozzáférési szabályzatok**lehetőséget, és győződjön meg arról, hogy van-e olyan egyszerű szolgáltatásnév, amely megfelel az API Management Service-példány nevével. Ha van, válassza ki a szolgáltatásnevet, és győződjön meg arról, hogy a **Get** engedély szerepel a **titkos engedélyek**területen.  
    ![Hozzáférési szabályzat hozzáadása a szolgáltatásnév számára](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Ha a API Management szolgáltatás nem szerepel a listában, válassza a **hozzáférési házirend hozzáadása**lehetőséget, majd hozza létre a következő hozzáférési szabályzatot:
    - **Konfigurálás sablonból**: Nincsenek
    - **Rendszerbiztonsági tag kiválasztása**: Keresse meg a API Management szolgáltatás nevét, majd válassza ki a listából.
    - **Kulcs engedélyei**: Nincsenek
    - **Titkos kód engedélyei**: Beolvasás
    - **Tanúsítvány engedélyei**: Nincsenek
1. A hozzáférési házirend létrehozásához kattintson **az OK gombra** .
1. Válassza ki **mentése** menti a módosításokat.

Ellenőrizze, hogy a probléma megoldódott. Ehhez próbálja meg létrehozni az egyéni tartományt a API Management szolgáltatásban a Key Vault tanúsítvány használatával.

## <a name="next-steps"></a>További lépések
További információ a API Management szolgáltatásról:

- További [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) a API Managementról.
* A háttér-szolgáltatás biztonságossá tételének egyéb módjaival kapcsolatban lásd: [kölcsönös Tanúsítványos hitelesítés](api-management-howto-mutual-certificates.md).

* [Hozzon létre egy API Management Service-példányt](get-started-create-service-instance.md).
* [Az első API kezelése](import-and-publish.md).