---
title: Bizalmas tárolók az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg a nem módosított tárolók támogatását a bizalmas tárolók esetében.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 528b843e24e1d63e4822c253b3636ef490e8fe8e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995839"
---
# <a name="confidential-containers"></a>Bizalmas tárolók

## <a name="overview"></a>Áttekintés

Lehetővé teheti a fejlesztők számára, hogy egy **meglévő Docker-alkalmazást (új vagy meglévő)** és biztonságosan futtassanak az Azure Kubernetes szolgáltatásban (ak) a bizalmas számítástechnikai csomópontok támogatásával.

A bizalmas tárolók segítenek a védelemben:

- adatok integritása 
- adatok titkossága
- kód integritása
- tároló-programkód védelme titkosítással
- hardver alapú garanciák
- meglévő alkalmazások futtatásának engedélyezése
- a megbízható hardver gyökerének létrehozása

A hardveres alapú megbízható végrehajtási környezet (TEE) egy fontos összetevő, amely erős biztosítékok biztosítására szolgál a megbízható számítástechnikai bázis (TCB) összetevőinek hardveres és szoftveres mérései révén. Ezeknek a méréseknek a ellenőrzése a várt számítás érvényesítését és a tároló alkalmazások illetéktelen módosításának ellenőrzését segíti.

A bizalmas tárolók támogatják a **Python, a Java, a Node js vagy a csomagolt szoftveralkalmazások (például az NGINX, a Redis cache, a Memcached stb**.) által fejlesztett egyéni alkalmazásokat, amelyek nem módosíthatók az AK-ban.

A bizalmas tárolók a tároló titkosságának leggyorsabb elérési útja, beleértve a tárolók védelmét a titkosításon keresztül, ami lehetővé teszi a lift és a váltást az üzleti logikája minimális változásainak megfelelően.

