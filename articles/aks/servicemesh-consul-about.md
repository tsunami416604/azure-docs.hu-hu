---
title: Konzul áttekintése
description: A konzul áttekintése
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594209"
---
# <a name="consul"></a>Konzul

## <a name="overview"></a>Áttekintés

[A Consul][consul] egy több adatközpont-tudatos szolgáltatáshálózati megoldás a szolgáltatások futásidejű platformokon történő összekapcsolására és biztonságossá tétele érdekében. [A Csatlakozás][consul-features] az az összetevő, amely szolgáltatásháló-képességeket biztosít.

## <a name="architecture"></a>Architektúra

Consul biztosít egy adatsík, amely áll [követ][envoy-proxy]-alapú [oldalkocsik][consul-sidecar] alapértelmezés szerint. A konzul csatlakoztatható proxyarchitektúrával rendelkezik. Ezek az intelligens proxyk szabályozzák az összes hálózati forgalmat az egybeesített alkalmazásokban és számítási feladatokban.

A vezérlősík a következő összetevőkön keresztül kezeli a konfigurációt és a [házirendet:][consul-architecture]

- **Kiszolgáló** – A konzuli fürtállapotot fenntartó kiszolgálómódban futó konzuli ügynök.

- **Ügyfél** – Könnyű ügyfélmódban futó konzuli ügynök. Minden számítási csomópontnak rendelkeznie kell egy futó ügyfélügynökkel. Ez az ügyfél brókerek konfiguráció és a szabályzat a számítási feladatok és a konzul konfigurációközött. 

Az alábbi architektúradiagram bemutatja, hogy az adatsíkon és a vezérlősíkon belüli különböző összetevők hogyan hatnak egymásra.

![A konzuli összetevők és az architektúra áttekintése.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Kiválasztási kritériumok

Fontos, hogy a számítási feladatok konzuljának értékelésekor megértse és figyelembe vegye a következő területeket:

- [Konzuli elvek](#consul-principles)
- [Funkciók](#capabilities)
- [Forgatókönyvek](#scenarios)


### <a name="consul-principles"></a>Konzuli elvek

A konzuli projektet a következő elvek [vezérelik:][consul-principles]

- **API-alapú** – Kodifikálja az összes konfigurációt és házirendet.

- **Futtatás és csatlakoztatásbárhol** – Számítási feladatok összekapcsolása futásidejű platformokon (Kubernetes, virtuális gépek, kiszolgáló nélküli).

- **Extend and Integrate** – A számítási feladatok biztonságos összekapcsolása az infrastruktúra között.


### <a name="capabilities"></a>Funkciók

A konzul a következő képességeket biztosítja:

- **Háló** – átjáró (több adatközpont), virtuális gépek (fürtcsomópontokon kívül), szolgáltatásszinkronizálás, beépített hibakeresési lehetőség

- **Proxyk** – Követ, beépített proxy, dugaszolható, l4 proxy elérhető windowsos számítási feladatokhoz

- **Forgalomirányítás** – útválasztás, felosztás, felbontás

- **Politika** – szándékok, ACL-k

- **Biztonság** – engedélyezés, hitelesítés, titkosítás, SPIFFE-alapú identitások, külső hitelesítésszolgáltató (Vault), tanúsítványkezelés és rotáció

- **Megfigyelhetőség** – metrikák, ui műszerfal, prometheus, grafana


### <a name="scenarios"></a>Forgatókönyvek

Konzul kiválóan alkalmas, és javasolt a következő forgatókönyvek:

- Meglévő konzulhoz csatlakoztatott munkaterhelések bővítése

- Megfelelőségi követelmények a tanúsítványkezelés körül

- Többfürtös szolgáltatásháló

- Virtuálisgép-alapú számítási feladatok, amelyeket a szolgáltatáshálóba kell foglalni



## <a name="next-steps"></a>További lépések

A következő dokumentáció ismerteti, hogyan telepítheti a Consul alkalmazást az Azure Kubernetes szolgáltatásra (AKS):

> [!div class="nextstepaction"]
> [Konzul telepítése az Azure Kubernetes szolgáltatásban (AKS)][consul-install]

A Konzul funkcióit és az építészetet is felfedezheti:

- [Konzul jellemzők][consul-features]
- [Konzul építészet][consul-architecture]
- [Consul - Hogyan működik a Connect][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
