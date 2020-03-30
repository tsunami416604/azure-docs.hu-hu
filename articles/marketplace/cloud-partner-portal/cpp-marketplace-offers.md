---
title: Az Azure és az AppSource Piactér ajánlatai
description: Az Azure és az AppSource piacterek ajánlatainak létrehozása és kezelése
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278484"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Az Azure és az AppSource Piactér ajánlatai

Ez a szakasz első része bemutatja az Azure és az AppSource piacterek ajánlatainak létrehozásához és kezeléséhez használt általános műveleteket.  Ez a rész biztosítja a hátteret, amelyet meg kell értenie az adott ajánlattípusok kezeléséhez, valamint olyan technikai információkat, amelyek minden ajánlattípusra vonatkoznak.  A szakasz többsége részletes útmutatást tartalmaz az adott ajánlattípusok létrehozásához és kezeléséhez.  

Az alábbi videó bemutatja a különböző képességek et és a különböző ajánlattípusok at érhető el az Azure Marketplace-en vagy AppSource.The following video introduces the different capabilities and different offer types available in Azure Marketplace or AppSource.  Emellett fontos technikai és üzleti szempontokat is lefed egy alkalmazás vagy szolgáltatás közzétételével ezeken a piacokon.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Alkalmazások és szolgáltatások készítése az Azure Piactérhez és az AppSource-hoz – Build 2018**

Ezekről a piacterekről az [Azure Marketplace és az AppSource közzétételi útmutatójában](../marketplace-publishers-guide.md)talál további információt.


## <a name="common-offer-operations"></a>Közös ajánlati műveletek

Az új ajánlat létrehozásának folyamata jelentősen eltér az ajánlattípusok között, például egy [Azure-alkalmazásajánlat](./azure-applications/cpp-azure-app-offer.md) és egy [tanácsadási szolgáltatásajánlat](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md)között.  Ezzel szemben a [Felhőpartner-portálon](https://cloudpartner.azure.com) egy ajánlaton végrehajtott egyéb műveletek közül sok meglehetősen szabványosított az ajánlattípusok között.  Ezeket a gyakori műveleteket – beleértve a közzétételt, az állapot megtekintését, a frissítést és a törlést – az [Ajánlatok kezelése](./manage-offers/cpp-manage-offers.md) című szakasz ismerteti.


## <a name="test-drive"></a>Tesztverzió

*A Test Drive* egy piactéri funkció, amely minden ajánlathoz engedélyezett "vásárlás előtt próbálja meg" bemutató opciót biztosít az ügyfeleknek.  A Test Drive funkció az ajánlattípusok következő részhalmazára korlátozódik: [Azure-alkalmazások](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), Dynamics [365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), Dynamics [365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [SaaS-alkalmazások](./saas-app/cpp-saas-offer.md)és [virtuális gépek](./virtual-machine/cpp-virtual-machine-offer.md).  Ehhez a funkcióhoz a közzétevőnek létre kell hoznia egy test drive-sablont, amely az ajánlatához van testreszabva.  További információt a Test Drive című részben [talál.](./test-drive/what-is-test-drive.md)

A [tesztmeghajtó-szűrő](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)alkalmazásával böngészhet a testdrive-bemutatókkal rendelkező meglévő piactéri ajánlatok között. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Az Azure Piactér és az AppSource ajánlattípusai

Az alábbi táblázat a Cloud Partner Portal által támogatott aktuális ajánlattípusokat [sorolja fel.](https://cloudpartner.azure.com)  Minden egyes ajánlattípushoz felsorolja azokat a piacteret(oka)t, ahol az ajánlat felsorolható, valamint az ajánlatmegoldás-technológia általános leírását.

|                Ajánlat típusa                |  Piactér  |   Leírás                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure-alkalmazás](./azure-applications/cpp-azure-app-offer.md) | Azure | A megoldás egy vagy több virtuális gépből (VM- ből), opcionális egyéni Azure-kódból áll, amelyeket egy Azure Resource Manger-sablonon keresztül telepítenek.  A központi telepítés lehet az ügyfél által egy megoldássablonon keresztül, vagy a közzétevő által kezelt. Ez a típus nagyobb rugalmasságot biztosít, mint a megadott virtuálisgép-ajánlat típus.  |
| [Tanácsadási szolgáltatás](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Mind | A Microsoft által minősített tanácsadók felsorolhatják tartományspecifikus szolgáltatásaikat az Azure Marketplace-en vagy az AppSource-on.  Szakértelmük segíti az ügyfeleket problémáik felmérésében, valamint az üzleti céljaiknak megfelelő megoldások létrehozásában és üzembe helyezésében.  |
| [Konténer](./containers/cpp-containers-offer.md)  | Azure | A megoldás egy Kubernetes-alapú szolgáltatásként vagy Azure Container-példányként kiépített Docker-tárolórendszerkép. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | A vállalati erőforrás-tervezési (ERP) és az üzletviteli rendszert kiterjesztő csomag. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Az ügyfélerőforrás-kezelési (CRM) rendszert értékesítési, szolgáltatási, projektszolgáltatási és terepi szolgáltatási moduljain keresztül bővítő csomag  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | A vállalati erőforrás-tervezési (ERP) szolgáltatást kiterjesztő csomag, amely támogatja a fejlett pénzügyeket, műveleteket, gyártást és ellátásilánc-kezelést |
| [IoT Edge modul](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Egy Docker-kompatibilis tároló, amely egy IoT Edge-eszközön fut.  Kis számítási modulokból áll, amelyek egyéni kód, más Azure-szolgáltatások és külső szolgáltatások kombinációját használják. |
| [Power BI alkalmazás](./power-bi/cpp-power-bi-offer.md) | AppSource | Testreszabható Power BI-tartalmakat tartalmazó Power BI-alkalmazás, beleértve az adatkészleteket, jelentéseket és irányítópultokat |
| [SaaS alkalmazás](./saas-app/cpp-saas-offer.md) | Azure | A megoldás egy szolgáltatásként működő szoftver-előfizetés, amelyet a közzétevő kezel, és amelyet a felhasználók az Azure Active Directoryt használó testreszabott felületen keresztül jelentkeznek be. |
| [Virtuális gép](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | A megoldás egyetlen virtuális gépen belül található, amely et az ügyfél előfizetésére telepített.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

További információt a [Közzétételi útmutató ajánlattípus szerint című témakörben](../publisher-guide-by-offer-type.md)talál.


## <a name="next-steps"></a>További lépések

Az ajánlatok kezelése című cikkben megismerheti a piactéri ajánlatokon végrehajtható általános műveleteket, valamint azok közös technikai jellemzőit és [eszközeit.](./manage-offers/cpp-manage-offers.md)
