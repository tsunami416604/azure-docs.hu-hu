---
title: Microsoft Azure Stack Edge áttekintése | Microsoft dokumentumok
description: Ismerteti az Azure Stack Edge, egy tárolási megoldás, amely egy fizikai eszköz hálózati alapú átvitel az Azure-ba.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: f463e8883efd5e2dfc4d7fff80912c193665b850
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78399803"
---
# <a name="what-is-azure-stack-edge"></a>Mi az Azure Stack Edge?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Az Azure Stack Edge egy AI-kompatibilis peremhálózati számítástechnikai eszköz, amely hálózati adatátviteli képességekkel rendelkezik. Ez a cikk áttekintést nyújt az Azure Stack Edge-megoldásról, az előnyökről, a kulcsképességekről és az eszköz üzembe helyezésének forgatókönyveiről.

Az Azure Stack Edge egy hardver-szolgáltatásként megoldás. A Microsoft egy felhővel kezelt eszközt szállít önnek egy beépített Field Programmable Gate Array (FPGA) rendszerrel, amely lehetővé teszi a gyorsított AI-következtetéseket, és rendelkezik a hálózati tárolóátjáró összes képességével.

## <a name="use-cases"></a>Használati esetek

Íme a különböző forgatókönyvek, ahol az Azure Stack Edge használható rapid Machine Learning (ML) következtetés a szélén, és az adatok előzetes feldolgozása az Azure-ba való elküldés előtt.

- **Következtetés az Azure Machine Learning** – Az Azure Stack Edge segítségével ml-modellek futtatásával gyors eredményeket kaphat, amelyek az adatok felhőbe küldése előtt is eljátszhatók. A teljes adatkészlet tetszés szerint átvihető az újratanítás és az ml-modellek továbbfejlesztése érdekében. Az Azure ML-es hardveres gyorsítású modellek Azure Stack Edge-eszközön való használatáról az [Azure ML-es hardveresen gyorsított modellek üzembe helyezése az Azure Stack Edge-en](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)című témakörben talál további információt.

- **Adatok előzetes feldolgozása** – Az adatok átalakítása az Azure-ba való elküldés előtt egy hatékonyabb adatkészlet létrehozásához. Az előfeldolgozás az alábbiakra használható: 

    - Adatok összesítése.
    - Módosítsa az adatokat, például távolítsa el a személyes adatokat.
    - Részhalmaza adatok optimalizálása tárolás és sávszélesség, vagy további elemzés.
    - Az IoT-események elemzése és reagálás rájuk 

- **Adatok átvitele hálózaton keresztül az Azure-ba** – Az Azure Stack Edge segítségével egyszerűen és gyorsan továbbíthat adatokat az Azure-ba további számítási és elemzési célokból, illetve archiválási célokra. 

## <a name="key-capabilities"></a>Főbb képességek

Az Azure Stack Edge a következő képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Gyorsított AI-következtetések| A beépített FPGA engedélyezte.|
|Számítás       |Lehetővé teszi az adatok elemzését, feldolgozását és szűrését.|
|Nagy teljesítmény | Nagy teljesítményű számítási és adatátviteli.|
|Az adatok elérése     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben. Az eszköz helyi gyorsítótára a legutóbb használt fájlok gyors elérésére szolgál.|
|Felhővel kezelt     |Az eszköz és a szolgáltatás kezelése az Azure Portalon keresztül.  |
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Támogatott protokollok     | A standard SMB és NFS protokollok támogatása az adatbetöltéshez. <br> A támogatott verziókról az [Azure Stack Edge rendszerkövetelményei](data-box-edge-system-requirements.md)című témakörben talál további információt.|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Titkosítás    | A BitLocker támogatja az adatok helyi titkosítását és biztonságos átvitelét a felhőbe *https*-en keresztül.|
|Sávszélesség-szabályozás| Szabályozás a sávszélesség-használat csúcsidőben való korlátozására.|
|ExpressRoute | Hozzáadott biztonság az ExpressRoute-on keresztül. Peering konfiguráció, ahol a helyi eszközökről a felhőbeli tárolási végpontok forgalmat az ExpressRoute-on keresztül. További információkat az [ExpressRoute áttekintésében](../expressroute/expressroute-introduction.md) találhat.

## <a name="components"></a>Összetevők

Az Azure Stack Edge-megoldás az Azure Stack Edge-erőforrásból, az Azure Stack Edge fizikai eszközből és egy helyi webes felhasználói felületből áll.

* **Azure Stack Edge fizikai eszköz** – A Microsoft által biztosított 1U-s, rackbe szerelt kiszolgáló, amely beállítható úgy, hogy adatokat küldjön az Azure-ba.
    
* **Azure Stack Edge-erőforrás** – egy erőforrás az Azure Portalon, amely lehetővé teszi az Azure Stack Edge-eszköz kezelését egy webes felületről, amely különböző földrajzi helyekről érhető el. Az Azure Stack Edge erőforrás használatával erőforrásokat hozhat létre és kezelhet, megtekintheti és kezelheti az eszközöket és riasztásokat, valamint kezelheti a megosztásokat.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    További információért válassza [a Rendelés létrehozása az Azure Stack Edge-eszközhöz](data-box-edge-deploy-prep.md#create-a-new-resource)című lehetőséget.

* **Azure Stack Edge helyi webes felhasználói felület** – a helyi webes felhasználói felület használatával futtassa a diagnosztikát, állítsa le és indítsa újra az Azure Stack Edge-eszközt, tekintse meg a másolási naplókat, és lépjen kapcsolatba a Microsoft támogatási szolgálatával a szolgáltatási kérelem benyújtásához.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    A webalapú felhasználói felület használatáról az [Azure Stack Edge felügyeletéhez a webalapú felhasználói felület használata című](data-box-edge-manage-access-power-connectivity-mode.md)lapban talál tájékoztatást.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure Stack Edge fizikai eszköz, az Azure-erőforrás és a céltárfiók, amelyre adatokat továbbít, nem kell minden ugyanabban a régióban.

- **Erőforrások rendelkezésre állása** – Az összes olyan régió listáját, ahol az Azure Stack Edge erőforrás elérhető, lásd: [Azure-termékek régiónként érhető el.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Az Azure Stack Edge az Azure Government Cloudban is telepíthető. További információ: [Mi az Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)
    
- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. Azok a régiók, ahol a tárfiókok az Azure Stack Edge-adatokat tárolják, az optimális teljesítmény érdekében közel kell lenniük ahhoz, ahol az eszköz található. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.

## <a name="next-steps"></a>További lépések

- Tekintse át az [Azure Stack Edge rendszerkövetelményeit.](data-box-edge-system-requirements.md)
- Ismerje meg az [Azure Stack Edge korlátait.](data-box-edge-limits.md)
- Telepítse [az Azure Azure Stack Edge-et](data-box-edge-deploy-prep.md) az Azure Portalon.
