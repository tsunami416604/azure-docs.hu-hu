---
title: Cognitive Services kapuzás folyamat
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan alkalmazhatja az új Cognitive Services tárolók és API-k korai elérését.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599512"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Az Azure Cognitive Services kapuzás folyamata

> [!NOTE]
> Miután egy szolgáltatási ajánlat elvégezte a megtekintett előzetes verziót, egy "nem kihelyezett" nyilvános előzetes verzióba kerül, amely nem igényel hozzáférést az alkalmazáshoz. Az előnézeti folyamat után a rendszer általánosan elérhetőként (GA) jelenik meg.

Az új Azure Cognitive Services-ajánlatok bevezetésekor egy olyan megtekinthető előzetes verzióval haladnak át, ahol az ügyfeleknek hozzáférést kell kérniük egy alkalmazáson keresztül. Ez a kapuzás folyamat segít azonosítani a szolgáltatások ajánlatait, mielőtt azok széles körben elérhetővé válnak. 

Ez a cikk végigvezeti az alkalmazási folyamaton, amely a Cognitive Services-ajánlatokat kínálja.

## <a name="eligibility-and-approval-process"></a>Jogosultsági és jóváhagyási folyamat

A kapuzás folyamat segít felmérni a kamatot, és jobban megismerni az ügyfelek igényeit. A Microsoft csapata érvényes Azure-előfizetéssel és érvényes üzleti helyzettel fogadja el az [alkalmazásokat](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) a Microsoft kereskedelmi ügyfeleitől. Az ügyfelek valószínűleg megtagadják az alkalmazásaikat, ha:

 - Nincsenek társítva egyetlen szervezethez sem
 - Nem rendelkezik érvényes Azure-előfizetéssel
 - Az alkalmazás a személyes e-mailen keresztül lett elküldve ( @hotmail.com , @gmail.com , @yahoo.com )
 - Nem volt megadva megfelelő indoklás vagy üzleti forgatókönyv

A különböző felhasználói szegmensek iránti igény miatt a rendszer megkísérli a jóváhagyási folyamat meggyorsítását. Azonban nem lehet véglegesíteni az ütemtervet. A döntés meghozatala után a Microsoft csapata a következő lépésekhez felveszi Önnel a kapcsolatot Önnel és a fiók-felügyeleti csapatával. Nagyra értékeljük a megértést és a türelmet.

Ha az alkalmazást jóváhagyják, a Microsoft csapata a részleteket, a dokumentációt és az útmutatást tartalmazó e-mailt küld Önnek. Az [itt](https://azure.microsoft.com/pricing/details/cognitive-services/)elérhető díjszabási részletek Cognitive Services.


Az alábbi szolgáltatások jelenleg a kapuzás folyamaton keresztül érhetők el:

## <a name="gated-web-apis"></a>Levezérelt webes API-k

|Szolgáltatás  |
|---------|
|Anomália detektor v2     | 

## <a name="gated-online-containers"></a>Lezárt online tárolók

| Szolgáltatás                             | Tároló (k)                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Computer Vision][cv-containers]    | Olvasás                                                                          |
| [Face][fa-containers]               | Face                                                                          |
| [Form Recognizer][fr-containers]    | Form Recognizer                                                               |
| [Speech Service API][sp-containers] | Beszéd – szöveg (egyéni és standard) és szöveg-beszéd (egyéni és standard) |
| [Translator Text][tt-containers]    | Translator Text                                                               |

> [!IMPORTANT]
> Ha egy szolgáltatás vagy tárolón kívüli ajánlat nem szerepel a listáján, akkor nem vagy nem elérhető, vagy nem érhető el.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Regisztráció a levezérelt szolgáltatásokra](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
