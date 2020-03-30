---
title: Adatok megjelenítése az Azure Data Explorerből a Kibana használatával
description: Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure Data Explorert a Kibana adatforrásaként.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065614"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Az Azure Data Explorer adatainak megjelenítése Kibanában a K2Bridge nyílt forráskódú összekötővel

A K2Bridge (Kibana-Kusto Bridge) lehetővé teszi az Azure Data Explorer adatforrásként való használatát, valamint az adatok megjelenítését kibana. A K2Bridge egy [nyílt forráskódú](https://github.com/microsoft/K2Bridge) tárolós alkalmazás, amely proxyként működik egy Kibana példány és egy Azure Data Explorer-fürt között. Ez a cikk azt ismerteti, hogyan hozhat létre a K2Bridge a kapcsolat létrehozásához.

A K2Bridge lefordítja a Kibana-lekérdezéseket Kusto lekérdezési nyelvre (KQL), és visszaküldi az Azure Data Explorer eredményeit Kibanának. 

   ![diagram](media/k2bridge/k2bridge-chart.png)

A K2Bridge támogatja a Kibana Discover lapját, ahol teheti:
* Az adatok keresése és feltárása
* Szűrés eredményei
* Mezők hozzáadása vagy eltávolítása az eredményrácsban
* Rekordtartalom megtekintése
* Keresések mentése és megosztása

Az alábbi képen egy Kibana-példány látható, amely et a K2Bridge kötött az Azure Data Explorerhez. A kibanai felhasználói élmény változatlan.

   [![Kibana oldal](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megjelenítheti az Azure Data Explorer adatait Kibanában, készítse elő a következőket:

* [Helm V3](https://github.com/helm/helm#install), a Kubernetes csomagkezelő
* Az Azure Kubernetes Service (AKS) fürt, vagy bármely más Kubernetes-fürt (1.14-es verzió az 1.16-os verzióra tesztelt és ellenőrzött). Ha AKS-fürtre van szüksége, olvassa el az AKS-fürt üzembe helyezése [az Azure CLI használatával](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) vagy az Azure Portal használatával című [témakört.](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Egy Azure Data Explorer-fürt,](create-cluster-database-portal.md)amely a következőket tartalmazza:
    * Az Azure Data Explorer fürt URL-címe 
    * Az adatbázis neve
    
* Az Azure Data Explorer ben az adatok megtekintésére jogosult Egyszerű Azure AD szolgáltatás, többek között a következők:
    * Az ügyfélazonosító 
    * Az ügyfél titkos

    A "Viewer" engedéllyel rendelkező szolgáltatásnév használata ajánlott. Nem ajánlott magasabb engedélyeket használni.

    * [Állítsa be a fürt nézetengedélyeit az Azure AD egyszerű szolgáltatásához.](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)

    Az Azure AD egyszerű szolgáltatásról további információt az [Egyszerű Azure-szolgáltatásnév létrehozása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>A K2Bridge futtatása az Azure Kubernetes szolgáltatáson (AKS)

Alapértelmezés szerint a K2Bridges Helm-diagramja a Microsoft tárolóbeállítás-jegyzékében (MCR) található, nyilvánosan elérhető lemezképre hivatkozik. Az MCR nem igényel hitelesítő adatokat, és beépített munkákat végez.

1. Töltse le a szükséges Helm listákat.

1. Adja hozzá az Elasticsearch-függőséget a Helmhez. 
    Az Elasticsearch-függőség oka az, hogy a K2Bridge egy belső kis Elasticsearch-példányt használ a metaadatokkal kapcsolatos kérelmek (például az indexminták és a mentett lekérdezések) kiszolgálásához. Ebben a belső példányban nem kerül mentésüzleti adatok, és implementációs részletnek tekinthetők. 

    1. Az Elasticsearch-függőség hozzáadása a Helmhez:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. A K2Bridge-diagram lefektusa a GitHub-tárház diagramkönyvtára alól:
        1. Klónozza a tárházat a [GitHubról.](https://github.com/microsoft/K2Bridge)
        1. Nyissa meg a K2Bridges gyökértárkönyvtárát.
        1. Futtassa a következőt:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. K2Bridge telepítése:

    1. Állítsa be a változókat a környezetének megfelelő értékekkel:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. (Nem kötelező) Engedélyezze az Azure Application Insights telemetriai adatait. 
        Ha első alkalommal használja az Azure Application Insightsot, először [hozzon létre egy Application Insights-erőforrást.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) A [műszerezési kulcsot át kell másolnia](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) egy változóba: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>A K2Bridge-diagram telepítése:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        A [konfigurációban](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) megtalálhatja a konfigurációs beállítások teljes készletét.

    1. A parancs kimenete azt javasolja, hogy a következő Helm parancs futtassa a Kibana telepítéséhez. Opcionálisan futtassa:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Portforwarding használatával elérheti kibana a localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Csatlakozzon Kibanához a http://127.0.0.1:5601talasszon.

    1. Tegye ki Kibanát a végfelhasználóknak. Erre több módszer is létezik. A használt módszer nagymértékben függ a használati esettől.

        Példa:

        Tegye elérhetővé a szolgáltatást LoadBalancer szolgáltatásként. Ehhez adja hozzá `--set service.type=LoadBalancer` a paramétert a K2Bridge Helm telepítési parancshoz ([fent](#install-k2bridge-chart)).        
    
        Majd futtassa ezt:
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        A kimenetnek így kell kinéznie: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        Ezután használhatja a létrehozott EXTERNAL-IP jelenik meg, és használja a `<EXTERNAL-IP>:5601`hozzáférést Kibana megnyitásával a böngésző .

1. Indexminták konfigurálása az adatok eléréséhez:  
Egy új Kibana például:
     1. Nyisd ki Kibanát.
     1. Keresse meg a Vezetőség et.
     1. Válassza az **Indexminták lehetőséget.** 
     1. Indexminta létrehozása.
Az index nevének pontosan meg kell egyeznie a tábla nevével vagy a függvény nevével, csillag nélkül. A megfelelő sort átmásolhatja a listából.

> [!Note]
> Más Kubernetes-szolgáltatókon való futtatáshoz módosítsa az `values.yaml` Elasticsearch storageClassName-t úgy, hogy illeszkedjen a szolgáltató által javasolthoz.

## <a name="visualize-data"></a>Adatok vizualizációja

Ha az Azure Data Explorer a Kibana adatforrásaként van konfigurálva, a Kibana segítségével megismerheti az adatokat. 

1. A Kibana bal oldali menüjében válassza a **Felfedezés** lapot.

1. A bal oldali legördülő listából válasszon egy indexmintát (ebben az esetben egy Azure Data Explorer-táblát), amely meghatározza a feltárni kívánt adatforrást.
    
   ![Tárgymutató-minta kijelölése](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Ha az adatok rendelkeznek időszűrő mezővel, megadhatja az időtartományt. Az oldal jobb felső részén állítson be egy időszűrőt. Alapértelmezés szerint a Discover az elmúlt 15 perc adatait jeleníti meg.

   ![Időszűrő](media/k2bridge/k2bridge-time-filter.png)
    
1. Az eredménytábla az első 500 rekordot mutatja. A dokumentum kibontásával json- vagy táblaformátumban vizsgálhatja meg a mezőadatokat.

   ![Rekord kibontása](media/k2bridge/k2bridge-expand-record.png)

1. Alapértelmezés szerint az eredménytábla oszlopokat tartalmaz a dokumentum _source és az időmezőhöz (ha van ilyen). Az eredménytáblához a hozzáadás gombra kattintva választhatja ki az eredménytáblához a **hozzáadás** lehetőséget a bal oldalsávon.

   ![Konkrét oszlopok](media/k2bridge/k2bridge-specific-columns.png)
    
1. A lekérdezési sávon a következő konkretinálhat:
    * Keresési kifejezés megadása
    * A Lucene lekérdezés szintaxisának használata. 
    Példa:
        * Keresés "hiba", hogy megtalálja az összes rekordot, amely tartalmazza ezt az értéket. 
        * Az "állapot: 200" kifejezésre keress, és az összes 200-as állapotértékű rekordot leszeretné. 
    * Logikai operátorok használata (ÉS, VAGY, NEM)
    * Helyettesítő karakterek (csillag " \* vagy kérdőjel használata "?") Például:
        * A `"destination_city: L*"` lekérdezés olyan rekordoknak felel meg, ahol a célvárosi érték "l" betűvel kezdődik (a K2Bridge nem felel meg a kis- és nagybetűk megkülönböztetésének).

    ![A lekérdezés futtatása](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > A [Keresés alkalmazásban](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)további keresési szabályokat és logikát találhat.

1. A keresési eredmények szűréséhez használja az oldal jobb oldali oldalsávján található **mezőlistát.** 
    A mezőlista a következő hely:
    * A mező első öt értéke
    * A mezőt tartalmazó rekordok száma
    * Az egyes értékeket tartalmazó rekordok százalékos aránya. 
    
    >[!Tip]
    > A (+) nagyító ikonnal megkeresheti az adott értékkel tartozó összes rekordot.
    
    ![Mezőlista](media/k2bridge/k2bridge-field-list.png)
   
    Az eredményeket az eredmények táblázategyes rekordainak eredménytáblázat-formátumnézetében lévő (+) nagyító ikonnal is szűrheti.
    
     ![Táblázatlista](media/k2bridge/k2bridge-table-list.png)
    
1. Válassza a **keresés mentéséhez** vagy **megosztásához** lehetőséget.

     ![Keresés mentése](media/k2bridge/k2bridge-save-search.png)
