---
title: Erőforrások kezelése az Azure Red Hat OpenShift | Microsoft Docs
description: Projektek, sablonok, képstreamek kezelése Azure Red Hat OpenShift-fürtben
services: openshift
keywords: a Red Hat openshift projects önkiszolgálót kér
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 0abc086553f5e903a71bcfd0b6322bcee56d2d8b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216929"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Projektek, sablonok, képstreamek kezelése Azure Red Hat OpenShift-fürtben

> [!IMPORTANT]
> Az Azure Red Hat OpenShift 3,11 2022. június 30-án megszűnik. Az új Azure Red Hat OpenShift 3,11-fürtök létrehozásának támogatása az 2020. november 30-ig folytatódik. A biztonsági rések elkerülése érdekében a rendszer leállítja a fennmaradó Azure Red Hat OpenShift 3,11-fürtöket a kivonulást követően.
> 
> Kövesse ezt az útmutatót [egy Azure Red Hat OpenShift 4-fürt létrehozásához](tutorial-create-cluster.md).
> Ha konkrét kérdései vannak, vegye [fel velünk a kapcsolatot](mailto:arofeedback@microsoft.com).

A OpenShift-tároló platformon a kapcsolódó objektumok csoportosítására és elkülönítésére szolgálnak a projektek. Rendszergazdaként hozzáférést biztosíthat a fejlesztőknek bizonyos projektekhez, így saját projektjeiket hozhatnak létre, és rendszergazdai jogosultságokat biztosíthatnak az egyes projektekhez.

## <a name="self-provisioning-projects"></a>Önálló kiépítési projektek

Lehetővé teheti a fejlesztők számára, hogy saját projektjeiket hozzanak létre. Egy API-végpont feladata a projekt kiépítés a Project-Request nevű sablon alapján. A webkonzol és a `oc new-project` parancs ezt a végpontot használja, ha egy fejlesztő új projektet hoz létre.

A Project-kérelem elküldésekor az API a következő paramétereket helyettesíti a sablonban:

| Paraméter               | Leírás                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | A projekt neve. Kötelező.             |
| PROJECT_DISPLAYNAME     | A projekt megjelenítendő neve. Lehet üres. |
| PROJECT_DESCRIPTION     | A projekt leírása. Lehet üres.  |
| PROJECT_ADMIN_USER      | Az adminisztráló felhasználó felhasználóneve.       |
| PROJECT_REQUESTING_USER | A kérelmező felhasználó felhasználóneve.           |

Az API-hoz való hozzáférés a fejlesztők számára az önkiszolgáló fürt szerepkörének kötésével adható meg. Ez a funkció alapértelmezés szerint minden hitelesített fejlesztő számára elérhető.

## <a name="modify-the-template-for-a-new-project"></a>Új projekt sablonjának módosítása 

1. Jelentkezzen be jogosultságokkal rendelkező felhasználóként `customer-admin` .

2. Szerkessze az alapértelmezett Project-Request sablont.

   ```
   oc edit template project-request -n openshift
   ```

3. A következő jegyzet hozzáadásával távolítsa el az alapértelmezett Project sablont az Azure Red Hat OpenShift (ARO) frissítési folyamatból: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   A Project-Request sablont az ARO frissítési folyamata nem frissíti. Ez lehetővé teszi, hogy az ügyfelek testre szabják a sablont, és megőrizzék ezeket a testreszabásokat a fürt frissítésekor.

## <a name="disable-the-self-provisioning-role"></a>Az önkiszolgáló szerepkör letiltása

Megakadályozhatja, hogy egy hitelesített felhasználói csoport új projektek önkiszolgáló létesítése után is megtörténjen.

1. Jelentkezzen be jogosultságokkal rendelkező felhasználóként `customer-admin` .

2. Szerkessze az önkiszolgálók fürt szerepkörének kötését.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. A következő jegyzet hozzáadásával távolítsa el a szerepkört az ARO-frissítési folyamatból: `openshift.io/reconcile-protect: "true"` .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Módosítsa a fürt szerepkörének kötését, hogy ne `system:authenticated:oauth` hozzon létre projekteket:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Az alapértelmezett sablonok és imageStreams kezelése

Az Azure Red Hat OpenShift letilthatja az alapértelmezett sablonok és a névtéren belüli képstreamek frissítéseit `openshift` .
Az összes `Templates` és a névtér frissítéseinek letiltása `ImageStreams` `openshift` :

1. Jelentkezzen be jogosultságokkal rendelkező felhasználóként `customer-admin` .

2. `openshift`Névtér szerkesztése:

   ```
   oc edit namespace openshift
   ```

3. `openshift`A következő jegyzet hozzáadásával távolítsa el a névteret az ARO frissítési folyamatból:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   A névtérben található minden egyes objektum `openshift` eltávolítható a frissítési folyamatból a jegyzet hozzáadásával `openshift.io/reconcile-protect: "true"` .

## <a name="next-steps"></a>Következő lépések

Próbálja ki az oktatóanyagot:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
