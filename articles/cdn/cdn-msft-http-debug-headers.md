---
title: Http-fejlécek debug for Azure CDN from Microsoft | Microsoft dokumentumok
description: A hibakeresési gyorsítótár-kérelem fejlécei további információkat nyújtanak a kért eszközre alkalmazott gyorsítótár-házirendről. Ezek a fejlécek a Microsoft Azure CDN-re jellemzőek.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260411"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Debug HTTP-fejléc a Microsoft Azure CDN-jéhez
A hibakeresési válasz `X-Cache`fejléce , a részleteket tartalmazza arról, hogy a CDN-verem melyik rétegéből szolgálták ki a tartalmat. Ez a fejléc a Microsoft Azure CDN-re jellemző.

### <a name="response-header-format"></a>Válaszfejléc formátuma

Fejléc | Leírás
-------|------------
X-Cache: TCP_HIT | Ez a fejléc akkor jelenik meg, amikor a tartalom a CDN peremhálózati gyorsítótárából kerül kiszolgálásra. 
X-Cache: TCP_REMOTE_HIT | Ez a fejléc akkor jelenik meg, amikor a tartalom a CDN regionális gyorsítótárából (Origin pajzsréteg) szolgál ki.
X-Cache: TCP_MISS | Ez a fejléc akkor jelenik meg, ha gyorsítótár-tévesztés történt, és a tartalom az Originből jelenik meg. 


