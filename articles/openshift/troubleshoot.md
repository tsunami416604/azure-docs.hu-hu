---
title: Az Azure Red Hat OpenShift hibáinak megoldása
description: Az Azure Red Hat OpenShift kapcsolatos gyakori problémák elhárítása és megoldása
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76274927"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Hibaelhárítás az Azure Red Hat OpenShift

Ez a cikk a Microsoft Azure Red Hat OpenShift-fürtök létrehozásakor vagy kezelésekor felmerülő gyakori problémákat ismerteti.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Sikertelen fürt létrehozásának újrapróbálása

Ha nem sikerül létrehozni egy Azure Red Hat OpenShift- `az` fürtöt a CLI-parancs használatával, a létrehozás újbóli próbálkozása továbbra is sikertelen lesz.
A `az openshift delete` használatával törölje a meghibásodott fürtöt, majd hozzon létre egy teljesen új fürtöt.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Rejtett Azure Red Hat OpenShift fürterőforrás-csoport

Jelenleg az Azure `Microsoft.ContainerService/openShiftManagedClusters` CLI (`az openshift create` parancs) által automatikusan létrehozott erőforrás el van rejtve a Azure Portal. **Az erőforráscsoport nézetben a** **rejtett típusok megjelenítése** elemre kattintva tekintheti meg az erőforráscsoportot.

![Képernyőkép a rejtett típus jelölőnégyzetről a portálon](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>A fürt létrehozása olyan hibát eredményez, amely nem talált regisztrált erőforrás-szolgáltatót

Ha a fürt létrehozásakor hiba lépett fel `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`, akkor az előzetes verzió részét képezi, és most meg kell [vásárolnia az Azure-beli virtuális gépek számára fenntartott példányokat](https://aka.ms/openshift/buy) az általánosan elérhető termék használatához. A foglalások a teljes körűen felügyelt Azure-szolgáltatások előre fizetésével csökkentik a költségeit. A foglalások és a pénzek megtakarítása című témakörben olvashat bővebben [*Azure Reservations*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) .

## <a name="next-steps"></a>További lépések

- Próbálja ki a [Red Hat OpenShift súgóját](https://help.openshift.com/) a OpenShift hibaelhárításával kapcsolatos további információkért.

- Válaszokat talál az [Azure Red Hat OpenShift kapcsolatos gyakori kérdésekre](openshift-faq.md).
