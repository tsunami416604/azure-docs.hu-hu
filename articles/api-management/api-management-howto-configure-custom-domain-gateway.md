---
title: Egyéni tartománynév konfigurálása a saját üzemeltetésű Azure API Management átjáróhoz | Microsoft dokumentumok
description: Ez a témakör a saját üzemeltetésű Azure API Management átjáró egyéni tartománynév konfigurálásának lépéseit ismerteti.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1f2184c7c62887a98a76877528b167d173c3d75b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335935"
---
# <a name="configure-a-custom-domain-name"></a>Egyéni tartománynév konfigurálása

Saját [üzemeltetésű Azure API Management átjáró](self-hosted-gateway-overview.md) kiépítésekor nincs hozzárendelve állomásnév, és az IP-címe alapján kell hivatkozni rá. Ez a cikk bemutatja, hogyan lehet egy meglévő egyéni DNS-nevet (más néven állomásnév) saját üzemeltetésű átjárót leképezni.

> [!NOTE]
> A saját üzemeltetésű átjárófunkció előzetes verzióban érhető el. Az előzetes verzió során a saját üzemeltetésű átjáró csak a Fejlesztői és prémium szintű csomagokban érhető el további díj nélkül. A fejlesztői szint egyetlen saját üzemeltetésű átjáró-telepítésre korlátozódik.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőkre van szüksége:

-   Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Egy API Management-példány. További információ: [Create an Azure API Management instance](get-started-create-service-instance.md).
- Saját üzemeltetésű átjáró. További információ: [Saját üzemeltetésű átjáró kiépítése](api-management-howto-provision-self-hosted-gateway.md)
-   Egyéni tartománynév, amely az Ön vagy a szervezet tulajdonában van. Ez a témakör nem ad útmutatást az egyéni tartománynév beszerzéséhez.
-   Az egyéni tartománynevet a saját üzemeltetett átjáró IP-címéhez hozzárendelő DNS-rekord. Ez a témakör nem ad útmutatást a DNS-rekordok üzemeltetéséhez.
-   Érvényes tanúsítvánnyal kell rendelkeznie nyilvános és személyes kulccsal (. PFX). A tulajdonos vagy a tulajdonos alternatív nevének (SAN) meg kell egyeznie a tartománynévvel (ez lehetővé teszi, hogy az API Management-példány biztonságosan tegye elérhetővé az URL-címeket a TLS-en keresztül).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Egyéni tartománytanúsítvány hozzáadása az API Management szolgáltatáshoz

1. Válassza a **Tanúsítványok** lehetőséget a **Biztonság csoportban.**
2. Válassza a **+ Hozzáadás** lehetőséget.
3. Adja meg a tanúsítvány erőforrásnevét az **azonosító** mezőbe.
4. Jelölje ki a tanúsítványt (. PFX) a **Tanúsítvány** mező vagy a mellette lévő mappaikon kiválasztásával.
5. Írja be a tanúsítvány jelszavát a **Jelszó** mezőbe.
6. Válassza a **Létrehozás** lehetőséget, ha hozzá szeretné adni a tanúsítványt az API Management szolgáltatáshoz.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Az Azure Portal használatával egyéni tartománynevet állíthat be a saját üzemeltetésű átjáróhoz

1. Válassza ki az **Átjárók elemet** a **Beállítások csoportban.**
2. Válassza ki azt a saját üzemeltetésű átjárót, amelyhez konfigurálni szeretné a tartománynevet.
3. Válassza az **Állomásnevek lehetőséget a** **Beállítások területen.**
4. Válassza a **+ Hozzáadás lehetőséget**
5. Írja be az állomásnév erőforrásnevét a **Név** mezőbe.
6. Írja be a tartománynevet a **Állomásnév** mezőbe.
7. Válasszon ki egy tanúsítványt a **Tanúsítvány** legördülő menüből.
8. Jelölje be **az Ügyféltanúsítvány egyeztetése** jelölőnégyzetet, ha az ezen az átjárón keresztül elérhetővé tett API-k bármelyike ügyféltanúsítvány-hitelesítést használ.
    > [!WARNING]
    > Ezt a beállítást az átjáróhoz konfigurált összes tartománynév megosztja.
9. Válassza a **Hozzáadás** lehetőséget, ha az egyéni tartománynevet hozzá szeretné rendelni a kijelölt saját üzemeltetésű átjáróhoz.

## <a name="next-steps"></a>További lépések

[A szolgáltatás frissítése és méretezése](upgrade-and-scale.md)
