---
title: Privát végpont konfigurálása (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Az Azure Private link használatával biztonságosan férhet hozzá a Azure Machine Learning-munkaterülethez egy virtuális hálózatról.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/21/2020
ms.openlocfilehash: 619960238125191e7bd4e702a49016c8fd58c847
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296654"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Azure Private-hivatkozás konfigurálása Azure Machine Learning munkaterülethez (előzetes verzió)

Ebből a dokumentumból megtudhatja, hogyan használhatja az Azure Private-hivatkozást a Azure Machine Learning munkaterülettel. Azure Machine Learning virtuális hálózatának beállításával kapcsolatos információkért lásd: [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md)

> [!IMPORTANT]
> Az Azure Private-hivatkozás használata Azure Machine Learning munkaterülettel jelenleg nyilvános előzetes verzióban érhető el. Ez a funkció csak a következő régiókban érhető el:
>
> * **USA keleti régiója**
> * **USA déli középső régiója**
> * **USA nyugati régiója**
> * **USA 2. nyugati régiója**
> * **Közép-Kanada**
> * **Délkelet-Ázsia**
> * **Kelet-Japán**
> * **Észak-Európa**
> * **Kelet-Ausztrália**
> * **Az Egyesült Királyság déli régiója**
>
> Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Private link segítségével privát végponton keresztül csatlakozhat a munkaterülethez. A magánhálózati végpont a virtuális hálózaton belüli magánhálózati IP-címek készlete. Ezután korlátozhatja a hozzáférést a munkaterülethez, hogy csak a magánhálózati IP-címeken keresztül történjen. A privát hivatkozás segít csökkenteni az adatkiszűrése kockázatát. Ha többet szeretne megtudni a privát végpontokról, tekintse meg az [Azure privát hivatkozását](/azure/private-link/private-link-overview) ismertető cikket.

> [!IMPORTANT]
> Az Azure Private-hivatkozás nem befolyásolja az Azure Control Plant (felügyeleti műveletek), például a munkaterület törlését vagy a számítási erőforrások kezelését. Például létrehozhatja, frissítheti vagy törölheti a számítási célt. Ezeket a műveleteket a rendszer a szokásos módon, a nyilvános interneten hajtja végre. Az adatsík műveletei, például a Azure Machine Learning Studio, az API-k (beleértve a közzétett folyamatokat is), vagy az SDK használja a privát végpontot.
>
> A Mozilla Firefox használata esetén problémák merülhetnek fel a munkaterület privát végpontjának elérésére tett kísérlet során. Ez a probléma a HTTPS-en keresztül a Mozilla-on keresztül köthető a DNS-hez. Javasoljuk, hogy megkerülő megoldásként használja a Google Chrome Microsoft Edge használatát.

> [!TIP]
> Azure Machine Learning számítási példány munkaterülettel és privát végponttal is használható. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el az **USA keleti**régiójában, az **USA déli középső** régiójában és az **USA 2. nyugati** régiójában.

## <a name="prerequisites"></a>Előfeltételek

Ha egy ügyfél által felügyelt kulccsal rendelkező magánhálózati kapcsolaton alapuló munkaterületet tervez használni, ezt a szolgáltatást támogatási jegy használatával kell kérnie. További információ: a [kvóták kezelése és növelése](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Privát végpontot használó munkaterület létrehozása

> [!IMPORTANT]
> Jelenleg csak az új Azure Machine Learning munkaterület létrehozásakor támogatjuk a privát végpontok engedélyezését.

A (z) sablon használatával [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) létrehozhat egy privát végponttal rendelkező munkaterületet.

További információ a sablon használatáról, beleértve a privát végpontokat is: [Azure Resource Manager sablon használata munkaterületek létrehozásához Azure Machine Learninghoz](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Munkaterület használata privát végponton keresztül

Mivel a munkaterület felé irányuló kommunikáció csak a virtuális hálózatról engedélyezett, a munkaterületet használó fejlesztési környezeteknek a virtuális hálózat tagjának kell lenniük. Például egy virtuális gép a virtuális hálózaton.

> [!IMPORTANT]
> A kapcsolat ideiglenes megszakadásának elkerülése érdekében a Microsoft javasolja a DNS-gyorsítótár kiürítését a munkaterülethez csatlakozó számítógépeken a privát hivatkozás engedélyezése után. 

Az Azure Virtual Machinesról a [Virtual Machines dokumentációjában](/azure/virtual-machines/)olvashat bővebben.


## <a name="next-steps"></a>Következő lépések

A Azure Machine Learning munkaterület biztonságossá tételével kapcsolatos további információkért tekintse meg a [Virtual Network elkülönítés és adatvédelem áttekintése](how-to-network-security-overview.md) című cikket.
