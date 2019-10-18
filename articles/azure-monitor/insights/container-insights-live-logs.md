---
title: A tárolók naplóinak Azure Monitor valós idejű megtekintése | Microsoft Docs
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
ms.openlocfilehash: d947b44177e9aa5777d759286d982e974e378497
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389788"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Naplók és események valós idejű megtekintése (előzetes verzió)
A tárolók Azure Monitor tartalmaz egy olyan funkciót, amely jelenleg előzetes verzióban érhető el, amely élő nézetet biztosít az Azure Kubernetes szolgáltatásbeli (ak) tároló-naplókhoz (StdOut/stderr) és az eseményekhez anélkül, hogy kubectl parancsokat kellene futtatnia. Ha bármelyik lehetőséget választja, egy új ablaktábla jelenik meg a **csomópontokon**, **vezérlőkön**és **tárolók** nézetben a teljesítményadatok tábla alatt. Az élő naplózást és a tárolórendszer által létrehozott eseményeket jeleníti meg, hogy a hibák elhárítása valós időben további segítséget nyújtson.

>[!NOTE]
>Ez a funkció minden Azure-régióban elérhető, beleértve az Azure China-t is. Jelenleg nem érhető el az Azure USA kormányzati szerveiben.

>[!NOTE]
>A funkció működéséhez az **Azure Kubernetes Service-fürt felhasználói szerepkörhöz** való hozzáférésre van szükség a fürt erőforrásához. [További információ az Azure Kubernetes-fürt felhasználói szerepköréről](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions).

Az élő naplók három különböző módszert támogatnak a naplókhoz való hozzáférés szabályozásához:

1. AK Kubernetes RBAC-hitelesítés nélkül engedélyezve
2. AK engedélyezve a Kubernetes RBAC-engedélyezéssel
3. Azure Active Directory (AD) SAML-alapú egyszeri bejelentkezéssel engedélyezett AK

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Kubernetes-fürt RBAC nélkül engedélyezve
 
Ha olyan Kubernetes-fürttel rendelkezik, amely nincs Kubernetes RBAC-hitelesítéssel konfigurálva, vagy az Azure AD egyszeri bejelentkezéssel van integrálva, nem kell végrehajtania ezeket a lépéseket. Mivel a Kubernetes-hitelesítés a Kube-API-t használja, csak olvasható engedélyek szükségesek.

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC-hitelesítés
Ha engedélyezte a Kubernetes RBAC-engedélyezést, akkor a fürt szerepkörének kötését kell alkalmaznia. Az alábbi példák bemutatják, hogyan konfigurálhatja a fürt szerepkörének kötését ebből a YAML-konfigurációs sablonból. 

1. Másolja és illessze be a YAML fájlt, és mentse LogReaderRBAC. YAML néven.  

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

## <a name="configure-aks-with-azure-active-directory"></a>Az AK konfigurálása Azure Active Directory

