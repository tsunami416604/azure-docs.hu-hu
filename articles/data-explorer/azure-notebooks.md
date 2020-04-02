---
title: Az Azure-jegyzetfüzetek használata adatok elemzéséhez az Azure Data Explorerben
description: Ez a témakör bemutatja, hogyan hozhat létre lekérdezést egy Azure Notebook használatával
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522406"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Az Azure-jegyzetfüzetek használata adatok elemzéséhez az Azure Data Explorerben

[Az Azure Notebooks](https://notebooks.azure.com/) egy Azure-felhőszolgáltatás, amely leegyszerűsíti a [Jupyter-jegyzetfüzetek](https://jupyter.org/)létrehozását és megosztását. Az Azure Notebooks megkönnyíti a dokumentáció, a kód és a kód futtatásának eredményeit.

> [!Note]
> * Az alábbi eljárás az Azure Notebooks környezetben lévő Python-ügyfelet használja az Azure Data Explorer lekérdezéséhez. Azonban a [KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) segítségével is lekérdezheti az Azure Data Explorer.
> * Egyes felhasználók az Edge használatával történő hitelesítési problémákról számoltak be; amíg az ilyen problémák megoldódnak, használjon másik böngészőt.

## <a name="create-a-project"></a>Projekt létrehozása

1. Nyissa meg az [Azure-jegyzetfüzeteket](https://notebooks.azure.com/) a böngészőben, és jelentkezzen be.

1. A fejlécben válassza a **Saját projektek** lapot. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Válassza a **+ Új projektek**lehetőséget .
    
1. Az **Új projekt létrehozása** párbeszédpanelen:
    1. Írja be a **Projekt nevét**.
    1. Törölje a jelet a **Nyilvános** jelölőnégyzetből.
        >[!Important]
        > Ha nem törli a jelet a Nyilvános jelölőnégyzetből, a projekt a nyílt interneten jelenik meg.
    1. Kattintson a **Létrehozás** gombra.
    
    ![Új projekt létrehozása](media/azurenotebooks/an-create-new-project-blank.png)

    A projektazonosító automatikusan létrejön.

## <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

1. Az új projektben hozzon létre egy jegyzetfüzetet. A jegyzetfüzetnek támogatott nyelvet kell [használnia.](https://github.com/Azure/azure-kusto-python#minimum-requirements)
Jegyzetfüzet létrehozásához válassza a **+ Új** és **a Jegyzetfüzet**lehetőséget.

    ![Új jegyzetfüzet létrehozása](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. Az **Új jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét.

1. Válassza **a Python 3.6** és **az Új**lehetőséget.
    
    ![Új jegyzetfüzet létrehozása](media/azurenotebooks/an-create-new-notebook.png) 
    
1. A projektben válassza ki az új jegyzetfüzetet.

    ![Új jegyzetfüzet kiválasztása](media/azurenotebooks/an-select-notebook.png)

    Várjon, amíg a Python kernel inicializálódik. Ha a kitöltött kör ikon üres kör ikonná változik, folytathatja a munkát.

    ![A rendszermag inicializálása](media/azurenotebooks/an-python-init-icon.png)

1. A rendszermodul importálásához írja be a következő parancsokat, és válassza a **Futtatás**lehetőséget:
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Cella futtatásához nyomja le a Shift+Enter billentyűkombinációt is.

1.  SDK-osztályok importálásához írja be a következő parancsokat, és válassza a **Futtatás**lehetőséget:
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  A kapcsolati karakterlánc létrehozásához és a Kusto ügyfél indításához írja be a következő parancsokat, és válassza a **Futtatás**lehetőséget:  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. A kérdésben nyisson meg [https://aka.ms/devicelogin](https://aka.ms/devicelogin)egy új böngészőlapot a lehetőséghez. 
   
1. Adja meg az engedélyezési kódot, és írja@microsoft.combe az AAD ( ) fiókját. A Kusto-ügyfél `kc` most már hitelesíthető az Azure Data Explorer az Ön identitását használva.

1. Térjen vissza a jegyzetfüzetbe a hitelesítés eredményének megtekintéséhez. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Kusto-lekérdezés végrehajtása

1. Írja be a Kusto-lekérdezést, és válassza a **Futtatás lehetőséget.** Példa:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>További lépések

* [Kusto-python GitHub-társzó](https://github.com/Azure/azure-kusto-python)
