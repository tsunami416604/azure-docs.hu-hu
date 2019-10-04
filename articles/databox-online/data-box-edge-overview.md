---
title: A Microsoft Azure Data Box Edge áttekintése | Microsoft Docs
description: Leírja Azure Data Box Edge, egy fizikai eszközt használó tárolási megoldást az Azure-ba való hálózati átvitelhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305420"
---
# <a name="what-is-azure-data-box-edge"></a>Mi az Azure Data Box Edge? 

Az Azure Data Box Edge egy mesterséges intelligenciára épülő peremhálózati számításokat végző eszköz, amely hálózati adatátviteli képességekkel rendelkezik. Ez a cikk áttekintést nyújt a Data Box Edge megoldásról, annak előnyeiről, főbb képességeiről és azokról a forgatókönyvekről, amelyekben üzembe helyezheti ezt az eszközt. 

A Data Box Edge egy szolgáltatásként nyújtott hardveres megoldás. A Microsoft a felhőben felügyelt eszközön egy beépített, programozható Gate array (FPGA) tömböt biztosít, amely lehetővé teszi a gyorsított AI-következtetést, és rendelkezik a Storage-átjáró összes képességével. 

## <a name="use-cases"></a>Használati esetek

Íme a különböző forgatókönyvek, ahol a Data Box Edge a gyors Machine Learning (ML), amely a peremhálózat szélén és az adatok előfeldolgozása előtt használható az Azure-ba való elküldés előtt.

- **Következtetés a Azure Machine learning** -with Data Box Edge használatával a ml-modellek futtatásával gyors eredményeket érhet el, amelyek a felhőbe való adatküldés előtt is elhelyezhetők. A teljes adatkészletet igény szerint átviheti, hogy továbbra is újratanítsa és javítsa a ML-modelleket. További információ a Data Box Edge eszközön az Azure ML hardveres gyorsított modellek használatáról: az [Azure ml hardveresen gyorsított modellek üzembe helyezése Data Box Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- Adatátalakítási adatok előfeldolgozása az Azure-ba való elküldés előtt, amely egy hatékonyabb adatkészlet létrehozásához szükséges. Az előfeldolgozás az alábbiakra használható: 

    - Adatok összesítése.
    - Módosítsa például a személyes adattörlést.
    - A tárterület és a sávszélesség optimalizálása, vagy további elemzés céljából.
    - Az IoT-események elemzése és reagálás rájuk 

- **Adatátvitel az Azure-ba hálózaton keresztül** – A Data Box Edge használatával könnyedén és gyorsan vihet át adatot az Azure-ba további számítási feladatok vagy elemzések elvégzéséhez, illetve archiválás céljából. 


## <a name="key-capabilities"></a>Főbb képességek

A Data Box Edge az alábbi képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Gyorsított AI-következtetés| A beépített FPGA engedélyezte.|
|Számítás       |Lehetővé teszi az adatok elemzését, feldolgozását és szűrését.|
|Nagy teljesítmény | Nagy teljesítményű számítási és adatforgalom.|
|Az adatok elérése     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben. Az eszköz helyi gyorsítótára a legutóbb használt fájlok gyors elérésére szolgál.|
|Felhő által felügyelt     |Az eszköz és a szolgáltatás kezelése a Azure Portal keresztül történik.  |
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Támogatott protokollok     | A standard SMB és NFS protokollok támogatása az adatbetöltéshez. <br> A támogatott verziókkal kapcsolatos további információért tekintse meg [a Data Box Edge rendszerkövetelményeit](data-box-edge-system-requirements.md).|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Encryption    | A BitLocker támogatja az adatok helyi titkosítását és biztonságos átvitelét a felhőbe *https*-en keresztül.|
|Sávszélesség-szabályozás| Sávszélesség-használat korlátozása csúcsidőben.|


## <a name="components"></a>Összetevők

A Data Box Edge megoldás a Data Box Edge-erőforrásból, a Data Box Edge fizikai eszközből és egy helyi webes felhasználói felületből áll.

* **Data Box Edge fizikai eszköz** – 1U állványra szerelt, Microsoft által biztosított kiszolgáló, amely az Azure-ba való adatküldésre konfigurálható. 
    
* **Data Box Edge-erőforrás** – Az Azure Portal erőforrása, amely segítségével a Data Box Edge eszköz különböző földrajzi helyekről elérhető webes felületről kezelhető. A Data Box Edge-erőforrás használatával erőforrásokat készíthet és kezelhet, eszközöket és riasztásokat tekinthet meg és kezelhet, illetve megosztásokat kezelhet.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    További információért lépjen a [Data Box Edge eszköz rendelésének létrehozása](data-box-edge-deploy-prep.md#create-a-new-resource)című témakörre.

* **Data Box helyi webes felhasználói felület** – A helyi webes felhasználói felülettel diagnosztikát futtathat, leállíthatja és újraindíthatja a Data Box Edge eszközt, megtekintheti a másolási naplókat, valamint szolgáltatáskérések elküldése érdekében kapcsolatba léphet a Microsoft ügyfélszolgálatával.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    A webalapú felhasználói felület használatáról a [Data Box a webalapú felhasználói felülettel végzett felügyeletéről](data-box-edge-manage-access-power-connectivity-mode.md) szóló cikkből tájékozódhat.


## <a name="region-availability"></a>Régiónkénti elérhetőség

A Data Box Edge fizikai eszköznek, az Azure-erőforrásnak és a céloldali tárfióknak, amelybe átviszi az adatokat, nem kell egyazon régióban lenniük.

- **Erőforrás rendelkezésre állása** – az összes olyan régió listájához, ahol a Data Box Edge erőforrás elérhető, keresse fel az [Azure-termékeket régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Edge a Azure Government felhőben is üzembe helyezhető. További információ: [Mi az Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)
    
- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. Azokat a régiókat, amelyekben a Storage-fiókok tárolják Data Box Edge az adataikat a lehető legközelebb kell elhelyezni, ahol az eszköz az optimális teljesítmény érdekében található. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható. 


## <a name="next-steps"></a>További lépések

- A [Data Box Edge rendszerkövetelményeinek](data-box-edge-system-requirements.md) áttekintése.
- A [Data Box Edge korlátainak](data-box-edge-limits.md) értelmezése.
- Az [Azure Data Box Edge](data-box-edge-deploy-prep.md) üzembe helyezése az Azure Portalon.




