---
title: Kezelheti, és az Azure Machine Learning modellek üzembe helyezése
description: Ismerje meg, hogyan telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető Azure Machine Learning használatával. A helyi gépen, vagy egyéb forrásokból az az Azure Machine Learning betanított modellek is telepítheti.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hjerez
ms.author: hjerez
ms.date: 09/24/2018
ms.openlocfilehash: 634f33b6d4ed6e272dfb3d1443b0afc63f822d43
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055883"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning"></a>Kezelheti, telepítheti és figyelheti a modellek Azure Machine Learning segítségével

Ebben a cikkben megismerheti az Azure Machine Learning segítségével telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető. A helyi gépen, vagy egyéb forrásokból az az Azure Machine Learning betanított modellek is telepítheti. 

[!["Az azure Machine Learning folyamatos integráció/folyamatos készregyártás (CI/CD) ciklus:](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="deployment-workflow"></a>Telepítési munkafolyamat

A telepítési munkafolyamat tevődik össze: 
1. **Regisztrálja a modellt** üzemeltetett az Azure Machine Learning-munkaterületet a beállításjegyzékben
1. **Lemezkép regisztrálása** , amely egy pontozó szkriptre és a egy hordozható tároló függőségeit modell párokat. 
1. **Üzembe helyezése** a kép webszolgáltatásként, amely a felhőben vagy a peremeszközökre
1. **Figyelés és az adatok gyűjtése**

Az egyes lépések egymástól függetlenül, vagy egy egyetlen központi telepítési parancs részeként teheti meg. 

A következő ábra szemlélteti a teljes üzembe helyezési folyamat:

[![Üzembe helyezési folyamat](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

## <a name="step-1-model-registration"></a>1. lépés: A modell regisztrálását

A modell beállításjegyzék nyomon követi az összes a modellek az Azure Machine Learning-munkaterületet.
Modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék növeli a verziót. Használható kereséskor a modellek regisztrálása során is megadható további metaadat-címkéket.

Lemezkép által használt modellek nem törölhető.

## <a name="step-2-image-registration"></a>. 2. lépés: Lemezkép-regisztrációs

Rendszerképek lehetővé teszik a megbízható modell-üzembehelyezés, a modell használatához szükséges összes összetevő együtt. Kép a következő elemeket tartalmazza:

* A modell
* A pontozó motor
* A pontozó fájl vagy az alkalmazás
* A modell pontozása szükséges függőségek

Naplózás és figyelés az SDK-összetevők a lemezképet is tartalmazhatnak. Az SDK-naplók adatait finomhangolása, vagy a modellt, beleértve a bemeneti és kimeneti a modell újratanítása használható.

Az Azure Machine Learning a népszerű keretrendszereket támogat, de általában is dolgozhat bármely keretrendszer, amely a pip telepítve lehet.

A munkaterület létrehozása után úgy más számos egyéb Azure-erőforrások használta a munkaterülethez.
A lemezkép létrehozására használt összes objektum munkaterületét az Azure storage-fiókban vannak tárolva. A rendszerkép létrehozása és az Azure Container Registry tárolja. A kép, amely is tárolódnak a regisztrációs adatbázisba, és kérdezhetők le, keresse meg a lemezkép létrehozásakor megadhat további metaadat-címkéket.

## <a name="step-3-deployment"></a>3. lépés: központi telepítés

Regisztrált lemezképeket telepíthet a felhőben vagy a peremhálózati eszközökre. Az üzembehelyezési folyamat létrehoz figyeléséhez szükséges erőforrásokat, terheléselosztás és automatikus méretezés a modellt. A telepített szolgáltatásokhoz való hozzáférés az üzembe helyezés során a biztonsági eszközök biztosításával leköthetőek tanúsítványalapú hitelesítéssel. Újabb-rendszerkép használata egy meglévő telepítéshez frissíteni is lehet.

Webszolgáltatások üzembe helyezéséhez is kereshetők. Kereshet például egy adott modell- vagy képfájl, központi telepítések.

Az alábbi célokhoz, a felhőben is üzembe helyezhetik a rendszerképeket:

* Azure Container Instance
* Azure Kubernetes Service
* Azure-beli FPGA-gépek
* Az Azure IoT Edge-eszközök

[További információ, ahol telepítheti](how-to-deploy-and-where.md).

A szolgáltatás üzemel, a következtetési kérelme, mert automatikusan kiegyenlített terhelésű és a fürt méretezése igény szerint bármely ugrásszerűen kielégítéséhez. A szolgáltatással kapcsolatos telemetriai adatokat az Azure Application Insights szolgáltatásba a munkaterülethez társított van rögzítve.

## <a name="step-4-monitoring-models-and-data-collection"></a>4. lépés: Figyelési modellek és az adatgyűjtés

Egy SDK-t a modell naplózása és az adatok rögzítése kísérheti bemeneti, kimeneti és más vonatkozó adatokat a modellben szereplő érhető el. Az adatok a munkaterület az Azure Storage-fiókban található blob van tárolva.

Az SDK-val való használatához a modellt importál az SDK-t a pontozó szkript vagy alkalmazás. Ezután használhatja az SDK-val való bejelentkezéshez az adatok, például a paraméterek, eredményeket vagy bemenet részletei.

Ha úgy dönt, hogy [a modelladatok gyűjtésének engedélyezése](how-to-enable-data-collection.md) minden alkalommal, amikor a lemezképet, a részleteket, például a hitelesítő adatokat a személyes blob-tárolóba, az adatok rögzítéséhez szükséges automatikusan megtörténik.

> [!Important]
> A Microsoft nem látja a modellben összegyűjtött adatokat. Az adatok közvetlenül az Azure storage-fiókját a munkaterület továbbítja.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hol és hogyan telepítheti a modellek](how-to-deploy-and-where.md) az Azure Machine Learning szolgáltatással.
