---
title: Erőforrások kezelése az Azure Red Hat OpenShift ben | Microsoft dokumentumok
description: Projektek, sablonok és képfolyamok kezelése Azure Red Hat OpenShift-fürtben
services: openshift
keywords: red hat openshift projektek kéri önellátó
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139113"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Projektek, sablonok és képfolyamok kezelése Azure Red Hat OpenShift-fürtben 

Az OpenShift tárolóplatformban a projektek a kapcsolódó objektumok csoportosítására és elkülönítésére szolgálnak. Rendszergazdaként hozzáférést biztosíthat a fejlesztőknek bizonyos projektekhez, lehetővé teheti számukra, hogy saját projekteket hozzanak létre, és rendszergazdai jogokat biztosíthatnek számukra az egyes projektekhez.

## <a name="self-provisioning-projects"></a>Önellátó projektek

Engedélyezheti a fejlesztőknek, hogy saját projekteket hozzanak létre. Egy API-végpont felelős a projekt kiépítése a projekt-kérelem nevű sablon szerint. A webkonzol `oc new-project` és a parancs ezt a végpontot használja, amikor egy fejlesztő új projektet hoz létre.

Projektkérelem benyújtásakor az API a következő paramétereket helyettesíti a sablonban:

| Paraméter               | Leírás                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | A projekt neve. Kötelező.             |
| PROJECT_DISPLAYNAME     | A projekt megjelenítendő neve. Lehet, hogy üres. |
| PROJECT_DESCRIPTION     | A projekt leírása. Lehet, hogy üres.  |
| PROJECT_ADMIN_USER      | A rendszergazda felhasználó felhasználóneve.       |
| PROJECT_REQUESTING_USER | A kérelmező felhasználó felhasználóneve.           |

Az API-hoz való hozzáférés az önkiszolgáló fürtszerepkör-kötéssel rendelkező fejlesztők számára biztosított. Ez a funkció alapértelmezés szerint minden hitelesített fejlesztő számára elérhető.

## <a name="modify-the-template-for-a-new-project"></a>Új projekt sablonjának módosítása 

1. Jelentkezzen be jogosultságokkal `customer-admin` rendelkező felhasználóként.

2. Az alapértelmezett projektkérelem-sablon szerkesztése.

   ```
   oc edit template project-request -n openshift
   ```

3. Távolítsa el az alapértelmezett projektsablont az Azure Red Hat OpenShift (ARO) frissítési folyamatából a következő megjegyzés hozzáadásával:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   A projektkérelem sablont az ARO frissítési folyamata nem frissíti. Ez lehetővé teszi az ügyfelek számára a sablon testreszabását és a testreszabások megőrzését a fürt frissítésekor.

## <a name="disable-the-self-provisioning-role"></a>Az önkiépítési szerepkör letiltása

Megakadályozhatja, hogy egy hitelesített felhasználói csoport új projekteket létesítsen.

1. Jelentkezzen be jogosultságokkal `customer-admin` rendelkező felhasználóként.

2. Az önkiszolgáló fürtszerepkör-kötés szerkesztése.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Távolítsa el a szerepkört az ARO frissítési `openshift.io/reconcile-protect: "true"`folyamatból a következő megjegyzéssel: .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Módosítsa a fürtszerepkör-kötést, hogy megakadályozza `system:authenticated:oauth` a projektek létrehozását:

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

## <a name="manage-default-templates-and-imagestreams"></a>Alapértelmezett sablonok és imageStreamek kezelése

Az Azure Red Hat OpenShift letilthatja a frissítéseket az `openshift` alapértelmezett sablonok és képstreamelések belül névtérben.
Az ALL `Templates` és `ImageStreams` a `openshift` névtér frissítéseinek letiltása:

1. Jelentkezzen be jogosultságokkal `customer-admin` rendelkező felhasználóként.

2. Névtér `openshift` szerkesztése:

   ```
   oc edit namespace openshift
   ```

3. A `openshift` névtér eltávolítása az ARO frissítési folyamatból a következő megjegyzés hozzáadásával:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   A `openshift` névtér bármely egyes objektuma eltávolítható a frissítési `openshift.io/reconcile-protect: "true"` folyamatból, ha jegyzetet ad hozzá.

## <a name="next-steps"></a>További lépések

Próbálja ki a bemutató:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)
