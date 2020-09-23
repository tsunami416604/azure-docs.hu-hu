---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 1b650fa5a0e9ba2f7019e6e67690d9d1fd65e72a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894894"
---
A Security Center valós idejű veszélyforrások elleni védelmet biztosít a tároló környezetek számára, és riasztásokat hoz létre a gyanús tevékenységekhez. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

A Security Center különböző szinteken biztosítja a veszélyforrások elleni védelmet: 

* **Gazdagép szintje (az Azure Defender for Servers által biztosított)** – ugyanazzal a log Analytics ügynökkel, amelyet más virtuális gépeken Security Center használ, az Azure Defender figyeli a linuxos AK-csomópontokat a gyanús tevékenységekhez, például a webrendszerhéj észleléséhez és az ismert gyanús IP-címekkel való kapcsolathoz. Az ügynök a tároló-specifikus elemzéseket is figyeli, például a privilegizált tárolók létrehozását, az API-kiszolgálókhoz való gyanús hozzáférést, valamint a Docker-tárolón belül futó Secure Shell-(SSH-) kiszolgálókat.

    >[!IMPORTANT]
    > Ha úgy dönt, hogy nem telepíti az ügynököket a gazdagépekre, a fenyegetések elleni védelem előnyeinek és biztonsági riasztásoknak csak egy részhalmazát fogja kapni. A hálózati elemzéssel és a rosszindulatú kiszolgálókkal folytatott kommunikációval kapcsolatos riasztásokat továbbra is megkapja.

    Az AK-gazdagép szintű riasztások listáját a [riasztások hivatkozási táblázata](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)tartalmazza.


* **AK-fürt szintje (az Azure Defender for Kubernetes által biztosított)** – a fürt szintjén a fenyegetések elleni védelem a Kubernetes-naplók elemzésén alapul. Az **ügynök** nélküli figyelés engedélyezéséhez engedélyezze az Azure Defendert. A riasztások ezen a szinten történő létrehozásához Security Center figyeli az AK által felügyelt szolgáltatásokat az AK által beolvasott naplók használatával. Az ezen a szinten található események közé tartoznak például az elérhető Kubernetes-irányítópultok, a magas jogosultsági szintű szerepkörök létrehozása és a bizalmas csatlakoztatások létrehozása.

    >[!NOTE]
    > A Security Center biztonsági riasztásokat hoz létre az Azure Kubernetes szolgáltatás műveleteihez, illetve a Kubernetes beállítás engedélyezése után előforduló központi telepítések esetén az előfizetési beállításokban. 

    Az AK-beli fürt szintű riasztások listáját a [riasztások hivatkozási táblázata](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)tartalmazza.

Emellett a biztonsági kutatók globális csapata folyamatosan figyeli a fenyegetés tájképét. A felderített tároló-specifikus riasztásokat és biztonsági réseket adják hozzá.

> [!TIP]
> A tárolói riasztások szimulálása a [blogbejegyzés](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)utasításait követve végezhető el.