---
title: Valós idejű naplók megtekintése az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Ez a cikk a tároló-naplók (StdOut/stderr) és az események valós idejű nézetét ismerteti, anélkül, hogy a kubectl-t használja a tárolók Azure Monitor.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 2eab6fa75e4adbbde7bcf20f18301a1e516235c2
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035347"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Naplók és események valós idejű megtekintése (előzetes verzió)
A tárolók Azure Monitor tartalmaz egy olyan funkciót, amely jelenleg előzetes verzióban érhető el, amely élő nézetet biztosít az Azure Kubernetes szolgáltatásbeli (ak) tároló-naplókhoz (StdOut/stderr) és az eseményekhez anélkül, hogy kubectl parancsokat kellene futtatnia. Ha bármelyik lehetőséget választja, egy új ablaktábla jelenik meg a csomópontokon, **vezérlőkön**éstárolók nézetben a teljesítményadatok tábla alatt. Az élő naplózást és a tárolórendszer által létrehozott eseményeket jeleníti meg, hogy a hibák elhárítása valós időben további segítséget nyújtson.

>[!NOTE]
>A szolgáltatás működéséhez **közreműködői** hozzáférés szükséges a fürterőforrás számára.
>

Az élő naplók három különböző módszert támogatnak a naplókhoz való hozzáférés szabályozásához:

1. Az AKS nélküli Kubernetes RBAC-hitelesítés engedélyezve
2. Az AKS Kubernetes RBAC-hitelesítés engedélyezve
3. Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezéssel engedélyezett AK

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-fürt nélkül RBAC engedélyezve
 
Ha egy Kubernetes-fürtöt, amely nincs beállítva a Kubernetes RBAC-hitelesítés vagy az Azure AD egyszeri bejelentkezéses integrálva van, nem kell az alábbi lépésekkel. Mivel a Kubernetes-hitelesítés a Kube-API-t használja, csak olvasható engedélyek szükségesek.

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

