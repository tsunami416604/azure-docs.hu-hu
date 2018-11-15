---
title: Méretezés a Media szolgáltatás számára fenntartott egység – Azure |} A Microsoft Docs
description: Ez a témakör a méretezési Media feldolgozására az Azure Media Services nyújt áttekintést.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: db1915f23c33b5cc0d504f8fcc21b9533228247f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634396"
---
# <a name="scaling-media-processing"></a>Skálázás médiafeldolgozás

Az Azure Media Services lehetővé teszi, hogy méretezhető médiafeldolgozás a fiókjában, mivel kezeli az Media szolgáltatás számára fenntartott egységek (helyet). Részletes ismertetőt talál [médiafeldolgozás skálázás](../previous/media-services-scale-media-processing-overview.md). Ez a cikk bemutatja, hogyan [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) méretezése a helyet.

> [!IMPORTANT]
> Tekintse át a ismertetett szempontok [ebben a szakaszban](#considerations).  
> 
>

## <a name="prerequisites"></a>Előfeltételek 

- Telepítse és a parancssori Felületet helyileg használja, ez a cikk az Azure CLI 2.0-s vagy újabb verziójára van szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

    Jelenleg nem minden [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) parancsok működnek az Azure Cloud shellben. Javasoljuk, hogy a parancssori Felületet helyileg használja.

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Méretezési csoport fenntartott Médiaegységek CLI-vel

A következő [az ams-fiók legutóbbi](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) parancsot a Media szolgáltatás számára fenntartott egységek a "amsaccount" fiók használatával beállítja a **száma** és **típus** paraméterek.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="considerations"></a>Megfontolandó szempontok

- A hangelemzés és videó elemzési feladatokat a Media Services v3 által aktivált vagy a Video Indexer azt javasoljuk 10 S3 szintű helyet a fiók kiépítése.
- Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).

## <a name="billing"></a>Számlázás

Díja a száma, típusa és helyet felhasznált idő alapján a fiókjában. A díjak vonatkoznak-e a feladatok futtatásához. – Gyakori kérdések című szakaszában talál részletes magyarázatát, a [Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/) lapot.   

## <a name="next-step"></a>Következő lépés

[Videók elemzése](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
