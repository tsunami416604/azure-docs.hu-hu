---
title: Adatok megjelenítése az Azure Adatkezelő Kibana használatával
description: Ebből a cikkből megtudhatja, hogyan állíthatja be az Azure Adatkezelőt a Kibana adatforrásaként
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164812"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Adatok megjelenítése az Azure Adatkezelő a Kibana-ben a K2Bridge nyílt forráskódú összekötővel

A K2Bridge (Kibana-Kusto Bridge) lehetővé teszi az Azure-Adatkezelő adatforrásként való használatát, valamint a Kibana-ben tárolt adatmegjelenítést. A K2Bridge egy [nyílt forráskódú](https://github.com/microsoft/K2Bridge) tároló alkalmazás, amely proxyként funkcionál egy Kibana-példány és egy Azure adatkezelő-fürt között. Ez a cikk azt ismerteti, hogyan lehet a K2Bridge használatával létrehozni a kapcsolódást.

A K2Bridge lefordítja a Kibana-lekérdezéseket a Kusto-lekérdezési nyelvre (KQL), és elküldi az Azure Adatkezelő eredményeit vissza Kibana. 

   ![diagram](media/k2bridge/k2bridge-chart.png)

A K2Bridge támogatja a Kibana felderítése lapot, ahol a következőket teheti:
* Keresés és az adatgyűjtés
* Szűrés eredményei
* Mezők hozzáadása vagy eltávolítása az eredmények rácsában
* Rekord tartalmának megtekintése
* Keresések mentése és megosztása

Az alábbi képen látható egy Kibana-példány, amely az Azure Adatkezelőhoz van kötve K2Bridge szerint. A Kibana felhasználói felülete változatlan marad.

   [![Kibana lap](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy az Azure Adatkezelő Kibana-ban lévő adatait láthatóvá tudja megjeleníteni, a következők állnak készen:

* [Helm v3](https://github.com/helm/helm#install), a Kubernetes csomagkezelő
* Az Azure Kubernetes Service (ak) fürtöt vagy bármely más Kubernetes-fürtöt (1,14-es verzió a 1,16-es verzióra) tesztelték és ellenőrizték. Ha AK-fürtre van szüksége, tekintse meg az AK-fürtök üzembe helyezése [Az Azure CLI használatával](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) vagy [a Azure Portal használatával](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal) című témakört.
* [Azure adatkezelő-fürt](create-cluster-database-portal.md), beleértve a következőket:
    * Az Azure Adatkezelő-fürt URL-címe 
    * Az adatbázis neve
    
* Egy Azure AD-szolgáltatás, amely az Azure Adatkezelőban tárolt adatmegjelenítésre engedélyt kapott, beleértve a következőket:
    * Az ügyfél azonosítója 
    * Az ügyfél titka

    A "Viewer" engedéllyel rendelkező egyszerű szolgáltatásnév használata ajánlott. A magasabb szintű engedélyek használata nem ajánlott.

    * [Állítsa be a fürt nézetének engedélyeit az Azure ad egyszerű szolgáltatásnév számára](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Az Azure AD egyszerű szolgáltatásával kapcsolatos további információkért lásd: [Azure ad-szolgáltatásnév létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>K2Bridge futtatása az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint a K2Bridges's Helm diagram a Microsoft Container Registry (MCR) webhelyen található nyilvánosan elérhető rendszerképre hivatkozik. A MCR nem igényel hitelesítő adatokat, és nem működik.

1. Töltse le a szükséges Helm-diagramokat.

1. Adja hozzá a Elasticsearch függőséget a Helmhoz. 
    A Elasticsearch függőség oka az, hogy a K2Bridge egy belső, kisméretű Elasticsearch-példányt használ a metaadatokkal kapcsolatos kérések szolgáltatásához (például index-minták és mentett lekérdezések). Ebben a belső példányban nem menti az üzleti adatokat, és a megvalósítás részleteit is figyelembe veheti. 

    1. A Elasticsearch függőség hozzáadása a Helmhoz:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. A K2Bridge diagram beszerzése a GitHub-adattár diagramok könyvtára:
        1. A tárház klónozása a [githubról](https://github.com/microsoft/K2Bridge).
        1. Nyissa meg a K2Bridges gyökérszintű tárház könyvtárát.
        1. Futtassa a következőt:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. K2Bridge üzembe helyezése:

    1. Állítsa be a változókat a környezetének megfelelő értékekkel:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Választható Engedélyezze az Azure Application Insights telemetria. 
        Ha első alkalommal használja az Azure Application Insights-t, először [létre kell hoznia egy Application Insights-erőforrást](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). A kialakítási kulcsot át kell [másolnia](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) egy változóba: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>A K2Bridge diagram telepítése:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        A [konfigurációban](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) megtalálhatja a konfigurációs beállítások teljes készletét.

    1. A parancs kimenete a következő Helm-parancs futtatását javasolja a Kibana telepítéséhez. Szükség esetén futtassa a következőket:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. Port továbbításának használata a Kibana eléréséhez a localhost-on: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. Kapcsolódjon a Kibana a http://127.0.0.1:5601tallózásával.

    1. Tegye elérhetővé a Kibana a végfelhasználók számára. Ehhez több módszer is van. A használt módszer nagy mértékben a használati esettől függ.

        Például:

        Tegye elérhetővé a szolgáltatást terheléselosztó szolgáltatásként. Ehhez adja hozzá a következő paramétert a K2Bridge Helm install parancshoz ([fenti](#install-k2bridge-chart)):

        `--set service.type=LoadBalancer`
    
        Majd futtassa ezt:

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        A kimenetnek az alábbihoz hasonlóan kell kinéznie: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        Ezután használhatja a megjelenő generált külső IP-címet, és használhatja a Kibana eléréséhez egy böngésző megnyitásával: `\<EXTERNAL-IP>:5601`.

1. Adja meg az index-mintákat az adatai eléréséhez:  
Egy új Kibana-példányban:
     1. Nyissa meg a Kibana.
     1. Navigáljon a felügyelet elemre.
     1. Válassza az **index mintázatok**lehetőséget. 
     1. Hozzon létre egy index mintát.
Az index nevének pontosan egyeznie kell a táblázat nevével vagy a függvény nevével, csillag nélkül. A megfelelő sort a listából is másolhatja.

> [!Note]
> Más Kubernetes-szolgáltatókon való futtatáshoz módosítsa a `values.yaml` Elasticsearch storageClassName a szolgáltató által javasoltnak megfelelően.

## <a name="visualize-data"></a>Adatok vizualizációja

Ha az Azure Adatkezelő a Kibana adatforrásaként van konfigurálva, a Kibana használatával megvizsgálhatja az adatforrásokat. 

1. A Kibana bal oldali menüjében válassza a **felderítés** fület.

1. A bal oldali legördülő listából válasszon ki egy index mintát (ebben az esetben egy Azure Adatkezelő táblázatot), amely meghatározza a felderíteni kívánt adatforrást.
    
   ![Válasszon ki egy index mintát](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Ha az adatai Időszűrő mezővel rendelkeznek, megadhatja az időtartományt. Az oldal jobb felső részén állítsa be az időszűrőt. Alapértelmezés szerint a felderítés az elmúlt 15 percben jeleníti meg az adatértékeket.

   ![Időszűrő](media/k2bridge/k2bridge-time-filter.png)
    
1. Az eredmények táblázat az első 500 rekordot jeleníti meg. A dokumentumok kibontásával megvizsgálhatja a mezőkben tárolt adattartalmakat a JSON-vagy a Table formats szolgáltatásban.

   ![Rekord kibontása](media/k2bridge/k2bridge-expand-record.png)

1. Alapértelmezés szerint a Results (eredmények) tábla a dokumentum _source és az időmező (ha létezik) oszlopait tartalmazza. A bal oldali oldalsávon a mező neve melletti **Hozzáadás** gombra kattintva kiválaszthatja az eredmények táblába felvenni kívánt oszlopokat.

   ![Adott oszlopok](media/k2bridge/k2bridge-specific-columns.png)
    
1. A lekérdezési sávon az alábbiak szerint kereshet:
    * Keresési kifejezés beírása
    * A Lucene lekérdezési szintaxis használata. 
    Például:
        * A "hiba" kifejezésre kattintva megkeresheti az összes olyan rekordot, amely tartalmazza ezt az értéket. 
        * Keressen a "Status: 200" kifejezésre, hogy az összes rekordot lekérje a 200 állapot értékével. 
    * Logikai operátorok használata (és, vagy nem)
    * Helyettesítő karakterek használata (csillag "\*" vagy kérdőjel "?") Például:
        * A lekérdezési `"destination_city: L*"` megfelel a rekordoknak, amelyekben a célként megadott város értéke "l" karakterrel kezdődik (a K2Bridge nem megkülönbözteti a kis-és nagybetűket).

    ![A lekérdezés futtatása](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > A keresésben további keresési szabályok és [logika található.](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)

1. A keresési eredmények szűréséhez használja a lap jobb oldali oldalsávján található **mezőlista** . 
    A mezőlista itt látható:
    * A mező első öt értéke
    * A mezőt tartalmazó rekordok száma
    * Az egyes értékeket tartalmazó rekordok százalékos aránya. 
    
    >[!Tip]
    > A (+) nagyító ikon használatával megkeresheti az összes olyan rekordot, amely egy adott értékkel rendelkezik.
    
    ![Mezőlista](media/k2bridge/k2bridge-field-list.png)
   
    Az eredményeket a (+) nagyító ikon használatával is szűrheti az eredmények táblázatának eredmény tábla formátum nézetében.
    
     ![Táblák listája](media/k2bridge/k2bridge-table-list.png)
    
1. A keresés **mentéséhez** vagy **megosztásához** válassza a lehetőséget.

     ![Keresés mentése](media/k2bridge/k2bridge-save-search.png)
