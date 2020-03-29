---
title: Az Azure Red Hat OpenShift – problémamegoldás
description: Az Azure Red Hat OpenShift gyakori problémáinak elhárítása és megoldása
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274927"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Az Azure Red Hat OpenShift hibaelhárítása

Ez a cikk a Microsoft Azure Red Hat OpenShift fürtök létrehozása vagy kezelése során felmerülő gyakori problémákat ismerteti.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Sikertelen fürt létrehozásának újbóli megkísérlése

Ha egy Azure Red Hat OpenShift fürt létrehozása a `az` CLI paranccsal sikertelen, a létrehozás újbóli megkísérlése továbbra is sikertelen lesz.
A `az openshift delete` sikertelen fürt törléséhez, majd egy teljesen új fürt létrehozásához használható.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Rejtett Azure Red Hat OpenShift fürterőforrás-csoport

Jelenleg az `Microsoft.ContainerService/openShiftManagedClusters` Azure CLI (`az openshift create` parancs) által automatikusan létrehozott erőforrás el van rejtve az Azure Portalon. Az **Erőforráscsoport** nézetben jelölje be a **Rejtett típusok megjelenítése** jelölőnégyzetet az erőforráscsoport megtekintéséhez.

![Képernyőkép a portál rejtett típusjelölőnégyzetéről](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Fürt létrehozása olyan hibát eredményez, amelyet nem talált regisztrált erőforrás-szolgáltató

Ha egy fürt létrehozása hibát `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`eredményez, amely , akkor az előzetes verzió része volt, és most meg kell vásárolnia az [Azure virtuális gép számára fenntartott példányokat](https://aka.ms/openshift/buy) az általánosan elérhető termék használatához. A foglalás csökkenti a kiadásokat a teljes körűen felügyelt Azure-szolgáltatások előre fizetett. A Foglalásokról és arról, hogy hogyan takarítanak meg pénzt, olvassa el a [*Mik azok az Azure-foglalások*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) című részét.

## <a name="next-steps"></a>További lépések

- Az OpenShift hibaelhárításról a [Red Hat OpenShift súgójában](https://help.openshift.com/) talál többet.

- Válaszok az [Azure Red Hat OpenShift szolgáltatással kapcsolatos gyakori kérdésekre.](openshift-faq.md)
