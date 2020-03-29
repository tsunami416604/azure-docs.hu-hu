---
title: Tárolóbiztonság
titleSuffix: Azure Cognitive Services
description: További információ a tároló védelméről
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: 20f78d9269d4b2270293c8746157ba495c694562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272633"
---
## <a name="azure-cognitive-services-container-security"></a>Az Azure Cognitive Services tárolójának biztonsága

A biztonságnak kell elsődleges fókuszt kell, hogy legyen, amikor alkalmazásokat fejleszt. A biztonság fontossága a siker mérőszáma. Ha olyan szoftvermegoldást tervez, amely kognitív szolgáltatások tárolóit is tartalmazza, létfontosságú, hogy megértse a rendelkezésre álló korlátozásokat és képességeket. A hálózati biztonságról az [Azure Cognitive Services virtuális hálózatok konfigurálása][az-security]című témakörben olvashat bővebben.

> [!IMPORTANT]
> Alapértelmezés szerint *nincs biztonság* a Cognitive Services tároló API-t. Ennek az az oka, hogy a tároló leggyakrabban egy olyan pod részeként fog futni, amelyet egy hálózati híd véd kívülről. A [felhőalapú Cognitive Services][request-authentication]elérésekor használt hitelesítéssel azonos módon működő hitelesítés azonban engedélyezésre is lehetőség van.

Az alábbi ábra az alapértelmezett és **nem biztonságos** megközelítést mutatja be:

![Tárolóbiztonság](../media/container-security.svg)

Alternatív és *biztonságos* megközelítésként a Cognitive Services-tárolók fogyasztói kiegészíthetik a tárolót egy elülső összetevővel, így a tároló végpontja privát. Vegyünk egy forgatókönyvet, amelyben az [Istio-t][istio] intress átjáróként használjuk. Istio támogatja a HTTPS/TLS és az ügyfél-tanúsítvány hitelesítése. Ebben a forgatókönyvben az Istio előtér elérhetővé teszi a tároló-hozzáférést, és bemutatja az istio-val előzetesen engedélyezési listán szereplő ügyféltanúsítványt.

[Nginx][nginx] egy másik népszerű választás ugyanabban a kategóriában. Mind az Istio, mind a Nginx szolgáltatáshálóként működik, és további funkciókat kínál, például terheléselosztást, útválasztást és díjvezérlést.

### <a name="container-networking"></a>Tárolóalapú hálózatkezelés

A Cognitive Services-tárolók számlázási célokra szükséges mérési adatok at kell megadni. Az egyetlen kivétel, az *offline tárolók,* mivel más számlázási módszert követnek. Ha nem engedélyezi a különböző hálózati csatornák listáját, amelyekre a Cognitive Services-tárolók támaszkodnak, megakadályozza a tároló működését.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Cognitive Services-tartományok és portok listájának engedélyezése

Az állomásnak engedélyeznie kell a **443-as listaportot** és a következő tartományokat:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Mély csomagvizsgálat letiltása

> [A mélycsomag-ellenőrzés](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) olyan adatfeldolgozási típus, amely részletesen ellenőrzi a számítógépes hálózaton keresztül küldött adatokat, és általában ennek megfelelően blokkolja, átirányítja vagy naplózza azokat.

Tiltsa le a DPI-t a Cognitive Services-tárolók által a Microsoft-kiszolgálókszámára létrehozott biztonságos csatornákon. Ennek elmulasztása megakadályozza a tároló megfelelő működését.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
