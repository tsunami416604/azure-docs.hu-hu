---
title: HTTP-fejlécek hibakeresése Azure CDN a Microsofttól | Microsoft Docs
description: A hibakeresési gyorsítótár-kérelmek fejlécei további információkat nyújtanak a kért eszközre alkalmazott gyorsítótár-házirendről. Ezek a fejlécek kifejezetten a Microsoft Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814076"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Hibakeresés HTTP-fejléce Azure CDN a Microsofttól
A hibakeresési válasz fejléce `X-Cache`részletesen ismerteti, hogy a CDN-verem melyik rétegét kézbesítették a tartalom. Ez a fejléc kifejezetten a Microsoft Azure CDN.

### <a name="response-header-format"></a>Válasz fejlécének formátuma

Fejléc | Leírás
-------|------------
X-cache: TCP_HIT | A rendszer ezt a fejlécet adja vissza, amikor a tartalmat a CDN Edge-gyorsítótárból kézbesíti. 
X-cache: TCP_REMOTE_HIT | A rendszer ezt a fejlécet adja vissza, amikor a tartalmat a CDN regionális gyorsítótárból (Origin Shield Layer) kézbesíti.
X-cache: TCP_MISS | A rendszer ezt a fejlécet adja vissza, ha a gyorsítótárból hiányzik a gyorsítótár, és a tartalom a forrástól származik. 


