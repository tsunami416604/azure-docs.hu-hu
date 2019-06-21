---
title: Valós idejű naplók megtekintése az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Ez a cikk bemutatja a tároló naplóinak (stdout/stderr) és az események valós idejű nézet kubectl használata az Azure Monitor szolgáltatással tárolók nélkül.
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
ms.date: 06/19/2019
ms.author: magoedte
ms.openlocfilehash: 7fd9248fd38054b7f0e1fad2888d8b0d4cf2e60c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274221"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Naplók és események megtekintése a valós idejű (előzetes verzió)
-Tárolókhoz az Azure Monitor tartalmaz egy szolgáltatást, amely jelenleg előzetes verziójú, amely az Azure Kubernetes Service (AKS) tároló naplóinak (stdout/stderr) és az események élő betekintést biztosít a kubectl-parancsok futtatása nélkül. Bármelyik lehetőséget választja, amikor egy új panel jelenik meg a teljesítmény adattábla a a **csomópontok**, **tartományvezérlők**, és **tárolók** megtekintése. Azt mutatja, élő naplózás és a tároló motor további segítik a hibaelhárítást a valós idejű által előállított eseményeket.

>[!NOTE]
>**Közreműködői** fürterőforrás való hozzáférésre szüksége a funkció működéséhez.
>

Élő naplókat a naplók elérése három különböző módszereket támogatja:

1. Az AKS nélküli Kubernetes RBAC-hitelesítés engedélyezve
2. Az AKS Kubernetes RBAC-hitelesítés engedélyezve
3. Az AKS az Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezés engedélyezve

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
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
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

2. Ha első alkalommal konfigurál, a következő parancs futtatásával hozzon létre a fürt szabály kötést: `kubectl create -f LogReaderRBAC.yaml`. Ha korábban engedélyezte a támogatás az élő naplók megtekintése előtt vezettünk be az élő eseménynaplójában, frissítse a konfigurációt, futtassa a következő parancsot: `kubectl apply -f LogReaderRBAC.yml`.

## <a name="configure-aks-with-azure-active-directory"></a>Az AKS konfigurálása az Azure Active Directoryval

Az AKS beállítható úgy, hogy a felhasználók hitelesítéséhez az Azure Active Directory (AD) használja. Ha első alkalommal konfigurál, [integrálása az Azure Active Directory és az Azure Kubernetes Service](../../aks/azure-ad-integration.md). A lépések létrehozása során a [ügyfélalkalmazás](../../aks/azure-ad-integration.md#create-the-client-application), adja meg a következőket:

- **Átirányítási URI-t (nem kötelező)** : Ez egy **webes** típusához, és az alap URL-cím lehet `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Miután felvette az alkalmazást, a a **áttekintése** lapon jelölje be **hitelesítési** a bal oldali panelen. Az a **hitelesítési** lap **speciális beállítások** implicit módon biztosítania **hozzáférési jogkivonatokat** és **azonosító-jogkivonatokat** , majd mentse a módosítások.

>[!NOTE]
>Az egyszeri bejelentkezést az Azure Active Directory konfigurálása hitelesítési csak egy új AKS-fürt kezdeti telepítése során is elvégezhető. Egy már üzembe helyezte az AKS-fürtöt az egyszeri bejelentkezés nem állíthatja be.
  
>[!IMPORTANT]
>Ha Ön újrakonfigurálása az Azure AD a felhasználók hitelesítéséhez a frissített, törölje a böngésző gyorsítótárát annak biztosítása érdekében a frissített hitelesítési token letöltése és alkalmazása URI-val.   

## <a name="view-live-logs-and-events"></a>Élő naplók megtekintése és események

Megtekintheti a valós idejű alkalmazásnapló-események az azok létrejöttekor a tároló motor által a **csomópontok**, **tartományvezérlők**, és **tárolók** megtekintése. A Tulajdonságok panelen válassza ki **megtekintheti az élő adatok (előzetes verzió)** lehetőséget és a egy ablaktábla, ahol megtekintheti napló- és események folyamatos továbbítása a teljesítmény adattábla következőkben.

![Csomópont tulajdonságai panelen nézet élő naplók lehetőség](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Napló- és eseményt üzenetek pedig csak a nézetben kiválasztott erőforrás típusa alapján.

| Nézet | Erőforrás típusa | Napló vagy esemény | Megjelenített adatokhoz |
|------|---------------|--------------|----------------|
| Csomópontok | Csomópont | Esemény | Ha egy csomópont van kiválasztva események sincs szűrve, és a Kubernetes-fürtre kiterjedő események megjelenítése. Panel címe a fürt nevét jeleníti meg. |
| Csomópontok | Pod | Esemény | A pod kiválasztásakor események a névtérhez vannak szűrve. A panel címsorában a pod névtere. | 
| Vezérlők | Pod | Esemény | A pod kiválasztásakor események a névtérhez vannak szűrve. A panel címsorában a pod névtere. |
| Vezérlők | tartományvezérlő | Esemény | A vezérlő kiválasztásakor a névtér események vannak szűrve. A panel címsorában a névteret a vezérlő. |
| Csomópontok és vezérlők és tárolók | Tároló | Naplók | A panel címsorában a pod a tároló nevére van csoportosítva. |

Ha az AKS-fürtöt az egyszeri bejelentkezési AAD használatával van konfigurálva, a böngésző-munkamenet során először használ hitelesítésre kéri. Válassza ki a fiókot és a hitelesítés befejezéséhez az Azure-ral.  

A sikeres hitelesítés után az élő napló panel alsó részén a középső ablaktáblán megjelenik. A fetch állapotjelzője mutatja egy zöld pipa, amely a panel jobb, ha az azt jelenti, és képes lekérni az adatokat.
    
  ![Élő naplók panel az adatok lekérése](./media/container-insights-live-logs/live-logs-pane-01.png)  

A keresősávba kiemelheti a szöveg a napló vagy eseményt, és a keresősávba, a jobb szélen kulcs szó szerint szűrheti, azt jeleníti meg, hány eredményt egyezik meg a szűrő.

  ![Élő naplók panelen szűrő példa](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Események megtekintése közben Emellett korlátozhatja az eredményeket használja a **szűrő** megszámlálásához a keresősávba, a jobb oldalon található. Milyen erőforrást jelölt ki, függően a megszámlálásához egy pod, névtérre vagy a választott fürt sorolja fel.  

Automatikus görgetés felfüggesztése és a panelen viselkedését vezérli, és lehetővé teszi, hogy manuálisan Görgessen végig az új adatok, olvassa el, kattintson a a **görgessen** lehetőséget. Automatikus görgetés újbóli engedélyezéséhez egyszerűen kattintson a **görgessen** újra lehetőséget. Napló vagy esemény adatok lekérésének kattintva is felfüggesztheti a **szüneteltetése** lehetőséget, és ha készen áll a folytatásához, egyszerűen kattintson **lejátszása**.  

![Élő naplók panelen szüneteltetése élő megtekintése](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Nyissa meg az Azure Monitor naplóira kiválasztásával megtekintheti a korábbi tárolónaplók **tárolónaplók megtekintése** a legördülő listából **megtekintés az analyticsben**.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan kell használni az Azure Monitor és egyéb monitorozhatja az AKS-fürt a folytatáshoz tekintse meg a [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).
- Nézet [lekérdezés példák jelentkezzen](container-insights-log-search.md#search-logs-to-analyze-data) , előre definiált lekérdezések és példák kiértékelése vagy riasztási, megjelenítése és elemzése a fürtök testreszabásához.
