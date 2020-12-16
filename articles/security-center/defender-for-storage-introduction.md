---
title: Azure Defender for Storage – az előnyök és szolgáltatások
description: Ismerje meg az Azure Defender for Storage szolgáltatás előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8979e315f188a5c21cce206c24f195f72096d438
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516519"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Az Azure Defender tároláshoz bemutatása


Az **Azure Defender for Storage** egy olyan Azure-alapú biztonsági intelligencia, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kihasználásához. A biztonsági AI és a [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) speciális képességeinek kihasználásával környezetfüggő biztonsági riasztásokat és ajánlásokat biztosít.

A biztonsági riasztások akkor lépnek életbe, ha a tevékenységben anomáliák vannak. Ezek a riasztások a Azure Security Centerba vannak integrálva, és e-mailben is elküldhető az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és javításával kapcsolatos javaslatokkal kapcsolatban.


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|A Storage-hoz készült **Azure Defender** számlázása [a díjszabási oldalon](security-center-pricing.md) látható.|
|Védett tárolási típusok:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) US Gov<br>![Nem](./media/icons/no-icon.png) Kínai gov, egyéb gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Milyen típusú riasztások biztosítják az Azure Defender for Storage szolgáltatást?

A biztonsági riasztások akkor aktiválódnak, ha:

- **Gyanús hozzáférési minták** – például sikeres hozzáférés egy Tor-kilépési csomópontból vagy egy olyan IP-címről, amely a Microsoft Threat Intelligence által gyanúsnak minősül
- **Gyanús tevékenységek** – például rendellenes Adatkiemelés vagy a hozzáférési engedélyek szokatlan módosítása
- **Rosszindulatú tartalom feltöltése** – például a lehetséges kártevő fájlok (a kivonatok elemzése alapján) vagy az adathalászat-tartalom üzemeltetése

A riasztások tartalmazzák az azokat kiváltó incidens részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat. A riasztások exportálhatók az Azure Sentinelbe vagy bármely más harmadik féltől származó SIEM vagy más külső eszközre.

> [!TIP]
> Javasoljuk, hogy az [Azure Defender for Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center) szolgáltatást az előfizetés szintjén konfigurálja, de az [Egyéni Storage-fiókok esetében is konfigurálható](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Mi a kivonatoló hírnevének elemzése kártevők számára?

Annak megállapításához, hogy a feltöltött fájl gyanús-e, az Azure Defender for Storage a [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684)által támogatott kivonat-elemzést használ. A veszélyforrások elleni védelem eszközei nem ellenőrzik a feltöltött fájlokat, hanem megvizsgálják a tárolási naplókat, és összehasonlítják az újonnan feltöltött fájlok kivonatait az ismert vírusok, trójaiak, kémprogramok és ransomware esetében. 

Ha egy fájl gyanúja szerint kártevőt tartalmaz, Security Center riasztást jelenít meg, és opcionálisan e-mailben is elküldheti a tároló tulajdonosának jóváhagyását a gyanús fájl törléséhez. Ha be szeretné állítani a fájlok automatikus eltávolítását, amely alapján a kivonatoló hírnevének elemzése a kártevőket tartalmazza, a Munkafolyamat-automatizálás üzembe helyezésével [aktiválhatja azokat a riasztásokat, amelyek "a Storage-fiókba feltöltött lehetséges kártevőket" tartalmaznak](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Az Security Center veszélyforrások elleni védelmi képességeinek engedélyezéséhez engedélyeznie kell az Azure Defender számára a megfelelő munkaterheléseket tartalmazó előfizetést.
>
> Az **Azure Defender a Storage szolgáltatáshoz** az előfizetés szintjén vagy az erőforrás szintjén is engedélyezhető.



## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta az Azure Defender for Storage szolgáltatással kapcsolatos tudnivalókat.

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- Azt határozza meg, hogy a riasztást a Security Center hozza-e létre, vagy egy másik biztonsági terméktől Security Center fogadta-e, exportálhatja. Ha a riasztásokat az Azure Sentinelbe, a harmadik féltől származó SIEM-re vagy más külső eszközre szeretné exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait.
- [A speciális Defender engedélyezése a Storage-ban](../storage/common/azure-defender-storage-configure.md)
- [Az Azure Defender tárolási riasztások listája](alerts-reference.md#alerts-azurestorage)
- [A Microsoft fenyegetés-felderítési képességei](https://go.microsoft.com/fwlink/?linkid=2128684)
