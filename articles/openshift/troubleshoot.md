---
title: Az Azure Red Hat OpenShift hibáinak megoldása
description: Az Azure Red Hat OpenShift kapcsolatos gyakori problémák elhárítása és megoldása
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 55360ef295ff80b700b059d053203458f9f384db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469082"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Hibaelhárítás az Azure Red Hat OpenShift

Ez a cikk a Microsoft Azure Red Hat OpenShift-fürtök létrehozásakor vagy kezelésekor felmerülő gyakori problémákat ismerteti.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Sikertelen fürt létrehozásának újrapróbálása

Ha nem sikerül létrehozni egy Azure Red Hat OpenShift-fürtöt a `az` CLI-parancs használatával, a létrehozás újbóli próbálkozása továbbra is sikertelen lesz.
`az openshift delete`A használatával törölje a meghibásodott fürtöt, majd hozzon létre egy teljesen új fürtöt.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Rejtett Azure Red Hat OpenShift fürterőforrás-csoport

Jelenleg az `Microsoft.ContainerService/openShiftManagedClusters` Azure CLI (parancs) által automatikusan létrehozott erőforrás el `az openshift create` van rejtve a Azure Portal. **Az erőforráscsoport nézetben a** **rejtett típusok megjelenítése** elemre kattintva tekintheti meg az erőforráscsoportot.

![Képernyőkép a rejtett típus jelölőnégyzetről a portálon](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>A fürt létrehozása olyan hibát eredményez, amely nem talált regisztrált erőforrás-szolgáltatót

Ha a fürt létrehozásakor hiba lépett fel `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` , akkor az előzetes verzió részét képezi, és most meg kell [vásárolnia az Azure-beli virtuális gépek számára fenntartott példányokat](https://aka.ms/openshift/buy) az általánosan elérhető termék használatához. A foglalások a teljes körűen felügyelt Azure-szolgáltatások előre fizetésével csökkentik a költségeit. A foglalások és a pénzek megtakarítása című témakörben olvashat bővebben [*Azure Reservations*](../cost-management-billing/reservations/save-compute-costs-reservations.md) .

## <a name="next-steps"></a>Következő lépések

- Próbálja ki a [Red Hat OpenShift súgóját](https://help.openshift.com/) a OpenShift hibaelhárításával kapcsolatos további információkért.

- Válaszokat talál az [Azure Red Hat OpenShift kapcsolatos gyakori kérdésekre](openshift-faq.md).