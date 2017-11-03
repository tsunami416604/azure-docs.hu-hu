---
title: "Egyéni összekötő gyakori kérdések – Azure Logic Apps |} Microsoft Docs"
description: "Követelmények, eseményindítók, és így tovább egyéni összekötők létrehozásával kapcsolatos gyakori kérdések"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>Gyakran ismételt kérdések: Egyéni összekötők

## <a name="requirements"></a>Követelmények

**K:** is készíthetők összekötő REST API-k nélkül? </br>
**V:** nem, hozhat létre egy összekötőt, amelyeket támogatnia stabil HTTP REST API-k a szolgáltatás. 

**K:** milyen eszközök használhatók hozzon létre egy összekötőt? </br>
**V:** Azure rendelkezik képességek és a szolgáltatások, mint egy API-t, például az Azure App Service API Management és több üzemeltetésére bármely szolgáltatás közzétételénél használható.

**K:** milyen hitelesítési típusok támogatottak? </br>
**V:** támogatott hitelesítési megfelelést használhatja:

* [OAuth 2.0](https://oauth.net/2/), többek között a következőket [Azure Active Directory](https://azure.microsoft.com/develop/identity/) vagy adott szolgáltatások, például a dropbox-ba, a Githubon és a SalesForce
* Általános OAuth 2.0
* [Az egyszerű hitelesítés](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [Az API-kulcs](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>Eseményindítók

**K:** is készíthetők eseményindítók webhookok nélkül? </br>
**V:** nem, az Azure Logic Apps és a Microsoft Flow támogatási csak webhook-alapú eseményindítók egyéni összekötők. Ha kíván igényelni más minták végrehajtásához, forduljon a [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com) amelyen további információkat talál az API-t.

## <a name="certification"></a>Minősítés

**A Q**: a számítógépen nem egy Microsoft partnert vagy egy független szoftverszállító (ISV). Továbbra is létrehozhatók összekötők? </br>
**A**: Igen, a szervezet regisztrálhatja az összekötők, belső használatra, de ha szeretné igazolja, és nyilvánosan kiadási az összekötőt, kell vagy saját a mögöttes szolgáltatás vagy a jelenlegi explicit jogosultsága ahhoz, hogy az API-val.

## <a name="other"></a>Egyéb

**K:** saját API-kat használjon dinamikus gazdagépet. Hogyan tegye végrehajtja a azokat a OpenAPI? </br>
**V:** egyéni összekötők dinamikus gazdagépek nem támogatják. Ehelyett használjon statikus gazdagépet fejlesztési és tesztelési célokra. Ha azt szeretné, az összekötő tanúsítására, kérje a Microsoft dinamikus megvalósításával kapcsolatos.

**K:** támogatják a Postman gyűjtemény V2? </br>
**V:** nem, csak a Postman gyűjtemény V1 jelenleg támogatott.

**K:** támogatják OpenAPI 3.0? </br>
**V:** nem, csak OpenAPI 2.0 jelenleg támogatott.