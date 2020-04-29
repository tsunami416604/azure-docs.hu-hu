---
title: Üzletmenet-folytonossági terv – QnA Maker
description: Az üzletmenet-folytonossági terv elsődleges célja, hogy egy rugalmas Tudásbázis-végpontot hozzon létre, amely nem gondoskodik a bot vagy az alkalmazás elfogyasztásának leállásáról.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887068"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Üzletmenet-folytonossági terv létrehozása a QnA Maker szolgáltatáshoz

Az üzletmenet-folytonossági terv elsődleges célja, hogy egy rugalmas Tudásbázis-végpontot hozzon létre, amely nem gondoskodik a bot vagy az alkalmazás elfogyasztásának leállásáról.

## <a name="business-continuity-with-traffic-manager"></a>Üzletmenet-folytonosság a Traffic Managerrel

> [!div class="mx-imgBorder"]
> ![QnA Maker BCP-terv](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A fentiekben képviselt magas szintű ötlet a következő:

1. Két párhuzamos QnA Maker- [szolgáltatás](set-up-qnamaker-service-azure.md) beállítása az [Azure párosított régiókban](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Készítsen biztonsági másolatot](../../../app-service/manage-backup.md) az elsődleges QnA Maker app Service-ről, és [állítsa vissza](../../../app-service/web-sites-restore.md) a másodlagos telepítésben. Ez biztosítja, hogy mindkét beállítás ugyanazzal az állomásnévvel és kulcsokkal működjön.

3. Tartsa szinkronban az elsődleges és a másodlagos Azure Search-indexeket. A GitHub [-minta használatával](https://github.com/pchoudhari/QnAMakerBackupRestore) megtekintheti, hogyan állíthatja vissza az Azure indexeket.

4. A Application Insights biztonsági mentése [folyamatos exportálással](../../../application-insights/app-insights-export-telemetry.md).

5. Az elsődleges és másodlagos csomagok beállítása után a [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) segítségével konfigurálja a két végpontot, és állítson be útválasztási módszert.

6. Létre kell hoznia egy Transport Layer Security (TLS), korábbi nevén SSL (SSL) tanúsítványt a Traffic Manager-végponthoz. [A TLS/SSL-tanúsítvány kötése](../../../app-service/configure-ssl-bindings.md) az App Servicesben.

7. Végül használja a Traffic Manager-végpontot a robotban vagy az alkalmazásban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kapacitás kiválasztása](./improve-knowledge-base.md)