---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c5c0c8f649e7cbab2c16688717de1aaabfb93c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75477147"
---
Ez a hivatkozás részletezi az Azure Storage méretezhetőségét és teljesítménycéljait. Az itt felsorolt méretezhetőségi és teljesítménycélok csúcsminőségű célok, de elérhetők. A tárfiók által elért kérelemsebesség és sávszélesség minden esetben a tárolt objektumok méretétől, a használt hozzáférési mintáktól és az alkalmazás által végrehajtott munkaterhelés típusától függ.

Győződjön meg arról, hogy tesztelje a szolgáltatást annak megállapítására, hogy a teljesítmény megfelel-e az Ön igényeinek. Ha lehetséges, kerülje a forgalom sebességének hirtelen kiugrásait, és győződjön meg arról, hogy a forgalom jól elosztott partíciók között.

Amikor az alkalmazás eléri azt a korlátot, amelyet egy partíció képes kezelni a számítási feladatokhoz, az Azure Storage az 503-as (kiszolgáló foglalt) vagy az 500-as (Operation Timeout) hibakóddal(500- as hibakóddal) kapcsolatban. Ha 503-as hibák fordulnak elő, fontolja meg az alkalmazás módosítását, hogy exponenciális visszamaradási házirendet használjon az újrapróbálkozásokhoz. Az exponenciális visszalépés lehetővé teszi, hogy a partíció terhelése csökkenjen, és enyhítse a partíció forgalmának kiugrásait.
