---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: b18cfce173da562aa7cffa48f336ff623c868f21
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129270"
---
## <a name="build-and-run-code-in-kubernetes"></a>Kód létrehozása és futtatása Kubernetesben
Most futtassuk a kódunkat! Futtassa ezt a parancsot a terminálablakban a webfrontend nevű kódolási **gyökérmappából**:

```cmd
azds up
```

Figyelje a parancs kimenetét, és számos dolgot megfigyelhet a folyamat közben:
- A program szinkronizálja a forráskódot az Azure-beli Dev Spaces-térrel.
- A kódmappában lévő Docker-adategységek alapján létrejön egy tároló-lemezkép az Azure-ban.
- Kubernetes-objektumok jönnek létre, amelyek a kódmappában található Helm-diagram szerint használják a tároló-lemezképet.
- Megjelennek a tároló végpontjára (vagy végpontjaira) vonatkozó információk. A mi esetünkben egy nyilvános HTTP URL-t várunk.
- Ha a fenti szakaszok sikeresen lezárultak, a tároló indulásakor megjelenik az `stdout` (és `stderr`) kimenet.

> [!Note]
> Ezek a lépések az `up` első futtatásakor hosszabb időt vesznek igénybe, de a további futtatások már gyorsabbak lesznek.

### <a name="test-the-web-app"></a>A webalkalmazás tesztelése
Keresse meg a konzolkimenetben az `up` parancs által létrehozott nyilvános URL-re vonatkozó adatokat. Ez az alábbi formátumban lesz: 

`Service 'webfrontend' port 'http' is available at <url>` 

Nyissa meg ezt az URL-t egy böngészőablakban, és betöltődik a webalkalmazás. Ahogy a tároló futni kezd, a rendszer `stdout` és `stderr` kimenetet streamel a terminálablakba.

> [!Note]
> Első alkalommal több percet is igénybe vehet, hogy rendelkezésre álljon a nyilvános DNS. Ha az URL-cím nem oldódik fel, használhatja a konzolkimeneten megjelenő alternatív http://localhost:<portnumber> URL-címet. Ha a localhost URL-t használja, úgy tűnhet, hogy a tároló helyileg fut, de valójában az AKS-ben fut. Az Ön kényelme, valamint a helyi gép és a szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz. Visszatérhet és kipróbálhatja a nyilvános URL-címet később, amikor kész a DNS-rekord.
