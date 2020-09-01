---
title: Microsoft Azure Stack Edge GPU-val – áttekintés | Microsoft Docs
description: Leírja, hogy Azure Stack Edge GPU-val, olyan tárolási megoldással, amely fizikai eszközt használ az Azure-ba irányuló hálózati átvitelhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: a8e1c83573de53962b3646304389023d91ab6dd3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256242"
---
# <a name="what-is-azure-stack-edge-with-gpu-preview"></a>Mi az Azure Stack Edge a GPU-val (előzetes verzió)?

Azure Stack Edge GPU-val egy mesterséges intelligenciával rendelkező peremhálózati számítástechnikai eszköz, amely hálózati adatátviteli képességekkel rendelkezik. Ez a cikk áttekintést nyújt az Azure Stack Edge-megoldásról, az előnyökről, a főbb képességekről, valamint azokról a forgatókönyvekről, amelyekben telepítheti az eszközt.

Azure Stack Edge GPU-val egy hardveres megoldás. A Microsoft egy olyan felhőalapú felügyelt eszközt üzemeltet, amely hálózati tároló átjáróként működik, és beépített grafikus processzorral (GPU) rendelkezik, amely gyorsított AI-következtetést tesz lehetővé. 

> [!IMPORTANT]
> A GPU-val Azure Stack Edge jelenleg előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Használati esetek

Az alábbi forgatókönyvek olyan helyzetekben használhatók, ahol a Azure Stack Edge gyors Machine Learning (ML), amely a peremhálózat szélén és az adatok előfeldolgozása előtt, az Azure-ba való küldés előtt is felhasználható.

- **Következtetés a Azure Machine learning** -Azure stack Edge használatával: ml-modellek futtatásával gyors eredményeket érhet el, amelyek a felhőbe való adatküldés előtt is elhelyezhetők. A teljes adatkészletet igény szerint átviheti, hogy továbbra is újratanítsa és javítsa a ML-modelleket. Az Azure ML hardveres gyorsított modellek az Azure Stack Edge-eszközön történő használatáról további információt az [Azure ml hardveres gyorsított modellek üzembe helyezése Azure stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)-ben című témakörben talál.

- Adatátalakítási adatok **előfeldolgozása** az Azure-ba való elküldés előtt, amely egy hatékonyabb adatkészlet létrehozásához szükséges. Az előfeldolgozás az alábbiakra használható: 

    - Adatok összesítése.
    - Módosítsa például a személyes adattörlést.
    - A tárterület és a sávszélesség optimalizálása, vagy további elemzés céljából.
    - Az IoT-események elemzése és reagálás rájuk 

- **Adatok átvitele a hálózaton keresztül az Azure** -ba – a Azure stack Edge használatával egyszerűen és gyorsan vihet át adatátvitelt az Azure-ba a további számítási és elemzési vagy archiválási célokra. 

## <a name="key-capabilities"></a>Főbb képességek

Azure Stack Edge a következő képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Gyorsított AI-következtetés| A beépített GPU (a modelltől függően egy vagy két) engedélyezte.|
|Edge számítástechnika      |Lehetővé teszi az adatok elemzését, feldolgozását és szűrését. Támogatja a virtuális gépeket és a Kubernetes-fürtöket.|
|Nagy teljesítmény | Nagy teljesítményű számítási és adatforgalom.|
|Az adatok elérése     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben. Az eszköz helyi gyorsítótára a legutóbb használt fájlok gyors elérésére szolgál.|
|Felhő által felügyelt     |Az eszköz és a szolgáltatás kezelése a Azure Portal keresztül történik.  |
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Támogatott protokollok     | A standard SMB, NFS és REST protokollok támogatása az adatok betöltéséhez. <br> További információ a támogatott verziókról: [Azure stack Edge rendszerkövetelményei](azure-stack-edge-system-requirements.md).|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Titkosítás    | A BitLocker támogatja az adatok helyi titkosítását és biztonságos átvitelét a felhőbe *https*-en keresztül.|
|Sávszélesség-szabályozás| Sávszélesség-használat korlátozása csúcsidőben.|
|ExpressRoute | A ExpressRoute használatával bővült a biztonság. Egyenrangú konfiguráció használata, ahol a helyi eszközökről a Felhőbeli tárolási végpontokra irányuló forgalom a ExpressRoute halad át. További információkat az [ExpressRoute áttekintésében](../expressroute/expressroute-introduction.md) találhat.

## <a name="components"></a>Összetevők

Az Azure Stack Edge-megoldás Azure Stack Edge-erőforrásból, Azure Stack Edge fizikai eszközből és egy helyi webes felhasználói felületből áll.

* **Azure stack Edge fizikai eszköz** – a Microsoft által biztosított 1U rackhez csatlakoztatott kiszolgáló, amely úgy konfigurálható, hogy az Azure-ba küldje az adatküldést.
    
* **Azure stack Edge erőforrás** – a Azure Portal egy olyan erőforrása, amely lehetővé teszi egy Azure stack Edge-eszköz felügyeletét egy olyan webes felületen, amelyet más földrajzi helyekről érhet el. Az Azure Stack Edge-erőforrás segítségével erőforrásokat hozhat létre és kezelhet, megtekintheti és kezelheti az eszközöket és a riasztásokat, valamint kezelheti a megosztásokat.  

    További információért látogasson el a [Azure stack Edge-eszköz rendelésének létrehozása](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)című témakörre.

* **Azure stack Edge helyi webes felülete** – a helyi webes felhasználói felületen diagnosztika futtatásával, a Azure stack Edge-eszköz kikapcsolásával, a naplók másolásával, valamint az Microsoft ügyfélszolgálata a szolgáltatási kérések elindításával megtekintheti a naplókat.

    A webalapú felhasználói felület használatával kapcsolatos információkért [tekintse meg a webalapú felhasználói felület használata az Azure stack Edge felügyeletéhez](azure-stack-edge-manage-access-power-connectivity-mode.md)című témakört.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Azure Stack Edge fizikai eszköz, az Azure-erőforrás és a célként megadott Storage-fiók, amelybe az adatok átvitele nem feltétlenül azonos régióban kell, hogy legyen.

- **Erőforrás rendelkezésre állása** – ebben az előzetes kiadásban az erőforrás az USA keleti régiójában, Nyugat-Európában és dél-Kelet-Ázsia régióban érhető el.
    
- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. Azokat a régiókat, amelyekben a Storage-fiókok tárolásához Azure Stack peremhálózati adatnak közel kell lennie ahhoz, hogy az eszköz az optimális teljesítmény érdekében legyen. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.

## <a name="next-steps"></a>További lépések

- Tekintse át az [Azure stack Edge rendszerkövetelményeit](azure-stack-edge-gpu-system-requirements.md).
- Az [Azure stack Edge korlátainak](azure-stack-edge-limits.md)megismerése.
- [Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md) üzembe helyezése Azure Portalban.
