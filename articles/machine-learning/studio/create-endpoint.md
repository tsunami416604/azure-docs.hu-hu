---
title: Webes végpontok létrehozása
titleSuffix: Azure Machine Learning Studio
description: Hozzon létre webes Szolgáltatásvégpontok az Azure Machine Learning Studióban. A web service-ben minden végpont egymástól függetlenül foglalkozik, szabályozva, és felügyelt.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: ac434a696f6e77e5ce61b430232166e7727eda38
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084691"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>A telepített Azure Machine Learning Studio-webszolgáltatásokhoz végpontok létrehozása

> [!NOTE]
> Ez a témakör ismerteti a technikákat egy **klasszikus** Machine Learning webszolgáltatáshoz.

A webszolgáltatások telepítése után egy alapértelmezett végpont kerül létrehozásra az adott szolgáltatáshoz. Az alapértelmezett végpont az API-kulcs használatával hívható meg. Saját további végpontokat adhat hozzá a Web Services portálon.
A web service-ben minden végpont egymástól függetlenül foglalkozik, szabályozva, és felügyelt. Minden végpont egy engedélyezési kulcsot az ügyfelek számára terjeszthető egy egyedi URL-CÍMÉT.

## <a name="add-endpoints-to-a-web-service"></a>Végpontok hozzáadása egy webszolgáltatás

A végpont egy webszolgáltatás, az Azure Machine Learning Web Services portál használatával is hozzáadhat. A végpont a létrehozás után szinkron API-k segítségével, a batch API-k, ezért használja fel, és excel-munkalapokat.

> [!NOTE]
> Ha további végpontokat hozzáadta a web Service, az alapértelmezett végpont nem törölhető.

1. Machine Learning Studio a bal oldali navigációs oszlopban kattintson a Web Services.
2. A webszolgáltatás irányítópultján alján kattintson **Végpontkezelés**. Az Azure Machine Learning Web Services portálon megnyitja a webszolgáltatás a végpontok lapot.
3. Kattintson az **Új** lehetőségre.
4. Írjon be egy nevet és leírást az új végpont. A Végpontnevek 24 karakter vagy kevesebb, hosszúságúnak kell lennie, és kisbetűket betűk vagy számok el kell készíteni. Válassza ki a naplózási szint, és hogy engedélyezve van-e a mintaadatok. A naplózás további információkért lásd: [naplózás engedélyezése a Machine Learning-webszolgáltatások](web-services-logging.md).

## <a id="scaling"></a> Webszolgáltatás méretezése további végpontokat hozzáadásával

Alapértelmezés szerint minden egyes közzétett webszolgáltatás 20 egyidejű kérés támogatására van konfigurálva, és magas, mint végpontonként 200 egyidejű kérelemre is lehet. Az Azure Machine Learning Studio automatikusan optimalizálja a beállításokat, hogy a legjobb teljesítmény elérése érdekében a webes szolgáltatás és portál értékét a rendszer figyelmen kívül hagyja.

Ha tervezi az API-t a párhuzamos hívások maximális értéke 200-nál magasabb terheléssel, több végpontot kell létrehoznia a azonos webszolgáltatásban. Ezután véletlenszerűen terjesztheti a betöltés összes őket.

Webszolgáltatás méretezése a gyakori feladat. Néhány ok méretezése támogatja a több mint 200 egyidejű kérelemre, növelheti a rendelkezésre állás több végpontot, vagy adjon meg különböző végpontok a web service fel. A méretezési csoport növelheti a azonos webszolgáltatás keresztül további végpontok hozzáadása a [Azure Machine Learning Web Service](https://services.azureml.net/) portálon.

Ne feledje, hogy egy nagy feldolgozási száma használatával hátrányos, ha nem hívás az API-t ennek megfelelően nagy sebesség lehet. Időnként időtúllépések és/vagy adatforgalmi csúcsokhoz a késéseknél Ha viszonylag kis terhelésű helyezi az API konfigurálva a magas terhelés jelenhet meg.

A szinkron API-k jellemzően olyan helyzetekben, ahol egy alacsony késleltetésű van szükség. Késés Itt azt jelenti, hogy az idő, az API-t egy kérés teljesítéséhez szükséges időt, és bármely hálózati késések a fiók nem. Tegyük fel, hogy egy 50 ezredmásodperces késés az API-e meg. A teljes mértékben használhassák a késleltetési szintű magas rendelkezésre álló kapacitás és a párhuzamos hívások maximális száma = 20, meg kell hívnia az API 20 * 1000 és 50 = 400 időpontok másodpercenként. Ez további kiterjesztése, a Max. egyidejű hívás 200-as lehetővé teszi hívás / másodperc, feltéve, hogy egy 50 ezredmásodperces késés az API 4000 időpontokat.

## <a name="next-steps"></a>További lépések

[Az Azure Machine Learning webszolgáltatás használata](consume-web-services.md).
