---
title: Az Azure Blockchain Workbench előzetes verzió – áttekintés
description: Az Azure Blockchain Workbench előzetes verzió és képességeinek áttekintése.
ms.date: 09/05/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: 162626ba2303ab04bd2963a5add61c556615ebec
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79203644"
---
# <a name="what-is-azure-blockchain-workbench"></a>Mi az Azure Blockchain Workbench?

Az Azure Blockchain Workbench Preview az Azure-szolgáltatások és képességek gyűjteménye, amelyek segítségével hozhat létre és helyezhet üzembe blokklánc-alkalmazásokat üzleti folyamatok és adatok más szervezetekkel való megosztásához. Az Azure Blockchain Workbench biztosítja az infrastruktúraszerkezetet a blokkláncalkalmazások építéséhez, lehetővé téve ezzel a fejlesztők számára, hogy az üzleti logika és az intelligens szerződések létrehozására összpontosítsanak. Emellett megkönnyíti a blokkláncalkalmazások létrehozását is, hiszen számos Azure-szolgáltatást és képességet integrál az általános fejlesztési feladatok automatizálásának elősegítése céljából.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Blokkláncalkalmazások létrehozása

A Blockchain Workbench használatával konfigurációkkal és intelligensszerződés-kód írásával határozhat meg blokkláncalkalmazásokat. Rögtön belevághat a blokkláncalkalmazás-fejlesztésbe, és a szerződés meghatározására, illetve az üzleti logika megírására összpontosíthat ahelyett, hogy szerkezetet és támogatási szolgáltatásokat kellene felépítenie.

## <a name="manage-applications-and-users"></a>Alkalmazások és felhasználók kezelése

Az Azure Blockchain Workbench webalkalmazást és REST API-kat biztosít a blokkláncalkalmazások és a felhasználók kezeléséhez. A Blockchain Workbench rendszergazdái kezelhetik az alkalmazás-hozzáféréseket, és alkalmazás-szerepkörökhöz rendelhetik hozzá a felhasználókat. Az Azure AD felhasználói automatikusan leképeződnek az alkalmazásban lévő tagokra.

## <a name="integrate-blockchain-with-applications"></a>Blokklánc integrálása az alkalmazásokkal

A Blockchain Workbench REST API-jai és az üzenetalapú API-k a meglévő rendszerekkel való integrációra is használhatók. Az API-k olyan felületet biztosítanak, amely lehetővé teszi többféle elosztott tranzakciónapló-technológia, tárterület és adatbázis-ajánlat cseréjét vagy felhasználását.

A Blockchain Workbench átalakíthatja az üzenetalapú API-ba küldött üzeneteket, hogy a blokklánc natív API-ja által elvárt formátumban hozzon létre tranzakciókat.  A Workbench aláírja és a megfelelő blokklánchoz irányítja a tranzakciókat. 

A Workbench automatikusan továbbítja az eseményeket a Service Bus és az Event Grid részére az üzenetek az alárendelt fogyasztóknak történő elküldése érdekében. A fejlesztők ezen üzenetküldő rendszerek bármelyikével elvégezhetik az integrációt a tranzakciók irányításához és az eredmények megtekintéséhez.

## <a name="deploy-a-blockchain-network"></a>Blokklánchálózat üzembe helyezése

Az Azure Blockchain Workbench előre konfigurált megoldásként egy Azure Resource Manager megoldássablon használatával egyszerűsíti a konzorcium blokklánchálózatának beállítását. A sablon egyszerűsített üzembe helyezést tesz lehetővé, amely a konzorcium futtatásához szükséges összes összetevőt telepíti. Jelenleg a Blockchain Workbench támogatja az Ethereumot.

## <a name="use-active-directory"></a>Active Directory használata

A meglévő blokkláncprotokollokkal a blokkláncidentitások címként jelennek meg a hálózaton. Az Azure Blockchain Workbench átveszi a blokkláncidentitást azáltal, hogy társítja azt egy Active Directory-identitással, így a vállalati alkalmazások Active Directory-identitásokkal történő felépítése egyszerűbbé válik.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Láncon belüli adatok szinkronizálása láncon kívüli tárolóval

Az Azure Blockchain Workbench megkönnyíti a blokkláncesemények és az adatok elemzését azáltal, hogy a blokkláncon szereplő adatokat automatikusan szinkronizálja a láncon kívüli tárolóval. Ahelyett, hogy közvetlenül a blokkláncról nyerné az adatokat, láncon kívüli adatbázisrendszerekről, például az SQL Serverről kérdezheti le azokat. A blokklánc-szakértelem nem szükséges az adatelemzési feladatokat végző végfelhasználók számára.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Blockchain hírekért látogasson el az [Azure Blockchain blogjára,](https://azure.microsoft.com/blog/topics/blockchain/) hogy naprakész legyen az Azure Blockchain mérnöki csapatának blockchain szolgáltatásajánlatairól és információiról.

A termékvisszajelzés biztosításához vagy új funkciók igényléséhez szavazzon egy ötletre az [Azure blockchain visszajelzési fórumán](https://aka.ms/blockchainuservoice)keresztül.

### <a name="community-support"></a>Közösségi támogatás

Vegye fel a kapcsolatot a Microsoft mérnökeivel és az Azure Blockchain közösségi szakértőivel.

* [Azure Blockchain MSDN fórum](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench-architektúra](architecture.md)
