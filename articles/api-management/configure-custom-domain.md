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
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: f613995dbdd787d0a031cb2c24d67c682b2d7cec
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446372"
---
# <a name="configure-a-custom-domain-name"></a>Egyéni tartománynév konfigurálása 

Az API Management (APIM) példány létrehozásakor az Azure hozzárendeli részterületét képezi az azure-API.NET webhelyen (például `apim-service-name.azure-api.net`). Azonban tehetők közzé a saját tartománynév használatával például az APIM-végpontok **contoso.com**. Ez az oktatóanyag bemutatja, hogyan meglévő egyéni DNS-név leképezése az Azure API Management-példány által elérhetővé tett végpontok.

> [!WARNING]
> Azzal, hogy alkalmazásaikat a biztonság növelése érdekében használja a tanúsítvány rögzítését kell használnia az egyéni tartománynév > és a tanúsítvány kezeli, amely nem az alapértelmezett tanúsítvány. Ügyfelek, amelyek az alapértelmezett tanúsítvány rögzítése helyett lesz > rögzített függőség véve nem szabályozzák, a tanúsítvány tulajdonságait, ez nem ajánlott.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM-példány. További információkért lásd: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
+ Egyéni tartománynév, amely az Ön tulajdonában van. Az egyéni tartománynevet szeretne használni, külön-külön beszerzett legyen, és egy DNS kiszolgálón lévő üzemeltetett. Ez a témakör bemutatja, hogyan működtethető az egyéni tartománynév a nem ad utasításokat.
+ Rendelkeznie kell érvényes tanúsítványt és a egy nyilvános és titkos kulcs (. PFX). Tulajdonos vagy a tulajdonos alternatív neve (SAN) meg kell egyeznie a a tartomány nevét (Ez lehetővé teszi a APIM, hogy biztonságosan URL-címek SSL-en keresztül).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Egyéni tartománynév beállítása az Azure portal használatával

1. Keresse meg az APIM-példányra, az a [az Azure portal](https://portal.azure.com/).
2. Válassza ki **egyéni tartományok és SSL**.
    
    Nincs számos végpontot, amelyhez hozzárendelhet egy egyéni tartomány nevét. Jelenleg az alábbi végpontok érhetők el: 
    + **Proxy** (alapértelmezett érték: `<apim-service-name>.azure-api.net`), 
    + **Portál** (alapértelmezett érték: `<apim-service-name>.portal.azure-api.net`),     
    + **Felügyeleti** (alapértelmezett érték: `<apim-service-name>.management.azure-api.net`), 
    + **SCM** (alapértelmezett érték: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > A végpontok egy része vagy azokat frissítheti. Gyakran, az ügyfelek frissítéséhez **Proxy** (az URL-cím segítségével az API Management szolgáltatáson keresztül közzétett API-t) és **portál** (a fejlesztői portál URL-cím). **Felügyeleti** és **SCM** végpontok belsőleg az APIM-ügyfelek, és így kevésbé gyakran hozzárendelt egy egyéni tartomány nevét.
3. Válassza ki a frissíteni kívánt végpontot. 
4. Kattintson a jobb oldali ablakban **egyéni**.

    + Az a **egyéni tartománynév**, adja meg a használni kívánt nevét. Például: `api.contoso.com`. <br/>Helyettesítő karaktereket tartalmazó tartománynevek (például *. tartomány.com) is támogatottak.
    + Az a **tanúsítvány**, adjon meg egy érvényes. Feltölteni kívánt PFX-fájlt. 
    + Ha a tanúsítvány jelszava, írja be a a **jelszó** mező.
1. Kattintson a alkalmazni.

    >[!NOTE]
    >A tanúsítvány társítását 15 percet vagy többet a központi telepítés méretétől függően eltarthat. Termékváltozat fejlesztőnek állásidő, alapszintű és a magasabb szintű Termékváltozat nem állásidővel kell számolnia.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>További lépések

[Frissítés és skálázás az a szolgáltatás](upgrade-and-scale.md)
