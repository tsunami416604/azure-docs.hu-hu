---
title: Azure-beli ív-adatkezelő létrehozása a Azure Portalban
description: Azure-beli ív-adatkezelő létrehozása a Azure Portalban
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 297efa83fb1563e3a360f652a6ac1bc2b1679998
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939981"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Azure-beli ív-adatkezelő létrehozása a Azure Portalban

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Bevezetés

Az Azure Portal használatával létrehozhat egy Azure arc-adatkezelőt.

Az Azure arc többek között az Azure Portal, noha a létrehozandó vagy kezelt erőforrás az Azure-infrastruktúrán kívül esik. A felhasználói élmény mintája ezekben az esetekben, különösen akkor, ha nincs közvetlen kapcsolat az Azure és a környezet között, a Azure Portal használatával hozzon létre egy parancsfájlt, amely azután letöltheti és végrehajthatja a környezetében, hogy biztonságos kapcsolatot hozzon létre az Azure-ba. Az Azure arc-kompatibilis kiszolgálók például ezt a mintát követik az [ív használatára képes kiszolgálók létrehozásához](../servers/onboard-portal.md).

Egyelőre, mivel az előzetes verzió csak az Azure arc-kompatibilis adatszolgáltatások közvetett csatlakoztatott üzemmódját támogatja, a Azure Portal használatával létrehozhat egy jegyzetfüzetet, amely letölthető és futtatható Azure Data Studio a Kubernetes-fürtön. A későbbiekben, ha a közvetlenül csatlakoztatott mód elérhető, az adatkezelőt közvetlenül a Azure Portal is kiépítheti. További információt a [kapcsolati módokról](connectivity.md)itt olvashat.

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Azure-beli ív-adatkezelő létrehozása a Azure Portal használatával

Az alábbi lépéseket követve hozzon létre egy Azure arc-adatkezelőt a Azure Portal és a Azure Data Studio használatával.

1. Először jelentkezzen be a [Azure Portal Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)-re.  A piactér keresési eredményei úgy lesznek szűrve, hogy megjelenjenek az "Azure arc adatkezelője".
2. Ha az első lépés nem adta meg a keresési feltételeket. Adja meg a keresési eredményeket, kattintson az Azure arc-adatkezelő elemre.
3. Válassza ki az Azure adatkezelő csempét a piactéren.
4. Kattintson a **Létrehozás** gombra.
5. Tekintse át az Azure arc-adatkezelő létrehozásának követelményeit, és telepítse a hiányzó előfeltételként szükséges szoftvereket, például a Azure Data Studio és a kubectl.
6. Kattintson az **adatkezelő adatai** gombra.
7. Válassza ki az előfizetést, az erőforráscsoportot és az Azure-beli helyet ugyanúgy, mint bármely más, a Azure Portalban létrehozott erőforrást. Ebben az esetben a kiválasztott Azure-beli hely lesz az erőforrás metaadatainak tárolása.  Maga az erőforrás minden Ön által választott infrastruktúrán létrejön. Nem kell az Azure-infrastruktúrán lennie.
8. Adja meg az adatvezérlő nevét.
9. Jelenleg csak a közvetett módon csatlakoztatott mód támogatott az előzetes verzióban.
10. Válasszon egy központi telepítési konfigurációs profilt.
11. Kattintson a **Megnyitás az Azure Studióban** gombra.
12. A következő képernyőn megjelenik a kiválasztott elemek és a létrehozott jegyzetfüzetek összegzése.  A jegyzetfüzet letöltéséhez kattintson a **kiépítési jegyzetfüzet letöltése** gombra.
13. Nyissa meg Azure Data Studio a jegyzetfüzetet, és kattintson a felül található **összes futtatása** gombra.
14. Az adatvezérlő létrehozásának befejezéséhez kövesse a jegyzetfüzetben megjelenő utasításokat és útmutatást.

## <a name="monitoring-the-creation-status"></a>A létrehozási állapot figyelése

A vezérlő létrehozása több percet is igénybe vehet. A következő parancsokkal figyelheti a folyamatot egy másik terminál ablakban:

> [!NOTE]
>  Az alábbi parancsok azt feltételezik, hogy létrehozott egy adatkezelőt és egy Kubernetes-névteret az "arc" névvel.  Ha más névtér-vagy adatvezérlő-nevet használt, az "ív" kifejezés helyére a nevét használhatja.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Egy adott Pod létrehozási állapotát is megtekintheti egy, az alábbihoz hasonló parancs futtatásával.  Ez különösen hasznos az esetleges problémák elhárításához.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>A létrehozási problémák elhárítása

Ha bármilyen problémába ütközik a létrehozással kapcsolatban, tekintse meg a [hibaelhárítási útmutatót](troubleshoot-guide.md).
