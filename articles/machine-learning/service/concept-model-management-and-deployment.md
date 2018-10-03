---
title: Kezelheti, és az Azure Machine Learning szolgáltatás a modellek üzembe helyezése
description: Ismerje meg, hogyan telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető Azure Machine Learning szolgáltatás használatával. A helyi gépen, vagy egyéb forrásokból az Azure Machine Learning szolgáltatás a betanított modellek is telepítheti.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hjerez
ms.author: hjerez
ms.date: 09/24/2018
ms.openlocfilehash: afba483172bc34b9d54afc3af755f0967affc875
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239166"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Kezelheti, telepítheti és figyelheti a modellek az Azure Machine Learning szolgáltatás

Ebben a cikkben megismerheti az Azure Machine Learning szolgáltatás használatával telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető. A helyi gépen, vagy egyéb forrásokból az az Azure Machine Learning betanított modellek is telepítheti. 

A következő ábra szemlélteti a teljes telepítési munkafolyamat: [ ![üzembe helyezést megvalósító munkafolyamat az Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Az üzembe helyezést megvalósító munkafolyamat a következő lépésekből áll:
1. **Regisztrálja a modellt** az Azure Machine Learning szolgáltatás munkaterületen üzemeltetett beállításjegyzékben
1. **Lemezkép regisztrálása** , amely egy pontozó szkriptre és a egy hordozható tároló függőségeit modell párokat. 
1. **Üzembe helyezése** a kép webszolgáltatásként, amely a felhőben vagy a peremeszközökre
1. **Adatainak figyelésére és gyűjtésére**

Az egyes lépések egymástól függetlenül, vagy egy egyetlen központi telepítési parancs részeként hajtható végre. Ezenkívül integrálható üzembe helyezését egy **CI/CD a munkafolyamat** alábbi képen szemléltetett módon.

[ !["Az azure Machine Learning folyamatos integráció/folyamatos készregyártás (CI/CD) ciklus:](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)


## <a name="step-1-register-model"></a>1. lépés: Regisztráljon modell

A modell beállításjegyzék nyomon követi az összes a modellek az Azure Machine Learning szolgáltatás munkaterületen.
Modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék növeli a verziót. Használható kereséskor a modellek regisztrálása során is megadható további metaadat-címkéket.

Lemezkép által használt modellek nem törölhető.

## <a name="step-2-register-image"></a>2. lépés: Regisztráljon kép

Rendszerképek lehetővé teszik a megbízható modell-üzembehelyezés, a modell használatához szükséges összes összetevő együtt. Kép a következő elemeket tartalmazza:

* A modell
* A pontozó motor
* A pontozó fájl vagy az alkalmazás
* A modell pontozása szükséges függőségek

Naplózás és figyelés az SDK-összetevők a lemezképet is tartalmazhatnak. Az SDK-naplók adatait finomhangolása, vagy a modellt, beleértve a bemeneti és kimeneti a modell újratanítása használható.

Az Azure Machine Learning a népszerű keretrendszereket támogat, de általában is dolgozhat bármely keretrendszer, amely a pip telepítve lehet.

A munkaterület létrehozása után úgy más számos egyéb Azure-erőforrások használta a munkaterülethez.
A lemezkép létrehozására használt összes objektum munkaterületét az Azure storage-fiókban vannak tárolva. A rendszerkép létrehozása és az Azure Container Registry tárolja. A kép, amely is tárolódnak a regisztrációs adatbázisba, és kérdezhetők le, keresse meg a lemezkép létrehozásakor megadhat további metaadat-címkéket.

## <a name="step-3-deploy-image"></a>3. lépés: A rendszerkép üzembe helyezése

Regisztrált lemezképeket telepíthet a felhőben vagy a peremhálózati eszközökre. Az üzembehelyezési folyamat létrehoz figyeléséhez szükséges erőforrásokat, terheléselosztás és automatikus méretezés a modellt. A telepített szolgáltatásokhoz való hozzáférés az üzembe helyezés során a biztonsági eszközök biztosításával leköthetőek tanúsítványalapú hitelesítéssel. Újabb-rendszerkép használata egy meglévő telepítéshez frissíteni is lehet.

Webszolgáltatások üzembe helyezéséhez is kereshetők. Kereshet például egy adott modell- vagy képfájl, központi telepítések.

[ ![Következtetési célok](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

A következő is üzembe helyezhetik a rendszerképeket [telepítési céljainak](how-to-deploy-and-where.md) a felhőben:

* Azure Container Instance
* Azure Kubernetes Service
* Azure-beli FPGA-gépek
* Az Azure IoT Edge-eszközök

A szolgáltatás üzemel, a következtetési kérelme, mert automatikusan kiegyenlített terhelésű és a fürt méretezése igény szerint bármely ugrásszerűen kielégítéséhez. [A szolgáltatással kapcsolatos telemetriai adatok](https://docs.microsoft.com/python/api/azureml-telemetry/azureml.telemetry?view=azure-ml-py) az Azure Application Insights szolgáltatásba a munkaterülethez társított rögzíthetők.

## <a name="step-4-monitor-models-and-collect-data"></a>4. lépés: Modellek adatainak figyelésére és gyűjtésére

Egy SDK-t a modell naplózása és az adatok rögzítése kísérheti bemeneti, kimeneti és más vonatkozó adatokat a modellben szereplő érhető el. Az adatok a munkaterület az Azure Storage-fiókban található blob van tárolva.

Az SDK-val való használatához a modellt importál az SDK-t a pontozó szkript vagy alkalmazás. Ezután használhatja az SDK-val való bejelentkezéshez az adatok, például a paraméterek, eredményeket vagy bemenet részletei.

Ha úgy dönt, hogy [a modelladatok gyűjtésének engedélyezése](how-to-enable-data-collection.md) minden alkalommal, amikor a lemezképet, a részleteket, például a hitelesítő adatokat a személyes blob-tárolóba, az adatok rögzítéséhez szükséges automatikusan megtörténik.

> [!Important]
> A Microsoft nem látja a modellben összegyűjtött adatokat. Az adatok közvetlenül az Azure storage-fiókját a munkaterület továbbítja.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hol és hogyan telepítheti a modellek](how-to-deploy-and-where.md) az Azure Machine Learning szolgáltatással.
