---
title: Üzletmenet-folytonossági terv - QnA Maker
description: Az üzletmenet-folytonossági terv elsődleges célja, hogy hozzon létre egy rugalmas tudásbázis-végpont, amely biztosítja a robot vagy az alkalmazás fogyasztó számára állási idő.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887068"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Üzletmenet-folytonossági terv létrehozása a QnA Maker szolgáltatáshoz

Az üzletmenet-folytonossági terv elsődleges célja, hogy hozzon létre egy rugalmas tudásbázis-végpont, amely biztosítja a robot vagy az alkalmazás fogyasztó számára állási idő.

## <a name="business-continuity-with-traffic-manager"></a>Üzletmenet-folytonosság a forgalomkezelővel

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A fent szereplő magas szintű elképzelés a következő:

1. Két párhuzamos [QnA Maker-szolgáltatás](set-up-qnamaker-service-azure.md) beállítása az [Azure párosított régióiban.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

2. [Készítsen biztonsági másolatot](../../../app-service/manage-backup.md) az elsődleges QnA Maker-alkalmazásszolgáltatásról, és [állítsa vissza](../../../app-service/web-sites-restore.md) a másodlagos beállításban. Ez biztosítja, hogy mindkét beállítás ugyanazzal az állomásnévvel és kulccsal működjön.

3. Tartsa szinkronban az elsődleges és másodlagos Azure-keresési indexeket. A GitHub-minta [itt](https://github.com/pchoudhari/QnAMakerBackupRestore) megtudhatja, hogyan biztonsági másolatot készíteni az Azure-indexek biztonsági mentését.

4. Az Application Insights biztonsági másolatot készít [a folyamatos exportálás sal.](../../../application-insights/app-insights-export-telemetry.md)

5. Az elsődleges és másodlagos halmok beállítása után a [traffic manager](../../../traffic-manager/traffic-manager-overview.md) segítségével konfigurálja a két végpontot, és állítson be egy útválasztási módszert.

6. Létre kell hoznia egy Transport Layer Security (TLS), korábbi nevén Secure Sockets Layer (SSL), tanúsítványt a traffic manager végpont. [Kösse meg a TLS/SSL tanúsítványt](../../../app-service/configure-ssl-bindings.md) az alkalmazásszolgáltatásokban.

7. Végül használja a traffic manager végpontot a robot ban vagy az alkalmazásban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kapacitás kiválasztása](./improve-knowledge-base.md)