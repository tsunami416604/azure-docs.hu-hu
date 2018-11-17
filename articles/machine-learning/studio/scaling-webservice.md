---
title: Az Azure Machine Learning webszolgáltatás egyidejűség mértékének növelése érdekében hogyan |} A Microsoft Docs
description: Egyidejűségi beállítása pedig az Azure Machine Learning webszolgáltatás növelésével további végpontokat hozzáadásával kapcsolatos további információkért.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.custom: (previous ms.author yahajiza)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
keywords: az Azure machine learning, a webszolgáltatások, operacionalizálás, a méretezés, végpont, egyidejűség
ms.assetid: c2c51d7f-fd2d-4f03-bc51-bf47e6969296
ms.service: machine-learning
ms.component: studio
ms.devlang: NA
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.openlocfilehash: f0b639d27dd5114c47bd5a1cfa0f6a72a6d78d83
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824183"
---
# <a name="scaling-an-azure-machine-learning-web-service-by-adding-additional-endpoints"></a>Az Azure Machine Learning webszolgáltatás skálázása további végpontok hozzáadása
> [!NOTE]
> Ez a témakör ismerteti a technikákat egy **klasszikus** Machine Learning webszolgáltatás. 
> 
> 

Alapértelmezés szerint minden egyes közzétett webszolgáltatás 20 egyidejű kérés támogatására van konfigurálva, és magas, mint végpontonként 200 egyidejű kérelemre is lehet. Az Azure Machine Learning automatikusan optimalizálja a beállításokat, hogy a legjobb teljesítmény elérése érdekében a webes szolgáltatás és portál értékét a rendszer figyelmen kívül hagyja. 

Ha tervezi az API-t a párhuzamos hívások maximális értéke 200-nál magasabb terheléssel, több végpontot kell létrehoznia a azonos webszolgáltatásban. Ezután véletlenszerűen terjesztheti a betöltés összes őket.

Webszolgáltatás méretezése a gyakori feladat. Néhány ok méretezése támogatja a több mint 200 egyidejű kérelemre, növelheti a rendelkezésre állás több végpontot, vagy adjon meg különböző végpontok a web service fel. A méretezési csoport növelheti a azonos webszolgáltatás keresztül további végpontok hozzáadása a [Azure Machine Learning Web Service](https://services.azureml.net/) portálon.

Új végpontok hozzáadására vonatkozó további információkért lásd: [végpontok létrehozása](create-endpoint.md).

Ne feledje, hogy egy nagy feldolgozási száma használatával hátrányos, ha nem hívás az API-t ennek megfelelően nagy sebesség lehet. Időnként időtúllépések és/vagy adatforgalmi csúcsokhoz a késéseknél Ha viszonylag kis terhelésű helyezi az API konfigurálva a magas terhelés jelenhet meg.

A szinkron API-k jellemzően olyan helyzetekben, ahol egy alacsony késleltetésű van szükség. Késés Itt azt jelenti, hogy az idő, az API-t egy kérés teljesítéséhez szükséges időt, és bármely hálózati késések a fiók nem. Tegyük fel, hogy egy 50 ezredmásodperces késés az API-e meg. A teljes mértékben használhassák a késleltetési szintű magas rendelkezésre álló kapacitás és a párhuzamos hívások maximális száma = 20, meg kell hívnia az API 20 * 1000 és 50 = 400 időpontok másodpercenként. Ez további kiterjesztése, a Max. egyidejű hívás 200-as lehetővé teszi hívás / másodperc, feltéve, hogy egy 50 ezredmásodperces késés az API 4000 időpontokat.

<!--Image references-->
[1]: ./media/scaling-webservice/machlearn-1.png
[2]: ./media/scaling-webservice/machlearn-2.png
