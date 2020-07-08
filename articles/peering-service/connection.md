---
title: Azure-partneri szolgáltatás kapcsolatai
description: További tudnivalók a Microsoft Azure társ-létesítési szolgáltatás kapcsolatairól
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: df337736a676c40f1345310afb9a2e685ddc1d5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84872021"
---
# <a name="peering-service-connection"></a>Társ-szolgáltatási kapcsolatok

A kapcsolatok általában egy logikai adatkészletre hivatkoznak, amely egy társ-szolgáltatás azonosítására szolgál. Meghatározása a következő attribútumok megadásával történik:

- Logikai név
- Csatlakozási partner
- Az ügyfél fizikai helye
- IP-előtagok

Az ügyfél a követelménynek megfelelően egyetlen kapcsolatot vagy több kapcsolatot hozhat létre. A rendszer a telemetria-gyűjtemények egységét is használja. Ha például telemetria-riasztásokat szeretne használni, akkor az ügyfélnek meg kell határoznia a figyelni kívánt kapcsolatokat.

> [!Note]
> Ha regisztrál a partneri szolgáltatásra, a Windows és az Office 365 telemetria elemezzük, hogy a kiválasztott előtagok késési mérőszámait is meg lehessen adni.
>A kapcsolódási telemetria kapcsolatos további információkért tekintse meg a társítási [szolgáltatás kapcsolódási telemetria](connection-telemetry.md).
>

>## <a name="how-to-register-a-connection"></a>Hogyan regisztrálhatok egy kapcsolatokat?

**Forgatókönyv** – tegyük fel, hogy a fiókirodák különböző földrajzi helyekre oszlanak, az alábbi ábrán látható módon. Ebben az esetben az ügyfélnek meg kell adnia a logikai nevet, a szolgáltatót (SP), az ügyfél fizikai helyét, valamint azokat az IP-előtagokat, amelyeket az ügyfél vagy a szolgáltató rendelt el, és amelyek egyetlen kapcsolathoz vannak társítva. Ezt a folyamatot meg kell ismételni a társítási szolgáltatás a különböző geo-redundáns kapcsolatokhoz való regisztrálásához.

![Geo redundáns kapcsolatok](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> Az állapot szintje – a rendszer az ügyfél fizikai helyét veszi figyelembe, amikor a Kapcsolódás a Egyesült Államok található.
>

## <a name="next-steps"></a>További lépések

Ha szeretne többet megtudni a peering Service-kapcsolatok regisztrálásának lépéseiről, tekintse meg a következő témakört: [a társ-nyilvántartó szolgáltatás regisztrálása a Azure Portal használatával](azure-portal.md).

A társ-szolgáltatási kapcsolatok telemetria kapcsolatos további tudnivalókért lásd: társítási [szolgáltatás kapcsolódási telemetria](connection-telemetry.md).

A telemetria méréséhez lásd: a [kapcsolatok telemetria mérése](measure-connection-telemetry.md).
