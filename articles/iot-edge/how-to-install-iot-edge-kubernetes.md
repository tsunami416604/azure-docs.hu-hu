---
title: Az IoT Edge telepítése a Kubernetes-re | Microsoft dokumentumok
description: További információ az IoT Edge kubernetes-i telepítéséről helyi fejlesztési fürtkörnyezet használatával
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471285"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Az IoT Edge telepítése a Kubernetes-re (előzetes verzió)

Az IoT Edge integrálható a Kubernetes-szel, rugalmas, magas rendelkezésre állású infrastruktúra-rétegként használva. Ez a támogatás itt illeszkedik egy magas szintű IoT Edge-megoldáshoz:

![k8s bevezető](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Egy jó mentális modell az integráció, hogy úgy gondolja, a Kubernetes, mint egy másik működési környezet IoT Edge alkalmazások futtatható mellett a Linux és a Windows.

## <a name="architecture"></a>Architektúra 
A Kubernetes, IoT Edge *egyéni erőforrás-definíció* (CRD) a peremhálózati számítási feladatok központi telepítések. Az IoT Edge-ügynök egy *CRD-vezérlő* szerepét veszi fel, amely egyezteti a felhőáltal felügyelt kívánt állapotot a helyi fürtállapottal.

A modul élettartamát a Kubernetes-ütemező kezeli, amely fenntartja a modul rendelkezésre állását, és kiválasztja azok elhelyezését. Az IoT Edge kezeli a tetején futó peremhálózati alkalmazásplatformot, és folyamatosan egyezteti az IoT Hubban megadott kívánt állapotot a peremhálózati fürt állapotával. Az alkalmazásmodell továbbra is az IoT Edge-modulokon és útvonalakon alapuló ismerős modell. Az IoT Edge-ügynök vezérlő *automatikus* fordítási IoT Edge alkalmazásmodell a Kubernetes natív konstrukciók, például a podok, üzembe helyezések, szolgáltatások stb.

Itt van egy magas szintű építészeti diagram:

![kubernetes ív](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

A peremhálózati központi telepítés minden összetevője az eszközre jellemző Kubernetes-névtérre van kiosztva, lehetővé téve, hogy ugyanazokat a fürterőforrásokat több peremhálózati eszköz és azok központi telepítései között ossza meg.

>[!NOTE]
>A Kubernetes IoT Edge [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)érhető el.

## <a name="tutorials-and-references"></a>Oktatóanyagok és referenciák 

További információkért tekintse meg az [IoT Edge on Kubernetes preview docs mini-site](https://aka.ms/edgek8sdoc) további információkért, beleértve a részletes oktatóanyagokat és hivatkozásokat.
