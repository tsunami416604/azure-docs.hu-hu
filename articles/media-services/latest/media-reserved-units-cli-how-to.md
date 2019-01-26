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
ms.openlocfilehash: 9f95be8a9d75065deedd3bd7c92907145e966494
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913053"
---
# <a name="scaling-media-processing"></a>Skálázás médiafeldolgozás

Az Azure Media Services lehetővé teszi, hogy méretezhető médiafeldolgozás a fiókjában, mivel kezeli az Media szolgáltatás számára fenntartott egységek (helyet). Részletes ismertetőt talál [médiafeldolgozás skálázás](../previous/media-services-scale-media-processing-overview.md). 

Ez a cikk bemutatja, hogyan [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) méretezése a helyet.

> [!NOTE]
> A hangelemzés és videó elemzési feladatokat a Media Services v3 által aktivált vagy a Video Indexer azt javasoljuk 10 S3 szintű helyet a fiók kiépítése. <br/>Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Előfeltételek 

+ Telepítse az [Azure CLI-t]( /cli/azure/install-azure-cli). Ehhez a cikkhez az Azure CLI 2.0-ás vagy újabb verziója szükséges. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. 

    Használhatja az [Azure Cloud Shellt](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) is.
+ [A Media Services-fiók létrehozása](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Méretezési csoport fenntartott Médiaegységek CLI-vel

1. Futtassa a következő parancsot: `login`. Futtassa ezt a parancsot, hogy az Azure cloud shell vagy a helyi CLI-rendszerhéj használata.

    ```azurecli
    az login
    ```
    
    Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben kell egy böngésző lap megnyitásához, és kövesse az utasításokat a parancssorban adja meg a hozzáférési kód után ellenőrizheti, hogy a [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) a böngészőben.
2. Futtassa a következő parancsot: `mru`.

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
