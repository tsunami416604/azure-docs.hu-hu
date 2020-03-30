---
title: A Microsoft Azure Data Box Gateway áttekintése | Microsoft Docs
description: A cikk ismerteti az Azure Data Box Gateway virtuálisberendezés-tároló megoldást, amellyel adatokat vihet át az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b8c6b4085f56bc12b67bf87177ba33b82c6a6db9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "69900590"
---
# <a name="what-is-azure-data-box-gateway"></a>Mi az Azure Data Box Gateway?

Az Azure Data Box Gateway egy tárolási megoldás, amellyel zökkenőmentesen vihet át adatokat az Azure-ba. Ez a cikk áttekintést nyújt az Azure Data Box Gateway megoldásról, annak előnyeiről, főbb képességeiről és azokról a forgatókönyvekről, amelyekben üzembe helyezheti ezt az eszközt.

A Data Box Gateway egy virtuális eszköz, amely egy, az Ön virtualizált környezetében vagy hipervizorán létrehozott virtuális gépen alapul. A virtuális eszköz a helyszínen található, és Ön írja rá az adatokat NFS és SMB protokoll használatával. Az eszköz ezután átviszi az adatait az Azure-beli blokkblobba, lapblobba vagy az Azure Filesba.

## <a name="use-cases"></a>Használati esetek

A Data Box Gatewayt használhatja felhőbe történő adatátvitelre, például felhőben történő archiválásra, vészhelyreállításra, vagy ha felhőszinten kell feldolgoznia az adatait. Itt találhatók azok a különböző forgatókönyvek, amelyekben a Data Box Gateway használata javasolt az adatátvitelhez.

- **Felhőben történő archiválás** – A Data Box Gateway segítségével több száz TB-nyi adatot másolhat át biztonságosan és hatékonyan az Azure Storage-ba. Az archiválási forgatókönyvek keretében az adatokat betöltheti egy alkalommal vagy folyamatosan is.

- **Folyamatos adatbetöltés** – Az adatok folyamatos betöltése az eszközbe az adatok méretétől függetlenül a felhőbe történő másoláshoz. Az adatok írása az átjáróeszközre, az eszköz feltölti az adatokat az Azure Storage-ba.  

- **Kezdeti tömeges átvitel, majd növekményes átvitel** – Használja az adatdobozt az offline módban (kezdőmag) és a Data Box Gateway-t a hálózaton keresztüli növekményes átvitelhez (folyamatos hírcsatorna).

További információ: [Azure Data Box Gateway használati esetek.](data-box-gateway-use-cases.md)

## <a name="benefits"></a>Előnyök

A Data Box Gateway az alábbi előnyökkel jár:

- **Egyszerű adatátvitel** – Használatával olyan könnyedén helyezhet át adatokat az Azure Storage-ból vagy az Azure Storage-ba, mintha csak egy helyi hálózati megosztással dolgozna.  
- **Nagy teljesítmény** – Nagy teljesítményű adatátvitelével problémamentessé válik a hálózati adatátvitel az Azure-ban.
- **Gyors hozzáférés és magas adatbetöltési sebesség munkaidőben** – a Data Box Gateway helyi gyorsítótárral rendelkezik, amelyet a virtuális eszköz kiépítésekor helyi kapacitásméretként határoz meg. Az adatlemez méretét a virtuális [eszköz minimális követelményeinek](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device)megfelelően kell megadni. A helyi gyorsítótár a következő előnyöket nyújtja:
    - A helyi gyorsítótár lehetővé teszi az adatok nagy mértékű betöltését. Ha csúcsidőben nagy mennyiségű adatot tölt be, a gyorsítótár tárolhatja az adatokat, és feltöltheti azadatokat a felhőbe.
    - A helyi gyorsítótár lehetővé teszi a gyors olvasási hozzáférést egy bizonyos küszöbértékig. Amíg az eszköz 50-60% tele nem lesz, az eszköz összes olvasása elérhető a gyorsítótárból, így gyorsabbak. Miután az eszközön használt terület meghaladja ezt a küszöbértéket, az eszköz elkezdi eltávolítani a helyi fájlokat.
 
- **Korlátozott sávszélesség-használat** – Az Azure-ba akkor is lehet adatot írni, amikor a hálózat használata a csúcsidő során korlátozva van.  

## <a name="key-capabilities"></a>Főbb képességek

A Data Box Gateway az alábbi képességekkel rendelkezik:

