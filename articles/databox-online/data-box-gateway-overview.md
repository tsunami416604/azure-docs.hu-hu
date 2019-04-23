---
title: A Microsoft Azure Data Box Gateway áttekintése | Microsoft Docs
description: A cikk ismerteti az Azure Data Box Gateway virtuálisberendezés-tároló megoldást, amellyel adatokat vihet át az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 04/08/2019
ms.author: alkohli
ms.openlocfilehash: 340ff99aae9acff49ffdeaa43463521debb16a07
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797584"
---
# <a name="what-is-azure-data-box-gateway"></a>Mi az Azure Data Box átjáró?

Az Azure Data Box Gateway egy tárolási megoldás, amellyel zökkenőmentesen vihet át adatokat az Azure-ba. Ez a cikk áttekintést nyújt az Azure Data Box Gateway megoldásról, annak előnyeiről, főbb képességeiről és azokról a forgatókönyvekről, amelyekben üzembe helyezheti ezt az eszközt.

A Data Box Gateway egy virtuális eszköz, amely egy, az Ön virtualizált környezetében vagy hipervizorán létrehozott virtuális gépen alapul. A virtuális eszköz a helyszínen található, és Ön írja rá az adatokat NFS és SMB protokoll használatával. Az eszköz ezután átviszi az adatait az Azure-beli blokkblobba, lapblobba vagy az Azure Filesba.

## <a name="use-cases"></a>Használati esetek

A Data Box Gatewayt használhatja felhőbe történő adatátvitelre, például felhőben történő archiválásra, vészhelyreállításra, vagy ha felhőszinten kell feldolgoznia az adatait. Itt találhatók azok a különböző forgatókönyvek, amelyekben a Data Box Gateway használata javasolt az adatátvitelhez.

- **Felhőben történő archiválás** – A Data Box Gateway segítségével több száz TB-nyi adatot másolhat át biztonságosan és hatékonyan az Azure Storage-ba. Az archiválási forgatókönyvek keretében az adatokat betöltheti egy alkalommal vagy folyamatosan is.

- **Folyamatos adatbetöltés** -folyamatosan betölteni az adatokat az az eszköz másolása a felhőbe, az adatok méretétől függetlenül. Mivel az adatok írása az átjáróeszközhöz, az eszköz feltölti az adatokat az Azure Storage.  

- **A kezdeti növekményes átviteli követ tömeges adatátviteli** -Data Box használatát a tömeges egy kapcsolat nélküli módban (kezdeti kezdőérték) és a Data Box-átjáró a növekményes átvitelt (folyamatban lévő hírcsatorna) a hálózati átvitel.

További információért ugorjon [Azure Data Box Gateway használati esetek](data-box-gateway-use-cases.md).

## <a name="benefits"></a>Előnyök

A Data Box Gateway az alábbi előnyökkel jár:

- **Egyszerű adatátvitel** – Használatával olyan könnyedén helyezhet át adatokat az Azure Storage-ból vagy az Azure Storage-ba, mintha csak egy helyi hálózati megosztással dolgozna.  
- **Nagy teljesítmény** – Nagy teljesítményű adatátvitelével problémamentessé válik a hálózati adatátvitel az Azure-ban.
- **Gyors hozzáférés és a magas Adatbetöltési díjait számoljuk fel munkaidőben** -Box adatátjáró rendelkezik a helyi gyorsítótárat, Ön által meghatározott helyi kapacitás méretének van a virtuális eszköz üzembe helyezésekor. Az adatlemez mérete megfelelően meg kell adni a [virtuális eszköz minimális követelmények](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). A helyi gyorsítótár a következő előnyöket nyújtja:
    - A helyi gyorsítótár adatbetöltés lehetővé teszi a nagy gyakorisággal. Nagy mennyiségű adatot betöltött csúcs munkaidő alatt, ha a gyorsítótárban tárolják az adatokat, és töltse fel a felhőbe.
    - A helyi gyorsítótár gyors olvasási hozzáférést biztosít, amíg egy bizonyos küszöbértéket. Amíg az eszköz 50 – 60 %-os teljes, az eszközről az olvasások érhető el a gyorsítótárban, így gyorsabban elérhetővé. Miután a használatban lévő tárterület az eszközön túllépik ezt a küszöbértéket, majd az eszköz elindul, és távolítsa el a helyi fájlok.
 
