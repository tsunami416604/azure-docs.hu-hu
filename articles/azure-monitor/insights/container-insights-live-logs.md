---
title: Valós idejű naplók megtekintése az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Ez a cikk bemutatja a tároló naplóinak (stdout/stderr), a valós idejű nézet kubectl használata az Azure Monitor szolgáltatással tárolók nélkül.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: 6fe8cccf60e60ada34e3b7847964958cf6e03c4a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788835"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Tároló naplók valós időben az Azure Monitor szolgáltatással tárolók (előzetes verzió) megtekintése
Ez a szolgáltatás, amely jelenleg előzetes verzióban érhető el, az Azure Kubernetes Service (AKS) tároló naplóinak (stdout/stderr) valós idejű betekintést biztosít a kubectl-parancsok futtatása nélkül. Ezt a beállítást, ha új panelen megjelenik a tárolók teljesítmény adattábla alább a **tárolók** megtekintése.  További segítség valós időben kapcsolatos hibák elhárítása a tároló motor által generált élő naplózási jeleníti meg. **Közreműködői** fürterőforrás való hozzáférésre szüksége a funkció működéséhez.

Élő naplók által támogatott három különböző módszereket a naplók elérése:

1. Az AKS nélküli Kubernetes RBAC-hitelesítés engedélyezve 
2. Az AKS Kubernetes RBAC-hitelesítés engedélyezve
3. Az AKS engedélyezve van az Azure Active Directory (AD) SAML egyszeri bejelentkezést a alapján. 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-fürt nélkül RBAC engedélyezve
 
Ha egy Kubernetes-fürtöt, amely nincs beállítva a Kubernetes RBAC-hitelesítés vagy az Azure AD egyszeri bejelentkezéses integrálva van, nem kell az alábbi lépésekkel. Mivel a Kubernetes engedélyezési a kube-API-t használ, csak olvasható engedélyek szükségesek.

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC-hitelesítés
Ha engedélyezte a Kubernetes RBAC-hitelesítés, szüksége lesz a alkalmazni a fürt szerepkör-kötés. A következő példa lépései bemutatják, hogyan konfigurálhatja a fürt szerepkör-kötés yaml konfigurációs sablon alapján.   

1. Másolja és illessze be a yaml-fájlt, és mentse LogReaderRBAC.yaml.  

   ```
   apiVersion: rbac.authorization.k8s.io/v1 
   kind: ClusterRole 
   metadata: 
      name: containerHealth-log-reader 
   rules: 
      - apiGroups: [""] 
        resources: ["pods/log"] 
        verbs: ["get"] 
   --- 
   apiVersion: rbac.authorization.k8s.io/v1 
   kind: ClusterRoleBinding 
   metadata: 
      name: containerHealth-read-logs-global 
   roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
   subjects: 
      - kind: User 
        name: clusterUser 
        apiGroup: rbac.authorization.k8s.io 
   ```

2. A fürt szabály kötést létrehozni a következő parancs futtatásával: `kubectl create -f LogReaderRBAC.yaml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Az AKS konfigurálása az Azure Active Directoryval
Az AKS beállítható úgy, hogy a felhasználók hitelesítéséhez az Azure Active Directory (AD) használja. Ha első alkalommal konfigurál, [integrálása az Azure Active Directory és az Azure Kubernetes Service](../../aks/aad-integration.md). A lépések létrehozása során a [ügyfélalkalmazás](../../aks/aad-integration.md#create-client-application) , és adja meg a **átirányítási URI-**, másik URI hozzáadása a listához kell `https://ininprodeusuxbase.microsoft.com/*`.  

>[!NOTE]
>Az egyszeri bejelentkezést az Azure Active Directory konfigurálása hitelesítési csak egy új AKS-fürt kezdeti telepítése során is elvégezhető. Egy már üzembe helyezte az AKS-fürtöt az egyszeri bejelentkezés nem állíthatja be.  
> 

## <a name="view-live-logs"></a>Élő naplók megtekintése
Amikor megtekintésekor **tárolók**, is **Zobrazit Kontejner naplók** vagy **élő tárolónaplók megtekintése**.  Ha bejelöli **nézet élő tárolónaplók**, egy új panel jelenik meg a tárolók teljesítmény adattábla és bemutató élő naplózás valós időben kapcsolatos hibák elhárítása További segítség a tároló motor által generált.  
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Az a **Microsoft Azure** menüjében válassza **figyelő** majd **tárolók**.  
3. Jelöljön ki egy tárolót a listában a **tárolók figyelt** megtekintése.  
4. Válassza ki a **tárolók** nézet és a egy kiválasztott tárolóban, a hivatkozásra a Tulajdonságok panelen **élő tárolónaplók megtekintése** szerepel a listán.  
5. Ha az AKS-fürtöt az egyszeri bejelentkezési AAD használatával van konfigurálva, a böngésző-munkamenet során először használ hitelesítésre kéri. Válassza ki a fiókot és a hitelesítés befejezéséhez az Azure-ral.  

A sikeres hitelesítés után az élő napló panel alsó részén a középső ablaktáblán megjelenik. A fetch állapotjelzője mutatja egy zöld pipa, amely a panel jobb, ha az azt jelenti, és képes lekérni az adatokat.
    
  ![Élő naplók panel az adatok lekérése](./media/container-insights-live-logs/live-logs-pane-01.png)  

A keresősávba szűrhet égbe tör kiemelheti a szöveg a naplóban.   

  ![Élő naplók panelen szűrő példa](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

Automatikus görgetés felfüggesztése és a panelen viselkedését vezérli, és lehetővé teszi, hogy manuálisan Görgessen végig a új naplóadatokat, olvassa el, kattintson a a **görgessen** lehetőséget.  Automatikus görgetés újbóli engedélyezéséhez egyszerűen kattintson a **görgessen** újra lehetőséget.  Naplóadatok lekérése kattintva is felfüggesztheti a **szüneteltetése** lehetőséget, és ha készen áll a folytatásához, egyszerűen kattintson **lejátszása**.  

![Élő naplók panelen szüneteltetése élő megtekintése](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan kell használni az Azure Monitor és egyéb monitorozhatja az AKS-fürt a folytatáshoz tekintse meg a [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).
