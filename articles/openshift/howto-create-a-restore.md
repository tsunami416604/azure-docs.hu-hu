---
title: Azure Red Hat OpenShift 4-fürtbeli alkalmazás-visszaállítás létrehozása a Velero használatával
description: Ismerje meg, hogyan hozhat létre visszaállítást Azure Red Hat-OpenShift a Velero használatával
ms.service: container-service
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: ARO, openshift, az ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 0cd6797bcdfadca807e25f8b3decf34bd553fc56
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89470051"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Azure Red Hat OpenShift 4-fürt alkalmazás-visszaállításának létrehozása

Ebben a cikkben előkészíti a környezetet egy Azure Red Hat OpenShift 4 fürtözött alkalmazás-visszaállítás létrehozásához. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges eszközök telepítése
> * Azure Red Hat OpenShift 4 alkalmazás-visszaállítás létrehozása

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Előkészületek

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Azure Red Hat OpenShift 4 alkalmazás biztonsági másolatának létrehozása

Az Azure Red Hat OpenShift 4 alkalmazás biztonsági mentésének létrehozásával kapcsolatban lásd: [Azure Red Hat OpenShift 4 biztonsági másolat létrehozása](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Azure Red Hat OpenShift 4 alkalmazás visszaállítása

Ezek a lépések lehetővé teszik egy olyan alkalmazás visszaállítását, amely korábban biztonsági mentést készített a Velero használatával.
A fürt által jelenleg felismert biztonsági másolatok listáját megtekintve megtekintheti, hogy mely biztonsági másolatok érhetők el a visszaállításhoz.  Ennek a lépésnek a végrehajtásához a következő parancsot kell futtatnia:

_(Ez a lépés azt feltételezi, hogy telepítette a Velero-t egy "Velero" nevű projektben)_

```bash
oc get backups -n velero
```

Ha rendelkezik a visszaállítani kívánt biztonsági másolattal, a visszaállítást a következő paranccsal kell végrehajtania:

```bash
velero restore create --from-backup <name of backup from above output list>
```

Ez a lépés hozza létre azokat az Kubernetes objektumokat, amelyekről biztonsági másolat létrehozásakor az előző lépésben készített biztonsági mentést.

A visszaállítás állapotának megtekintéséhez hajtsa végre a következő lépést:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Ha a fázis azt mondja `Completed` , az Azure Red Hat 4 alkalmazást vissza kell állítani.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Red Hat OpenShift 4 fürtözött alkalmazást állítottak vissza. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * OpenShift v4-fürt alkalmazás-visszaállításának létrehozása a Velero használatával


A következő cikkből megismerheti az Azure Red Hat OpenShift 4 támogatott erőforrásait.

* [Azure Red Hat OpenShift v4 – támogatott források](supported-resources.md)