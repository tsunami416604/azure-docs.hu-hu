---
title: Egyéni tartománynév beállítása saját üzemeltetésű Azure API Management átjáróhoz | Microsoft Docs
description: Ez a témakör az egyéni tartománynevek saját üzemeltetésű Azure API Management átjáróhoz való konfigurálásának lépéseit ismerteti.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: dacf1329d35117c65bcc48a82ac27a767ebd2b3b
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203182"
---
# <a name="configure-a-custom-domain-name"></a>Egyéni tartománynév konfigurálása

Ha saját üzemeltetésű [Azure API Management-átjárót](self-hosted-gateway-overview.md) hoz létre, nem rendeli hozzá az állomásnevet, és az IP-címe alapján kell hivatkozni rá. Ez a cikk bemutatja, hogyan képezhető le egy meglévő egyéni DNS-név (más néven állomásnév) egy saját üzemeltetésű átjáróként.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

-   Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Egy API Management példány. További információ: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
- Egy saját üzemeltetésű átjáró. További információ: [a saját üzemeltetésű átjáró kiépítése](api-management-howto-provision-self-hosted-gateway.md)
-   Az Ön vagy a szervezete tulajdonában álló egyéni tartománynév. Ez a témakör nem nyújt útmutatást az Egyéni tartománynév beszerzéséhez.
-   A DNS-kiszolgálón tárolt DNS-rekord, amely az egyéni tartománynevet a saját üzemeltetésű átjáró IP-címére képezi le. Ez a témakör nem nyújt útmutatást a DNS-rekordok üzemeltetéséhez.
-   Rendelkeznie kell egy nyilvános és titkos kulccsal rendelkező érvényes tanúsítvánnyal (. PFX). A tulajdonos vagy a tulajdonos alternatív neve (SAN) meg kell egyeznie a tartománynévvel (Ez lehetővé teszi API Management példány számára, hogy biztonságosan tegye elérhetővé a TLS-t).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>Egyéni tartományi tanúsítvány hozzáadása a API Management szolgáltatáshoz

1. Válassza ki a **tanúsítványok** elemet a **Biztonság**területen.
2. Válassza a **+ Hozzáadás** lehetőséget.
3. Adja meg a tanúsítvány **azonosító** mezőjébe az erőforrás nevét.
4. Válassza ki a tanúsítványt tartalmazó fájlt (. PFX) kattintson a **tanúsítvány** mezőre vagy a mellette található mappa ikonra.
5. Adja meg a tanúsítványhoz tartozó jelszót a **jelszó** mezőben.
6. Válassza a **Létrehozás** lehetőséget a tanúsítvány API Management szolgáltatáshoz való hozzáadásához.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Egyéni tartománynév beállítása a saját üzemeltetésű átjáróhoz a Azure Portal használatával

1. Válassza ki az **átjárókat** a **Beállítások**területen.
2. Válassza ki azt a saját üzemeltetésű átjárót, amelyhez konfigurálni kívánja a tartománynevet.
3. Válassza a **gazdagépek** lehetőséget a **Beállítások**területen.
4. Válassza a **+ Hozzáadás** lehetőséget
5. Adja meg az állomásnév erőforrás nevét a **név** mezőben.
6. Adja meg a tartomány nevét az **állomásnév** mezőben.
7. Válasszon ki egy tanúsítványt a **tanúsítvány** legördülő listából.
8. Jelölje be az **ügyféltanúsítvány egyeztetése** jelölőnégyzetet, ha az átjárón keresztül elérhető API-k bármelyike ügyféltanúsítvány-alapú hitelesítést használ.
    > [!WARNING]
    > Ezt a beállítást az átjáróhoz konfigurált összes tartománynév megosztja.
9. A **Hozzáadás** gombra kattintva rendelje hozzá az egyéni tartománynevet a kiválasztott saját üzemeltetésű átjáróhoz.

## <a name="next-steps"></a>További lépések

[A szolgáltatás frissítése és méretezése](upgrade-and-scale.md)
