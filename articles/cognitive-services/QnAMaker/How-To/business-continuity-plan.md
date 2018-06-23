---
title: A kérdések és válaszok készítő szolgáltatás - Microsoft kognitív szolgáltatások üzleti continuty terv létrehozása |} Microsoft Docs
titleSuffix: Azure
description: Egy üzletmenet folytonosságát biztosító terve a kérdések és válaszok készítő szolgáltatás létrehozása
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348607"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>A kérdések és válaszok készítő szolgáltatás üzleti folytonossági terv létrehozása

Az üzletmenet folytonosságát biztosító terve elsődleges célja a hozzon létre egy rugalmas Tudásbázis végpontot, amely állásidő nem biztosítja a Botot vagy az alkalmazás fel azt.

![Kérdések és válaszok készítő bcp terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A magas szintű ötlet ahogy fent a következőképpen történik:

1. Állítson be két párhuzamos [kérdések és válaszok készítő szolgáltatások](../How-To/set-up-qnamaker-service-azure.md) a [Azure párosítva régiók](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

2. Az elsődleges és másodlagos Azure search-indexek szinkronban tartsa. A github-minta [Itt](https://github.com/pchoudhari/QnAMakerBackupRestore) történő biztonsági mentés-visszaállítás Azure indexeket.

3. Készítsen biztonsági másolatot az Application Insights segítségével [a folyamatos exportálás](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry).

4. Ha az elsődleges és másodlagos verem hoztak létre, a [a traffic manager](https://docs.microsoft.com/en-us/azure/traffic-manager/) konfigurálja a végpontokat, és állítson be egy útválasztási módszer.

5. A traffic manager-végpont az SSL-tanúsítvány létrehozásához kellene. [Az SSL-tanúsítvány kötését](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl) az App services szolgáltatásban.

6. Végül használja a traffic manager-végpont a Botot vagy az alkalmazás.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Válassza ki az a kérdés-válasz készítő telepítésre vonatkozó kapacitás](../Tutorials/choosing-capacity-qnamaker-deployment.md)
