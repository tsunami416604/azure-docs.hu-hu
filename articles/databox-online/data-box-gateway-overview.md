---
title: A Microsoft Azure Data Box Gateway áttekintése | Microsoft Docs
description: A cikk ismerteti az Azure Data Box Gateway virtuálisberendezés-tároló megoldást, amellyel adatokat vihet át az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 4f1ab6d955c81ce6f7b141eef42341f43bb379f6
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165317"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>Mi az az Azure Data Box Gateway (előzetes verzió)? 

Az Azure Data Box Gateway egy tárolási megoldás, amellyel zökkenőmentesen vihet át adatokat az Azure-ba. Ez a cikk áttekintést nyújt az Azure Data Box Gateway megoldásról, annak előnyeiről, főbb képességeiről és azokról a forgatókönyvekről, amelyekben üzembe helyezheti ezt az eszközt. 

A Data Box Gateway egy virtuális eszköz, amely egy, az Ön virtualizált környezetében vagy hipervizorán létrehozott virtuális gépen alapul. A virtuális eszköz a helyszínen található, és Ön írja rá az adatokat NFS és SMB protokoll használatával. Az eszköz ezután átviszi az adatait az Azure-beli blokkblobba, lapblobba vagy az Azure Filesba. 

> [!IMPORTANT]
> A Data Box Gateway előzetes verzióban érhető el. A megoldás üzembe helyezése előtt kérjük tekintse át az [előzetes verziókra vonatkozó használati feltételeket](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Használati esetek

A Data Box Gatewayt használhatja felhőbe történő adatátvitelre, például felhőben történő archiválásra, vészhelyreállításra, vagy ha felhőszinten kell feldolgoznia az adatait. Itt találhatók azok a különböző forgatókönyvek, amelyekben a Data Box Gateway használata javasolt az adatátvitelhez.

- **Felhőben történő archiválás** – A Data Box Gateway segítségével több száz TB-nyi adatot másolhat át biztonságosan és hatékonyan az Azure Storage-ba. Az archiválási forgatókönyvek keretében az adatokat betöltheti egy alkalommal vagy folyamatosan is.

- **Adatösszesítés** – Az Azure Storage-ban több forrás adatait összesítheti egyetlen helyen adatfeldolgozás és -elemzés céljából.  

- **Integráció a helyszíni számítási feladatokkal** – Integrálhatja olyan helyszíni számítási feladatokkal, mint például a biztonsági mentés és a visszaállítás, amelyek felhőalapú tárolást használnak, és helyi hozzáférésre van szükségük a gyakran használt fájlokhoz. 

## <a name="benefits"></a>Előnyök

A Data Box Gateway az alábbi előnyökkel jár:

- **Egyszerű adatátvitel** – Használatával olyan könnyedén helyezhet át adatokat az Azure Storage-ból vagy az Azure Storage-ba, mintha csak egy helyi hálózati megosztással dolgozna.  
- **Nagy teljesítmény** – Nagy teljesítményű adatátvitelével problémamentessé válik a hálózati adatátvitel az Azure-ban. 
- **Gyors hozzáférés** – A legutóbbi fájlokat a gyorsítótárba menti a helyszíni fájlok gyorsabb elérése érdekében.  
- **Korlátozott sávszélesség-használat** – Az Azure-ba akkor is lehet adatot írni, amikor a hálózat használata a csúcsidő során korlátozva van.  

## <a name="key-capabilities"></a>Főbb képességek

A Data Box Gateway az alábbi képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Sebesség     | Teljesen automatizált és nagymértékben optimalizált adatátvitel és sávszélesség.|
|Támogatott protokollok     | A standard SMB és NFS protokollok támogatása az adatbetöltéshez. <br> További információt a támogatott verziókkal kapcsolatban [a Data Box Gateway rendszerkövetelményeiben](data-box-gateway-system-requirements.md) találhat.|
|Az adatok elérése     | Az adatok közvetlen elérése az Azure Storage-blobokból és az Azure Filesból felhőalapú API-k használatával az adatok további feldolgozásához a felhőben.|
|Gyors hozzáférés     | Az eszközön helyi gyorsítótár található, amellyel gyorsabban elérhetők a legutóbb használt fájlok.|
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Titkosítás    | A BitLocker támogatja az adatok helyi titkosítását és biztonságos átvitelét a felhőbe *https*-en keresztül.       |
|Rugalmasság     | Beépített hálózati rugalmasság        |


## <a name="specifications"></a>Specifikációk

A Data Box Gateway virtuális eszköz az alábbi műszaki adatokkal rendelkezik:

| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Virtuális processzorok (magok)   | Minimum 4 |            
| Memory (Memória)  | Minimum 8 GB|
| Rendelkezésre állás|Egyetlen csomópont|
| Lemezek| Operációsrendszer-lemez: 250 GB <br> Adatlemez: legalább 2 TB, dinamikusan kiosztott és SSD-másolatokkal kiegészített|
| Hálózati illesztők|1 vagy több virtuális hálózati adapter|
| Natív fájlmegosztási protokollok|SMB és NFS  |
| Biztonság| Hitelesítés az eszköz és az adatok hozzáférésének feloldásához <br> Átvitel alatt álló adatok titkosítása AES-256 bites titkosítás használatával|
| Kezelés| Helyi webes felhasználói felület – kezdeti beállítás, diagnosztika és az eszköz energiagazdálkodása <br> Azure Portal – a Data Box Gateway-eszközök napi szintű felügyelete       |


## <a name="components"></a>Összetevők

A Data Box Gateway megoldás a Data Box Gateway-erőforrásból, a Data Box Gateway-virtuáliseszközből és egy helyi webes felhasználói felületből áll.

* **Data Box Gateway virtuális eszköz** – Egy eszköz, amely egy, az Ön virtualizált környezetében vagy hipervizorán létrehozott virtuális gépen fut, és lehetővé teszi az adatok Azure-ba történő küldését. 
    
* **Data Box Gateway-erőforrás** – Az Azure Portal erőforrása, amely lehetővé teszi a Data Box Gateway-eszközök kezelését egy webes felületről, amelyet eltérő földrajzi helyekről is elérhet. A Data Box Gateway-erőforrás használatával erőforrásokat készíthet és kezelhet, eszközöket és riasztásokat tekinthet meg és kezelhet, illetve megosztásokat kezelhet.  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Data Box helyi webes felhasználói felület** – A helyi webes felhasználói felülettel diagnosztikát futtathat, leállíthatja és újraindíthatja a Data Box Gateway eszközt, megtekintheti a másolási naplókat, valamint szolgáltatáskérések elküldése érdekében kapcsolatba léphet a Microsoft ügyfélszolgálatával.

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>Régiónkénti elérhetőség

A Data Box Edge fizikai eszköznek, az Azure-erőforrásnak és a céloldali tárfióknak, amelybe átviszi az adatokat, nem kell egyazon régióban lenniük.

- **Az erőforrás elérhetősége** – Ebben a kiadásban a Data Box Edge-erőforrás a következő régiókban érhető el:
    - **Egyesült Államok** – Az USA 2. nyugati régiója és az USA keleti régiója
    - **Európai Unió** – Nyugat-Európa
    - **Ázsia és a Csendes-óceáni térség** – Délkelet-Ázsia

- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők. 

    A Data Box adatait tároló tárfiókok régióinaknak az eszköz közelében kell lennie az optimális teljesítmény elérése érdekében. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható. 


## <a name="next-steps"></a>További lépések

- A [Data Box Gateway rendszerkövetelményeinek](data-box-gateway-system-requirements.md) áttekintése.
- A [Data Box Gateway korlátainak](data-box-gateway-limits.md) megismerése.
- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.