![A bizalmas tároló átalakítása](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>Bizalmas tárolók segítői

Meglévő Docker-tároló futtatásához a bizalmas számítástechnikai csomópontokon lévő alkalmazásoknak absztrakt réteget vagy SGX ENKLÁVÉHOZ szoftvert kell használniuk a speciális CPU-utasításkészlet kihasználása érdekében. A SGX ENKLÁVÉHOZ szoftver emellett lehetővé teszi, hogy a bizalmas alkalmazások kódja védve legyen, és közvetlen végrehajtást hozzon létre a CPU számára a vendég operációs rendszer, a gazda operációs rendszer vagy a hypervisor eltávolításához. Ez a védelem csökkenti az operációs rendszer vagy a hypervisor rétegek általános felületi támadási területeit és biztonsági réseit.

A bizalmas tárolók teljes mértékben támogatottak az AK-ban, és az Azure-partnerek és a nyílt forráskódú szoftverek (OSS) projektjein keresztül engedélyezve vannak. A fejlesztők a szolgáltatások, az Azure-szolgáltatásokhoz való integráció és az eszközök támogatása alapján választhatják ki a szoftvergyártókat.

## <a name="partner-enablers"></a>Partneri segítők
> [!NOTE]
> Az alábbi megoldások Azure-partnereken keresztül érhetők el, és licencelési díjakat is igényelhetnek. Egymástól függetlenül ellenőrizze a partneri szoftverek feltételeit. 

### <a name="fortanix"></a>Fortanix

A [Fortanix](https://www.fortanix.com/) lehetővé teszi a fejlesztők számára, hogy egy portált és CLI-alapú felületet hozzanak elérhetővé a tároló alkalmazások létrehozásához, és a SGX enklávéhoz képes bizalmas tárolókat az alkalmazás módosításának vagy újrafordításának szükségessége nélkül. A Fortanix rugalmasságot biztosít az alkalmazások széles körének futtatásához és kezeléséhez, beleértve a meglévő alkalmazásokat, az új enklávé-natív alkalmazásokat és az előre csomagolt alkalmazásokat. A felhasználók az [enklávé Manager](https://em.fortanix.com/) felhasználói felületével vagy [REST API](https://www.fortanix.com/api/em/) -kkal hozhatnak létre bizalmas tárolókat az Azure Kubernetes Service [gyorskonfigurálás](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) útmutatójának követésével.

![Fortanix üzembe helyezési folyamata](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

A [Scone](https://scontain.com/index.html?lang=en) olyan biztonsági házirendeket támogat, amelyek tanúsítványokat, kulcsokat és titkokat generálnak, és gondoskodnak arról, hogy csak az alkalmazás tanúsított szolgáltatásai legyenek láthatók. Így az alkalmazások szolgáltatásai automatikusan hitelesítik egymást a TLS-n keresztül, anélkül, hogy módosítani kellene az alkalmazásokat és a TLS-t. Ezt egy egyszerű lombik-alkalmazás súgója ismerteti itt: https://sconedocs.github.io/flask_demo/  

A SCONE a meglévő legtöbb bináris alkalmazást a enklávékban futó alkalmazásokba alakíthatja anélkül, hogy módosítania kellene az alkalmazást, vagy újra le kellene fordítani az adott alkalmazást. A SCONE az adatfájlok és a Python-programkódok titkosításával is védi az értelmezett nyelveket, például a Pythont. A SCONE biztonsági szabályzatának segítségével az egyik képes megvédeni a titkosított fájlokat a jogosulatlan hozzáférések, a módosítások és a visszaállítások ellen. "Sconify" – egy meglévő Python-alkalmazás magyarázata [itt](https://sconedocs.github.io/sconify_image/)

![Scontain folyamat](./media/confidential-containers/scone-workflow.png)

A bizalmas számítástechnikai csomópontokon Scone üzemelő példányok teljes mértékben támogatottak és integráltak. Ismerkedjen meg egy minta alkalmazással https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

A [Anjuna](https://www.anjuna.io/) olyan SGX enklávéhoz platformot biztosít, amely lehetővé teszi a nem módosított tárolók futtatását az AK-on. További információ a **közelgő** funkciókról és a felhasználói folyamatról [itt](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)olvasható.

Ismerkedjen meg a minta Redis Cache és a Python egyéni [alkalmazással](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Anjuna folyamat](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS-segítők 
> [!NOTE]
> Az alábbi megoldások nyílt forráskódú projektekben érhetők el, és nem kapcsolódnak közvetlenül az Azure bizalmas számítástechnika (ACC) vagy a Microsoft számára.  

### <a name="graphene"></a>Grafén

A [grafén](https://grapheneproject.io/) egy egyszerű vendég operációs rendszer, amelynek célja, hogy egyetlen linuxos alkalmazást futtasson minimális gazdagép-követelményekkel. A grafén elkülönített környezetekben futtathatják az alkalmazásokat, amelyek a teljes operációs rendszer futtatásához hasonló előnyökkel rendelkeznek, és jó eszköz-támogatást biztosítanak a meglévő Docker-tároló alkalmazás grafén védett tárolók (Főtitkárság) számára történő átalakításához.

Ismerkedjen meg egy minta alkalmazással és üzembe helyezéssel [az AK-](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) ban

### <a name="occlum"></a>Occlum
A [Occlum](https://occlum.io/) egy memória-biztonságos, többfolyamatos függvénytár operációs rendszer (LibOS) az Intel SGX enklávéhoz. Lehetővé teszi, hogy az örökölt alkalmazások SGX ENKLÁVÉHOZ fussanak, és a forráskód nem módosítható. A Occlum transzparens módon védi a felhasználói munkaterhelések titkosságát, és lehetővé teszi a meglévő Docker-alkalmazások egyszerű emelését és átváltását.

A Occlum támogatja az AK-alapú központi telepítéseket. Kövesse a telepítési utasításokat a különböző példákkal [rendelkező alkalmazásokkal](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Bizalmas tárolók bemutatója
Tekintse meg a bizalmas egészségügyi bemutatót bizalmas tárolókkal. A minta [itt](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md)érhető el. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Segítség kérése

Kérdése van a megvalósítással kapcsolatban, vagy szeretne lenni az Ön számára? E-mail küldése a következő címre: acconaks@microsoft.com

## <a name="reference-links"></a>Hivatkozásokra mutató hivatkozások

[Microsoft Azure igazolás](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