|Képesség |Leírás  |
|---------|---------|
|Sebesség     | Teljesen automatizált és nagymértékben optimalizált adatátvitel és sávszélesség.|
|Támogatott protokollok     | A standard SMB és NFS protokollok támogatása az adatbetöltéshez. <br> További információt a támogatott verziókkal kapcsolatban [a Data Box Gateway rendszerkövetelményeiben](data-box-gateway-system-requirements.md) találhat.|
|Az adatok elérése     | Miután az eszköz által küldött adatok a felhőben, tovább módosítható közvetlenül a felhő API-k elérésével.|
|Gyors hozzáférés     | Az eszközön helyi gyorsítótár található, amellyel gyorsabban elérhetők a legutóbb használt fájlok.|
|Offline feltöltés     | A kapcsolat nélküli mód támogatja az offline feltöltési forgatókönyveket.|
|Adatfrissítés     | Lehetőség van a helyi fájlok legújabb változatra való frissítésére a felhőből.|
|Rugalmasság     | Beépített hálózati rugalmasság        |


## <a name="specifications"></a>Specifikációk

A Data Box Gateway virtuális eszköz az alábbi műszaki adatokkal rendelkezik:

| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Virtuális processzorok (magok)   | Minimum 4 |
| Memory (Memória)  |Minimum 8 GB|
| Rendelkezésre állás|Egyetlen csomópont|
| Lemezek|Operációsrendszer-lemez: 250 GB <br> Adatlemez: legalább 2 TB, dinamikusan kiosztott és SSD-másolatokkal kiegészített|
| Hálózati illesztők |1 vagy több virtuális hálózati adapter|
| Natív fájlmegosztási protokollok|SMB és NFS  |
| Biztonság|Hitelesítés az eszköz és az adatok hozzáférésének feloldásához <br> Átvitel alatt álló adatok titkosítása AES-256 bites titkosítás használatával|
| Kezelés|Helyi webes felhasználói felület – kezdeti beállítás, diagnosztika és az eszköz energiagazdálkodása <br> Azure Portal – a Data Box Gateway-eszközök napi szintű felügyelete       |

## <a name="components"></a>Összetevők

A Data Box Gateway megoldás a Data Box Gateway-erőforrásból, a Data Box Gateway-virtuáliseszközből és egy helyi webes felhasználói felületből áll.

- **Data Box Gateway virtuális eszköz** – Egy eszköz, amely egy, az Ön virtualizált környezetében vagy hipervizorán létrehozott virtuális gépen fut, és lehetővé teszi az adatok Azure-ba történő küldését.
    
- **Data Box Gateway-erőforrás** – Az Azure Portal erőforrása, amely lehetővé teszi a Data Box Gateway-eszközök kezelését egy webes felületről, amelyet eltérő földrajzi helyekről is elérhet. A Data Box Gateway erőforrással megtekintheti és kezelheti az eszközöket, a megosztásokat, a felhasználókat és a riasztásokat. További információt az [Azure Portal használatával való kezelés című témakörben talál.](data-box-gateway-manage-shares.md)

- **Data Box helyi webes felhasználói felület** – A helyi webes felhasználói felület segítségével futtathat diagnosztikát, leállíthatja és újraindíthatja az eszközt, támogatási csomagot hozhat létre, vagy a Microsoft támogatási szolgálatához fordulhat szolgáltatási kérelem benyújtásához. További információt a [Helyi webes felhasználói felület kezeléséről](data-box-gateway-manage-access-power-connectivity-mode.md)talál.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Data Box Gateway fizikai eszköz, az Azure-erőforrás és a céltárfiók, amelyre adatokat továbbít, nem minden kell ugyanabban a régióban.

- **Erőforrások elérhetősége** – Az összes olyan régió listáját, ahol a Data Box Edge erőforrás elérhető, látogasson el az [Azure-termékek régiónként érhető el.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox) A Data Box Gateway az Azure Government Cloudban is telepíthető. További információ: [Mi az Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- **Céloldali tárfiókok** – Az adatokat tároló tárfiókok minden Azure-régióban elérhetők.

    A Data Box adatait tároló tárfiókok régióinaknak az eszköz közelében kell lennie az optimális teljesítmény elérése érdekében. Az eszköztől távol található tárfiók esetében hosszú késések és lassabb teljesítmény várható.


## <a name="next-steps"></a>További lépések

- A [Data Box Gateway rendszerkövetelményeinek](data-box-gateway-system-requirements.md) áttekintése.
- A [Data Box Gateway korlátainak](data-box-gateway-limits.md) megismerése.
- Az [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) üzembe helyezése az Azure Portalon.

