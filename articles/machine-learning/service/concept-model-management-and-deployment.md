---
title: Kezelheti, regisztrálása, üzembe helyezheti és figyelheti a gépi Tanulási modelleket
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető Azure Machine Learning szolgáltatás használatával. A helyi gépen, vagy egyéb forrásokból az Azure Machine Learning szolgáltatás a betanított modellek is telepítheti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: d7f24e242168fc969d0f0d920dce0e6d9562a295
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241840"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Kezelheti, telepítheti és figyelheti a modellek az Azure Machine Learning szolgáltatás

Ebben a cikkben megismerheti az Azure Machine Learning szolgáltatás használatával telepíthet, kezelheti és figyelheti a modellek révén folyamatosan fejleszthető. A helyi gépen, vagy egyéb forrásokból az az Azure Machine Learning betanított modellek is telepítheti. 

A következő ábra szemlélteti a teljes telepítési munkafolyamat: [ ![Az Azure Machine Learning telepítési munkafolyamat](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Az üzembe helyezést megvalósító munkafolyamat a következő lépésekből áll:
1. **Regisztrálja a modellt** az Azure Machine Learning szolgáltatás munkaterületen üzemeltetett beállításjegyzékben
1. **Lemezkép regisztrálása** , amely egy pontozó szkriptre és a egy hordozható tároló függőségeit modell párokat. 
1. **Üzembe helyezése** a kép webszolgáltatásként, amely a felhőben vagy a peremeszközökre
1. **Adatainak figyelésére és gyűjtésére**
1. **Frissítés** központi telepítést, használjon új lemezképet.

Az egyes lépések egymástól függetlenül, vagy egy egyetlen központi telepítési parancs részeként hajtható végre. Ezenkívül integrálható üzembe helyezését egy **CI/CD a munkafolyamat** alábbi képen szemléltetett módon.

[ !["Az azure Machine Learning folyamatos integráció/folyamatos készregyártás (CI/CD) ciklus:](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>1. lépés: Modell regisztrálása

Modell regisztrálását segítségével tárolja és verzió a modellek az Azure-felhőben, a munkaterületén. A modell beállításjegyzék megkönnyíti a betanított modellek rendszerezéséhez és nyomon követésére.
 
A regisztrált modellek nevét és verzióját azonosítja. Minden alkalommal, amikor egy modell regisztrálni a neve megegyezik egy meglévő, a beállításjegyzék növeli a verziót. Használható kereséskor a modellek regisztrálása során is megadható további metaadat-címkéket. Az Azure Machine Learning szolgáltatásban minden olyan modell, amely tölthetők be a Python 3 támogatja. 

Lemezkép által használt modellek nem törölhető.

További információkért lásd: a register-modell szakasz, [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel).

Regisztrálás a modell pickle formátumban tárolja egy példa: [oktatóanyag: Egy rendszerkép osztályozási modell betanításához](tutorial-deploy-models-with-aml.md).

ONNX-modellekkel használatával kapcsolatos információkért lásd: a [ONNX és az Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentumot.

## <a name="step-2-register-image"></a>2. lépés: Lemezkép regisztrálása

Rendszerképek lehetővé teszik a megbízható modell-üzembehelyezés, a modell használatához szükséges összes összetevő együtt. Kép a következő elemeket tartalmazza:

* A modell
* A pontozó motor
* A pontozó fájl vagy az alkalmazás
* A modell pontozása szükséges függőségek

Naplózás és figyelés az SDK-összetevők a lemezképet is tartalmazhatnak. Az SDK-naplók adatait finomhangolása, vagy a modellt, beleértve a bemeneti és kimeneti a modell újratanítása használható.

Az Azure Machine Learning a népszerű keretrendszereket támogat, de általában is dolgozhat bármely keretrendszer, amely a pip telepítve lehet.

A munkaterület létrehozása után úgy más számos egyéb Azure-erőforrások használta a munkaterülethez.
A lemezkép létrehozására használt összes objektum munkaterületét az Azure storage-fiókban vannak tárolva. A rendszerkép létrehozásakor megadhat további metaadat-címkéket. A metaadat-címkéket is tárolódnak a regisztrációs adatbázisba, és kérdezhetők le a lemezkép található.

További információkért tekintse meg a beállítás, és regisztrálja a lemezkép szakaszában [modellek üzembe helyezése](how-to-deploy-and-where.md#configureimage).

## <a name="step-3-deploy-image"></a>3. lépés: Rendszerkép üzembe helyezése

Regisztrált lemezképeket telepíthet a felhőben vagy a peremhálózati eszközökre. Az üzembe helyezési folyamat figyeléséhez, terheléselosztás és automatikus méretezési szükséges összes erőforrást a modellt hoz létre. A telepített szolgáltatásokhoz való hozzáférés az üzembe helyezés során a biztonsági eszközök biztosításával leköthetőek tanúsítványalapú hitelesítéssel. Újabb-rendszerkép használata egy meglévő telepítéshez frissíteni is lehet.

Webszolgáltatások üzembe helyezéséhez is kereshetők. Kereshet például egy adott modell- vagy képfájl, központi telepítések.

[ ![Következtetési célok](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Az alábbi üzembe helyezési célokhoz, a felhőben is üzembe helyezhetik a rendszerképeket:

* Azure Container Instance
* Azure Kubernetes Service
* Azure-beli FPGA-gépek
* Az Azure IoT Edge-eszközök

A szolgáltatás üzemel, a következtetési kérelme, mert automatikusan kiegyenlített terhelésű és a fürt méretezése igény szerint bármely ugrásszerűen kielégítéséhez. [A szolgáltatással kapcsolatos telemetriai rögzíthetők](how-to-enable-app-insights.md) a a munkaterülethez társított Azure Application Insights szolgáltatásba.

További információkért lásd: az üzembe helyezés szakaszában [modellek üzembe helyezése](how-to-deploy-and-where.md#deploy).

## <a name="step-4-monitor-models-and-collect-data"></a>4. lépés: A figyelő modelleket és adatokat gyűjthet

Egy SDK-t a modell naplózása és az adatok rögzítése kísérheti bemeneti, kimeneti és más vonatkozó adatokat a modellben szereplő érhető el. Az adatok a munkaterület az Azure Storage-fiókban található blob van tárolva.

Az SDK-val való használatához a modellt importál az SDK-t a pontozó szkript vagy alkalmazás. Ezután használhatja az SDK-val való bejelentkezéshez az adatok, például a paraméterek, eredményeket vagy bemenet részletei.

Ha úgy dönt, hogy a modelladatok gyűjtésének engedélyezése minden alkalommal, amikor a lemezképet, automatikus kiépítése a részleteket, például a hitelesítő adatokat a személyes blob-tárolóba, az adatok rögzítéséhez szükséges.

> [!Important]
> A Microsoft nem látja a modellben összegyűjtött adatokat. Az adatok közvetlenül az Azure storage-fiókját a munkaterület továbbítja.

További információkért lásd: [a modelladatok gyűjtésének engedélyezése](how-to-enable-data-collection.md).

## <a name="step-5-update-the-deployment"></a>5. lépés: Az üzemelő példány frissítése

A modell frissítések automatikusan nincs regisztrálva. Ehhez hasonlóan egy új rendszerképet regisztrálása nem frissíti automatikusan létrehozott egy előző verzióról a lemezkép központi telepítések. Ehelyett kell manuálisan regisztrálja a modellt, regisztrálja a lemezképet, és frissítse a modellt. További információkért lásd: szakaszában frissítése [modellek üzembe helyezése](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>További lépések

Tudjon meg többet [hol és hogyan telepítheti a modellek](how-to-deploy-and-where.md) az Azure Machine Learning szolgáltatással.

Ismerje meg, hogyan hozhat létre ügyfél alkalmazások és szolgáltatások, amelyek [webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md).
