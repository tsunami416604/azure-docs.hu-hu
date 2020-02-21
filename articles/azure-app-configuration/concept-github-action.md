---
title: GitHub-műveletek használata az Azure app Configuration Sync használatával
description: A GitHub-műveletek használatával aktiválhatja az alkalmazás konfigurációs példányának frissítését, ha a meghatározott műveleteket egy GitHub-adattáron hajtja végre.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523713"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Az App Configuration-példány szinkronizálása GitHub-műveletek segítségével
Az Azure app Configuration a GitHub-műveletek használatával indítja el az alkalmazás-konfigurációs példány frissítéseit egy GitHub-adattáron végrehajtott műveletek alapján. A GitHub-munkafolyamatok olyan konfigurációs frissítéseket indítanak el, amelyek lehetővé teszik a frissítések integrálását az alkalmazás kódjának frissítéséhez használt munkafolyamatba.

A GitHub-műveletek [munkafolyamat](https://help.github.com/articles/about-github-actions#workflow) a GitHub-tárházban automatikus folyamatot határoz meg. Ezzel a folyamattal megtudhatja, hogyan hozhat létre és helyezhet üzembe GitHub-projektet a GitHubon. Az Azure-alkalmazás konfigurációja lehetővé teszi az *Azure-alkalmazás konfigurációjának szinkronizálását* , amely lehetővé teszi az alkalmazás-konfigurációs példányok frissítéseinek frissítését a forrás tárházban végzett módosítások során. 

A tárház `/.github/workflows/` útvonalán található YAML (. YML) a munkafolyamatot határozza meg. Ez a definíció tartalmazza a munkafolyamat lépéseit és paramétereit.

A GitHub-események, például az adattárba való leküldés megindíthatnak egy GitHub-műveleti munkafolyamatot.  Az *Azure-alkalmazás konfigurációs szinkronizálási* művelete lehetővé teszi, hogy egy adott GitHub-művelet bekövetkeztekor egy alkalmazás-konfigurációs példány frissítését aktiválja. A konfigurációs frissítéseket aktiválhatja az alkalmazás konfigurációs fájljainak leküldésekor, áttekintésekor vagy elágazásakor ugyanúgy, mint az alkalmazás kódjával.

A GitHub [dokumentációja](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) részletes áttekintést nyújt a GitHub-munkafolyamatokról és-műveletekről. 

## <a name="enable-github-actions-in-your-repository"></a>GitHub-műveletek engedélyezése a tárházban
A GitHub-művelet elindításához nyissa meg a tárházat, és válassza a **műveletek** fület. kattintson az **Új munkafolyamat**elemre, majd **állítsa be a munkafolyamatot**. Végül keresse meg a piactéren az "Azure app Configuration Sync" kifejezést.
> [!div class="mx-imgBorder"]
> ![válassza a művelet fület](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![válassza ki az alkalmazás-konfiguráció szinkronizálása műveletet](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Konfigurációs fájlok szinkronizálása leküldéses üzenet után
Ez a művelet szinkronizálja az Azure-alkalmazás konfigurációs fájljait, ha a módosítást `appsettings.json`ba küldi. Amikor egy fejlesztő leküldi a `appsettings.json`módosítást, az alkalmazás konfigurációja szinkronizálási művelet frissíti az alkalmazás konfigurációs példányát az új értékekkel.

A munkafolyamat első szakasza azt határozza meg, hogy a művelet *elindít egy* `appsettings.json`t tartalmazó *leküldéses* műveletet a *fő* ágra. A második szakasz azokat a feladatokat sorolja fel, amelyek a művelet elindítása után futnak. A művelet megkeresi a kapcsolódó fájlokat, és frissíti az alkalmazás konfigurációs példányát az adattárban titkosként tárolt kapcsolati sztring használatával.  A GitHubon található titkok használatával kapcsolatos további információkért lásd a [githubról szóló cikket](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) a titkosított titkok létrehozásával és használatával kapcsolatban.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Dinamikus felirat használata szinkronizáláskor
Az előző művelet frissíti az alkalmazás konfigurációs példányát, amikor `appsettings.json` frissül. Ez a művelet minden egyes szinkronizáláshoz beszúr egy dinamikus címkét, így biztosítva, hogy minden egyes szinkronizálás egyedi módon azonosítható legyen, és lehetővé tegye a kód módosításának hozzárendelését a konfiguráció módosításaira.

A munkafolyamat első szakasza azt határozza meg, hogy a művelet *elindít egy* `appsettings.json`t tartalmazó *leküldéses* műveletet a *fő* ágra. A második szakasz egy olyan feladatot futtat, amely egyedi címkét hoz létre a konfigurációs frissítéshez a véglegesítő kivonat alapján. Ekkor a rendszer frissíti az alkalmazás konfigurációs példányát az új értékekkel és a frissítés egyedi címkéjével.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Szigorú szinkronizálás használata
Ha a szigorú mód engedélyezve van, a szinkronizálás biztosítja, hogy az alkalmazás konfigurációs példánya pontosan a megadott előtaghoz és címkéhez tartozó konfigurációs fájllal egyezzen. A konfigurációs fájlban nem szereplő, azonos előtaggal és címkével rendelkező kulcs-érték párok törlődnek. 
 
Ha a szigorú mód nincs engedélyezve, a szinkronizálás csak a konfigurációs fájl kulcs-értékeit fogja beállítani. A rendszer nem törli a kulcs-érték párokat. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>A GitHub-művelet maximális mélységének használata
A beágyazott JSON-attribútumok alapértelmezett viselkedése a teljes objektum összeolvasztása.  Az alábbi JSON a kulcs-érték párokat határozza meg:

| Paraméter | Érték |
| --- | --- |
| Objektum: belső: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Ha a beágyazott objektum a konfigurációs példánynak leküldhető értékre van kiválasztva, a *mélység* értékével állíthatja le az összeolvasztást a megfelelő mélységben. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

A 2. mélysége miatt a fenti példa a következő kulcs-érték párokat adja vissza:

| Paraméter | Érték |
| --- | --- |
| Objektum: belső | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>A műveleti bemenetek ismertetése
A bemeneti paraméterek a művelet által a Futtatás során használt adatokat határozzák meg.  Az alábbi táblázat az alkalmazás konfigurációs szinkronizálása által elfogadott bemeneti paramétereket és az egyes értékek várt értékeit tartalmazza.  A GitHub-műveletekkel kapcsolatos művelet-bemenetekkel kapcsolatos további információkért lásd a GitHub [dokumentációját](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> A bemeneti azonosítók a kis-és nagybetűk megkülönböztetése.


| Bemeneti név | Kötelező? | Érték |
|----|----|----|
| configurationFile | Igen | A tárházban található konfigurációs fájl relatív elérési útja.  A glob-minták támogatottak, és több fájlt is tartalmazhatnak. |
| format | Igen | A konfigurációs fájl fájlformátuma.  Az érvényes formátumok a következők: JSON, YAML, Properties. |
| connectionString | Igen | Az alkalmazás konfigurációs példányához tartozó kapcsolatok karakterlánca. A rendszer a GitHub-tárházban titkosként tárolja a kapcsolatok karakterláncát, és csak a titkos nevet kell használni a munkafolyamatban. |
| elválasztó | Igen | A konfigurációs fájl kulcs-érték párokra való összeolvasztásakor használt elválasztó.  Az érvényes értékek a következők:. , ; : - _ __ / |
| prefix | Nem | A kulcsok elejéhez hozzáadandó előtag. |
| label | Nem | Kulcs-érték párok beállításakor használt címke Ha nincs megadva, a rendszer null címkét használ. |
| szigorú | Nem | Logikai érték, amely meghatározza, hogy engedélyezve van-e a szigorú üzemmód. Az alapértelmezett értéke FALSE (hamis). |
| mélység | Nem | A konfigurációs fájl összeolvasztásának maximális mélysége.  A mélységnek pozitív számnak kell lennie.  Az alapértelmezett érték nem rendelkezik maximális mélységgel. |
| tags | Nem | Megadja a kulcs-érték párokon beállított címkét.  A várt formátum a következő alakzat JSON-objektumának sztringesített formája: {[propertyName: string]: string;} Minden tulajdonságnév-érték címkévé válik. |

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan automatizálható az alkalmazás konfigurációjának szinkronizálása a GitHub művelettel, és hogyan használható az alkalmazás-konfigurációs példány frissítéseinek automatizálására. A következő [cikkből](./concept-app-configuration-event.md)megtudhatja, hogy az Azure-alkalmazások konfigurációjának hogyan működik a kulcs-érték párok változásaira.
