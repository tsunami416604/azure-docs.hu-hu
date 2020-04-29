---
title: A IoT Edge telepítése a Kubernetes-on | Microsoft Docs
description: Ismerje meg, hogyan telepítheti a IoT Edget a Kubernetes egy helyi fejlesztési fürt-környezet használatával
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b2068c3944f9e7616b0666c7bafcafc68ee0cd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471285"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>A IoT Edge telepítése a Kubernetes (előzetes verzió)

IoT Edge integrálható a Kubernetes a rugalmas, magasan elérhető infrastruktúra-rétegként használva. Itt találja a támogatást magas szintű IoT Edge megoldásként:

![k8s bemutatása](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Az integráció jó mentális modellje a Kubernetes, mint egy másik működési környezet, IoT Edge alkalmazások a Linux és a Windows rendszereken kívül is futhatnak.

## <a name="architecture"></a>Architektúra 
A Kubernetes-on a IoT Edge *egyéni erőforrás-definíciót* (CRD) biztosít az Edge-munkaterhelések telepítéséhez. IoT Edge ügynök feltételezi egy *CRD-vezérlő* szerepkörét, amely összehangolja a felhőben felügyelt kívánt állapotot a helyi fürt állapotával.

A modul élettartamát a Kubernetes Scheduler kezeli, amely fenntartja a modul rendelkezésre állását, és kiválasztja az elhelyezést. IoT Edge felügyeli a fent futó Edge Application platformot, és folyamatosan összehangolja a IoT Hubban megadott kívánt állapotot a peremhálózati fürt állapotával. Az alkalmazás modellje továbbra is ismert modell IoT Edge modulok és útvonalak alapján. A IoT Edge-ügynök vezérlője a Kubernetes natív szerkezetek (például hüvelyek, üzembe helyezések, szolgáltatások stb.) esetében végzi el az *automatikus* fordítási IoT Edge alkalmazási modelljét.

A magas szintű architektúra diagramja:

![kubernetes Arch](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

Az Edge-telepítés minden összetevője az eszközre jellemző Kubernetes-névtérre van korlátozva, így több peremhálózati eszköz és a hozzájuk tartozó üzemelő példányok között is megosztható.

>[!NOTE]
>A Kubernetes IoT Edge [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

## <a name="tutorials-and-references"></a>Oktatóanyagok és referenciák 

További információért tekintse [meg a IoT Edge on Kubernetes Preview docs mini-site](https://aka.ms/edgek8sdoc) című témakört, beleértve a részletes oktatóanyagokat és referenciákat.
