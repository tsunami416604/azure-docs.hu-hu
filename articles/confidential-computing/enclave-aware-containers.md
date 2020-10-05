---
title: Enklávé Aware-tárolók az Azure-ban
description: enklávé Ready Application-tárolók támogatása az Azure Kubernetes Service-ben (ak)
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: b388b8efb7d993baa243975dbf499576246f3185
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91000977"
---
# <a name="enclave-aware-containers"></a>Enklávé-Aware tárolók

Az enklávé egy védett memória-régió, amely titkosságot biztosít az adatok és a programkódok végrehajtásához. Ez egy megbízható végrehajtási környezet (TEE) egy példánya, amelyet a hardver biztosít. Az AK-beli bizalmas számítástechnikai csomópontok az [Intel Software Guard Extensions (SGX enklávéhoz)](https://software.intel.com/sgx) használatával elszigetelt enklávé-környezeteket hoznak létre az egyes tároló alkalmazások közötti csomópontokban.

Csakúgy, mint az Intel SGX ENKLÁVÉHOZ Virtual Machines, a enklávékban való futtatásra fejlesztett tároló alkalmazások két összetevővel rendelkeznek:

- Nem megbízható összetevő (a gazdagép neve) és
- Egy megbízható összetevő (az enklávé néven).

![Enklávé-Aware tároló architektúrája](./media/enclave-aware-containers/enclaveawarecontainer.png)

Az enklávé Aware containers Application Architecture segítségével a lehető leghatékonyabban vezérelheti a megvalósítást, miközben a kód helyigényét az enklávéban alacsonyan tartja. Az enklávéban futó kód minimalizálása segít csökkenteni a támadási felületi területeket.   

## <a name="enablers"></a>Adottságok

### <a name="open-enclave-sdk"></a>Az Enclave SDK megnyitása
Az Open enklávé SDK egy hardveres, nyílt forráskódú kódtár, amely a hardveres megbízható végrehajtási környezeteket használó C, C++ alkalmazások fejlesztésére szolgál. Az aktuális implementáció támogatást nyújt az Intel SGX ENKLÁVÉHOZ, valamint az [op-Tee operációs rendszer az ARM-TrustZone](https://optee.readthedocs.io/en/latest/general/about.html)való előzetes támogatásához.

Ismerkedés az Open enklávé-alapú [Container alkalmazással](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs)

### <a name="intel-sgx-sdk"></a>Intel SGX ENKLÁVÉHOZ SDK
Az Intel karbantartja a szoftverfejlesztői készletet a Linux-és Windows-tárolók számítási feladataihoz használt SGX ENKLÁVÉHOZ-alkalmazások létrehozásához. A Windows-tárolók jelenleg nem támogatottak az AK bizalmas számítástechnikai csomópontjain.

Ismerkedés az Intel SGX ENKLÁVÉHOZ- [alapú alkalmazásokkal](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html)

### <a name="confidential-consortium-framework-ccf"></a>Bizalmas konzorciumi keretrendszer (CCF)
A bizalmas konzorciumi keretrendszer (CCF) egy nyílt forráskódú keretrendszer, amely a több féltől származó számítási és adatmennyiségre összpontosító, biztonságos, nagy rendelkezésre állású és teljesítményű alkalmazások új kategóriájának kiépítését teszi elérhetővé. A CCF lehetővé teszi a nagyvállalati követelményeknek megfelelő, nagy kapacitású, bizalmas hálózatok bevezetését, amelyekkel felgyorsítható a konzorcium-alapú blockchain és a többtényezős számítási technológia termelésének és nagyvállalati bevezetése.

Ismerkedjen meg az Azure bizalmas számítástechnikai és [a CCF használatába](https://github.com/Microsoft/CCF)

### <a name="confidential-inferencing-onnx-runtime"></a>Bizalmas származtatású ONNX Runtime

A nyílt forráskódú enklávé-alapú ONNX-futtatókörnyezet biztonságos csatornát hoz létre az ügyfél és a következtetés szolgáltatás között, így biztosítva, hogy sem a kérés, sem a válasz nem hagyhatja el a biztonságos enklávét. 

Ez a megoldás lehetővé teszi, hogy meglévő ML betanított modellt használjon, és bizalmasan fusson, miközben megbízhatósági kapcsolatot biztosít az ügyfél és a kiszolgáló között az igazolásokkal és ellenőrzésekkel. 

Ismerkedjen meg a ML Model lifttel, és váltson át a [ONNX runtimere](https://aka.ms/confidentialinference)

## <a name="container-samples-implementations"></a>A Container Samples implementációi

[Azure Samples for enklávé Aware-tárolók az AK-ban](https://github.com/Azure-Samples/enclave-aware-container-samples)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
[Confidential Containers]: /confidential-computing/containercompute/confidential-containers