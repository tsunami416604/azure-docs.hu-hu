---
title: Internetes elemző ügyfél beágyazása | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan ágyazhatja be az Internet Analyzer JavaScript-ügyfelet az alkalmazásba.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74896382"
---
# <a name="embed-the-internet-analyzer-client"></a>Az Internet Analyzer ügyfél beágyazása

Ez a cikk bemutatja, hogyan ágyazhatja be a JavaScript-ügyfelet az alkalmazásba. Az ügyfél telepítése szükséges a tesztek futtatásához és a scorecard-elemzés fogadásához. **A profilspecifikus JavaScript-ügyfél az első teszt konfigurálása után kerül megadva.** Innen új parancsfájl beágyazása nélkül is hozzáadhat vagy eltávolíthat teszteket a profilhoz. Az Internet Analyzer programról további információt az [áttekintésben talál.](internet-analyzer-overview.md) 

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

Az Internet Analyzer megfelelő működéséhez hozzáférést kell biztosítani az Azure-hoz és más Microsoft-szolgáltatásokhoz. Az ügyfél beágyazása előtt engedélyezze a `fpc.msedge.net` hálózati hozzáférést és az előre konfigurált végpontOK URL-címeit (a [CLI-n](internet-analyzer-cli.md)keresztül látható).

## <a name="find-the-client-script-url"></a>Az ügyfélparancsfájl URL-címének megkeresése

A parancsfájl URL-címe megtalálható az Azure Portalon keresztül, vagy az Azure CLI egy teszt konfigurálása után. További információt az [Internet Analyzer Resource létrehozása című témakörben talál.](internet-analyzer-create-test-portal.md)

1. lehetőség: Az Azure Portalon ezzel a [hivatkozással](https://aka.ms/InternetAnalyzerPreviewPortal) nyissa meg az Azure Internet Analyzer előnézeti portállapját. Nyissa meg az Internet Analyzer profilját, és tekintse meg a parancsfájl URL-címét a **Beállítások > konfiguráció**lapon.

2. lehetőség: Az Azure CLI használatával `scriptFileUri` ellenőrizze a tulajdonság.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Ügyfél adatai

A parancsfájl kifejezetten a profilhoz és a tesztekhez jön létre. A betöltés után a parancsfájl végrehajtása 2 másodperces késleltetéssel történik. Először kapcsolatba lép az Internet Analyzer szolgáltatással a tesztekben konfigurált végpontok listájának lekéréséhez. Ezután futtatja a méréseket, és feltölti az időzített eredményeket az Internet Analyzer szolgáltatásba.

## <a name="client-examples"></a>Példák ügyfélre

Ezek a példák néhány alapvető módszert mutatnak be az ügyfél JavaScript beágyazására a weboldalba vagy az alkalmazásba. A `0bfcb32638b44927935b9df86dcfe397` parancsfájl URL-címéhez példaként használjuk a profilazonosítót.

### <a name="run-on-page-load"></a>Futtatás oldalbetöltéssel
A legegyszerűbb módszer a metacímke blokkon belüli parancsfájlcímke használata. Ez a címke oldalanként egyszer hajtja végre a parancsfájlt.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>További lépések

Olvassa el az [Internet Analyzer GYIK-et](internet-analyzer-faq.md)
