---
title: Azure-és AppSource Marketplace-ajánlatok
description: Azure-és AppSource-Piactéri ajánlatok létrehozása és kezelése
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: 746b1b51d593b21bdf85bca4eeb75c135196093a
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962856"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure-és AppSource Marketplace-ajánlatok

Ez a szakasz első része az Azure-és AppSource-Piactéri ajánlatok létrehozásához és kezeléséhez használt általános műveleteket ismerteti.  Ez a rész az egyes ajánlati típusok kezeléséhez szükséges hátteret, valamint az összes ajánlat típusával közös technikai információkat tartalmazza.  Ennek a szakasznak a többsége részletes útmutatást tartalmaz az egyes ajánlatok létrehozásának és kezelésének módjáról.  

A következő videó bemutatja az Azure Marketplace-en vagy AppSource-ban elérhető különböző funkciókat és különböző ajánlati típusokat.  Az alkalmazás vagy szolgáltatás ezen piacokon való közzétételének fontos technikai és üzleti szempontjait is ismerteti.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Alkalmazások és szolgáltatások létrehozása az Azure Marketplace-hez és a AppSource – Build 2018**

További információ ezekről a piactérekről: az [Azure Marketplace és az AppSource közzétételi útmutatója](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Gyakori ajánlati műveletek

Az új ajánlat létrehozásának folyamata nagy mértékben különbözik az ajánlati típusoktól, például egy [Azure-alkalmazás ajánlata](./azure-applications/cpp-azure-app-offer.md) és egy [tanácsadói szolgáltatás ajánlata](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md)között.  Ezzel szemben a [Cloud Partner Portal](https://cloudpartner.azure.com) egy ajánlatán végrehajtott egyéb műveletek többsége meglehetősen szabványosított az ajánlati típusok között.  Ezek a gyakori műveletek – beleértve a közzétételt, a megtekintési állapotot, a frissítést és a törlést – az [ajánlatok kezelése](./manage-offers/cpp-manage-offers.md) részben szerepelnek.


## <a name="test-drive"></a>Kipróbálás

A *Test Drive* egy Piactéri szolgáltatás, amely a "kipróbálás előtt" lehetőséget nyújt az ügyfeleknek az egyes ajánlatokhoz.  A test Drive funkció a következő típusú ajánlatokra korlátozódik: [Azure-alkalmazások](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 a Customer engagement](./dyn365ce/cpp-customer-engagement-offer.md), a [Dynamics 365 for Finance és Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS-alkalmazások](./saas-app/cpp-saas-offer.md)és [virtuális gépek](./virtual-machine/cpp-virtual-machine-offer.md).  Ehhez a funkcióhoz a közzétevőnek létre kell hoznia egy, az ajánlatához testreszabott tesztelési meghajtó-sablont.  További információt a [Test Drive](./test-drive/what-is-test-drive.md)című szakaszban talál.

A test Drive- [szűrők](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)alkalmazásával tallózhat a meglévő Piactéri ajánlatokban. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace-és AppSource-ajánlatok típusai

A következő táblázat felsorolja a [Cloud Partner Portal](https://cloudpartner.azure.com)által támogatott aktuális ajánlatokat.  Minden ajánlat típusa esetén felsorolja azokat a piactér (eke) t, amelyeken az ajánlat listázható, valamint az ajánlat megoldási technológiájának általános leírását.

|                Csomag típusa                |  Piactér  |   Leírás                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-alkalmazás](./azure-applications/cpp-azure-app-offer.md) | Azure | A megoldás egy vagy több virtuális gépről (VM), opcionális egyéni Azure-kódra épül, amely Azure Resource Manger-sablonon keresztül helyezhető üzembe.  Az üzembe helyezés lehet az ügyfél vagy egy megoldás sablonja, vagy a közzétevő által felügyelt. Ez a típus nagyobb rugalmasságot biztosít, mint a virtuális gép által kínált ajánlat típusa.  |
| [Tanácsadási szolgáltatás](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | mind | A Microsoft által minősített tanácsadók az Azure Marketplace-en vagy a AppSource-on is listázhatja a tartományra jellemző szolgáltatásokat.  Szakértelmük segítséget nyújt az ügyfeleknek a problémáik felméréséhez, valamint a megfelelő megoldások létrehozásához és üzembe helyezéséhez az üzleti célok teljesítése érdekében.  |
| [Tároló](./containers/cpp-containers-offer.md)  | Azure | A megoldás egy Kubernetes-alapú szolgáltatásként vagy Azure Container instances-ben kiépített Docker-tároló képe. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Egy csomag, amely kibővíti ezt a vállalati erőforrás-tervezési (ERP) és az üzleti felügyeleti rendszerét. |
| [Dynamics 365 ügyfél-engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Egy csomag, amely kiterjeszti ezt az ügyfél-erőforrás-kezelési (CRM) rendszert az értékesítés, a szolgáltatás, a Project Service és a Field Service moduljain keresztül.  |
| [Dynamics 365 pénzügyi és üzemeltetési műveletekhez](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Olyan csomag, amely kiterjeszti ezt a vállalati erőforrás-tervezési (ERP) szolgáltatást, amely támogatja a fejlett pénzügyi, üzemeltetési, gyártási és ellátási láncok kezelését. |
| [IoT Edge modul](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Egy IoT Edge eszközön futó Docker-kompatibilis tároló.  Olyan kis számítási modulokból áll, amelyek egyéni kód, más Azure-szolgáltatások és harmadik féltől származó szolgáltatások kombinációját használják. |
| [Power BI alkalmazás](./power-bi/cpp-power-bi-offer.md) | AppSource | Olyan Power BI alkalmazás, amely testreszabható Power BI tartalmakat, például adatkészleteket, jelentéseket és irányítópultokat csomagol |
| [SaaS-alkalmazás](./saas-app/cpp-saas-offer.md) | Azure | A megoldás egy, a közzétevő által kezelt szoftver-szolgáltatás előfizetése, amelyet a felhasználók a Azure Active Directoryt használó testreszabott felületen keresztül jelentkeznek be. |
| [Virtuális gép](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | A megoldás az ügyfél előfizetésében üzembe helyezett egyetlen virtuális gépen belül található.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

További információ: [közzétételi útmutató ajánlat típusa szerint](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>További lépések

A piactéren elérhető általános műveletekről, valamint azok közös műszaki attribútumairól és eszközeiről az [ajánlatok kezelése](./manage-offers/cpp-manage-offers.md)című cikkben tájékozódhat.
