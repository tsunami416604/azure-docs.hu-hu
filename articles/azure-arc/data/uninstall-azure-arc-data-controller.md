---
title: Azure arc-adatkezelő törlése
description: Azure arc-adatkezelő törlése
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a040200c5746defcaee84a951521d5919c0c4d28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91660676"
---
# <a name="delete-azure-arc-data-controller"></a>Azure arc-adatkezelő törlése

A következő cikk az Azure arc-adatkezelők törlését ismerteti.

A folytatás előtt győződjön meg arról, hogy az adatvezérlőn létrehozott összes adatszolgáltatás el lett távolítva a következő módon:

## <a name="log-in-to-the-data-controller"></a>Bejelentkezés az adatvezérlőbe

Jelentkezzen be a törölni kívánt adatkezelőbe:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Meglévő adatszolgáltatások törlése &

A következő parancs futtatásával ellenőrizze, hogy van-e létrehozva SQL felügyelt példány:

```
azdata arc sql mi list
```

A fenti listából minden egyes felügyelt SQL-példány esetében futtassa a DELETE parancsot a következő módon:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Hasonlóképpen, a PostgreSQL nagy kapacitású-példányok kereséséhez futtassa a következőt:

```
azdata arc postgres server list
```

És minden PostgreSQL nagy kapacitású-példány esetében futtassa a DELETE parancsot a következő módon:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Vezérlő törlése

Miután az SQL felügyelt példányai és a PostgreSQL nagy kapacitású példányai el lettek távolítva, az adatvezérlőt a következőképpen törölheti:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>SCCs eltávolítása (csak Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Fürt szintjén lévő objektumok törlése

A névtér hatókörű objektumain kívül, ha a fürt szintjén lévő objektumokat is törölni szeretné, például a CRDs, `clusterroles` és `clusterrolebindings` futtatnia kell a következő parancsokat:

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Szükség esetén törölheti az Azure arc-adatvezérlő névterét


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Következő lépések

[Mik azok az Azure Arc-kompatibilis adatszolgáltatások?](overview.md)
