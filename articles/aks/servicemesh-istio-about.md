---
title: Istio áttekintése
description: Az Istio áttekintése
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593900"
---
# <a name="istio"></a>Istio között

## <a name="overview"></a>Áttekintés

[Istio][istio] egy teljes funkcionalitású, testreszabható, és bővíthető szolgáltatás háló.

## <a name="architecture"></a>Architektúra

Istio biztosít egy adatsík, amely áll [követ-alapú][envoy-proxy]oldalkocsik. Ezek az intelligens proxyk szabályozzák az összes hálózati forgalmat az egybeesített alkalmazásokban és számítási feladatokban.

A vezérlősík a következő összetevőkön keresztül kezeli a konfigurációt, a házirendet és a telemetriai [adatokat:][what-is-istio]

- **Mixer** – Hozzáférés-vezérlési és -használati házirendek kényszerítése. A [Prometheusba][prometheus]telemetriai adatokat gyűjt.

- **Próba –** Szolgáltatásfelderítési és forgalomkezelési házirendet/-konfigurációt biztosít a proxykszámára.

- **Citadel -** Biztosítja az identitás és a biztonsági képességek, amelyek lehetővé teszik az mTLS szolgáltatások között.

- **Hasáb** – Absztrakt, és konfigurációt biztosít az összetevőkhöz.

Az alábbi architektúradiagram bemutatja, hogy az adatsíkon és a vezérlősíkon belüli különböző összetevők hogyan hatnak egymásra.


![Istio-összetevők és architektúra áttekintése.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Kiválasztási kritériumok

Fontos, hogy az Istio számítási feladatainak értékelésekor megértse és vegye figyelembe a következő területeket:

- [Tervezési célok](#design-goals)
- [Funkciók](#capabilities)
- [Forgatókönyvek](#scenarios)


### <a name="design-goals"></a>Tervezési célok

A következő tervezési célok [irányítják][design-goals] az Istio projektet:

- **Átláthatóság maximalizálása** – Lehetővé teszi az elfogadást a minimális munkamennyiséggel, hogy valódi értéket kapjon a rendszerből.

- **Bővíthetőség** - Képesnek kell lennie arra, hogy növekedjen és alkalmazkodjon a változó igényekhez.

- **Hordozhatóság** – Egyszerűen futtatható különböző környezetekben – felhőalapú, helyszíni környezetekben.

- **Házirend egységessége** – a házirend-definíció konzisztenciája az erőforrások sokféleségében.


### <a name="capabilities"></a>Funkciók

Az Istio a következő képességeket biztosítja:

- **Háló** – átjárók (többfürtös), virtuális gépek (hálóbővítés)

- **Forgalomirányítás** – útválasztás, felosztás, időmegosztás, megszakítók, újrapróbálkozások, bejutás, kijutás

- **Házirend** – hozzáférés-vezérlés, sebességkorlát, kvóta, egyéni házirend-adapterek

- **Biztonság** – hitelesítés (jwt), engedélyezés, titkosítás (mTLS), külső hitelesítésszolgáltató (HashiCorp Vault)

- **Megfigyelhetőség** – arany mérőszámok, tükör, nyomkövetés, egyedi adapterek, prometheus, grafana

### <a name="scenarios"></a>Forgatókönyvek

Istio kiválóan alkalmas, és javasolt a következő forgatókönyvek:

- Bővíthetőség és gazdag képességek megkövetelése

- Hálóbővítés virtuálisgép-alapú számítási feladatokhoz

- Többfürtös szolgáltatásháló

## <a name="next-steps"></a>További lépések

A következő dokumentáció ismerteti, hogyan telepítheti az Istio-t az Azure Kubernetes szolgáltatásra (AKS):

> [!div class="nextstepaction"]
> [Istio telepítése az Azure Kubernetes szolgáltatásban (AKS)][istio-install]

Az Istio-fogalmakat és további telepítési modelleket is felfedezheti:

- [Istio fogalmak][what-is-istio]
- [Istio telepítési modellek][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
