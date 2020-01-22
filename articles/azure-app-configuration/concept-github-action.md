---
title: GitHub-műveletek használata az Azure app Configuration Sync használatával
description: A GitHub-műveletek használatával aktiválhatja az alkalmazás konfigurációs példányának frissítését, ha a meghatározott műveleteket egy GitHub-adattáron hajtja végre.
author: jpconnock
ms.author: jeconnoc
ms.date: 01/14/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 52fa9a94d86dac2d49b078f98aaa494fbebf52d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294447"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Az alkalmazás konfigurációs példányának szinkronizálása a GitHub-műveletekkel
Az Azure-alkalmazás konfigurációja GitHub-műveleteket használ egy alkalmazás konfigurációs példányának frissítéséhez, amikor egy GitHub-adattáron végrehajtott művelet aktiválja. A GitHub-munkafolyamatok segítségével frissítheti az alkalmazások konfigurációját, így az alkalmazás-konfiguráció frissítéseinek az alkalmazás kódjának frissítéséhez használt munkafolyamatba való integrálásával is felhasználható.

A GitHub-műveletek [munkafolyamat](https://help.github.com/articles/about-github-actions#workflow) a GitHub-tárházban definiált automatizált folyamat. Ezzel a folyamattal megtudhatja, hogyan hozhat létre és helyezhet üzembe GitHub-projektet a GitHubon. Az Azure-alkalmazás konfigurációja lehetővé teszi az *Azure-alkalmazás konfigurációjának szinkronizálását* , amely lehetővé teszi az alkalmazás-konfigurációs példányok frissítéseinek frissítését a forrás tárházban végzett módosítások során. 

A munkafolyamatot egy YAML (. YML) határozza meg, amely a tárház `/.github/workflows/` útvonalán található. Ez a definíció a munkafolyamatot meghatározó különböző lépéseket és paramétereket tartalmazza.

A GitHub-események, például az adattárba való leküldés megindíthatnak egy GitHub-műveleti munkafolyamatot.  Az Azure biztosítja az *Azure app Configuration Sync* műveletet, amely lehetővé teszi, hogy egy adott GitHub-művelet bekövetkeztekor egy alkalmazás-konfigurációs példány frissítését aktiválja. Ez lehetővé teszi a csapatok számára, hogy a GitHub alapvető funkcióit használják az alkalmazás-konfigurációs fájlok leküldésekor, áttekintésekor vagy elágazásakor ugyanúgy, mint az alkalmazás kódjával.

A GitHub [dokumentációja](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) részletes áttekintést nyújt a GitHub-munkafolyamatokról és-műveletekről. 

## <a name="enable-github-actions-in-your-repository"></a>GitHub-műveletek engedélyezése a tárházban
A GitHub-művelet elindításához nyissa meg a tárházat, és válassza a **műveletek** fület. Keresse meg és válassza ki a GitHub műveletet a piactéren az "Azure app Configuration Sync" kifejezésre való kereséssel. 

> [!div class="mx-imgBorder"]
> ![válassza a művelet fület](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![válassza ki az alkalmazás konfigurációjának SYN műveletét](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Konfigurációs fájlok szinkronizálása leküldéses üzenet után
Ez a művelet szinkronizálja az Azure-alkalmazás konfigurációs fájljait, ha a módosítást `appsettings.json`ba küldi. Ha egy fejlesztő a `appsettings.json`módosítást hajt végre, az alkalmazás konfigurációs szinkronizálási művelete frissíti az alkalmazás konfigurációs példányát az új értékekkel.

A munkafolyamat első szakasza azt határozza meg, hogy a művelet *elindít egy* `appsettings.json`t tartalmazó *leküldéses* műveletet a *fő* ágra. A második szakasz azokat a feladatokat sorolja fel, amelyek a művelet elindítása után futnak. A művelet megkeresi a kapcsolódó fájlokat, és frissíti az alkalmazás konfigurációs példányát az adattárban titkosként tárolt kapcsolati sztring használatával.  A titkoknak a githubon való használatáról további információt a titkosított titkok létrehozásáról és használatáról szóló [cikkben](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) talál.

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
Az előző művelettel egyszerűen frissült az alkalmazás konfigurációs példánya, amikor `appsettings.json` frissül. Ez a művelet minden egyes szinkronizáláshoz beszúr egy dinamikus címkét, így biztosítva, hogy minden egyes szinkronizálás egyedi módon azonosítható legyen.  Ez lehetővé teszi, hogy a kód módosításai gyorsan le legyenek képezve a konfiguráció módosításaira.

A munkafolyamat első szakasza azt határozza meg, hogy a művelet *elindít egy* `appsettings.json`t tartalmazó *leküldéses* műveletet a *fő* ágra. A második szakasz egy feladatot futtat, amely létrehoz egy egyedi címkét a konfigurációs frissítéshez a véglegesítő kivonat alapján. Ekkor a rendszer frissíti az alkalmazás konfigurációs példányát az új értékekkel és a frissítés egyedi címkéjével.

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

| Jelmagyarázat | Value (Díj) |
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

A 2. mélység miatt a fenti példa a következő kulcsot adja vissza: érték pár:

| Jelmagyarázat | Value (Díj) |
| --- | --- |
| Objektum: belső | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>A műveleti bemenetek ismertetése
A bemeneti paraméterek a művelet által a Futtatás során használt adatokat határozzák meg.  Az alábbi táblázat az alkalmazás konfigurációs szinkronizálása által elfogadott bemeneti paramétereket és az egyes értékek várt értékeit tartalmazza.  A GitHub-műveletekkel kapcsolatos művelet-bemenetekkel kapcsolatos további információkért lásd a GitHub [dokumentációját](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> A bemeneti azonosítók a kis-és nagybetűk megkülönböztetése.


| Bemeneti név | Kötelező? | Value (Díj) |
|----|----|----|
| configurationFile | Igen | A tárházban lévő konfigurációs fájl elérési útja, a tárház gyökeréhez képest.  A glob-minták támogatottak, és több fájlt is tartalmazhatnak. |
| formátum | Igen | A konfigurációs fájl fájlformátuma.  Az érvényes formátumok a következők: JSON, YAML, Properties. |
| connectionString | Igen | Az alkalmazás konfigurációs példányához tartozó kapcsolatok karakterlánca. A rendszer a GitHub-tárházban titkosként tárolja a kapcsolatok karakterláncát, és csak a titkos nevet kell használni a munkafolyamatban. |
| elválasztó | Igen | A konfigurációs fájl kulcs-érték párokra való összeolvasztásakor használt elválasztó.  Az érvényes értékek a következők:. , ; : - _ __ / |
| előtag | Nem | A kulcsok elejéhez hozzáadandó előtag. |
| label | Nem | Kulcs-érték párok beállításakor használt címke Ha nincs megadva, a rendszer null címkét használ. |
| szigorú | Nem | Logikai érték, amely meghatározza, hogy engedélyezve van-e a szigorú üzemmód. Alapértelmezett értéke hamis. |
| mélység | Nem | A konfigurációs fájl összeolvasztásának maximális mélysége.  A mélységnek pozitív számnak kell lennie.  Az alapértelmezett érték nem rendelkezik maximális mélységgel. |
| tags | Nem | Megadja a kulcs-érték párokon beállított címkét.  A várt formátum a következő alakzat JSON-objektumának sztringesített formája: {[propertyName: string]: string;} Minden tulajdonságnév-érték címkévé válik. |

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan automatizálható az alkalmazás konfigurációjának szinkronizálása a GitHub művelettel, és hogyan használható az alkalmazás-konfigurációs példány frissítéseinek automatizálására. A következő [cikkből](./concept-app-configuration-event.md)megtudhatja, hogy az Azure-alkalmazások konfigurációjának hogyan működik a kulcs-érték párok változásaira.
