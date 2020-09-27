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
ms.openlocfilehash: dce70441e5e8487bfc015df0a946ab3cd74c14f0
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397589"
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

A névtér hatókörű objektumain kívül, ha a fürt szintjén lévő objektumokat is törölni szeretné, például a CRDs, a `clusterroles` és a `clusterrolebindings` parancsot, futtassa a következő parancsokat:

```
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete postgresql-11s.arcdata.microsoft.com 
kubectl delete postgresql-12s.arcdata.microsoft.com
kubectl delete clusterroles azure-arc-data:cr-arc-metricsdc-reader
kubectl delete clusterrolebindings azure-arc-data:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Szükség esetén törölheti az Azure arc-adatvezérlő névterét


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>További lépések

[Mik azok az Azure arc-kompatibilis adatszolgáltatások?](overview.md)
