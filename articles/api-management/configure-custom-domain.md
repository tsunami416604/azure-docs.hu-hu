---
title: Egyéni tartománynév konfigurálása az Azure API Management példányhoz
titleSuffix: Azure API Management
description: Ez a témakör azt ismerteti, hogyan konfigurálhat egyéni tartománynevet az Azure API Management-példányhoz.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4587909ad6fca6cdf21d54d11d89f797bbb29833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335840"
---
# <a name="configure-a-custom-domain-name"></a>Egyéni tartománynév konfigurálása

Amikor létrehoz egy Azure API Management szolgáltatáspéldányt, az `azure-api.net` Azure hozzárendeli azt egy altartományhoz `apim-service-name.azure-api.net`(például). Az API Management-végpontokat azonban saját egyéni tartománynévvel, például **contoso.com**is elérhetővé teheti. Ez az oktatóanyag bemutatja, hogyan képezheti le egy meglévő egyéni DNS-nevet egy API Management-példány által elérhetővé tett végpontokhoz.

> [!IMPORTANT]
> Az API Management csak olyan kérelmeket fogad [el,](https://tools.ietf.org/html/rfc2616#section-14.23) amelyek állomásfejléc-értékei megfelelnek az alapértelmezett tartománynévnek vagy a konfigurált egyéni tartományneveknek.

> [!WARNING]
> Azoknak az ügyfeleknek, akik tanúsítványrögzítéssel kívánják növelni alkalmazásaik biztonságát, egyéni tartománynevet és tanúsítványt kell használniuk, amelyet ők kezelnek, nem pedig az alapértelmezett tanúsítványt. Azok az ügyfelek, akik ehelyett az alapértelmezett tanúsítványt rögzítik, szigorú függőséget kapnak a nem általuk nem szabályozható tanúsítvány tulajdonságaitól, ami nem ajánlott.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőkre van szüksége:

-   Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Egy API Management-példány. További információ: [Create an Azure API Management instance](get-started-create-service-instance.md).
-   Egyéni tartománynév, amely az Ön vagy a szervezet tulajdonában van. Ez a témakör nem ad útmutatást az egyéni tartománynév beszerzéséhez.
-   Egy DNS-kiszolgálón tárolt CNAME rekord, amely az egyéni tartománynevet az API Management példány alapértelmezett tartománynevére képezi le. Ez a témakör nem ad útmutatást a CNAME rekord üzemeltetéséhez.
-   Érvényes tanúsítvánnyal kell rendelkeznie nyilvános és személyes kulccsal (. PFX). A tulajdonos vagy a tulajdonos alternatív nevének (SAN) meg kell egyeznie a tartománynévvel (ez lehetővé teszi, hogy az API Management-példány biztonságosan tegye elérhetővé az URL-címeket a TLS-en keresztül).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Egyéni tartománynév beállítása az Azure Portalon

1. Nyissa meg az API Management-példányt az [Azure Portalon.](https://portal.azure.com/)
1. Válassza **az Egyéni tartományok lehetőséget**.

    Számos végponthoz rendelhet egyéni tartománynevet. Jelenleg a következő végpontok érhetők el:

    - **Átjáró** (alapértelmezett: `<apim-service-name>.azure-api.net`)
    - **Portál** (az `<apim-service-name>.portal.azure-api.net`alapértelmezett érték: )
    - **Kezelés** (alapértelmezett: `<apim-service-name>.management.azure-api.net`)
    - **SCM** (alapértelmezett: `<apim-service-name>.scm.azure-api.net`)
    - **NewPortal** (az `<apim-service-name>.developer.azure-api.net`alapértelmezett érték: ).

    > [!NOTE]
    > Csak az **átjáró** végpont érhető el a konfiguráció a felhasználási rétegben.
    > Frissítheti az összes végpontot vagy néhányat. Az ügyfelek általában frissítik az **átjárót** (ez az URL-cím az API Management en keresztül elérhetővé tett API hívására szolgál) és a **Portálra** (a fejlesztői portál URL-címére).
    > **A felügyeleti** és **scm-végpontokat** csak az API Management-példány tulajdonosai használják belsőleg, és így ritkábban rendelnek egyéni tartománynevet.
    > A **prémium szintű** támogatja a beállítás több állomásnevét az **átjáró** végpont.

1. Jelölje ki a frissíteni kívánt végpontot.
1. A jobb oldali ablakban kattintson az **Egyéni gombra.**

    - Az **Egyéni tartománynév**mezőben adja meg a használni kívánt nevet. Például: `api.contoso.com`.
    - A **tanúsítványban**válasszon ki egy tanúsítványt a Key Vaultból. Érvényes . PFX fájlt, és adja meg **a jelszót**, ha a tanúsítvány jelszóval védett.

    > [!NOTE]
    > A helyettesítő tartománynevek, például a Felhasználás szint kivételével minden rétegben `*.contoso.com` támogatottak.

    > [!TIP]
    > Azt javasoljuk, hogy az Azure Key Vault a tanúsítványok kezeléséhez, és azokat az automatikus forgatásbeállításhoz.
    > Ha az Azure Key Vault használatával kezeli az egyéni tartomány TLS/SSL tanúsítvány, győződjön meg arról, hogy a tanúsítvány be van illesztve a Key Vault [ _tanúsítványként_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), nem _egy titkos._
    >
    > TLS/SSL-tanúsítvány lekéréséhez az API Managementnek rendelkeznie kell a listával, és titkos kulcsokat kell beszereznie a tanúsítványt tartalmazó Azure Key Vaulton. Az Azure Portal használatakor az összes szükséges konfigurációs lépés automatikusan befejeződik. A parancssori eszközök vagy felügyeleti API használataesetén ezeket az engedélyeket manuálisan kell megadni. Ez két lépésben történik. Először használja a Felügyelt identitások lapot az API Management-példányon, és győződjön meg arról, hogy a felügyelt identitás engedélyezve van, és jegyezze fel az adott lapon megjelenő elsődleges azonosítót. Másodszor adja meg az engedélylistát, és titkos kulcsokat kap a fő azonosító az Azure Key Vault a tanúsítványt tartalmazó.
    >
    > Ha a tanúsítvány automatikus elforgatásra van beállítva, az API Management automatikusan felveszi a legújabb verziót a szolgáltatás leállása nélkül (ha az API Felügyeleti szint SLA-val rendelkezik – azaz a fejlesztői szint kivételével minden rétegben).

1. Kattintson az Apply (Alkalmaz) gombra.

    > [!NOTE]
    > A tanúsítvány hozzárendelésének folyamata a telepítés méretétől függően 15 percet vagy többet is igénybe vehet. Fejlesztői termékváltozat rendelkezik állásidő, alapszintű és magasabb termékváltozatok nem rendelkeznek állásidő.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS-konfiguráció

Amikor a DNS-t az egyéni tartománynévhez konfigurálja, két lehetősége van:

-   Konfiguráljon egy CNAME-rekordot, amely a konfigurált egyéni tartománynév végpontjára mutat.
-   Konfiguráljon egy A-rekordot, amely az API Management átjáró IP-címére mutat.

> [!NOTE]
> Bár az API Managment példány IP-címe statikus, néhány esetben változhat. Emiatt ajánlott a CNAME használata az egyéni tartomány konfigurálásakor. Ezt vegye figyelembe a DNS-konfigurációs módszer kiválasztásakor. További információ [az IP dokumentációs cikkben](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) és az [API Management GYIK-ben.](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)

## <a name="next-steps"></a>További lépések

[A szolgáltatás frissítése és méretezése](upgrade-and-scale.md)
