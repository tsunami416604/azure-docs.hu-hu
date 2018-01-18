---
title: "Helyi üzembe helyezés Git használatával az Azure App Service szolgáltatásban"
description: "Útmutató az Azure App Service helyi Git-telepítésének engedélyezése."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: cfowler
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 7667f94ac4e7edd4e94d6605adefea469102a0c4
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi üzembe helyezés Git használatával az Azure App Service szolgáltatásban

Ez az oktatóanyag bemutatja, hogyan üzembe az alkalmazást, amely [Azure Web Apps](app-service-web-overview.md) a Git-tárházat a helyi számítógépen. App Service támogatja ezt a módszert a a **helyi Git** a központi telepítési lehetőség a [Azure-portálon].

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Git. Letöltheti a bináris telepítési [Itt](http://www.git-scm.com/downloads).
* Git alapszintű ismeretét.
* Egy Microsoft Azure-fiók. Ha nincs fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial), vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Ha az Azure App Service első lépései az Azure-fiók regisztrálása előtt szeretné, folytassa a [App Service kipróbálása](https://azure.microsoft.com/try/app-service/), ahol azonnal létrehozhat egy rövid élettartamú alapszintű alkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
>

## <a name="Step1"></a>1. lépés: A helyi tárház létrehozása

A következő feladatokat hozzon létre egy új Git-tárházat.

1. Indítsa el a parancssori eszköz, például a **Git bash eszközt** (Windows) vagy **Bash** (Unix rendszerhéj). OS X rendszeren a parancssor keresztül hozzáférhet a **Terminálszolgáltatások** alkalmazás.
1. Keresse meg azt a könyvtárat, amelyben a tartalom központi telepítéséhez volna található.
1. A következő paranccsal egy új Git-tárház inicializálása:

  ```bash
  git init
  ```

## <a name="Step2"></a>2. lépés: A tartalom véglegesítése

App Service számos programozási nyelven készült alkalmazások támogatja.

1. Ha a tárház már nem tartalmazza a tartalmat, statikus .html fájl hozzáadásához az alábbiak szerint; vagy hagyja ki ezt a lépést:
   * Egy szövegszerkesztő használatával hozzon létre egy új fájlt **index.html** a Git-tárház gyökérkönyvtárában
   * Adja hozzá a következő szöveget a index.html tartalma fájlt, és mentse: *Hello Git!*
1. A parancssorból ellenőrizze, hogy a Git-tárház gyökerében. A következő parancs használatával adja hozzá a fájlok a tárházhoz:

    ```bash
    git add -A
    ```
    
1. Ezt követően véglegesítse a módosításokat a tárházba a következő paranccsal:

    ```bash
    git commit -m "Hello Azure App Service"
    ```

## <a name="Step3"></a>3. lépés: Az App Service alkalmazás tárház engedélyezése

A következő lépésekkel ahhoz, hogy egy App Service-alkalmazás Git-tárházat.

1. Jelentkezzen be az [Azure-portálon].
1. Az App Service alkalmazás nézetben kattintson **beállítások > központi telepítés forrásának**. Kattintson a **forrás választása**, majd kattintson a **helyi Git-tárház**, és kattintson a **OK**.

    ![Helyi git-tárház](./media/app-service-deploy-local-git/local_git_selection.png)

1. Ha ez az első idő beállítása a tárházat az Azure-ban, a bejelentkezési hitelesítő adatok létrehozása az szeretné. Használhatja őket jelentkezzen be a Azure tárház és leküldéses módosítása a helyi Git-tárház a. A webalkalmazás nézetből kattintson **beállítások > üzembe helyezési hitelesítő adatok**, majd konfigurálja a központi telepítés felhasználónevét és jelszavát. Amikor elkészült, kattintson a **mentése**.

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>4. lépés: A projekt telepítése

Az alábbi lépések segítségével közzététele az alkalmazás az App Service helyi Git használatával.

1. A webalkalmazás nézetben az Azure portálon kattintson **beállítások > Tulajdonságok** a a **Git URL-cím**.

    ![](./media/app-service-deploy-local-git/git_url.png)

    **Git URL-cím** a helyi tárházból telepítendő Távoli hivatkozás. Az URL-cím a következő lépések segítségével.
1. A parancssor használatával ellenőrizze, hogy a helyi Git-tárház gyökérkönyvtárában található.
1. Használjon `git remote` hozzáadása a távoli leírásában felsorolt **Git URL-cím** az 1. lépés. A parancs a következőhöz hasonlít:

    ```
    git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git
    ```

   > [!NOTE]
   > A **távoli** parancs hozzáad egy elnevezett hivatkozást egy távoli tárházba. Ebben a példában a webalkalmazás-tárház "azure" nevű hivatkozást hoz létre.
   >

1. Küldje le a tartalmat az új App Service **azure** távoli létrehozott.

    ```bash
    git push azure master
    ```

    A korábban létrehozott Azure-portálon az üzembe helyezési hitelesítő adatok alaphelyzetbe jelszó megadását kéri. Adja meg a jelszót (vegye figyelembe, hogy Gitbash nem jeleníti meg a konzol csillag, írja be a jelszót). 
1. Térjen vissza az alkalmazást az Azure portálon. A legutóbbi leküldéses a naplóbejegyzést kell megjelennie a **központi telepítések** nézet.

    ![](./media/app-service-deploy-local-git/deployment_history.png)

1. Kattintson a **Tallózás** a weblap alkalmazás a tartalom központi telepítésének ellenőrzése gombra.

## <a name="Step5"></a>Hibaelhárítás

Hibák vagy problémák gyakran fordul elő, ha a Git használatával az Azure App Service alkalmazás közzététele a következők:

---
**Jelenség**:`Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**OK**: Ez a hiba akkor fordulhat elő, ha az alkalmazás nem válik működőképessé.

**Megoldási**: Indítsa el az alkalmazást az Azure-portálon. Git-telepítés nem érhető el, ha a webalkalmazás le van állítva.

---
**Jelenség**:`Couldn't resolve host 'hostname'`

**OK**: Ez a hiba akkor fordulhat elő, ha a megadott létrehozásakor az "azure" távoli címadatokat helytelen volt.

**Megoldási**: használja a `git remote -v` paranccsal listát készíthet az összes távvezérlő, és a kapcsolódó URL-címe. Ellenőrizze, hogy helyes-e az "azure" távoli URL-CÍMÉT. Ha szükséges, távolítsa el és hozza létre újból a távoli, a helyes URL-cím használatával.

---
**Jelenség**:`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**OK**: Ez a hiba akkor fordulhat elő, ha nem ad meg egy ágat során `git push`, vagy ha nem állított a `push.default` értéket `.gitconfig`.

**Megoldási**: végrehajtani a leküldéses műveletet, adja meg a főágba. Példa:

```bash
git push azure master
```

---
**Jelenség**:`src refspec [branchname] does not match any.`

**OK**: Ez a hiba akkor fordulhat elő, ha megkísérli leküldése egy ágat a főadatbázison kívül az "Azure" távoli.

**Megoldási**: végrehajtani a leküldéses műveletet, adja meg a főágba. Példa:

```bash
git push azure master
```

---
**Jelenség**:`RPC failed; result=22, HTTP code = 5xx.`

**OK**: Ez a hiba akkor fordulhat elő, ha megkísérli a nagy git-tárház leküldéses HTTPS-KAPCSOLATON keresztül.

**Megoldási**: a helyi gépen a postBuffer nagyobb git konfigurációjának módosítása

```bash
git config --global http.postBuffer 524288000
```

---
**Jelenség**:`Error - Changes committed to remote repository but your web app not updated.`

**OK**: Ez a hiba akkor fordulhat elő, ha telepít egy Node.js-alkalmazás, amely meghatározza a további szükséges modulokat package.json fájlt tartalmazó.

**Megoldási**: további üzeneteket tartalmazó "npm hiba!" Ez a hiba a naplózott előtt legyen, és a hiba esetén további környezetet biztosíthat. A következő ismert oka ezt a hibát, és a megfelelő "npm hiba!" üzenet:

* **Nem megfelelően formázott package.json fájl**: npm hiba! Nem lehetett olvasni a függőségek.
* **Natív modul, amely nem rendelkezik Windows bináris terjesztési**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      VAGY
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>További források

* [A Git dokumentációja](http://git-scm.com/documentation)
* [Project Kudu dokumentációja](https://github.com/projectkudu/kudu/wiki)
* [Folyamatos üzembe helyezés az Azure App Service](app-service-continuous-deployment.md)
* [A PowerShell használata az Azure-ban](/powershell/azure/overview)
* [Az Azure parancssori felület használatával](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure-portálon]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure Command-Line Interface]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
