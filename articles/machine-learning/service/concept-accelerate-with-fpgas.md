---
title: Mi az az FPGA? – Projekt Brainwave – az Azure gépi tanulás
description: Annak érdekében, modellek és mély Neurális hálózatokat és a FPGAs útmutató.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: ace39e415bc06013008091a43d37a992a984e46b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736711"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Mi az az FPGA és a projekt Brainwave?

Ez a cikk bemutatja azokat a programozható kapu tömbök (FPGA), és hogyan FPGA integrálva van az Azure gépi tanulási valós idejű AI biztosításához.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. CPU, GPU és ASIC

FPGAs programozható logikai blokkok tömbjét tartalmazza, és újrakonfigurálható hierarchiáját összeköttetések használatára, amelyek lehetővé teszik a blokkok után gyártási különböző módon kell konfigurálni.

FPGAs programozhatóság és a teljesítmény összehasonlítása az egyéb modulok biztosítják:

![Az Azure Machine Learning FPGA összehasonlítása](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **Központi egység (CPU)** általános célú processzorok vannak. Processzorteljesítmény nem ideális grafikus és a videó feldolgozáshoz.
- **Grafikus processzor (GPU) egységek** párhuzamos feldolgozást kínál, és egy népszerű választás az AI számításokat. A párhuzamos feldolgozása gyorsabb kép megjelenítési mint processzorok Feldolgozóegységekkel eredményt.
- **Az alkalmazás-specifikus integrált áramköröket (ASIC)**, például a Google TensorFlow processzor egységek vannak a testreszabott kapcsolatok. Ezek a modulok adja meg a legmagasabb hatékonyságát, feladatspecifikus rugalmatlan is.
- **FPGAs**, például az Azure-on elérhető, ASIC megközelíti a teljesítményt, de később újra kell konfigurálni a rugalmasságot kínálnak.

FPGAs jelenleg minden új Azure-kiszolgálóval. A Microsoft már használja FPGAs Bing keresési besorolási, mély Neurális hálózat (DNN) értékelési és szoftveralapú hálózatkezelési (SDN) gyorsítás. A rendszer ezeket FPGA késés csökkentésére, más feladatok elvégzéséhez processzorok felszabadítása közben.

## <a name="project-brainwave-on-azure"></a>Azure-projekt Brainwave

Projekt Brainwave egy hardver architektúra, amely úgy van kialakítva, Intel FPGA eszközök alapján, és annak érdekében, valós idejű AI számítások használt. E gazdaságos FPGA engedélyezve architektúra képzett Neurális hálózat futtatható lehető leggyorsabb és kisebb késést biztosít. Projekt Brainwave is parallelize előre képzett DNNs FPGAs horizontális a szolgáltatás között.

- Teljesítmény

    FPGAs teszik lehetővé a valós idejű inferencing kérelmek kis késleltetésű. Kötegelés azt jelenti, hogy egy kérelem kisebb darabokra összeállításának, és a hardver hasznosíthatóságát javítják processzorra etetésére. Kötegelés nem hatékony és késést okozhat. Brainwave nincs szükség a kötegelés; ezért a késés, 10-szer alacsonyabb Processzor- és a GPU képest.

- Rugalmasság

    FPGAs újra kell konfigurálni a machine learning modellek különböző típusú. Erre a rugalmasságra megkönnyíti annak érdekében, az alkalmazások, a legtöbb optimális numerikus pontosság és a használt memória típus alapján.

    Új machine learning módszerek vannak fejlesztés alatt áll, rendszeres időközönként, és a projekt Brainwave hardver tervezési is fejlődnek gyorsan. Mivel FPGAs újrakonfigurálható, is lehet a gyorsan változó AI algoritmusok követelményeinek mindig naprakész.

- Méretezés

    A Microsoft Azure a világ legnagyobb felhő használatára irányuló befektetéséből FPGAs. Azure méretezési infrastruktúrája Brainwave futtathatja.

Azure Machine Learning segítségével integrált projekt Brainwave előnézete jelenleg rendelkezésre áll. És egy korlátozott előzetes is készen áll a projekt Brainwave kerüljön a peremhálózaton, hogy, hogy a saját vállalatok és a létesítményekben számítógépes sebesség előnyeit élvezheti.

Az aktuális előnézeti Brainwave korlátozódik TensorFlow üzembe helyezési és a ResNet50-alapú Neurális hálózatokat Intel FPGA hardver kép besorolási és felismerést. Nincsenek további gyűjtemény modellek és egyéb keretrendszerekre támogatásához tervek.

A következő esetekben használja a FPGA projekt Brainwave-architektúra:

- Automatikus optikai hálózatvizsgáló rendszer. Lásd: [valós idejű AI: Microsoft időről előzetes verziója projekt Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Föld borítóján leképezés. Lásd: [használata a mély tanulási megállapítás FPGAs föld lefedi a terabájt Légifelvételes képek leképezésekor végrehajtásához](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-create-an-fpga-service"></a>Hogyan FPGA szolgáltatás létrehozása?

A magas szintű folyamata egy kép felismerési szolgáltatás létrehozása az Azure-ban ResNet50 egy featurizer, a következőképpen történik:

1. ResNet50 már telepítve van az Brainwave. Egyéb diagramok (dátum bemeneti, besorolást, és így tovább) rendelkező TensorFlow létrehozhatja, és adja meg az adatcsatorna (bemenet -> featurize -> besorolása) szolgáltatás definíciós json-fájl használatával. A definíció- és diagramjait tömörítése zip-fájl, és a zip-fájl feltöltése az Azure Blob storage.
2. Regisztrálja a modell Azure ML Nyelvimodell-API használata a zip-fájl, a Blob Storage tárolóban.
3. A szolgáltatás üzembe helyezése az Azure ML Nyelvimodell-API használatával regisztrált modellt.

Az oktatóanyagok esetén lásd: [Azure ML hardver gyorsított modellek gyors üzembe helyezés](https://github.com/Azure/aml-real-time-ai/blob/master/notebooks/resnet50/00_QuickStart.ipynb).

## <a name="start-using-fpga"></a>Ismerkedés a FPGA

Az Azure Machine Learning hardver gyorsított modellek DNN betanított modellek telepítése Azure felhőben FPGAs teszik lehetővé. Első lépések, olvassa el:

- [A modell rendszerbe állítása a egy FPGA webszolgáltatásként](how-to-deploy-fpga-web-service.md)
- [A Microsoft Azure Machine Learning hardver gyorsított projekt Brainwave technológiával modellek](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>További lépések

[A modell rendszerbe állítása a egy FPGA webszolgáltatásként](how-to-deploy-fpga-web-service.md)

[Útmutató a FPGA SDK telepítése](reference-fpga-package-overview.md)

[Hyperscale hardver: Azure + FPGA fölött léptékű ML: Build 2018 (videó)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[A Microsoft FPGA alapú konfigurálható felhő (videó) belül](https://channel9.msdn.com/Events/Build/2017/B8063)

[A Microsoft Project Brainwave unveils a valós idejű AI](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)