---
title: A Machine Learning Studio webszolgáltatás - Azure méretezése |} A Microsoft Docs
description: Útmutató az Azure Machine Learning Studio webszolgáltatás-egyidejűség mértékének növelése további végpontokat hozzáadásával.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.author: amlstudiodocs
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
ms.openlocfilehash: 736c27f5c8ad6efb2b4e36f44998ce06d7b69248
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256349"
---
# <a name="scaling-an-azure-machine-learning-studio-web-service-by-adding-additional-endpoints"></a>Az Azure Machine Learning Studio webszolgáltatás skálázása további végpontok hozzáadása
> [!NOTE]
> Ez a témakör ismerteti a technikákat egy **klasszikus** Machine Learning webszolgáltatáshoz. 
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
