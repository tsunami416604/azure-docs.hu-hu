---
title: Az Azure és az appsource-ban Marketplace-ajánlat |} A Microsoft Docs
description: Az Azure és az appsource-ban Piacterein kínál létrehozása és kezelése
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pbutlerm
ms.openlocfilehash: f13d49fde7f0e40f6dcb026fcb20cb11c028c64b
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100883"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Az Azure és az appsource-ban Marketplace-ajánlat

Ez ez a szakasz első része bemutatja a hozhat létre és kezelheti a ajánlatok az Azure és az AppSource-piactér általános műveleteiről.  Ez a rész a meghatározott típusú kezeléséhez ismernie kell a háttérben, valamint a közös technikai információkat kínálnak típusokat.  Ez a szakasz a legtöbb hozhat létre és kezelhet meghatározott típusú részletes utasításokat tartalmaz.  

A következő videó bemutatja a különböző képességekkel és az Azure Marketplace-en vagy az appsource-ban elérhető ajánlatáról típus.  Emellett fontos műszaki és üzleti alkalmazások vagy szolgáltatások közzététele a piactér aspektusait ismerteti.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Alkalmazások létrehozása és a szolgáltatások az Azure Marketplace és AppSource - Build 2018.**

Ezek a piactér kapcsolatos további információkért lásd: [közzétételi útmutató az Azure Marketplace és AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Az ajánlat olyan gyakori műveleteket

A folyamaton, létrehozhat egy új ajánlat szolgáltatásfrissítési jelentősen különböző típusú, például egy [Azure alkalmazásra vonatkozó ajánlat](./azure-applications/cpp-azure-app-offer.md) és a egy [tanácsadási szolgáltatási ajánlat](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Ezzel szemben a többi művelet számos hajt végre egy ajánlatot az a [Cloud Partner Portalon](https://cloudpartner.azure.com) viszonylag szabványosítottak ajánlattípusokról között.  Ezek olyan gyakori műveleteket – beleértve a közzététel, állapot megtekintése, frissítési és törlési – a szakaszban ismertetett [ajánlatok kezelése](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Tesztverzió

*Próbálja ki a* lehetővé az ügyfelek számára egy bemutató "próbálja ki a vásárlás előtt" beállítást engedélyezve ajánlatok piactéren funkció.  A Test Drive funkció korlátozódik ajánlattípusokról következő részét: [Az Azure-alkalmazások](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [ SaaS-alkalmazások](./saas-app/cpp-saas-offer.md), és [virtuális gépek](./virtual-machine/cpp-virtual-machine-offer.md).  Ez a funkció a közzétevő számára hozzon létre egy Test Drive-sablont, az ajánlat testre szabott van szükség.  További információkért lásd: a szakasz [Test Drive](../cloud-partner-portal-orig/what-is-test-drive.md).

Megnyithatja a meglévő marketplace-ajánlat, amelyek rendelkeznek a Test Drive bemutatók alkalmazásával a [test drive szűrő](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Az Azure Marketplace és AppSource ajánlat típusok

A következő táblázat felsorolja az aktuális az ajánlat által támogatott típusok a [Cloud Partner Portalon](https://cloudpartner.azure.com).  Az ajánlat alkalmazástípust lista felsorolja az ajánlat listázó marketplace(s), valamint az ajánlat megoldás technológia általános leírása.

|                Csomag típusa                |  Piactér  |   Leírás                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-alkalmazás](./azure-applications/cpp-azure-app-offer.md) | Azure | Megoldás áll egy vagy több telepített virtuális gépek (VM), nem kötelező egyéni Azure code egy Azure Resource Manager-sablon segítségével.  Üzembe helyezés lehet megoldássablon az ügyfél vagy a közzétevő felügyeli. Ez a típus segítségével, mint a megadott virtuális gép ajánlattípusra nagyobb rugalmasságot biztosítanak.  |
| [Tanácsadási szolgáltatás](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Mindkét | A Microsoft minősített tanácsadók listázhatja a tartomány-specifikus szolgáltatások az Azure Marketplace-en vagy az appsource-ban.  Összpontosíthassák segítséget nyújt az ügyfeleknek értékelésekor problémák és a létrehozott és a megfelelő megoldások az üzleti célok eléréséhez.  |
| [Tároló](./containers/cpp-containers-offer.md)  | Azure | Megoldás, vagy egy Kubernetes-alapú szolgáltatás, vagy az Azure Container Instances szolgáltatásban üzembe helyezett Docker-tároló rendszerképét. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Csomag, amely kiterjeszti a vállalat erőforrás tervezési (ERP) és üzleti felügyeleti rendszer. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Egy csomagot, amely kibővíti az ügyfelek erőforrás-kezelő (CRM) rendszer a sales, a szolgáltatás, a project service és a mező szolgáltatás modulok.  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Egy csomagot, amely kiterjeszti a vállalatierőforrás-tervezési (ERP) szolgáltatást, hogy támogatja a speciális pénzügyi, műveletek, gyártási és ellátásilánc-kezelés. |
| [IoT Edge-modul](./iot-edge-module/cpp-offer-process-parts.md) | Azure | A Docker-kompatibilis tároló, amely IoT Edge-eszköz futtat.  Az egyéni kódot, más Azure-szolgáltatások és a 3. fél szolgáltatások kombinációját használó, kis számítási modulokat tartalmaz. |
| [Power BI App](./power-bi/cpp-power-bi-offer.md) | AppSource | Jelentések és irányítópultok csatlakozhat adatokhoz a közös adattárolás adatfolyamok használó csomag. |
| [SaaS-alkalmazás](./saas-app/cpp-saas-offer.md) | Azure | Megoldás, a szoftver--szolgáltatásként előfizetés, a közzétevő, hogy mely felhasználók jelentkezzen be, amely az Azure Active Directoryt használja testre szabott felületen keresztül kezeli. |
| [Virtuális gép](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Megoldás az ügyfél-előfizetés keretében üzembe egy virtuális gépen belül található.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

További információkért lásd: [közzétételi útmutató ajánlat típus szerint](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan hajthat végre a marketplace-ajánlat és a gyakori technikai attribútumok és eszközök a témakör általános műveleteiről [ajánlatok kezelése](./manage-offers/cpp-manage-offers.md).