2. Ha első alkalommal konfigurálja, a következő parancs futtatásával alkalmazza a fürt szabály kötését: `kubectl create -f LogReaderRBAC.yaml`. Ha korábban engedélyezte az élő naplók előzetes verzióját az élő eseménynaplók bevezetése előtt, a konfiguráció frissítéséhez futtassa a következő parancsot: `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>Az AKS konfigurálása az Azure Active Directoryval

Az AKS beállítható úgy, hogy a felhasználók hitelesítéséhez az Azure Active Directory (AD) használja. Ha első alkalommal konfigurálja, tekintse meg az [Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../../aks/azure-ad-integration.md)című témakört. Az [ügyfélalkalmazás](../../aks/azure-ad-integration.md#create-the-client-application)létrehozásának lépései során a következőket kell megadnia:

-  **Átirányítási URI**: Két webalkalmazás-típust kell létrehozni. Az első alap URL-címnek a `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` értéknek kell lennie, a második `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`URL-címnek pedig az értéknek kell lennie.
- Az alkalmazás regisztrálását követően az **Áttekintés** oldalon válassza a **hitelesítés** lehetőséget a bal oldali ablaktáblán. A **hitelesítés** lapon, a **Speciális beállítások** területen a **hozzáférési tokenek** és **azonosító tokenek** implicit megadása, majd a módosítások mentése.

>[!NOTE]
>Az egyszeri bejelentkezés Azure Active Directory használatával történő hitelesítés konfigurálása csak az új AK-fürtök kezdeti telepítése során hajtható végre. Egy már üzembe helyezte az AKS-fürtöt az egyszeri bejelentkezés nem állíthatja be.
  
>[!IMPORTANT]
>Ha a frissített URI használatával újrakonfigurálta az Azure AD-t a felhasználói hitelesítéshez, törölje a böngésző gyorsítótárát, és győződjön meg arról, hogy a frissített hitelesítési jogkivonat letöltése és alkalmazása megtörtént.   

## <a name="view-live-logs-and-events"></a>Élő naplók és események megtekintése

A valós idejű naplózási eseményeket úgy tekintheti meg, ahogy a tároló motorja hozza létrea csomópontok, a **vezérlők**és a **tárolók** nézetből. A Tulajdonságok panelen válassza az **élő adatok megtekintése (előzetes verzió)** lehetőséget, és egy ablaktábla jelenik meg a teljesítményadatok tábla alatt, ahol a naplókat és az eseményeket egy folyamatos adatfolyamban tekintheti meg.

![Csomópont tulajdonságai ablaktábla élő naplók megtekintése lehetőség](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

A log és az Event üzenet a nézetben kiválasztott erőforrástípus alapján van korlátozva.

| Nézet | Erőforrás típusa | Napló vagy esemény | Bemutatott adatforrások |
|------|---------------|--------------|----------------|
| Csomópontok | Csomópont | Esemény | Ha egy csomópontot jelölt ki, a rendszer nem szűri és nem jeleníti meg a fürtre kiterjedő Kubernetes eseményeket. A panel címe a fürt nevét jeleníti meg. |
| Csomópontok | Pod | Esemény | Ha egy Pod van kiválasztva, a rendszer szűri az eseményeket a névterében. A panel címe a pod névterét jeleníti meg. | 
| Vezérlők | Pod | Esemény | Ha egy Pod van kiválasztva, a rendszer szűri az eseményeket a névterében. A panel címe a pod névterét jeleníti meg. |
| Vezérlők | Vezérlő | Esemény | Ha a vezérlőt kiválasztotta, a rendszer szűri az eseményeket a névterében. A panel címe a vezérlő névterét jeleníti meg. |
| Csomópontok/vezérlők/tárolók | Tároló | Logs | A panel címe annak a pod-nek a nevét mutatja, amelybe a tároló van csoportosítva. |

Ha az AKS-fürtöt az egyszeri bejelentkezési AAD használatával van konfigurálva, a böngésző-munkamenet során először használ hitelesítésre kéri. Válassza ki a fiókot és a hitelesítés befejezéséhez az Azure-ral.  

A sikeres hitelesítés után az élő napló panel alsó részén a középső ablaktáblán megjelenik. A fetch állapotjelzője mutatja egy zöld pipa, amely a panel jobb, ha az azt jelenti, és képes lekérni az adatokat.
    
  ![Élő naplók panel az adatok lekérése](./media/container-insights-live-logs/live-logs-pane-01.png)  

A keresősáv alapján szűrheti a kulcs szót, hogy kiemelje a naplóban vagy az eseményben szereplő szöveget, és a jobb szélen látható keresési sávban azt mutatja, hogy hány találat felel meg a szűrőnek.

  ![Élő naplók panelen szűrő példa](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Az események megtekintése közben az eredményeket a keresősáv jobb oldalán található **szűrő** pirulával is korlátozhatja. Attól függően, hogy melyik erőforrást választotta ki, a pirula egy Pod, Namespace vagy fürtöt listáz ki, amelyről a választott forrás van kiválasztva.  

Az autogörgetés felfüggesztéséhez és a panel működésének szabályozásához, valamint az új adatolvasás manuális görgetésének engedélyezéséhez kattintson a **görgetőgomb** lehetőségre. Az autoscroll újbóli engedélyezéséhez egyszerűen kattintson ismét a **görgetőgomb** lehetőségre. Szüneteltetheti a naplózási vagy az esemény adatainak lekérését is, ha a **szüneteltetés** lehetőségre kattint, és ha készen áll a folytatásra, egyszerűen kattintson a **Lejátszás**gombra.  

![Élő naplók panelen szüneteltetése élő megtekintése](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

A naplók megtekintése Azure Monitor lehetőségre kattintva megtekintheti a korábbi tárolók naplóit, ha kiválasztja a **tároló naplóinak megjelenítése** lehetőséget az **elemzés**legördülő lista nézetében.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan kell használni az Azure Monitor és egyéb monitorozhatja az AKS-fürt a folytatáshoz tekintse meg a [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásának, megjelenítésének vagy elemzésének kiértékeléséhez és testreszabásához.
