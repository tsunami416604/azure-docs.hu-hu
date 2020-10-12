---
title: Azure Defender for Storage – az előnyök és szolgáltatások
description: Ismerje meg az Azure Defender for Storage szolgáltatás előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4677426337a48d4fde74f61b8a4ad6fcb695f420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577815"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Bevezetés az Azure Defender for Storage használatába

Az **Azure Defender for Storage** észleli az Azure Storage-fiókokban potenciálisan ártalmas tevékenységeket. Az Ön adatai védetté tehetik, hogy blob-tárolóként, fájlmegosztásként vagy adattavakként vannak tárolva.

Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését *anélkül* , hogy biztonsági szakértőnek kellene lennie, és segít a biztonsági monitorozási rendszerek kezelésében.


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|A Storage-hoz készült **Azure Defender** számlázása [a díjszabási oldalon](security-center-pricing.md) látható.|
|Védett tárolási típusok:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) US Gov<br>![Nem](./media/icons/no-icon.png) Kínai gov, egyéb gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Milyen típusú riasztások biztosítják az Azure Defender for Storage szolgáltatást?

A biztonsági riasztások akkor aktiválódnak, ha:

- **Gyanús tevékenység** – például a Storage-fiók sikeresen elérhető a Tor aktív kilépési csomópontjának nevezett IP-címről.
- **Rendellenes viselkedés** – például a hozzáférési minta módosítása egy Storage-fiókra
- **Lehetséges kártevők feltöltése** – a kivonatoló hírnevének elemzése azt jelzi, hogy egy feltöltött fájl kártevőket tartalmaz

A riasztások tartalmazzák az azokat kiváltó incidens részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat.

> [!TIP]
> A tárolási riasztások szimulálása a [blogbejegyzés](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)utasításait követve végezhető el.


## <a name="what-is-hash-reputation-analysis-for-malware"></a>Mi a kivonatoló hírnevének elemzése kártevők számára?

Annak megállapításához, hogy a feltöltött fájl gyanús-e, az Azure Defender for Storage a [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684)által támogatott kivonat-elemzést használ. A veszélyforrások elleni védelem eszközei nem ellenőrzik a feltöltött fájlokat, hanem megvizsgálják a tárolási naplókat, és összehasonlítják az újonnan feltöltött fájlok kivonatait az ismert vírusok, trójaiak, kémprogramok és ransomware esetében. 

Ha egy fájl gyanúja szerint kártevőt tartalmaz, Security Center riasztást jelenít meg, és opcionálisan e-mailben is elküldheti a tároló tulajdonosának jóváhagyását a gyanús fájl törléséhez. Ha be szeretné állítani a fájlok automatikus eltávolítását, amely alapján a kivonatoló hírnevének elemzése a kártevőket tartalmazza, a Munkafolyamat-automatizálás üzembe helyezésével [aktiválhatja azokat a riasztásokat, amelyek "a Storage-fiókba feltöltött lehetséges kártevőket" tartalmaznak](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Az Security Center veszélyforrások elleni védelmi képességeinek engedélyezéséhez engedélyeznie kell az Azure Defender számára a megfelelő munkaterheléseket tartalmazó előfizetést.
>
> Az **Azure Defender a Storage szolgáltatáshoz** az előfizetés szintjén vagy az erőforrás szintjén is engedélyezhető.



## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta az Azure Defender for Storage szolgáltatással kapcsolatos tudnivalókat.

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- Azt határozza meg, hogy a riasztást a Security Center hozza-e létre, vagy egy másik biztonsági terméktől Security Center fogadta-e, exportálhatja. Ha a riasztásokat az Azure Sentinelbe, a harmadik féltől származó SIEM-re vagy más külső eszközre szeretné exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait.
- [A speciális Defender engedélyezése a Storage-ban](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [Az Azure Defender tárolási riasztások listája](alerts-reference.md#alerts-azurestorage)
- [A Microsoft fenyegetés-felderítési képességei](https://go.microsoft.com/fwlink/?linkid=2128684)