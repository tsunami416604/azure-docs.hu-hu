---
title: Microsoft Azure Stack Edge – áttekintés | Microsoft Docs
description: Leírja Azure Stack Edge, egy fizikai eszközt használó tárolási megoldás az Azure-ba való hálózati átvitelhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 654ca34ae4c0e393920965083567e3d9c71148d0
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863631"
---
# <a name="what-is-azure-stack-edge"></a>Mi az Azure Stack Edge? 

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Az Azure Stack Edge a hálózati adatátviteli képességekkel rendelkező AI-kompatibilis Edge számítástechnikai eszköz. Ez a cikk áttekintést nyújt az Azure Stack Edge-megoldásról, az előnyökről, a főbb képességekről, valamint azokról a forgatókönyvekről, amelyekben telepítheti az eszközt. 

Az Azure Stack Edge egy szolgáltatásként nyújtott hardveres megoldás. A Microsoft a felhőben felügyelt eszközön egy beépített, programozható Gate array (FPGA) tömböt biztosít, amely lehetővé teszi a gyorsított mesterséges intelligenciát, és rendelkezik a hálózati tárolók átjárójának összes képességével. 

## <a name="use-cases"></a>Használati példák

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

|Szolgáltatás |Leírás  |
|---------|---------|
|Gyorsított AI-következtetés| A beépített FPGA engedélyezte.|
|Számítás       |Lehetővé teszi az adatok elemzését, feldolgozását és szűrését.|
|Kiemelkedő teljesítmény | Nagy teljesítményű számítási és adatforgalom.|
|Adathozzáférés     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben. Az eszköz helyi gyorsítótára a legutóbb használt fájlok gyors elérésére szolgál.|
|Felhő által felügyelt     |Az eszköz és a szolgáltatás kezelése a Azure Portal keresztül történik.  |
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Támogatott protokollok     | A standard SMB és NFS protokollok támogatása az adatbetöltéshez. <br> A támogatott verziókról a [Azure stack Edge rendszerkövetelményei](data-box-edge-system-requirements.md)című témakörben olvashat bővebben.|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Titkosítás    | A BitLocker támogatja az adatok helyi titkosítását és biztonságos átvitelét a felhőbe *https*-en keresztül.|
|Sávszélesség-szabályozás| Sávszélesség-használat korlátozása csúcsidőben.|


## <a name="components"></a>Összetevők

Az Azure Stack Edge-megoldás Azure Stack Edge-erőforrásból, Azure Stack Edge fizikai eszközből és egy helyi webes felhasználói felületből áll.

* **Azure stack Edge fizikai eszköz** – a Microsoft által biztosított 1U rackhez csatlakoztatott kiszolgáló, amely úgy konfigurálható, hogy az Azure-ba küldje az adatküldést. 
    
* **Azure stack Edge erőforrás** – a Azure Portal egy olyan erőforrása, amely lehetővé teszi egy Azure stack Edge-eszköz felügyeletét egy olyan webes felületen, amelyet más földrajzi helyekről érhet el. Az Azure Stack Edge-erőforrás segítségével erőforrásokat hozhat létre és kezelhet, megtekintheti és kezelheti az eszközöket és a riasztásokat, valamint kezelheti a megosztásokat.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    További információért látogasson el a [Azure stack Edge-eszköz rendelésének létrehozása](data-box-edge-deploy-prep.md#create-a-new-resource)című témakörre.

* **Azure stack Edge helyi webes felülete** – a helyi webes felhasználói felületen diagnosztika futtatásával, a Azure stack Edge-eszköz kikapcsolásával, a naplók másolásával, valamint az Microsoft ügyfélszolgálata a szolgáltatási kérések elindításával megtekintheti a naplókat.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    A webalapú felhasználói felület használatával kapcsolatos információkért [tekintse meg a webalapú felhasználói felület használata az Azure stack Edge felügyeletéhez](data-box-edge-manage-access-power-connectivity-mode.md)című témakört.


## <a name="region-availability"></a>Régiónkénti elérhetőség

Azure Stack Edge fizikai eszköz, az Azure-erőforrás és a célként megadott Storage-fiók, amelybe az adatok átvitele nem feltétlenül azonos régióban kell, hogy legyen.

- **Erőforrás rendelkezésre állása** – az összes olyan régió listájához, ahol az Azure stack Edge-erőforrás elérhető, lépjen az [Azure-termékek régiónként elérhető területére](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge a Azure Government felhőben is üzembe helyezhető. További információ: [Mi az Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)
    
- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. Azokat a régiókat, amelyekben a Storage-fiókok tárolásához Azure Stack peremhálózati adatnak közel kell lennie ahhoz, hogy az eszköz az optimális teljesítmény érdekében legyen. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható. 


## <a name="next-steps"></a>Következő lépések

- Tekintse át az [Azure stack Edge rendszerkövetelményeit](data-box-edge-system-requirements.md).
- Az [Azure stack Edge korlátainak](data-box-edge-limits.md)megismerése.
- Az [Azure Azure stack Edge](data-box-edge-deploy-prep.md) üzembe helyezése Azure Portal-ben.




