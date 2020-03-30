---
title: Webszolgáltatás-végpontok létrehozása
titleSuffix: ML Studio (classic) - Azure
description: Webszolgáltatás-végpontok létrehozása az Azure Machine Learning Studio (klasszikus). A webszolgáltatás minden végpontját egymástól függetlenül kezelik, szabályoztatják és kezelik.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 980ed1e54de30ec8a2dc0c1fdac6546d31f48a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218201"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Végpontok létrehozása üzembe helyezett Azure Machine Learning Studio (klasszikus) webszolgáltatásokhoz

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Ez a témakör a **Classic** Machine Learning webszolgáltatásra vonatkozó technikákat ismerteti.

A webszolgáltatások telepítése után egy alapértelmezett végpont kerül létrehozásra az adott szolgáltatáshoz. Az alapértelmezett végpont az API-kulcs használatával hívható meg. A webszolgáltatások portálról további végpontokat adhat hozzá saját kulcsaikkal.
A webszolgáltatás minden végpontját egymástól függetlenül kezelik, szabályoztatják és kezelik. Minden végpont egy egyedi URL-cím egy engedélyezési kulccsal, amelyet terjeszthet az ügyfelek számára.

## <a name="add-endpoints-to-a-web-service"></a>Végpontok hozzáadása webszolgáltatáshoz

Az Azure Machine Learning Web Services portálhasználatával végpontot adhat hozzá egy webszolgáltatáshoz. A végpont létrehozása után felhasználhatja azt szinkron API-kon, kötegelt API-kon és excel-munkalapokon keresztül.

> [!NOTE]
> Ha további végpontokat adott hozzá a webszolgáltatáshoz, nem törölheti az alapértelmezett végpontot.

1. A Machine Learning Studio (klasszikus) alkalmazásban a bal oldali navigációs oszlopban kattintson a WebServices elemre.
2. A webszolgáltatás irányítópultjának alján kattintson a **Végpontok kezelése**elemre. Az Azure Machine Learning webszolgáltatások portálja megnyílik a webszolgáltatás végpontok lapján.
3. Kattintson **az Új gombra.**
4. Írja be az új végpont nevét és leírását. A végpontneveknek legfeljebb 24 karakter hosszúságúnak kell lenniük, és kisbetűs ábécéből vagy számokból kell kiesniük. Válassza ki a naplózási szintet, és hogy a mintaadatok engedélyezve vannak-e. A naplózásról a [Machine Learning webszolgáltatások naplózásának engedélyezése](web-services-logging.md)című témakörben talál további információt.

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a>Webszolgáltatás méretezése további végpontok hozzáadásával

Alapértelmezés szerint minden közzétett webszolgáltatás 20 egyidejű kérelem támogatására van konfigurálva, és akár 200 egyidejű kérelem is lehet. Az Azure Machine Learning Studio (klasszikus) automatikusan optimalizálja a beállítást, hogy a legjobb teljesítményt nyújtsa a webszolgáltatás számára, és a portál értékét figyelmen kívül hagyja.

Ha azt tervezi, hogy az API-t nagyobb terheléssel hívja meg, mint amelyet a 200-as max egyidejű hívások értéke támogat, több végpontot kell létrehoznia ugyanazon a webszolgáltatáson. Ezután véletlenszerűen eloszthatja a terhelést az összes között.

A webszolgáltatás méretezése gyakori feladat. Néhány ok, askálázás több mint 200 egyidejű kérelmek támogatása, több végponton keresztül a rendelkezésre állás növelése, vagy külön végpontok a webszolgáltatás. Növelheti a skálázást további végpontok hozzáadásával ugyanahhoz a webszolgáltatáshoz az [Azure Machine Learning webszolgáltatás-portálon](https://services.azureml.net/) keresztül.

Ne feledje, hogy a magas egyidejűségi szám használata káros lehet, ha nem hívja meg az API-t ennek megfelelően magas arányban. Előfordulhat, hogy szórványos időtúlárak és/vagy kiugrások a késés, ha egy viszonylag alacsony terhelést egy API-t nagy terhelésre konfigurált.

A szinkron API-k általában olyan helyzetekben használatosak, ahol alacsony késésre van szükség. A késés itt azt jelenti, hogy az API-nak egy kérés teljesítéséhez szükséges idő, és nem veszi figyelembe a hálózati késéseket. Tegyük fel, hogy 50 ms-os késéssel rendelkező API-val rendelkezik. A rendelkezésre álló kapacitás teljes körű felhasználása a magas és maximális egyidejű hívások = 20, meg kell hívni ezt az API-t 20 * 1000 / 50 = 400-szor másodpercenként. Ezt tovább bővítve a 200-as számú maximális egyidejű hívások lehetővé teszik az API 4000-es másodpercenkénti hívását, 50 ms-os késést feltételezve.

## <a name="next-steps"></a>További lépések

[Azure Machine Learning webszolgáltatás felhasználása.](consume-web-services.md)
