---
title: Az Azure Machine Learning és FPGAs
description: Annak érdekében, modellek és mély Neurális hálózatokat és a FPGAs útmutató.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>Mi az a mező programozható kapu tömb (FPGA)?

A mező programozható kapu (FPGA) tömbök integrált áramköröket, amely képes arra, hogy igény szerint. Egy FPGA új logika végrehajtásához igény szerint módosíthatók. Az Azure Machine Learning hardver gyorsított modellek betanított modellek telepítése Azure felhőben FPGAs teszik lehetővé.

Ez a funkció működteti projekt Brainwave, amely kezeli a mély Neurális hálózatokat (DNN) fordítása egy FPGA programba. 

## <a name="why-use-an-fpga"></a>Egy FPGA miért érdemes használni?

FPGAs meg leginkább kis késleltetésű, és azok különösen hatékony pontozó adatok kötegelt méretben egyik (nincs olyan nagy kötegelt méretének követelmény).  Költséghatékony és az Azure-ban elérhetővé válnak.  Projekt Brainwave is parallelize előre képzett DNNs ezek FPGAs horizontális a szolgáltatás között.

## <a name="next-steps"></a>További lépések

[A modell rendszerbe állítása a egy FPGA webszolgáltatásként](how-to-deploy-fpga-web-service.md)

[Útmutató a FPGA SDK telepítése](reference-fpga-package-overview.md)