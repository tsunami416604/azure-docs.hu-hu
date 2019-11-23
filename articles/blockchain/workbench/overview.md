---
title: Azure Blockchain Workbench Preview overview
description: Overview of Azure Blockchain Workbench Preview and its capabilities.
ms.date: 09/05/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: 9c40f95aa1490362b9232ec8d87a199d7a41c731
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324552"
---
# <a name="what-is-azure-blockchain-workbench"></a>Mi az Azure Blockchain Workbench?

Azure Blockchain Workbench Preview is a collection of Azure services and capabilities designed to help you create and deploy blockchain applications to share business processes and data with other organizations. Az Azure Blockchain Workbench biztosítja az infrastruktúraszerkezetet a blokkláncalkalmazások építéséhez, lehetővé téve ezzel a fejlesztők számára, hogy az üzleti logika és az intelligens szerződések létrehozására összpontosítsanak. Emellett megkönnyíti a blokkláncalkalmazások létrehozását is, hiszen számos Azure-szolgáltatást és képességet integrál az általános fejlesztési feladatok automatizálásának elősegítése céljából.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Blokkláncalkalmazások létrehozása

A Blockchain Workbench használatával konfigurációkkal és intelligensszerződés-kód írásával határozhat meg blokkláncalkalmazásokat. Rögtön belevághat a blokkláncalkalmazás-fejlesztésbe, és a szerződés meghatározására, illetve az üzleti logika megírására összpontosíthat ahelyett, hogy szerkezetet és támogatási szolgáltatásokat kellene felépítenie.

## <a name="manage-applications-and-users"></a>Alkalmazások és felhasználók kezelése

Az Azure Blockchain Workbench webalkalmazást és REST API-kat biztosít a blokkláncalkalmazások és a felhasználók kezeléséhez. A Blockchain Workbench rendszergazdái kezelhetik az alkalmazás-hozzáféréseket, és alkalmazás-szerepkörökhöz rendelhetik hozzá a felhasználókat. Az Azure AD felhasználói automatikusan leképeződnek az alkalmazásban lévő tagokra.

## <a name="integrate-blockchain-with-applications"></a>Blokklánc integrálása az alkalmazásokkal

A Blockchain Workbench REST API-jai és az üzenetalapú API-k a meglévő rendszerekkel való integrációra is használhatók. Az API-k olyan felületet biztosítanak, amely lehetővé teszi többféle elosztott tranzakciónapló-technológia, tárterület és adatbázis-ajánlat cseréjét vagy felhasználását.

A Blockchain Workbench át tudja alakítani az üzenetalapú API-ja számára küldött üzeneteket, hogy a tranzakciók a blokklánc natív API-ja által elvárt formátumban jöhessenek létre.  A Workbench aláírja és a megfelelő blokklánchoz irányítja a tranzakciókat. 

A Workbench automatikusan továbbítja az eseményeket a Service Bus és az Event Grid részére az üzenetek az alárendelt fogyasztóknak történő elküldése érdekében. A fejlesztők ezen üzenetküldő rendszerek bármelyikével elvégezhetik az integrációt a tranzakciók irányításához és az eredmények megtekintéséhez.

## <a name="deploy-a-blockchain-network"></a>Blokklánchálózat üzembe helyezése

Az Azure Blockchain Workbench előre konfigurált megoldásként egy Azure Resource Manager megoldássablon használatával egyszerűsíti a konzorcium blokklánchálózatának beállítását. A sablon egyszerűsített üzembe helyezést tesz lehetővé, amely a konzorcium futtatásához szükséges összes összetevőt telepíti. Jelenleg a Blockchain Workbench támogatja az Ethereumot.

## <a name="use-active-directory"></a>Active Directory használata

A meglévő blokkláncprotokollokkal a blokkláncidentitások címként jelennek meg a hálózaton. Az Azure Blockchain Workbench átveszi a blokkláncidentitást azáltal, hogy társítja azt egy Active Directory-identitással, így a vállalati alkalmazások Active Directory-identitásokkal történő felépítése egyszerűbbé válik.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Láncon belüli adatok szinkronizálása láncon kívüli tárolóval

Az Azure Blockchain Workbench megkönnyíti a blokkláncesemények és az adatok elemzését azáltal, hogy a blokkláncon szereplő adatokat automatikusan szinkronizálja a láncon kívüli tárolóval. Ahelyett, hogy közvetlenül a blokkláncról nyerné az adatokat, láncon kívüli adatbázisrendszerekről, például az SQL Serverről kérdezheti le azokat. Blockchain expertise is not required for end users who are doing data analysis tasks.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench-architektúra](architecture.md)
