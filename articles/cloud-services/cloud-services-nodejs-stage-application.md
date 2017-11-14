---
title: "Egy felhőalapú szolgáltatás telepítését (Node.js) tesztelése |} Microsoft Docs"
description: "Útmutató: egy átmeneti környezet az Azure alkalmazás telepítése, majd telepíteni kell a virtuális IP-cím (VIP) lapozóterület használata termelési környezetben."
services: cloud-services
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: 
ms.assetid: d65d26a6-b424-49cd-a88c-7ef46bb112a8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: e35bbd31f39722c2e95ce58772dc6bd5eaf839a0
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="staging-an-application-in-azure"></a>Átmeneti egy alkalmazás az Azure-ban
A burkolt alkalmazás tesztelése előtt helyezze át az éles környezetben, amelyben az alkalmazás elérhető az interneten Azure-ban az átmeneti környezet is telepíthető. Az átmeneti hasonlít pontosan az éles környezetben, azzal a különbséggel, hogy csak alkalmazást érheti el az előkészített az Azure által létrehozott rejtjelezett URL. Miután ellenőrizte, hogy az alkalmazás megfelelően működik-e, azt is telepíthető az éles környezetbe egy virtuális IP-cím (VIP) felcserélés elvégzésével.

> [!NOTE]
> A cikkben leírt lépéseket csak az Azure felhőalapú szolgáltatásként üzemeltetett csomópont alkalmazások vonatkoznak.
> 
> 

## <a name="step-1-stage-an-application"></a>1. lépés: Az alkalmazás tesztelése
Ez a feladat bemutatja, hogyan adhat egy alkalmazás tesztelése a használatával a **Microsoft Azure PowerShell**.

1. A szolgáltatás közzétételekor egyszerűen adja át a **-tárolóhely** paramétert a **Publish-AzureServiceProject** parancsmag.
   
   ```powershell
   Publish-AzureServiceProject -Slot staging
   ```
2. Jelentkezzen be a [a klasszikus Azure portálon] válassza **Felhőszolgáltatások**. Szolgáltatás létrehozása után a felhő és a **átmeneti** oszlop állapota frissült **futtató**, kattintson a szolgáltatás nevére.
   
   ![a portál egy futó szolgáltatással megjelenítése][cloud-service]
3. Válassza ki a **irányítópult**, majd válassza ki **átmeneti**.
   
   ![cloud service irányítópult][cloud-service-dashboard]
4. Az értéket a **webhely URL-címe** jobb bejegyzés. A DNS-neve: egy rejtjelezett belső azonosítója, ami Azure jön létre.
   
    ![webhely URL-címe][cloud-service-staging-url]

Most már ellenőrizheti, hogy az alkalmazás megfelelően működjön a tesztelési környezetben az átmeneti tárolási hely URL-cím használatával.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>2. lépés: A tárhellyel való felcserélése VIP által éles alkalmazás frissítése
Miután ellenőrizte, hogy egy alkalmazás, a tesztelési környezetben frissített verzióját, akkor gyorsan elérhetővé teheti azt éles környezetben a virtuális IP-címek (VIP) a átmeneti és üzemi környezetekben a csere által.

> [!NOTE]
> Ez a lépés feltételezi, hogy már telepített egy alkalmazást éles és előkészített a frissített verzió az alkalmazás.
> 
> 

1. Jelentkezzen be a [a klasszikus Azure portálon], kattintson a **Felhőszolgáltatások** , és válassza a szolgáltatás nevét.
2. Az a **irányítópult**, jelölje be **átmeneti**, és kattintson a **felcserélése** az oldal alján. Ekkor megnyílik a virtuális IP-Címcsere párbeszédpanel.
   
   ![VIP-csere párbeszédpanel][vip-swap-dialog]
3. Tekintse át az adatokat, és kattintson a **OK**. A két központi telepítést először az átmeneti telepítési vált, az üzemi és átmeneti vált az éles környezet frissítése.

Sikeresen előkészítette a központi telepítés, és éles környezet frissíteni a virtuális IP-címek csere a tesztelési az üzemelő példányhoz.

## <a name="additional-resources"></a>További források
* [A szolgáltatás frissítése telepítéséről üzemi csere virtuális IP-címek az Azure-ban]

[a klasszikus Azure portálon]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[A szolgáltatás frissítése telepítéséről üzemi csere virtuális IP-címek az Azure-ban]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
