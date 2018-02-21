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
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: 96e233a26af95d4373323867046ca01fe1304608
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="configure-a-custom-domain-name"></a>Egyéni tartománynév konfigurálása 

API-felügyeleti (APIM) példányt hoz létre, amikor Azure hozzárendeli egy altartomány azure-api.net, (például `apim-service-name.azure-api.net`). Azonban Ön is elérhetővé teheti a saját tartománynevét, például a használó APIM végpontok **contoso.com**. Az oktatóanyag bemutatja, hogyan képezheti végpontok Azure API Management-példány által elérhetővé tett egy meglévő egyéni DNS-névvel.

> [!WARNING]
> Az ügyfelek, akik kíván használni a tanúsítvány rögzítését, melyekkel biztonságosabbá teheti az alkalmazások kell használnia egy egyéni tartománynevet > és a tanúsítvány kezelnek, amelyek nem az alapértelmezett tanúsítvány. PIN-kód az alapértelmezett tanúsítvány ügyfelek inkább lesz > erős függőség véve nem szabályozzák, a tanúsítvány tulajdonságainak, ez nem ajánlott.

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
    >A tanúsítvány társítását is igénybe vehet, 15 perc vagy több, a központi telepítés méretétől függően. Fejlesztői SKU állásidő, a alapvető és magasabb SKU nincs leállás.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>További lépések

[Frissítés és a szolgáltatás méretezésére](upgrade-and-scale.md)
