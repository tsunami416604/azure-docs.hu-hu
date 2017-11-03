---
title: "Az Azure API Management-példány egyéni tartománynév beállítása |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure API Management-példány egyéni tartománynév beállítása."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: a7abba87035016eee05b4ab663404f10127bba1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-custom-domain-name"></a>Egyéni tartománynév konfigurálása 

API-felügyeleti (APIM) példányt hoz létre, amikor Azure hozzárendeli egy altartomány azure-api.net, (például `apim-service-name.azure-api.net`). Azonban Ön is elérhetővé teheti a saját tartománynevét, például a használó APIM végpontok **contoso.com**. Az oktatóanyag bemutatja, hogyan képezheti végpontok Azure API Management-példány által elérhetővé tett egy meglévő egyéni DNS-névvel.


## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie kell:

+ Aktív Azure-előfizetés.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM példánya. További információkért lásd: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Ön tulajdonában van egy egyéni tartománynevet. Az egyéni tartománynév szeretné használni, hálózatok külön-külön szerezhetők be legyen, és egy DNS-kiszolgálón lévő. Ez a témakör nem utasításokat ad az egyéni tartománynév üzemeltetése.
+ Érvényes tanúsítvány nyilvános és titkos kulccsal kell rendelkeznie (. PFX). Tulajdonos vagy a tulajdonos alternatív neve (SAN) meg kell egyeznie a a tartomány nevét (Ez lehetővé teszi a APIM biztonságosan teszi közzé az URL-címek SSL-en keresztül).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Egyéni tartománynév beállítása az Azure-portál használatával

1. Keresse meg a APIM példányhoz, illetve a [Azure-portálon](https://portal.azure.com/).
2. Válassza ki **egyéni tartományok és SSL**.
    
    Nincs több végpontot, amelyhez hozzárendelheti egy egyéni tartománynevet. Jelenleg a következő végpontok érhetők el: 
    + **Proxy** (alapértelmezett érték: `<apim-service-name>.azure-api.net`), 
    + **Portál** (alapértelmezett érték: `<apim-service-name>.portal.azure-api.net`),     
    + **Felügyeleti** (alapértelmezett érték: `<apim-service-name>.management.azure-api.net`), 
    + **Az SCM** (alapértelmezett érték: `<apim-service-name>.scm.azure-api.net`).

    >[!NOTE]
    > A végpontok vagy némelyikük frissítheti. Gyakran, az ügyfelek frissítése **Proxy** (az URL-cím segítségével az API Management keresztül közzétett API) és **Portal** (a fejlesztői portálján URL-cím). **Felügyeleti** és **SCM** végpontok belsőleg APIM ügyfelek kerül, és így ritkábban egy egyéni tartománynevet.
3. Válassza ki a frissíteni kívánt végpont. 
4. Kattintson a jobb oldalon látható **egyéni**.

    + Az a **egyéni tartománynév**, adja meg a használni kívánt nevet. Például: `api.contoso.com`. <br/>Helyettesítő karakteres tartománynevek (például *. tartomány.com) is támogatottak.
    + Az a **tanúsítvány**, adjon meg érvényes. PFX-fájlt, amelyet szeretne feltölteni. 
    + Ha a tanúsítvány jelszóval rendelkezik, akkor adja meg a a **jelszó** mező.
1. Az Alkalmaz gombra.

    >[!NOTE]
    >A tanúsítvány társítását 15 percig is eltarthat.

## <a name="next-steps"></a>Következő lépések

[Frissítés és a szolgáltatás méretezésére](upgrade-and-scale.md)