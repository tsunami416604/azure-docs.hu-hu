---
title: Skálázhatja a Media szolgáltatás számára fenntartott egység – Azure CLI használatával |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan méretezhető médiafeldolgozás a Media Services az Azure CLI használatával.
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
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b40ab6bcc2f718eda85ff64d69a6689e12d60ab8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094835"
---
# <a name="scaling-media-processing"></a>Skálázás médiafeldolgozás

Az Azure Media Services lehetővé teszi, hogy méretezhető médiafeldolgozás a fiókjában, mivel kezeli az Media szolgáltatás számára fenntartott egységek (helyet). Részletes ismertetőt talál [médiafeldolgozás skálázás](../previous/media-services-scale-media-processing-overview.md). 

Ez a cikk bemutatja, hogyan [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) méretezése a helyet.

> [!NOTE]
> A hangelemzés és videó elemzési feladatokat a Media Services v3 által aktivált vagy a Video Indexer azt javasoljuk 10 S3 szintű helyet a fiók kiépítése. <br/>Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Méretezési csoport fenntartott Médiaegységek CLI-vel

Futtassa a következő parancsot: `mru`.

A következő [az ams-fiók legutóbbi](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) parancsot a Media szolgáltatás számára fenntartott egységek a "amsaccount" fiók használatával beállítja a **száma** és **típus** paraméterek.

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Számlázás

Díja a száma, típusa és helyet felhasznált idő alapján a fiókjában. A díjak vonatkoznak-e a feladatok futtatásához. – Gyakori kérdések című szakaszában talál részletes magyarázatát, a [Media Services-díjszabás](https://azure.microsoft.com/pricing/details/media-services/) lapot.   

## <a name="next-step"></a>Következő lépés

[Videók elemzése](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
