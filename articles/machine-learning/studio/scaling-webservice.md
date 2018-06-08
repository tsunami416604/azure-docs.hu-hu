---
title: Egyidejűségi beállítása pedig az Azure Machine Learning webszolgáltatás növelése |} Microsoft Docs
description: Megtudhatja, hogyan növelheti a párhuzamosságot az Azure Machine Learning webszolgáltatás további végpontok hozzáadásával.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
keywords: az Azure gépi tanulási, web services operationalization, a méretezés, a végponthoz, egyidejűségi beállítása
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.component: studio
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.openlocfilehash: 2f950d93c0d923e20451eb1622dd4b1393f343a7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835899"
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Az Azure Machine Learning webszolgáltatás méretezhetővé további végpontok hozzáadása
> [!NOTE]
> Ez a témakör ismerteti a technikákról egy **klasszikus** Machine Learning webszolgáltatáshoz. 
> 
> 

Alapértelmezés szerint minden egyes közzétett webszolgáltatás 20 egyidejű kérelmek támogatására van konfigurálva, és magas, mint 200 egyidejű kérelemre is lehet. Az Azure Machine Learning automatikusan optimalizálja a beállításokat, hogy a legjobb teljesítmény elérése érdekében a webszolgáltatás és a portál értéket a rendszer figyelmen kívül hagyja. 

Ha tervezi az API 200 egyidejű hívások maximális értéknél nagyobb terhelésű támogatja, több végpontot a azonos webszolgáltatáson kell létrehoznia. Majd véletlenszerűen terjesztheti a terhelés összes őket.

A gyakori feladatok egy webszolgáltatás-bővítmény skálázás. Néhány ok, amiért méretezési támogatja a több mint 200 egyidejű kérelemre, növelje a rendelkezésre állása, a több végpontot, vagy adjon meg külön végpontok a webszolgáltatás fel. A skála további végpontokat a azonos webszolgáltatás keresztül hozzáadásával növelhető a [Azure Machine Learning webszolgáltatás](https://services.azureml.net/) portálon.

Új végpontok hozzáadásával kapcsolatos további információkért lásd: [végpontok létrehozása](create-endpoint.md).

Ne feledje, hogy a nagy feldolgozási száma használatával hátrányos, ha nem hívás az API-t a ennek megfelelően nagy mértékben lehet. Szórványos időtúllépések és/vagy a teljesítményt a a késés Ha viszonylag kis terhelésű elhelyezése egy API-t nagy terhelés konfigurált jelenhet meg.

A szinkron API-k általában hol van szükség egy alacsony késési igényű helyzetekben használják. Itt késés azt jelenti, hogy az API-hoz, egy kérés teljesítéséhez szükséges, és nem derül ki minden hálózati késésekből származik. Tegyük fel, hogy rendelkezik-e az API-k és egy 50-ms késleltetés. A teljes lefoglalhatja a késleltetési szintű magas rendelkezésre álló kapacitásból és az egyidejű hívások maximális számának = 20, meg kell hívnia az API 20 * 1000 / 50 = 400 másodpercenként alkalommal fordult elő. Ez további kiterjesztése, egyidejű hívások maximális 200 teszi hívja az API 4000 hányszor másodpercenként, feltéve, hogy egy 50-ms késleltetés.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
