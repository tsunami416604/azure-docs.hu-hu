---
title: A Linkerd áttekintése
description: A Linkerd áttekintése
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593767"
---
# <a name="linkerd"></a>Linkerd között

## <a name="overview"></a>Áttekintés

[Linkerd][linkerd] egy könnyen használható és könnyű szolgáltatás háló.

## <a name="architecture"></a>Architektúra

A Linkerd egy ultrakönnyű [Linkerd][linkerd-proxy] speciális proxy oldalkocsikból álló adatsíkot biztosít. Ezek az intelligens proxyk szabályozzák az összes hálózati forgalmat az egybeesített alkalmazásokban és számítási feladatokban. A proxyk is elérhetővé teszik a metrikák at [Prometheus][prometheus] metrikák végpontok.

A vezérlősík a konfigurációt és az összesített [telemetriai adatokat][linkerd-architecture]a következő összetevőkön keresztül kezeli:

- **Controller** - Biztosítja api, amely hajtja a Linkerd CLI és a Dashboard. A proxyk konfigurációját biztosítja.

- **Koppintás** – Valós idejű órák létrehozása kérésekre és válaszokra.

- **Identitás** – Olyan identitás- és biztonsági képességeket biztosít, amelyek lehetővé teszik az mTLS szolgáltatások közötti futtatását.

- **Web** - Biztosítja a Linkerd irányítópultot.


Az alábbi architektúradiagram bemutatja, hogy az adatsíkon és a vezérlősíkon belüli különböző összetevők hogyan hatnak egymásra.


![A Linkerd összetevőinek és architektúrájának áttekintése.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Kiválasztási kritériumok

Fontos, hogy a Linkerd számítási feladatokhoz való értékelésekor megértse és figyelembe vegye a következő területeket:

- [Tervezési elvek](#design-principles)
- [Funkciók](#capabilities)
- [Forgatókönyvek](#scenarios)


### <a name="design-principles"></a>Tervezési alapelvek

A Linkerd projektet a következő tervezési elvek [vezérelik:][design-principles]

- **Keep it Simple** - Legyen könnyen használható és érthető.

- **Erőforrás-követelmények minimalizálása** – Minimális teljesítmény- és erőforrásköltség.

- **Just Work** – Ne szakítsa meg a meglévő alkalmazásokat, és ne igényeljön összetett konfigurációt.


### <a name="capabilities"></a>Funkciók

A Linkerd a következő képességeket biztosítja:

- **Háló** – beépített hibakeresési lehetőség

- **Forgalomirányítás** – felosztás, időmegosztások, újrapróbálkozások, be- és be- és visszatöltés

- **Biztonság** – titkosítás (mTLS), tanúsítványok autorotated 24 óránként

- **Megfigyelhetőség** – arany metrikák, koppintás, nyomkövetés, szolgáltatásprofilok és útvonal-mérőszámok, web irányítópult topológiai grafikonokkal, prométheusz, grafana


### <a name="scenarios"></a>Forgatókönyvek

A Linkerd a következő forgatókönyvekhez megfelelő és ajánlott:

- Egyszerűen használható a képességekkel kapcsolatos alapvető követelményekkel

- Alacsony késleltetés, alacsony rezsi, a megfigyelhetőségre és az egyszerű forgalomirányításra összpontosítva


## <a name="next-steps"></a>További lépések

A következő dokumentáció bemutatja, hogyan telepítheti a Linkerd szolgáltatást az Azure Kubernetes szolgáltatásra (AKS):

> [!div class="nextstepaction"]
> [A Linkerd telepítése az Azure Kubernetes szolgáltatásban (AKS)][linkerd-install]

A Linkerd szolgáltatásait és architektúráját is felfedezheti:

- [Linkerd jellemzők][linkerd-features]
- [Linkerd architektúra][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md