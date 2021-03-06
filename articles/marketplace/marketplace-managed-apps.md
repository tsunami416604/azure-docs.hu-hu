---
title: Azure-alkalmazások által felügyelt alkalmazás-ajánlat – közzétételi útmutató – Azure Marketplace
description: Ez a cikk ismerteti a felügyelt alkalmazások Azure Marketplace-en való közzétételének követelményeit.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: d4fb3354b7035149b80191528b2f5335b593b764
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433554"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Közzétételi útmutató az Azure által felügyelt alkalmazásokhoz

Az Azure-beli *felügyelt alkalmazások* egyik módja egy Azure-alkalmazás közzététele az Azure Marketplace-en. A felügyelt alkalmazások olyan Transact-ajánlatok, amelyek üzembe helyezése és számlázása az Azure Marketplace-en keresztül történik. A felhasználó által megjelenő listázási lehetőség a *Letöltés*.

Ez a cikk a felügyelt alkalmazási ajánlat típusára vonatkozó követelményeket ismerteti.

Használja a felügyelt alkalmazás ajánlat típusát a következő feltételek teljesülése esetén:

- A virtuális gép (VM) vagy egy teljes infrastruktúra-szolgáltatás (IaaS) alapú megoldás használatával üzembe helyezi az előfizetésen alapuló megoldást az ügyfél számára.
- Ön vagy az ügyfele megköveteli, hogy a megoldást egy partner felügyelje.

>[!NOTE]
>Egy partner lehet például rendszerintegrátor vagy felügyelt szolgáltató (MSP).  

## <a name="managed-application-offer-requirements"></a>A felügyelt alkalmazások ajánlatára vonatkozó követelmények

|Követelmények |Részletek  |
|---------|---------|
|Azure-előfizetés | A felügyelt alkalmazásokat az ügyfél előfizetéséhez kell telepíteni, de egy harmadik fél is felügyelheti. |
|Számlázás és mérés    |  Az erőforrások az ügyfél Azure-előfizetésében érhetők el. Az utólagos elszámolású fizetési modellt használó virtuális gépeket a Microsofton keresztül, az ügyfél Azure-előfizetésén keresztül számlázzák. <br><br> A saját licencű virtuális gépekhez a Microsoft az ügyfél-előfizetésben felmerülő összes infrastrukturális költséget számláz, de a szoftveres licencelési díjakat közvetlenül az ügyféllel együtt kell lebonyolítani.        |
|Azure-kompatibilis virtuális merevlemez (VHD)    |   A virtuális gépeket Windows vagy Linux rendszerre kell építeni.<br><br>A linuxos virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Az Azure-ban támogatott Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md).<br><br>A Windows virtuális merevlemezek létrehozásával kapcsolatos további információkért lásd: [Azure-alkalmazási ajánlat létrehozása](./create-new-azure-apps-offer.md). |

---

> [!NOTE]
> A felügyelt alkalmazásokat az Azure Marketplace-en keresztül kell üzembe helyezni. Ha az ügyfél kommunikációja aggodalomra ad okot, az érdeklődők megosztásának engedélyezése után érdemes megszólítani az érdeklődőket.  

> [!Note]
> Már elérhető egy felhőalapú megoldás-szolgáltató (CSP) partneri csatorna. Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](./cloud-solution-providers.md)című témakört.

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, Ismerje meg, hogyan [növelheti Felhőbeli üzletét az Azure Marketplace-szel](https://azuremarketplace.microsoft.com/sell).

A partner Centerben való regisztráció és a használat megkezdése:

- Az ajánlat létrehozásához vagy befejezéséhez [Jelentkezzen be a partner Centerben](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
- További információért lásd [Az Azure-alkalmazások létrehozásáról szóló ajánlatot](./create-new-azure-apps-offer.md) .