Az AK konfigurálható úgy, hogy Azure Active Directory (AD) használatát használja a felhasználói hitelesítéshez. Ha első alkalommal konfigurálja, tekintse meg az [Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../../aks/azure-ad-integration.md)című témakört. Az [ügyfélalkalmazás](../../aks/azure-ad-integration.md#create-the-client-application)létrehozásának lépései során a következőket kell megadnia:

-  **Átirányítási URI**: **két** webalkalmazás-típust kell létrehozni. Az első alap URL-címnek `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` értéknek kell lennie, és a második alap URL-címnek `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` értékűnek kell lennie.
- Az alkalmazás regisztrálását követően az **Áttekintés** oldalon válassza a **hitelesítés** lehetőséget a bal oldali ablaktáblán. A **hitelesítés** lapon, a **Speciális beállítások** területen a **hozzáférési tokenek** és **azonosító tokenek** implicit megadása, majd a módosítások mentése.

>[!NOTE]
>Ha ezt a funkciót az Azure China régióban használja, az első alap URL-címnek `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` értékűnek kell lennie, és a második URL-címnek `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` értékűnek kell lennie.

>[!NOTE]
>Az egyszeri bejelentkezés Azure Active Directory használatával történő hitelesítés konfigurálása csak az új AK-fürtök kezdeti telepítése során hajtható végre. Az egyszeri bejelentkezés nem konfigurálható egy már üzembe helyezett AK-fürthöz.
  
>[!IMPORTANT]
>Ha a frissített URI használatával újrakonfigurálta az Azure AD-t a felhasználói hitelesítéshez, törölje a böngésző gyorsítótárát, és győződjön meg arról, hogy a frissített hitelesítési jogkivonat letöltése és alkalmazása megtörtént.   

## <a name="view-live-logs-and-events"></a>Élő naplók és események megtekintése

A valós idejű naplózási eseményeket úgy tekintheti meg, ahogy a tároló motorja hozza létre a **csomópontok**, a **vezérlők**és a **tárolók** nézetből. A Tulajdonságok panelen válassza az **élő adatok megtekintése (előzetes verzió)** lehetőséget, és egy ablaktábla jelenik meg a teljesítményadatok tábla alatt, ahol a naplókat és az eseményeket egy folyamatos adatfolyamban tekintheti meg.

![Csomópont tulajdonságai ablaktábla élő naplók megtekintése lehetőség](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

A log és az Event üzenet a nézetben kiválasztott erőforrástípus alapján van korlátozva.

| Megtekintés | Erőforrás típusa | Napló vagy esemény | Bemutatott adatforrások |
|------|---------------|--------------|----------------|
| Csomópontok | Csomópont | Esemény | Ha egy csomópontot jelölt ki, a rendszer nem szűri és nem jeleníti meg a fürtre kiterjedő Kubernetes eseményeket. A panel címe a fürt nevét jeleníti meg. |
| Csomópontok | Pod | Esemény | Ha egy Pod van kiválasztva, a rendszer szűri az eseményeket a névterében. A panel címe a pod névterét jeleníti meg. | 
| Tartományvezérlők | Pod | Esemény | Ha egy Pod van kiválasztva, a rendszer szűri az eseményeket a névterében. A panel címe a pod névterét jeleníti meg. |
| Tartományvezérlők | Tartományvezérlő | Esemény | Ha a vezérlőt kiválasztotta, a rendszer szűri az eseményeket a névterében. A panel címe a vezérlő névterét jeleníti meg. |
| Csomópontok/vezérlők/tárolók | Tároló | Naplók | A panel címe annak a pod-nek a nevét mutatja, amelybe a tároló van csoportosítva. |

Ha az AK-fürt a HRE használatával van konfigurálva egyszeri bejelentkezéssel, a rendszer felszólítja, hogy a böngésző-munkamenet során először használja a hitelesítést. Válassza ki a fiókját, és fejezze be a hitelesítést az Azure-ban.  

A sikeres hitelesítés után az élő napló ablaktábla megjelenik a középső ablaktábla alsó részén. Ha a beolvasás állapota jelző zöld pipa jelenik meg, amely a panel jobb szélén található, az azt jelenti, hogy lekérheti az adatgyűjtést.
    
  ![Az élő naplók ablaktábla beolvasott adathalmaza](./media/container-insights-live-logs/live-logs-pane-01.png)  

A keresősáv alapján szűrheti a kulcs szót, hogy kiemelje a naplóban vagy az eseményben szereplő szöveget, és a jobb szélen látható keresési sávban azt mutatja, hogy hány találat felel meg a szűrőnek.

  ![Példa az élő naplók panel szűrőre](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Az események megtekintése közben az eredményeket a keresősáv jobb oldalán található **szűrő** pirulával is korlátozhatja. Attól függően, hogy melyik erőforrást választotta ki, a pirula egy Pod, Namespace vagy fürtöt listáz ki, amelyről a választott forrás van kiválasztva.  

Az autogörgetés felfüggesztéséhez és a panel működésének szabályozásához, valamint az új adatolvasás manuális görgetésének engedélyezéséhez kattintson a **görgetőgomb** lehetőségre. Az autoscroll újbóli engedélyezéséhez egyszerűen kattintson ismét a **görgetőgomb** lehetőségre. Szüneteltetheti a naplózási vagy az esemény adatainak lekérését is, ha a **szüneteltetés** lehetőségre kattint, és ha készen áll a folytatásra, egyszerűen kattintson a **Lejátszás**gombra.  

![Élő naplók ablaktábla élő nézetének szüneteltetése](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

A naplók megtekintése Azure Monitor lehetőségre kattintva megtekintheti a korábbi tárolók naplóit, ha kiválasztja a **tároló naplóinak megjelenítése** lehetőséget az elemzés legördülő lista **nézetében**.

## <a name="next-steps"></a>Következő lépések

- A Azure Monitor használatának megismeréséhez és az AK-fürt egyéb szempontjainak figyeléséhez lásd: az [Azure Kubernetes szolgáltatás állapotának megtekintése](container-insights-analyze.md).

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásának, megjelenítésének vagy elemzésének kiértékeléséhez és testreszabásához.
