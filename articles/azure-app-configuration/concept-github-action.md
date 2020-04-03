---
title: A GitHub-tárház szinkronizálása az alkalmazáskonfigurációval
description: A GitHub-műveletek segítségével automatikusan frissítheti az alkalmazáskonfigurációs példányt a GitHub-tárház frissítésekén.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585481"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>A GitHub-tárház szinkronizálása az alkalmazáskonfigurációval

Azok a csapatok, amelyek továbbra is szeretnék használni a meglévő forrásvezérlési gyakorlataikat, a GitHub-műveletek segítségével automatikusan szinkronizálhatják a GitHub-tárházat az alkalmazáskonfigurációs tárolójukkal. Ez lehetővé teszi, hogy a szokásos módon módosítsa a konfigurációs fájlokat, miközben az alkalmazáskonfigurációs előnyöket, például: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• A kódon kívüli központosított konfiguráció <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Konfiguráció frissítése a teljes alkalmazás újratelepítése nélkül <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integráció olyan szolgáltatásokkal, mint az Azure App Service és functions. 

A GitHub-műveletek [munkafolyamat](https://help.github.com/articles/about-github-actions#workflow) a GitHub-tárházban egy automatikus folyamatot határoz meg. Az *Azure App konfigurációs szinkronizálási* művelet elindítja az alkalmazáskonfigurációs példány frissítéseit, amikor a forrástárházban módosításokat hajt végre. A tárat a `/.github/workflows/` tárház elérési útján található YAML (.yml) fájlt használja a lépések és paraméterek meghatározásához. A konfigurációs frissítéseket az alkalmazás konfigurációs fájljainak lenyomásakor, véleményezésekor vagy elágazásakor ugyanúgy aktiválhatja, mint az alkalmazáskóddal.

A GitHub [dokumentációja](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) részletes áttekintést nyújt a GitHub-munkafolyamatokról és -műveletekről. 

## <a name="enable-github-actions-in-your-repository"></a>GitHub-műveletek engedélyezése a tárházban
A GitHub-művelet használatának megkezdéséhez nyissa meg a tárházat, és válassza a **Műveletek** lapot. Kattintson az **Új munkafolyamat**elemre, majd saját maga állítsa be **a munkafolyamatot.** Végül keressen a piactéren az "Azure App Configuration Sync" kifejezésre.
> [!div class="mx-imgBorder"]
> ![A Művelet lap kijelölése](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Az alkalmazáskonfiguráció szinkronizálási műveletének kiválasztása](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Konfigurációs fájlok szinkronizálása leküldéses leküldéses után
Ez a művelet szinkronizálja az Azure App `appsettings.json`Configuration fájljait, amikor egy módosítást a rendszerbe rendel. Amikor egy fejlesztő leküldéses egy `appsettings.json`módosítást, az Alkalmazáskonfigurációs szinkronizálás művelet frissíti az alkalmazáskonfigurációs példányt az új értékekkel.

A munkafolyamat első szakasza azt határozza meg, hogy `appsettings.json` a művelet a *főágnak* tartalmazó *leküldéses műveletet* *váltson* ki. A második szakasz felsorolja a feladatok futtatását, ha a művelet aktiválódik. A művelet ellenőrzi a megfelelő fájlokat, és frissíti az alkalmazás konfigurációs példányát a tárházban titkos kódként tárolt kapcsolati karakterlánc használatával.  A githubon a titkos kulcsok használatáról a [GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) titkosított titkos kulcsok létrehozásáról és használatáról szóló cikkében olvashat bővebben.

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

## <a name="use-a-dynamic-label-on-sync"></a>Dinamikus címke használata szinkronizálásesetén
Az előző művelet frissíti az `appsettings.json` alkalmazáskonfigurációs példányt, amikor frissül. Ez a művelet dinamikus címkét szúr be minden szinkronizálásra, biztosítva, hogy minden szinkronizálás egyedileg azonosítható legyen, és lehetővé teszi a kódmódosítások leképezését a konfigurációs módosításokhoz.

A munkafolyamat első szakasza azt határozza meg, hogy `appsettings.json` a művelet a *főágnak* tartalmazó *leküldéses műveletet* *váltson* ki. A második szakasz egy feladatot futtat, amely a véglegesítési kivonat alapján egyedi címkét hoz létre a konfigurációs frissítéshez. A feladat ezután frissíti az alkalmazáskonfigurációs példányt az új értékekkel és a frissítés egyedi címkéjével.

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
Ha a szigorú mód engedélyezve van, a szinkronizálás biztosítja, hogy az alkalmazáskonfigurációs példány pontosan megegyezik az adott előtag és címke konfigurációs fájljának. A konfigurációs fájlban nem szereplő előtaggal és címkével rendelkező kulcs-érték párok törlődnek. 
 
Ha a szigorú mód nincs engedélyezve, a szinkronizálás csak a konfigurációs fájl kulcsértékeit állítja be. A kulcs-érték párok nem törlődnek. 

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

## <a name="use-max-depth-to-limit-github-action"></a>A maximális mélység használata a GitHub-művelet korlátozásához
A beágyazott JSON-attribútumok alapértelmezett viselkedése a teljes objektum összeolvasztása.  Az alábbi JSON határozza meg ezt a kulcs-érték párt:

| Kulcs | Érték |
| --- | --- |
| Objektum:Belső:Belső billentyű | Belső érték |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Ha a beágyazott objektum a konfigurációs példányba leadott érték, a *mélységérték* segítségével leállíthatja az összeolvasztást a megfelelő mélységben. 

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

2 mélységben a fenti példa most a következő kulcs-érték párt adja vissza:

| Kulcs | Érték |
| --- | --- |
| Objektum:Belső | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>A műveletbemenetek ismertetése
A bemeneti paraméterek a művelet által futásidőben használt adatokat határozzák meg.  Az alábbi táblázat az App Configuration Sync által elfogadott bemeneti paramétereket és az egyes konfigurációs szinkronizáláshoz várt értékeket tartalmazza.  A GitHub-műveletek műveletbemeneteiről a GitHub [dokumentációjában](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)olvashat bővebben.

> [!Note]
> A bemeneti azonosítók nem szokták a kis- és nagybetűket.


| Bemenet neve | Kötelező? | Érték |
|----|----|----|
| konfigurációs fájl | Igen | A tárházban lévő konfigurációs fájl relatív elérési útja.  A Glob-minták támogatottak, és több fájlt is tartalmazhatnak. |
| Formátum | Igen | A konfigurációs fájl fájlformátuma.  Érvényes formátumok: JSON, YAML, tulajdonságok. |
| connectionString (kapcsolati karakterlánc) | Igen | Az alkalmazáskonfigurációs példány csatlakozási karakterlánca. A kapcsolati karakterláncot titkos ként kell tárolni a GitHub-tárházban, és csak a titkos nevet kell használni a munkafolyamatban. |
| Elválasztó | Igen | Az elválasztó a konfigurációs fájl kulcs-érték párokká történő összeolvasztásakor használatos.  Érvényes értékek: . , ; : - _ __ / |
| Előtag | Nem | A kulcsok indításához hozzáadandó előtag. |
| label | Nem | A kulcs-érték párok beállításakor használt címke. Ha nincs megadva, null címke lesz használva. |
| Szigorú | Nem | Logikai érték, amely meghatározza, hogy a szigorú mód engedélyezve van-e. Az alapértelmezett érték a hamis. |
| Mélység | Nem | Maximális mélység a konfigurációs fájl összeolvasztásához.  A mélységnek pozitív számnak kell lennie.  Az alapértelmezett érték nem rendelkezik maximális mélységgel. |
| címkét | Nem | Megadja a kulcs-érték párok címkekészletét.  A várt formátum a JSON-objektum karakterláncba kötött formája, amely a következő alakzatból áll: { [propertyName: string]: string; } Minden tulajdonságnév-érték címké válik. |

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az alkalmazáskonfigurációs szinkronizálási GitHub-művelettel, és arról, hogyan automatizálható az alkalmazáskonfigurációs példány frissítései. Ha meg szeretné tudni, hogy az Azure App Configuration hogyan reagál a kulcsérték-párok változásaira, folytassa a következő [cikkel.](./concept-app-configuration-event.md)