- **Korlátozott sávszélesség-használat** – Az Azure-ba akkor is lehet adatot írni, amikor a hálózat használata a csúcsidő során korlátozva van.  

## <a name="key-capabilities"></a>Főbb képességek

A Data Box Gateway az alábbi képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Sebesség     | Teljesen automatizált és nagymértékben optimalizált adatátvitel és sávszélesség.|
|Támogatott protokollok     | A standard SMB és NFS protokollok támogatása az adatbetöltéshez. <br> További információt a támogatott verziókkal kapcsolatban [a Data Box Gateway rendszerkövetelményeiben](data-box-gateway-system-requirements.md) találhat.|
|Az adatok elérése     | Amint az eszköz által küldött adatok a felhőben, akkor további módosíthatók a felhőalapú API-k közvetlen elérésével.|
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
| Memory (Memória)  |Minimum 8 GB|
| Rendelkezésre állás|Egyetlen csomópont|
| Lemezek|Operációsrendszer-lemez: 250 GB <br> Adatlemez: 2 TB-os minimális, dinamikusan kiosztott és SSD-k által kell készíteni|
| Hálózati illesztők |1 vagy több virtuális hálózati adapter|
| Natív fájlmegosztási protokollok|SMB és NFS  |
| Biztonság|Hitelesítés az eszköz és az adatok hozzáférésének feloldásához <br> Átvitel alatt álló adatok titkosítása AES-256 bites titkosítás használatával|
| Kezelés|Helyi webes felhasználói felület – kezdeti beállítás, diagnosztika és az eszköz energiagazdálkodása <br> Azure Portal – a Data Box Gateway-eszközök napi szintű felügyelete       |

## <a name="components"></a>Összetevők

A Data Box Gateway megoldás a Data Box Gateway-erőforrásból, a Data Box Gateway-virtuáliseszközből és egy helyi webes felhasználói felületből áll.

- **Data Box Gateway virtuális eszköz** – Egy eszköz, amely egy, az Ön virtualizált környezetében vagy hipervizorán létrehozott virtuális gépen fut, és lehetővé teszi az adatok Azure-ba történő küldését.
    
- **Data Box Gateway-erőforrás** – Az Azure Portal erőforrása, amely lehetővé teszi a Data Box Gateway-eszközök kezelését egy webes felületről, amelyet eltérő földrajzi helyekről is elérhet. Használja a Data Box-Gateway-erőforrás megtekintése és kezelése az eszköz, a megosztások, a felhasználók és a riasztásokat. További információkért lásd: hogyan [kezelése az Azure portal használatával](data-box-gateway-manage-shares.md).

- **Data Box helyi webes felületén** – a helyi webes felhasználói felületének használatával diagnosztika futtatása, állítsa le és indítsa újra az eszközt, hozzon létre egy támogatási csomagot, vagy forduljon a Microsoft Support, küldjön szolgáltatási kérelmet. További információkért lásd: hogyan [helyi webes felhasználói felület használata kezelheti](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Régiónkénti elérhetőség

Data Box-átjáró fizikai eszközt, az Azure erőforrás- és, amelyhez adatátvitel történik a célként megadott tárfiók nem az összes kell ugyanabban a régióban.

- **Erőforrás rendelkezésre állási** – ebben a kiadásban a Data Box-Gateway-erőforrás érhető el ezekben a régiókban, amely támogatja a nyilvános felhő:
    - **Egyesült Államok** – USA keleti RÉGIÓJA
    - **Európai Unió** – Nyugat-Európa
    - **Ázsia Csendes-óceáni** – Délkelet-Ázsia

    Az Azure Government cloud Data Box-átjáró is telepíthető. További információkért lásd: [Mi az Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők.

    A Data Box adatait tároló tárfiókok régióinaknak az eszköz közelében kell lennie az optimális teljesítmény elérése érdekében. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.


## <a name="next-steps"></a>További lépések

- A [Data Box Gateway rendszerkövetelményeinek](data-box-gateway-system-requirements.md) áttekintése.
- A [Data Box Gateway korlátainak](data-box-gateway-limits.md) megismerése.
- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.

