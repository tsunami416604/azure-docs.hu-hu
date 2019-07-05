---
title: Az Azure API Management-példány az egyéni tartománynév beállítása |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure API Management-példány az egyéni tartománynév beállítása.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509297"
---
# <a name="configure-a-custom-domain-name"></a>Egyéni tartománynév konfigurálása

Amikor létrehoz egy Azure API Management szolgáltatáspéldányt, Azure rendel hozzá egy altartományt az azure-API.NET webhelyen (például `apim-service-name.azure-api.net`). Azonban tehetők közzé a saját egyéni tartománynév használatával például az API Management végpontok **contoso.com**. Ez az oktatóanyag bemutatja, hogyan meglévő egyéni DNS-név leképezése az API Management-példány által elérhetővé tett végpontok.

> [!WARNING]
> Azzal, hogy alkalmazásaikat a biztonság növelése érdekében használja a tanúsítvány rögzítését kell használnia az egyéni tartománynév > és a tanúsítvány kezeli, amely nem az alapértelmezett tanúsítvány. Ügyfelek, amelyek az alapértelmezett tanúsítvány rögzítése helyett lesz > rögzített függőség véve nem szabályozzák, a tanúsítvány tulajdonságait, ez nem ajánlott.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie:

-   Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API Management-példány. További információkért lásd: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
-   Egyéni tartománynév, amely az Ön tulajdonában van. Az egyéni tartománynevet szeretne használni, külön-külön beszerzett legyen, és egy DNS kiszolgálón lévő üzemeltetett. Ez a témakör bemutatja, hogyan működtethető az egyéni tartománynév a nem ad utasításokat.
-   Rendelkeznie kell érvényes tanúsítványt és a egy nyilvános és titkos kulcs (. PFX). Tulajdonos vagy a tulajdonos alternatív neve (SAN) meg kell egyeznie a a tartomány nevét (Ez lehetővé teszi az API Management példány hogy biztonságosan URL-címek SSL-en keresztül).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Egyéni tartománynév beállítása az Azure portal használatával

1. Keresse meg az API Management-példány a [az Azure portal](https://portal.azure.com/).
1. Válassza ki **egyéni tartományok és SSL**.

    Nincsenek számos végpontot, amelyhez hozzárendelhet egy egyéni tartomány nevét. Jelenleg az alábbi végpontok érhetők el:

    - **Proxy** (alapértelmezett érték: `<apim-service-name>.azure-api.net`),
    - **Portál** (alapértelmezett érték: `<apim-service-name>.portal.azure-api.net`),
    - **Felügyeleti** (alapértelmezett érték: `<apim-service-name>.management.azure-api.net`),
    - **SCM** (alapértelmezett érték: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > A végpontok egy része vagy azokat frissítheti. Gyakran, az ügyfelek frissítéséhez **Proxy** (az URL-cím segítségével az API Management szolgáltatáson keresztül közzétett API-t) és **portál** (a fejlesztői portál URL-cím). **Felügyeleti** és **SCM** végpontok belsőleg az API Management példány csak a tulajdonos, és így kevésbé gyakran hozzárendelt egy egyéni tartomány nevét. A legtöbb esetben csak egyetlen egyéni tartománynevet is állítható be egy adott végpontot. Azonban a **prémium** csomag támogatja a több állomásnevet a **Proxy** végpont.

1. Válassza ki a frissíteni kívánt végpontot.
1. Kattintson a jobb oldali ablakban **egyéni**.

    - Az a **egyéni tartománynév**, adja meg a használni kívánt nevét. Például: `api.contoso.com`. Helyettesítő karaktereket tartalmazó tartománynevek (például \*. tartomány.com) is támogatottak.
    - Az a **tanúsítvány**, válasszon ki egy tanúsítványt a Key Vaultból. Emellett feltölthet egy érvényes. PFX fájlt, és adja meg a **jelszó**, ha a tanúsítvány jelszóval védett.

    > [!TIP]
    > Azt javasoljuk, hogy az Azure Key Vaulttal a tanúsítványok kezeléséhez, és a autorotate állítja be őket.
    > Ha az Azure Key Vault használatával kezelheti az egyéni tartomány SSL-tanúsítványt, ellenőrizze, hogy a tanúsítvány szúr be a Key Vault [, egy _tanúsítvány_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), nem egy _titkos_.
    >
    > SSL-tanúsítvány beolvasása, az API Management engedélyekkel kell rendelkeznie a lista egy get titkos kulcsok az Azure Key Vault tanúsítványt tartalmazó. Ha a szükséges konfigurációs lépésekkel automatikusan befejeződik az Azure portal használatával. Parancssori eszközök vagy a felügyeleti API-t használ, ha manuálisan ezeket az engedélyeket kell biztosítani. Ez két lépésben történik. Első lépésként használatával felügyelt identitások lapon az API Management-példány a győződjön meg arról, hogy engedélyezve van-e a felügyelt identitást, és jegyezze fel a résztvevő-azonosító az adott oldalon látható. Második engedélylistában adjon, és titkos kulcsok engedélyekkel a résztvevő-azonosító beszerzése az Azure Key Vault tanúsítványt tartalmazó.
    >
    > Ha a tanúsítvány autorotate értékre van állítva, az API Management kiesik a legújabb verzióra automatikusan a szolgáltatás állásidő nélkül (ha az API Management csomag esetében a szolgáltatásiszint-szerződés – vagyis minden szinten, a fejlesztői csomag kivételével).

1. Kattintson a alkalmazni.

    > [!NOTE]
    > A tanúsítvány társítását 15 percet vagy többet a központi telepítés méretétől függően eltarthat. Termékváltozat fejlesztőnek állásidő, alapszintű és a magasabb szintű Termékváltozat nem állásidővel kell számolnia.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>További lépések

[Frissítés és skálázás az a szolgáltatás](upgrade-and-scale.md)
